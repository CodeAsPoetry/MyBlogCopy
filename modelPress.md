---
title: 模型压缩调研
date: 2019-07-20 13:14:39
categories: 深度学习
tags: 模型压缩
---
## 1. 模型压缩的背景原理

* 背景：为了追求更高的性能，深度学习模型越来越复杂、参数越来越多。在工程应用上，耗费的计算和存储资源越来越多，成本也越来越昂贵；关键是吧，并不是差不差钱的问题，有的应用场景对模型推断的响应时间有更严格的要求，模型太大，计算量大，无法满足这一硬性要求。还有就是硬件环境不允许太大的模型，比如嵌入型设备等，存在着多种多样的限制。因此，模型压缩派上用场……<!--more-->


* 原理(普适通用方法论，具体方法有具体原理)：LeCun是真的牛逼，89年，在那个训练网络都费劲的年代，一篇《Optimal brain damage》，提出了模型参数剪枝的思想，个人认为这是模型压缩普适的通用方法论。具体关于论文解读，移步后续链接。


## 2. 模型压缩的研究方向

先给出参考链接：

* [模型压缩综述](https://blog.csdn.net/wspba/article/details/75671573)
* [模型压缩论文集锦1](https://github.com/chester256/Model-Compression-Papers)
* [模型压缩论文集锦2](https://github.com/sun254/awesome-model-compression-and-acceleration)

### 2.1 核的稀疏化(规则、非规则)

核的稀疏化，是在训练过程中对权重的更新加以正则项诱导，使其更加稀疏，使大部分的权值都为0，可分为规则和非规则。规则稀疏化后，裁剪起来更加容易，尤其是对im2col的矩阵操作，效率更高；而非规则稀疏化后，参数需要特定的存储方式，或者需要平台上稀疏矩阵操作库的支持。

* Learning Structured Sparsity in Deep Neural Networks：在网络的目标函数上增加了group lasso的限制项，可以实现filter级与channel级以及shape级稀疏化。(规则)

* Dynamic Network Surgery for Efficient DNNs：pruning/splicing，pruning 就是将认为不重要的weight裁掉，但是往往无法直观的判断哪些weight是否重要，因此在这里增加了一个splicing的过程，将哪些重要的被裁掉的weight再恢复回来。通过在W上增加一个T来实现，T为一个2值矩阵，起到的相当于一个mask的功能，当某个位置为1时，将该位置的weight保留，为0时裁剪。在训练过程中通过一个可学习mask将weight中真正不重要的值剔除，从而使得weight变稀疏。由于在删除一些网络的连接，会导致网络其他连接的重要性发生改变，所以通过优化最小损失函数来训练删除后的网络比较合适。(非规则)

* Training Skinny Deep Neural Networks with Iterative Hard Thresholding Methods：通过训练一个稀疏度高的网络来降低模型的运算量，通过在网络的损失函数中增加一个关于W的L0范式可以降低W的稀疏度，但是L0范式就导致这是一个N-P难题。先正常训练网络s1轮，然后Ok(W)表示选出W中数值最大的k个数，而将剩下的值置为0，supp(W,k)表示W中最大的k个值的序号，继续训练s2轮，仅更新非0的W，然后再将之前置为0的W放开进行更新，继续训练s1轮，这样反复直至训练完毕。同样也是对参数进行诱导的方式，边训练边裁剪，先将认为不重要的值裁掉，再通过一个restore的过程将重要却被误裁的参数恢复回来。(非规则)


### 2.2 模型剪裁(规则、非规则)

对训练好的模型进行裁剪，是目前模型压缩中使用最多的方法，通常是寻找一种有效的评判手段，来判断参数的重要性，将不重要的连接或者滤波器进行裁剪来减少模型的冗余，也分为规则和非规则方式。关键在于评判手段，如基于量级的裁剪方式，用weight值的大小来评判其重要性；基于filter中激活为0的值的数量等……

* An Entropy-based Pruning Method for CNN Compression：基于熵值裁剪，利用熵值判定filter的重要性。 将每一层的输出通过一个Global average Pooling将feature map转换为一个长度为c（filter数量）的向量，对于n张图像可以得到一个n*c的矩阵，对于每一个filter，将它分为m个bin，统计每个bin的概率，然后计算它的熵值，利用熵值来判定filter的重要性，再对不重要的filter进行裁剪，在每一层裁剪过后只使用很少的训练步骤来恢复性能，能够有效的避免模型进入到局部最优。

* Designing Energy-Efficient Convolutional Neural Networks using Energy-Aware Pruning：考虑到模型的带宽以及能量的消耗，从能量利用率上最大限度的裁剪模型，提出基于能量效率的裁剪方式。

* Coarse Pruning of Convolutional Neural Networks with Random Masks：采取随机裁剪，对每一种随机方式统计模型性能，确定局部最优的裁剪方式。方法简单粗暴，看起来也比较有效，没有考虑每一层对于裁剪的敏感性，也没有评价参数的重要性，可能需要尝试多个mask才能得到较好的结果。

* Pruning Convolutional Neural Networks for Resource Efficient Transfer Learning Inference：将裁剪问题当做一个组合优化问题：从众多的权重参数中选择一个最优的组合B，使得被裁剪的模型的代价函数的损失最小。OBD的思路，但是OBD需要求二阶导数，实现起来很难，本文提出Taylor expansion方法可以很好的解决这个问题。

### 2.3 知识蒸馏(迁移学习)

* Distilling the Knowledge in a Neural Network：有很好性能的教师网络，而学生网络的训练含有两个目标：一个是hard target，即原始的目标函数，为小模型的类别概率输出与label真值的交叉熵；另一个为soft target，为小模型的类别概率输出与大模型的类别概率输出的交叉熵，将hard和soft的target通过加权平均来作为学生网络的目标函数。

* Paying More Attention to Attention: Improving the Performance of Convolutional Neural Networks via Attention Transfer ：使用复杂网络中能够提供视觉相关位置信息的Attention map来监督小网络的学习，并且结合了低、中、高三个层次的特征。

### 2.4 量化(二值化)

* Quantized Neural Networks: Training Neural Networks with Low Precision Weights and Activations：引入低精度的权重和激活，并且被用于计算参数梯度。前馈过程利用bit-wise运算代替代数运算，QNNs极大减少内存大小和访问次数以及计算资源的消耗。用1个bit权重和两个bit的激活量化 AlexNet，在ImageNet datasets上取得51% top-1 accuracy.

* Binarized Neural Networks: Training Neural Networks with Weights and Activations Constrained to +1 or −1: 引入二值化的权重和激活，并且被用于计算参数梯度。前馈过程利用bit-wise运算代替代数运算，极大减少内存大小和访问次数以及计算资源的消耗。(NIPS16)

* (一个作者写的两篇摘要出奇地一样。。。。。。)

### 2.5 低秩分解

* Coordinating Filters for Faster Deep Neural Networks(ICCV 17): 对滤波器(卷积核)做近似矩阵分解，将权重信息映射到低秩空间，如用多个基向量表示其余向量，做矩阵压缩。

* [低秩近似参考链接](https://www.cnblogs.com/missidiot/p/9869182.html)

### 2.6 精细的模型设计

* MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications

* Aggregated Residual Transformations for Deep Neural Networks

* ShuffleNet: An Extremely Efficient Convolutional Neural Network for Mobile Devices

* 各种花样的拆分、分组，随机合并，“Group”很重要！！！

## 3. 模型压缩现有的开源工程技术

### 3.1 Deep Compression: 

* Compressing Deep Neural Networks with Pruning, Trained Quantization and Huffman Coding(经典之作)

* 通过只学习重要连接，来修剪网络；量化重要的共享权重；应用哈夫曼编码

* 剪枝，连接数量被减少9x到13x，量化每个连接的表示位数从32将到5

* 在ImageNet数据集上， AlexNet被减少了35x，从240MB减少到6.9MB，准确率上没有任何损失

* Github 上已实现各种相应版本的代码：[Tensorflow版](https://github.com/hiteshvaidya/Model-Compression)、[pyTorch版](https://github.com/jack-willturner/DeepCompression-PyTorch)、[caffe版](https://github.com/may0324/DeepCompression-caffe)

### 3.2 Learn2Compress

* [Learn2Compress](https://ai.googleblog.com/2018/05/custom-on-device-ml-models.html)使用多种神经网络优化和压缩技术，包括：

* 通过移除对预测作用最小的权重和计算，对模型进行剪枝，这将对稀疏的输入输出模型十分有效，可以将模型压缩2x，经预训练后能达到原始模型97%的精度；

* 训练阶段运用量化技术特别有效，通过减少模型权重和激活的位数，提高推断速度。如，用8位的固定的表示取代浮点数，可以加速模型推断，减少乘法计算，进一步压缩模型大小4x；

* 知识蒸馏，遵循教师-学生网络策略。

### 3.3 PaddleSlim

* [来自机器之心的介绍文章](https://www.jiqizhixin.com/articles/2019-05-07-12)

* PaddleSlim 是百度飞桨(PaddlePaddle)联合视觉技术部发布的模型压缩工具库，除了支持传统的网络剪枝、参数量化和知识蒸馏等方法外，还支持最新的神经网络结构搜索(NAS)和自动模型压缩技术。

* [github工程地址](https://github.com/PaddlePaddle/models/tree/v1.4/PaddleSlim)





 
