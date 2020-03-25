---
title: Learing Docker Note
date: 2019-08-14 21:04:40
categories: 工程技术
tags: docker
mathjax: true
---

出于工作需要，将cuBERT打包成docker镜像，以便可以用来加速以BERT为原型的工程模型而无需在不同机器上部署时导致重复编译等问题，在同事的帮助下，快速上手使用Docker。因此，对于Docker，目前来看，笔者的使用能力大于对docker的深层理解能力(后续恶补一下docker的本质，深层理解一下)。

以在docker上部署cuBERT模型为例，讲解整个过程中的docker命令。<!--more-->

首先，我们需要一个可以RUN起来的docker镜像，然后我们将这个镜像启动，在启动的容器中进行一系列猛如虎的操作，如安装cmake、cuBERT的编译、安装等，最后commit出新的镜像，神功告成。

 ```bash
mkdir CleanCuBERT
cd CleanCuBERT
mkdir cuBERT
mkdir Share
vim Dockerfile
 ```

在 Dockerfile 中，写入：

```tex
From docker-reg.xxx.com/library/python:3.5
ENV TZ=Asia/Shanghai
COPY ./cuBERT /usr/src/app/cuBERT
RUN pip3 install -i http://mirrors.aliyun.com/pypi/simple/ \
    --trusted-host mirrors.aliyun.com -r /usr/src/app/cuBERT/requirements.txt
ENV PYTHONPATH /usr/src/app
WORKDIR "/usr/src/app"
RUN python3 -m compileall -b . && find . -name '*.py'-delete
ENTRYPOINT ["/usr/src/app/cuBERT/entrypoint.sh"]
```

接着执行：

```bash
cd cuBERT
mkdir pythonRun
vim requirements.txt
vim entrypoint.sh
chmod +x entrypoint.sh
cd pythonRun
vim runTrue.py
```

注意，一定要执行chmod命令，不然docker一启动容器就会退出，因为无法获取执行 entrypoint.sh 的权限。

在  requirements.txt 中，写入：

```tex
tensorflow==1.14.0
tqdm==4.32.2
Cython==0.29.13
```

在 entrypoint.sh 中，写入：

```bash
#!/bin/bash
cd cuBERT/pythonRun
python3.5 runTrue.pyc
```

在 runTrue.py 中，写入：

```python
# !/bin/bash

import time
if __name__ == "__main__":
    print("Run......")
    while True:
        print("Hello, Docker!")
        time.sleep(1)
```

OK! 有了上述操作，如果系统安装好docker环境，就可以建立镜像了。

将路径切换到 Dockerfile 所在目录，执行

```bash
docker build -t xxx:xx .
```

其中，xxx是镜像名字，xx是镜像的tag。

如果一切顺利，你会看到通知你成功生成镜像。可以通过 docker images 查看。

然后执行

```bash
docker run --name xxxx -d xxx:xx
```

其中，xxxx 是准备生成的容器的名字，xxx 是之前得到的镜像名，xx是镜像名的tag。如果一切顺利，容器就正常启动了，可以执行 docker ps -a 查看容器启动状态，正常情况下是 up 状态，如果是 Exit 说明GG了，如果GG，可以执行 docker log 容器id 来查看日志。

容器启动成功，状态是 up ，可以执行

```bash
docker exec -it 容器id /bin/bash
```

进入容器内部，你会发现这里“麻雀虽小，五脏俱全”，哈哈哈😂。

再介绍几个命令：

* 停止容器

```bash
docker stop 容器id或者容器名
```

* 再次运行容器

```bash
docker start 容器id或者容器名
```

* 删除容器

```bash
docker rm 容器id或者容器名
```

* 删除镜像

```bash
docker rmi 镜像名：tag号 或者 镜像id
```

之后在启动的容器中，就可以按照cuBERT博客介绍的那样去安装部署cuBERT/mklBERT了。考虑到在容器内要使用git clone源代码，安装cmake、甚至不可避免 vim 文件，你会发现如果能在本地和容器间互传文件是有多么美好。docker这个牛逼的东西，它支持......

执行 exit 退出 docker 容器的 bash 环境，执行

```bash
docker cp 待拷贝文件 容器名：容器中待接收路径
```

这样，利用 docker cp 命令把 cmake-3.15.2-linux-x86_64.tar.gz 、cuBERT源码、测试加速的源码和pb模型等拷贝到容器中，然后按照之前 cuBERT 博客介绍的编译、安装就好。

一切顺利，切记执行 docker commit 命令，把经过一顿猛如虎操作的容器再次保存为镜像，否则当你一旦执行了删除当前容器的命令，否则前功尽弃，一夜回到解放前，千年道行一朝散！！！

```bash
docker commit -a "作者名字" -m "镜像说明" 将要保存的容器id 将要生成的新镜像名字：tag号
```

执行之后，就可以长舒一大口气，微微一笑，深藏功与名。如果有强迫症，可以再次执行 docker images 查看，确保万无一失。如果是晚期重度已弃疗的强迫症，可以尝试 docker run 新镜像，看能不能正常启动，然后一套流程走一遍。如果是需要将新镜像上传到镜像仓库的，docker push 新镜像名字：tag号 了解一下。

 