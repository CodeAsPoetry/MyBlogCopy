---
title: Go语言进阶
date: 2020-03-30 21:49:48
categories: Go语言学习
tags: Go进阶
mathjax: true
---

28法则、能动手不逼逼、单点突破

<!--more-->

1. 内建方法(不需引用包就可以使用)——make：
   * 创建slice（可扩建的数组）、map、chan（管道，用于两个线程之间交互的）
   * 返回类型引用
   * 为何此三种类型需要make来创建，是因为需要go去初始化它们
2. 内建方法——new
   * 内存置零
   * 返回传入类型的指针地址
3. 内建方法——append、delete、copy
   * slice  -> append&copy
   * map -> delete，传入参是个 key ，可以把 key-value 全部删掉
4. 内建方法——panic、recover 处理异常
   * panic 抛出异常，与工程代码中可预见的error不同，这些是一些致命的异常
   * recover 捕获异常
5. 内建方法——len、cap、close
   * len 实际长度，cap 容量，close 关闭chan(协程与协程的数据通道)
   * len -> string、array、slice、map、chan
   * cap -> slice、array、chan
   * Close -> chan
6. 结构体——创建、初始化
   * 定义 struct (声明)
   * 初始化 (匿名)
   * 指针 (new)
7. 结构体——属性、函数
   * 属性和函数定义 函数在初始化之后才可以调用
   * 两种作用域 首字母大小写，小写只能内部使用
8. 结构体——组合
   * 面向对象特性：继承
   * 组合实现：结构体和结构体组合
9. 接口——面向对象实现，抽象出接口是为了多态
   * 声明接口
10. 接口——多态
   * 面向对象特性：多态
   * 接口定义变量(类比：定义父指针引用)
   * Go语言中接口是隐式实现方式
11. 并发
    * 并发实现：协程，比线程更小、更轻量、更易用，初始内存4～5KB
    * 多协程间的通信 chan 实现
    * 多协程间的同步
12. 协程
    * 启动协程：go
    * 多核CPU设置：runtime
13. 协程通信
    * channel
    * select：随机从不同的 chan 里面读取数据
14. 

