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

假设原先的$Q, K, V$的维度为$d_{\text{model}}$，则每个注意力头将他们分别映射到$d_k, d_k, d_v$的子空间。最终attention的输出维度也是$d_v$。n个头的attention输出结果拼在一起，得到的维度大小为$n \times d_v$。

## Experiments

## Analysis

## Conclusion & Summary

