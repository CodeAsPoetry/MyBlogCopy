---
title: Multi-Task Deep Neural Networks for Natural Language Understanding
date: 2019-09-16 14:35:20
categories: 自然语言处理
tags: 论文笔记
mathjax: true
---

## 摘要

在论文中，我们提出一个多任务深度神经网络(MT-DNN) 用于从多个自然语言理解(NLU) 任务中学习特征表达。MT-DNN 不仅利用了大量的跨任务的数据，而且从正则化效应中获益，这使得模型可以学到泛化性更好的特征表达，以便有助于适应新任务和新领域。MT-DNN通过引入以“BERT”闻名的预训练双向transformer语言模型，扩展了之前在文献[2015]提出的模型。<!--more-->MT-DNN在10个NLU任务，包括SNLI，SciTail，得到了最新领先的结果，并且，九个GLUE任务中有八个，将GLUE基线推高至 82.7% ( 2.2% 的绝对提升)。我们还演示了使用SNLI和SciTail数据集，MT-DNN所学习的特征表达与通过预训练的BERT学习到的特征表式相比，允许使用更少的域内标签来实现域的自适应。代码和预训练模型可以在[https://github.com/namisan/mt-dnn](https://github.com/namisan/mt-dnn.) 公开获得。



## 论文地址及参考博客

* 论文链接：[https://arxiv.org/pdf/1901.11504.pdf](https://arxiv.org/pdf/1901.11504.pdf)
* 参考博客：[https://blog.csdn.net/leo_95/article/details/89249522](https://blog.csdn.net/leo_95/article/details/89249522)