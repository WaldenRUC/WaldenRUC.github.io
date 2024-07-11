---
title: 'Accelerate'
date: 2024-07-11
permalink: /posts/2024/07/accelerate/
tags:
  - Pytorch
  - Python
---
# Accelerate学习笔记

## 本文主要记录huggingface-accelerate中可能被遗忘的知识点。主要内容参考[accelerate教程](https://huggingface.co/datasets/HuggingFace-CN-community/translation/blob/main/eat_accelerate_in_30_minites.md)和[官方文档](https://huggingface.co/docs/accelerate/package_reference/accelerator)


### 为什么要使用accelerate?

在大模型时代，单卡训练模型特别耗时且浪费了多卡通信的优势，因此在多GPU的条件下训练模型需要一个便捷的方法。accelerate提供了一个便捷的操作，并且加速了训练的过程。同一套代码可以在CPU/单GPU/多GPU（DDP）条件下通用。

### DP还是DDP？

DP：DataParallel: 实现简单，单机多卡，GPU分成主节点和从节点，存在负载不均衡的问题

DDP: DistributedDataParallel: 实现复杂，各个GPU是**平等的**，无负载不均衡


### 官方示例

```python
import os,PIL 
import numpy as np
from torch.utils.data import DataLoader, Dataset
import torch 
from torch import nn 

import torchvision 
from torchvision import transforms
import datetime

#======================================================================
# import accelerate
from accelerate import Accelerator
from accelerate.utils import set_seed
#======================================================================


def create_dataloaders(batch_size=64):
    transform = transforms.Compose([transforms.ToTensor()])

    ds_train = torchvision.datasets.MNIST(root="./mnist/",train=True,download=True,transform=transform)
    ds_val = torchvision.datasets.MNIST(root="./mnist/",train=False,download=True,transform=transform)

    dl_train =  torch.utils.data.DataLoader(ds_train, batch_size=batch_size, shuffle=True,
                                            num_workers=2,drop_last=True)
    dl_val =  torch.utils.data.DataLoader(ds_val, batch_size=batch_size, shuffle=False, 
                                          num_workers=2,drop_last=True)
    return dl_train,dl_val


def create_net():
    net = nn.Sequential()
    net.add_module("conv1",nn.Conv2d(in_channels=1,out_channels=512,kernel_size = 3))
    net.add_module("pool1",nn.MaxPool2d(kernel_size = 2,stride = 2)) 
    net.add_module("conv2",nn.Conv2d(in_channels=512,out_channels=256,kernel_size = 5))
    net.add_module("pool2",nn.MaxPool2d(kernel_size = 2,stride = 2))
    net.add_module("dropout",nn.Dropout2d(p = 0.1))
    net.add_module("adaptive_pool",nn.AdaptiveMaxPool2d((1,1)))
    net.add_module("flatten",nn.Flatten())
    net.add_module("linear1",nn.Linear(256,128))
    net.add_module("relu",nn.ReLU())
    net.add_module("linear2",nn.Linear(128,10))
    return net 



def training_loop(epochs = 5,
                  lr = 1e-3,
                  batch_size= 1024,
                  ckpt_path = "checkpoint.pt",
                  mixed_precision="no", #'fp16' or 'bf16'
                 ):
    
    train_dataloader, eval_dataloader = create_dataloaders(batch_size)
    model = create_net()
    

    optimizer = torch.optim.AdamW(params=model.parameters(), lr=lr)
    lr_scheduler = torch.optim.lr_scheduler.OneCycleLR(optimizer=optimizer, max_lr=25*lr, 
                              epochs=epochs, steps_per_epoch=len(train_dataloader))
    
    #======================================================================
    # initialize accelerator and auto move data/model to accelerator.device
    set_seed(42)
    accelerator = Accelerator(mixed_precision=mixed_precision)
    accelerator.print(f'device {str(accelerator.device)} is used!')
    model, optimizer,lr_scheduler, train_dataloader, eval_dataloader = accelerator.prepare(
        model, optimizer,lr_scheduler, train_dataloader, eval_dataloader)
    #======================================================================
    

    for epoch in range(epochs):
        model.train()
        for step, batch in enumerate(train_dataloader):
            features,labels = batch
            preds = model(features)
            loss = nn.CrossEntropyLoss()(preds,labels)
            
            #======================================================================
            #attention here! 
            accelerator.backward(loss) #loss.backward()
            #======================================================================
            
            optimizer.step()
            lr_scheduler.step()
            optimizer.zero_grad()
            
        
        model.eval()
        accurate = 0
        num_elems = 0
        
        for _, batch in enumerate(eval_dataloader):
            features,labels = batch
            with torch.no_grad():
                preds = model(features)
            predictions = preds.argmax(dim=-1)
            
            #======================================================================
            #gather data from multi-gpus (used when in ddp mode)
            predictions = accelerator.gather_for_metrics(predictions)
            labels = accelerator.gather_for_metrics(labels)
            #======================================================================
            
            accurate_preds =  (predictions==labels)
            num_elems += accurate_preds.shape[0]
            accurate += accurate_preds.long().sum()

        eval_metric = accurate.item() / num_elems
        
        #======================================================================
        #print logs and save ckpt  
        accelerator.wait_for_everyone()
        nowtime = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')
        accelerator.print(f"epoch【{epoch}】@{nowtime} --> eval_metric= {100 * eval_metric:.2f}%")
        net_dict = accelerator.get_state_dict(model)
        accelerator.save(net_dict,ckpt_path+"_"+str(epoch))
        #======================================================================
        
#training_loop(epochs = 5,lr = 1e-3,batch_size= 1024,ckpt_path = "checkpoint.pt",
#             mixed_precision="no") 
```
输出结果：
```shell
device cuda is used!
epoch【0】@2023-01-15 12:06:45 --> eval_metric= 95.20%
epoch【1】@2023-01-15 12:07:01 --> eval_metric= 96.79%
epoch【2】@2023-01-15 12:07:17 --> eval_metric= 98.47%
epoch【3】@2023-01-15 12:07:34 --> eval_metric= 98.78%
epoch【4】@2023-01-15 12:07:51 --> eval_metric= 98.87%
```

