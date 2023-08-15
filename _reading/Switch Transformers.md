---
title: 'Reading Format'
date: 2022-06-16
permalink: /reading/switch_transformers
tags:
 - network structure
---

### Reading Time: 23/07/31

| Title | Paper URL | Code | Publish Conf |
|---|---|---|---|
| Switch Transformers: Scaling to Trillion Parameter Models with Simple and Efficient Sparsity | [download](https://arxiv.org/abs/2101.03961) | [code](https://github.com/tensorflow/mesh/blob/master/mesh_tensorflow/transformer/moe.py) | JMLR'22 | 


## Background & Introduction

混合专家模型(MoE)的优势在于训练大模型时，根据输入的数据，**仅选择一部分模型参数**参与计算，来维持较低的计算开销。但MoE仍然存在**复杂性高，通信成本高，训练不稳定**等问题。

本文提出的模型Switch Transformer**简化了MoE通信算法，设计了直观的改进模型**。

## Methods

传统的MoE模型通过一个门控网络，确定每个专家网络的权重，然后将专家网络加权求和后的值作为输出。本文将transformer中的FFN视为专家，通过并行地设置多个FFN，并用一个路由函数选择最合适的FFN。

## Experiments

## Analysis

## Conclusion & Summary

