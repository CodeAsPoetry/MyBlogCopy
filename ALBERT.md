---
title: ALBERT A Lite BERT for Self-supervised Learning of Language Representations
date: 2019-10-17 16:06:26
categories: 自然语言处理
tags: 论文笔记
mathjax: true
---

## 摘要

在预训练自然语言特征表示阶段，增加模型的尺寸大小通常会导致在下游任务的性能提升。然而，在某些时候，由于GPU/TPU内存的限制，更长的训练时间和不可预见的性能退化，都使模型尺寸的增大变得相当困难。为了解决这个问题，我们提出两个减少参数的技术，来降低内存消费和增加BERT的训练速度。综合经验实验证据表明我们所提的方法可以产生相对原始BERT来说，尺寸压缩更好的模型。<!--more-->我们也使用一个半监督的损失函数，来聚焦对句子间连贯性进行建模，并且发现它可以持续有效帮助多个句子输入的下游任务。因此，我们的最佳模型在GLUE，RACE和SQuAD 基准测试中建立了最新的技术成果，而与BERT-large相比，参数却更少。