---
title: Improving Multi-Task Deep Neural Networks via Knowledge Distillation for Natural Language Understanding
date: 2019-09-16 14:35:51
categories: 自然语言处理
tags: 论文笔记
mathjax: true
---

## 摘要

该论文探索了使用知识蒸馏来提升从多个NLU任务中学习文本特征表示的多任务深度神经网络(MT-DNN)模型[文献2019]。尽管集成学习可以提升模型性能，但是部署一套像多个MT-DNN这样巨大的模型集合是相当地昂贵。这里，我们在多任务学习设置中应用知识蒸馏方法[文献 Hinton等人，2015]。对于每个任务，我们训练一个表现优于任何单一模型、由多个不同的MT-DNN(作为教师)构成的集合，然后通过多任务学习从这些集合中的‘教师们’蒸馏知识，训练一个单个的MT-DNN(作为学生)。<!--more-->我们表明，蒸馏得到的MT-DNN在9个GLUE任务中有7个显著优于原始MT-DNN，比起原始单个MT-DNN，将GLUE基线提升到 83.7% ( 1.5% 的绝对提升)。代码和预训练模型可以在[https://github.com/namisan/mt-dnn](https://github.com/namisan/mt-dnn.) 公开获得。



## 论文地址及参考博客

- 论文链接：[https://arxiv.org/pdf/1904.09482.pdf](https://arxiv.org/pdf/1904.09482.pdf)
- 参考博客：[https://blog.csdn.net/ljp1919/article/details/90476134](https://blog.csdn.net/ljp1919/article/details/90476134)

