---
title: Attention Is All You Need
date: 2019-07-21 19:03:45
categories: 自然语言处理
tags: 论文笔记
mathjax: true
---

## 摘要

主流的序列转换模型大多都是基于复杂的、包含编解码器的循环神经网络或者卷积神经网络，通过注意机制将编解码器连接起来。本文提出一种新颖简单的网络架构，Transformer，完全抛弃了循环和卷积神经网络，仅仅基于注意机制来实现。<!--more-->(略)

## 1. 介绍

循环神经网络、长短时记忆网络、门控循环神经网络等在序列建模和转换的各种任务中，比如语言模型、机器翻译等确立了强有力地位。大量的努力推高循环语言模型和编解码结构的性能边界。但是序列固有的顺序性，在训练样本时，阻止了计算并行性的实现。当序列长度更长时，这个问题就更明显，因为有限的内存限制了跨样本批次。虽然有一些小方法避免，不过序列计算的限制性依旧存在。

注意机制很成熟，它无视输入或输出序列中字词间的距离，可以对字词间的依赖建模。除了极少数情况，注意机制一般结合循环神经网络使用。

本文提出transformer，彻底抛弃循环神经网络，完全基于注意机制来对输入输出之间的全局依赖进行建模。它可以允许更多的并行，要求更少的训练，并且达到目前最先进模型的性能。

## 2. 背景

Extended Neural GPU，ByteNet，ConvS2S也都是以减少序列计算量为目标，它们都使用卷积神经网络作为基本单元块，对输入输出所有的位置并行计算隐状态。在这些模型中，任意两个输入或输出位置的相关性信号所要求的操作数量，随着位置间距离的增加而增加，对于ConvS2S，线性增长；对于ByteNet，对数性增长。这使得模型很难学到远距离间的依赖。在Transformer中，这一操作数被减少到一个常数数量，尽管付出了由位置上的平均注意加权引起的有效分辨率降低的成本，但是，本文又通过多头注意机制抵消了这一影响，后续会介绍。

自注意，也被叫做内部注意机制，是一种在单句内通过相关的不同位置来计算该序列数学表征的注意机制。自注意被广泛成功用于阅读理解、摘要总结、文本蕴含和学习任务独立的句子表征等任务中。

端到端的记忆网络是基于循环的注意机制，而不是循环序列对齐。在单语QA和语言模型任务中展示了很好的性能。

竭尽所能的调研，我们发现Transformer是第一个取代序列对齐的RNN或者卷积，转向完全依赖自注意来计算输入、输出的数学表征的转换模型。


## 3. 模型架构

绝大多数具有竞争力的神经序列转换模型都有编解码结构。编码器将符号表征的输入序列 $(x_1,...,x_n)$ 映射到连续的特征向量表示 $z=(z_1,...,z_n)$ 。给定 $z$ ，解码器每个时刻产生一个输出序列 $(y_1,...,y_m)$ 的元素。每一步模型都是自回归的，以之前产生的符号作为额外输入，产生下一个输出。

Transformer，整体结构由编码器和解码器中堆叠自注意层、点积和全连接层构成，分别在图一的左右两半部分展示： 

<img src="/images/Transformer.jpg" width = "60%" height = "600"/>

### 3.1 编解码堆叠

* 编码器：编码器由6个同样的层堆叠组成。每一层都有两个子层，第一个是多头自注意机制，第二个是一个简单的点乘的全连接前馈网络。两个子层的每一个应用残差连接，随后再接一个层正则化。也就是说，每个子层的输出是：LayerNorm(x + Sublayer(x))，其中 Sublayer(x)  是子层自身的实现函数。为了促进这些残差连接，模型的所有子层，还有嵌入层，产生维度 $d_{model}=512$ 的输出。

   

* 解码器：解码器也由

### 3.2



### 3.3



## 4. 自注意缘由

## 5. 训练

## 6. 结果

## 7. 结论

## 参考

## 附录

## 工程理解

