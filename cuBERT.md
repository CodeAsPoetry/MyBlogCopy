---
title: cuBERT
date: 2019-08-14 21:03:51
categories: 自然语言处理
tags: BERT推理加速
mathjax: true
---

## cuBERT/mklBERT介绍

[参考github工程地址](https://github.com/zhihu/cuBERT) ，其中README.md有详细的指导过程，按照提示正常复现就妥。

此工程本质上利用tensorflow-C的核心库，针对显卡，则通过CUDA定制优化；针对CPU，则通过英特尔MKL定制优化。专门为BERT模型加速推理，而无需改动tensorflow框架以及代码层的结构。

因为绝大多数公司出于成本的考虑，在部署BERT模型时，都会选择CPU部署方式，因此CPU上BERT的推理加速就显得格外重要。<!--more-->训练往往在显卡上，因为随着模型越来越复杂、参数越来越多，训练阶段极大耗时，且不仅有前向推理，还有梯度反向计算，简单比较，不难发现其实部署到CPU，在测试阶段加速前向推理更有价值。因此，本文主要介绍mklBERT。

关于MKL，参考的资料有：

* [博客](https://www.cnblogs.com/bonelee/p/8327905.html) 
* [Tensorflow官方API性能提升部分涉及到MKL加速](https://tensorflow.google.cn/guide/performance/overview)
* [Intel官网MKL资料](https://software.intel.com/zh-cn/articles/introducing-dnn-primitives-in-intelr-mkl)

*_MKL加速原理_*：在英特尔® 数学核心函数库(MKL)中引入深度神经网络(DNN)基元，应用于深度学习框架，确保通用构建模块的高效实施。这些 DNN 拓扑依靠大量的标准构建模块或基元，对以多维张量集表示的数据进行计算。这些基元包括卷积、归一化、激活和内积函数，以及处理张量所需的函数。在英特尔架构上高效执行计算需要通过向量化利用 SIMD 指令并通过线程利用多个计算内核。向量化非常重要，因为现代处理器在最多 512 位长(16 个单精度数字)的数据向量上进行运算，并且每个周期最多可执行两个乘法和加法(融合乘加或 FMA)运算。利用向量化需要将数据连续定位在内存中。由于张量的尺寸通常较小，所以更改数据布局会带来巨大的开销；设法在拓扑中执行所有运算，同时不更改不同基元的数据布局。

英特尔 MKL 为使用最广泛的运算(针对适用于矢量化的数据布局实施)提供了基元，具体有，直接批量卷积；内积；池化：最大值、最小值、平均值；标准化：跨渠道的本地响应标准化 (LRN)、批次规标准化；激活：修正线性单元 (ReLU)；数据操作：多维转置(转换)、拆分、合并、求和和缩放。

使用 TensorFlow 中现成的池分配器开发了一款自定义池分配器，自定义池分配器确保了 TensorFlow 和英特尔 MKL 共享相同的内存池(使用英特尔 MKL imalloc 功能)，不必过早地将内存返回至操作系统，因此避免了昂贵的页面缺失和页面清除。此外，优化了多个线程库(TensorFlow 使用的 pthread 和英特尔 MKL 使用的 OpenMP)，使它们能共存，而不是互相争夺 CPU 资源。(解释了下面测试加速效果实验中，使用mkl加速的mklBERT占用更大的内存，能够更多的占有CPU计算资源。)批处理大小会影响可用并行性、工作集大小和总体内存性能的另一个重要参数。(这解释了实验结果中由批处理大小的设置导致模型在资源占用上的变动。)

## 工程复现

系统：Ubuntu16.04

* 从github上克隆工程，git clone https://github.com/zhihu/cuBERT
* 因为我的Ubuntu16.04系统自带的cmake版本较低，我需要重装，Ubuntu16.04安装最新版cmake，[参考教程](https://www.jianshu.com/p/f07ed9ce75e1) 
* 进入工程目录，执行：mkdir build && cd build
* 因为在CPU部署，执行，cmake -DCMAKE_BUILD_TYPE=Release -DcuBERT_ENABLE_MKL_SUPPORT=ON ..
* 之后，再执行，make -j4
* 执行，sudo make install
* 我们需要下载BERT模型和对应的词表，并在脚本中配置好对应路径。github给出的模型下载是Dropbox，鉴于大中华局域网，大家八仙过海，各显神通吧。
*  cd python 、执行 python setup.py bdist_wheel ，这里有个小坑，当时执行时，python使用的是3.5，执行结果是生成一个可以安装的python包，然而当时工程配置的python是3.6，然后就傻逼了。原因在命令行执行这些步骤没有切换到conda环境，执行完是在Anaconda系统的工程使用，所以一定要对应起来。
* 安装上步得到的python包，pip install dist/cuBERT-xxx.whl
* 执行工程中自带的python测试脚本

出于工程需求，需要在有限的资源上对比传统的session加载ckpt模型和cuBERT/mklBERT加载pb模型分别在前向推断速度和资源占用效率。

* 对于传统的tensorflow框架通过session加载ckpt并且run得到prediction这种方式，限制CPU核数资源：

```pyt
cpu_num = int(os.environ.get('CPU_NUM', 1))
config = tf.ConfigProto(device_count = {"CPU": cpu_num },
                                        Inter_op_parallelism_threads = cpu_num,
                                        Intra_op_parallelism_threads = cpu_num,
                                        log_device_placement = True)
with tf.Session(config = config) as s:
    pass
```

* 对于mklBERT加载pb模型，限制CPU核数资源：

```python
os.environ["MKL_NUM_THREADS"] = "1"
os.environ["OMP_NUM_THREADS"] = "1"
```

## 加速效果展示



