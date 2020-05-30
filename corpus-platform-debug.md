---
title: 语料平台前后端联调
date: 2020-05-11 10:32:05
categories: 工程技术
tags: Iris, mongodb
mathjax: true 
---

* GoLand 开发工具 git push 到代码仓库
* ssh到14，进入到 corpus_platform ，git pull 拉下源码
* 进入到 docker 目录，运行 build.sh
* docker push 到 docker 镜像仓库
* 编辑 docker-compose.yml 文件
* docker-compose -f docker-compose.yml up -d 启动镜像
* Docker ps | grep corpus-platform 查看启动的容器

<!--more-->