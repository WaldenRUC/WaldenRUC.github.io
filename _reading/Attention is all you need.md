---
title: 'Attention Is All You Need'
date: 2017-06-03
permalink: /reading/2017/06/Attention_Is_All_You_Need
tags:
  - network structure
---

### Reading Time: 23/06/25

| Title | Paper URL | Code | Publish Conf |
|---|---|---|---|
| Attention Is All You Need | [download](/files/reading/Attention_Is_All_You_Need/paper.pdf) | [code](https://github.com/jadore801120/attention-is-all-you-need-pytorch) | NeurIPS'17 | 

## Background & Introduction

现有的神经网络方法通常为堆叠的RNN或CNN架构。

RNN的主要问题是循环的结构导致其不能并行化(parallelization)计算。现有RNN网络也采用了注意力机制，但主体架构仍是循环神经网络。

本文提出了与现有网络架构完全不同的、仅仅依赖自注意力机制的Transformer，与它所对应的encoder和decoder结构。
该网络采用注意力机制，以从输入和输出中获取总体的依赖关系(global dependencies)。

Transformer的优势在于并行化，且模型的效果更好。

## Methods

encoder-decoder架构：
1. 用编码器将输入序列$(x_1, \cdots, x_n)$映射为连续的表示$\mathbf{z} = (z_1, \cdots, z_n)$
2. 用解码器将连续的表示逐步地解码成输出序列$(y_1, \cdots, y_m)$. 在解码阶段是自回归的(auto-regressive)，只能感知已生成的序列。

编码器分为上下两部分。
靠近输入端的部分，是一个**多头自注意力层**；远离输入端的部分，是一个**感知位置的全连接层**。

在每一个部分之后，连接着一个残差层和归一层(Add & Norm)。

解码器分为三部分。第一部分和第三部分对应着编码器的两个部分，而中间层是一个**交叉注意力层**。

Attention: 将一个query和对应的key,value映射成一个输出。
输出的值为value的带权和。

本文提出的是"Scaled Dot-Product Attention"，流程如下:
1. 计算query和**所有**key的点积，并按维数标准化(scaling factor)。
2. 将得到的结果softmax以得到权重。
3. 将该权重乘以每个key对应的value。

用公式表示为
$$
\text{Attention}(Q, K, V) = \text{softmax}(\frac{QK^T}{\sqrt{d_k}})V
$$

在脚注中，论文作者阐述了用$\sqrt{d_k}$作为缩放因子的原因：假设两个$d_k$维度的随机向量，他们点积的结果服从$\mu = 0, \sigma = d_k$的正态分布。因此我们用这个缩放因子来将点积结果标准化。否则，较大的方差会导致点积的结果差异较大，从而导致softmax之后的结果偏大或偏小，可能计算出极小的梯度。

多头注意力的作用：让模型关注不同子空间的表征信息。

每个子空间的输出将会被拼接在一起，然后投影映射回原来的维度空间。

假设原先的$Q, K, V$的维度为$d_{\text{model}}$，则每个注意力头将他们分别映射到$d_k, d_k, d_v$的子空间。最终attention的输出维度也是$d_v$。n个头的attention输出结果拼在一起，得到的维度大小为$n \times d_v$。接下来模型通过一个全连接层$W^{O}$，将结果映射回$d_{\text{model}}$子空间，和原先的$Q, K, V$维度相同。

在attention计算之后，是一个多层感知机。
感知机的第一层将输出变换到2048维，第二层变回512维。在第一层之后连接的是ReLU激活函数。公式表示为：
$$
\text{FFN}(x) = \text{max}(0, xW_1+b_1)W_2 + b_2
$$

位置编码：为了具有RNN的时序感知能力，Transformer添加了位置编码(positional encodings)。位置编码和词向量同维度，且他们相加的和作为encoder或decoder的输入。

假设某个词在句子中的位置为pos，则它的第2i和2i+1维度的位置编码（标量）为:
$$
PE_{(pos, 2i)} = sin(\frac{pos}{10000^{\frac{2i}{d_{\text{model}}}}})
$$
$$
PE_{(pos, 2i+1)} = cos(\frac{pos}{10000^{\frac{2i}{d_{\text{model}}}}})
$$
这样做的意义：根据三角函数公式，这样定义可以使得序列中(pos+k)位置的位置编码可以**由pos和k位置的位置编码线性表出**，从而使得模型能学习序列中的相对位置信息：
$$
PE(pos+k, 2i) = PE(pos, 2i)\times PE(k, 2i+1) + PE(pos, 2i+1)\times PE(k, 2i)
$$
$$
PE(pos+k, 2i+1) = PE(pos, 2i+1)\times PE(k, 2i+1) - PE(pos, 2i)\times PE(k, 2i)
$$

## Experiments

本文在WMT-2014机器翻译任务上开展实验，分别测试了英-德翻译和英-法翻译的模型表现。

有意思的是本文的优化器学习率的设置:
$$
\text{lr} = d_{\text{model}}^{-0.5} \times \text{min}(steps^{-0.5}, steps \times warmup\_steps^{-1.5})
$$
合理的解释是，越大的hidden state使得模型更大，因而微调时的学习率更小。在热身阶段的学习率逐步提升，在一定步数之后缓慢下降。

本文在两处地方添加了dropout：（1）在每个sub-layer的输出后；（2）在token/position embedding相加之后。

## Analysis

本文尝试了多种参数组合，例如注意力头的数量、k/v的维度大小等等(Table 3)。本文发现降低注意力机制中的key维度将影响模型的质量；用可学习的参数替换本文提出的正弦位置编码几乎不影响模型的性能。


## Conclusion & Summary

本文用一种完全基于注意力的架构刷了机器翻译的榜，而且作者也将希望寄托于扩展到多模态。这篇文章也是BERT的基础，因此我们需要研究其代码逻辑，参考了[该博客](https://zhuanlan.zhihu.com/p/398039366)。

首先看Embedding层：首先假设训练开始前的词向量(look-up table)是随机初始化的，并且根据词向量维度大小，对词嵌入缩放(缩放比例为$\sqrt{d_{model}}$)，当维度越大，放大比例越高。
```python
class Embeddings(nn.Module):
    def __init__(self, d_model, vocab):
        """
        :param d_model: word embedding维度
        :param vocab: 语料库词的数量
        """
        super(Embeddings, self).__init__()
        self.lut = nn.Embedding(vocab, d_model)
        self.d_model = d_model

    def forward(self, x):
        """
        :param x: 一个batch的输入，size = [batch, L], L为batch中最长句子长度。如果句子过长，则无法从预先设置的PE表中找到句子中对应词的positional embedding。
        """
        return self.lut(x) * math.sqrt(self.d_model)  #这里乘了一个权重，不改变维度. size = [batch, L, d_model]
```

接下来是位置向量的定义：transformer layer的输入向量是词向量与位置向量的总和，**并通过一层dropout**。

```python
class PositionalEncoding(nn.Module):
    def __init__(self, d_model, dropout, max_len=5000):
        """
        :param d_model: pe编码维度，一般与word embedding相同，方便相加
        :param dropout: drop out
        :param max_len: 语料库中最长句子的长度，即word embedding中的L
        """
        super(PositionalEncoding, self).__init__()
        self.dropout = nn.Dropout(p=dropout)

        # 计算pe编码
        pe = torch.zeros(max_len, d_model) # 建立空表，每行代表一个词的位置，每列代表一个编码位
        position = torch.arange(0, max_len).unsqueeze(1) # 建个arrange表示词的位置以便公式计算，size=(max_len,1)
        div_term = torch.exp(
            torch.arange(0, d_model, 2) * -(math.log(10000.0) / d_model)
        )   # 计算公式中10000**（-2i/d_model)
                             
        pe[:, 0::2] = torch.sin(position * div_term)  # 计算偶数维度的pe值
        pe[:, 1::2] = torch.cos(position * div_term)  # 计算奇数维度的pe值
        pe = pe.unsqueeze(0)  # size=(1, L, d_model)，为了后续与word_embedding相加,意为batch维度下的操作相同，这一步我感觉其实没必要，后续相加的时候会自动广播
        self.register_buffer('pe', pe)  # pe值是不参加训练的

    def forward(self, x):
        # 输入的最终编码 = word_embedding + positional_embedding
        x = x + Variable(self.pe[:, :x.size(1)],requires_grad=False) #size = [batch, L, d_model]
        return self.dropout(x) # size = [batch, L, d_model]
```
register_buffer()的作用：希望某些参数不更新，但仍然保留在model.state_dict()中。
在填充pe表时，position的大小是(max_len, 1)，div_term的大小是（d_model/2)，因此根据广播法则，从尾部维度开始计算，他们相乘得到的向量维度为(max_len, d_model/2)，分别填充词向量分量的偶数位置和奇数位置。这部分的代码写得很好，建议多学习下。

Self-Attention模块：多头自注意力层和FFN
```python
def attention(query, key, value, mask=None, dropout=None):

    d_k = query.size(-1)
    scores = torch.matmul(query, key.transpose(-2, -1))/math.sqrt(d_k)
    if mask is not None:
        scores = scores.masked_fill(mask == 0, -1e9)    # 注意：将传入参数mask=0的部分对应的scores的位置置为0.
    p_attn = F.softmax(scores, dim = -1)
    if dropout is not None:
        p_attn = dropout(p_attn)            # 注意：在计算value之前，对softmax结果执行dropout
    return torch.matmul(p_attn, value), p_attn
    
class MultiHeadedAttention(nn.Module):
    def __init__(self, h, d_model, dropout=0.1):
        super(MultiHeadedAttention, self).__init__()
        assert d_model % h == 0         # 最终的d_model由d_k拼接而成
        self.d_k = d_model // h
        self.h = h
        self.linears = clones(nn.Linear(d_model, d_model), 4)
        self.attn = None
        self.dropout = nn.Dropout(p=dropout)

    def forward(self, query, key, value, mask=None):
        if mask is not None:
            # Same mask applied to all h heads.
            mask = mask.unsqueeze(1)
        nbatches = query.size(0)

        # 1) Do all the linear projections in batch from d_model => h x d_k
        query, key, value = [l(x).view(nbatches, -1, self.h, self.d_k).transpose(1, 2) for l, x in zip(self.linears, (query, key, value))]
        
        # 2) Apply attention on all the projected vectors in batch.
        x, self.attn = attention(query, key, value, mask=mask, dropout=self.dropout)

        # 3) "Concat" using a view and apply a final linear.
        x = x.transpose(1, 2).contiguous().view(nbatches, -1, self.h * self.d_k)
        return self.linears[-1](x)
```

FFN和layernorm就比较容易理解，线性层+relu+dropout+线性层。layernorm将每个样本的特征向量(大小为d_model)归一化。假设两个样本特征向量分别为(1, 1, 1, 1)和(2, 2, 2, 2)，则他们layernorm的结果均为(0, 0, 0, 0)。
```python
class PositionwiseFeedForward(nn.Module):
    def __init__(self, d_model, d_ff, dropout=0.1):
        super(PositionwiseFeedForward, self).__init__()
        self.w_1 = nn.Linear(d_model, d_ff)
        self.w_2 = nn.Linear(d_ff, d_model)
        self.dropout = nn.Dropout(dropout)

    def forward(self, x):
        return self.w_2(self.dropout(F.relu(self.w_1(x))))
        
class LayerNorm(nn.Module):
    def __init__(self, features, eps=1e-6):
        super(LayerNorm, self).__init__()
        self.a_2 = nn.Parameter(torch.ones(features))
        self.b_2 = nn.Parameter(torch.zeros(features))  # 问题：为什么要添加可学习参数a_2和b_2？
        self.eps = eps

    def forward(self, x):
        mean = x.mean(-1, keepdim=True)
        std = x.std(-1, keepdim=True)
        return self.a_2 * (x - mean) / (std + self.eps) + self.b_2
```

最后就是解码器的多头注意力与编码器的交互：
```python
class DecoderLayer(nn.Module):
    def __init__(self, size, self_attn, src_attn, feed_forward, dropout):
        super(DecoderLayer, self).__init__()
        self.size = size
        self.self_attn = self_attn
        self.src_attn = src_attn
        self.feed_forward = feed_forward
        self.sublayer = clones(SublayerConnection(size, dropout), 3)

    def forward(self, x, memory, src_mask, tgt_mask):
        m = memory
        x = self.sublayer[0](x, lambda x: self.self_attn(x, x, x, tgt_mask))
        x = self.sublayer[1](x, lambda x: self.src_attn(x, m, m, src_mask))
        return self.sublayer[2](x, self.feed_forward)
```
这里第二个attention传参分别是x, m, m，即query为解码器的输入，key和value分别来自编码器的输出。