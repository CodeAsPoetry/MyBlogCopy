---
title: tf-serving 模型部署
date: 2020-05-10 14:59:41
categories: 工程技术
tags: tf-serving
mathjax: true 
---

想做一个自动部署机器学习模型的B-S插件平台，提高算法探索调研的效率。需要很多的知识点，先搞一波 tf.Serving。参考资源：[TensorFlow Serving入门](https://www.jianshu.com/p/afe80b2ed7f0)。Mac下安装 Anaconda、Tensorflow、Docker环境，参考相应教程。<!--more-->

拉取 Tensorflow 的 Serving 的 Docker 镜像

```bash
docker pull tensorflow/serving
```

在部署自己模型的时候需要参考 Demo，因此 git clone Tensorflow 的 Serving 的源码

```bash
git clone https://github.com/tensorflow/serving
```

运行 TF Serving

```bash
docker run -p 8501:8501 \
--mount type=bind,\
source=/tmp/tfserving/serving/tensorflow_serving/servables/tensorflow/testdata/saved_model_half_plus_two_cpu,\
target=/models/half_plus_two \
-e MODEL_NAME=half_plus_two -t tensorflow/serving &
```

客户端验证服务

```bash
curl -d '{"instances": [1.0, 2.0, 5.0]}' \
-X POST http://localhost:8501/v1/models/half_plus_two:predict 
```

运行截图：

<img src="/images/tf_serving_docker.jpg" width = "100%" height = "600"/>

<img src="/images/tf_serving_curl.jpg" width = "100%" height = "600"/>