上述代码分为几个步骤：
1. 通过create_dataloaders()方法处理数据集，返回Train/dev dataloader
2. 加载nn.Sequential模型（实际情况中，继承nn.Module能设定更多个性化参数）
3. 设定AdamW参数优化器和学习率调度器
4. 初始化Accelerator，这里有几个参数需要注意设置：
- mixed_precision: 混合精度训练，可以选择no/fp16/bf16/fp8
- gradient_accumulation_steps: 梯度累加，在k(default=1)个step之后再根据累加的梯度更新参数，然后清空梯度
5. 将模型、优化器、学习率函数、dataloader全通过accelerator.prepare()包装一下
6. 在计算参数梯度的时候，使用accelerator.backward(loss)而非loss.backward()
7. 更新参数的时候，正常地更新优化器和学习率调度器，然后清空累计的梯度
8. 在预测的时候，需要用accelerator.gather_for_metrics()方法合并多卡预测的结果（包括labels，因为dataloader返回的结果也在不同的卡上）
9. 使用accelerator.wait_for_everyone()等待所有进程结束
10. 使用accelerator.get_state_dict()和accelerator.save()读取和保存模型参数字典


使用命令行运行程序时：
```shell
torchrun \ # python -m torch.distributed.run 
    --nproc_per_node 2 \
    --nnodes 2 \
    --rdzv_id 2299 \ # A unique job id 
    --rdzv_backend c10d \
    --rdzv_endpoint master_node_ip_address:29500 \
    ./nlp_example.py
```
可以用torchrun或者python -m distributed.run启动程序。

这段代码规定了使用两个机器（nnodes=2），每个机器两张卡（nproc_per_node=2）









