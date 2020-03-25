---
title: 解决搜狗新闻语料库的小bug
categories: 工程技术
date: 2018-09-17 16:59:36
tags: 技术坑
mathjax: true
---

从搜狗实验平台下载得到的搜狐新闻数据集完整版，按照这篇[博客](https://www.cnblogs.com/jkmiao/p/7007763.html)
在win10系统下，用Linux模拟器Cygwin进行处理，发现报错，信息："iconv: (标准输入):1:0: 无法转换"，但是同样方式处理迷你版发现正常。<!--more-->

用Sublime Text3打开.dat文件，发现mini版可以识别出原始编码是GB18030，但是完整版却识别不了，要不太大打不开，要不打开一堆乱码，即使强制设置编码为GB18030。

解决办法:将完整版.dat文件直接强制改动后缀名为.txt,此后在Cygwin模拟器下输入博客中命令，将命令“cat news_sohusite_xml.full.tar.gz | iconv -f gb18030 -t utf-8 | grep "-content-" > news_sohusite.txt”改为“cat news_sohusite_xml.txt | iconv -f gb18030 -t utf-8 | grep "-content-" > news_sohusite.txt”，则运行成功。

content前后-应改为<>，md语法有混淆。


