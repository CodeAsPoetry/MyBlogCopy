---
title: 前端技术的概念性了解
date: 2020-03-23 18:14:39
categories: 前端技术
tags: 前端概念
mathjax: true
---

忆当年，大二上学期那个冬天，轮番泡图书馆以及宅在寝室闭关捣鼓网站前端技术。当时作为攻读着超强宇宙专业矿物加工的我没有任何关于技术栈的概念，就是在用JavaSE编程时，发现GUI制作的界面挺丑的，也正巧赶上当时HTML5 大行其道，于是好奇心驱动着捣鼓HTML5，其后发现光HTML5也不成，得上手CSS3，再之后发现网站得有简单的动态效果吧，又搞了搞javascript，顺手了解了一波浏览器DOM树结构，再再之后搞了搞JQuery。然后顿感前端苦海无涯，然后出于搞个产品的路子，把 mysql 和 thinkphp玩了一把，后来又搞了搞ajax。突然发现技术路上我是个精通各种语言的hello world的货色，难受啊。 <!--more-->再再之后又回归JavaSE、JavaEE、Oracle。现在，用 Python 搞NLP去了，顺带搞了把Django和Flask，现在出于公司项目，又开搞go和mongoDB，以及知识图谱Neo4j。神奇的是公司项目采用Restful接口，前后端分离。后端采用 go iris + mongodb，前端采用 react 。于是感概前端技术又飞跃了，正好赶上 github收购npm，于是就从概念上调研了一波目前的前端技术。

相对于之前熟悉的MVC架构，可能在Restful接口实现的前后端分离系统中略显陈旧。首先自己默默大致科普了nodejs与js的联系与区别，顺带了解npm，然后对于经常听到的如React和vue等做个大致了解。

[MVC架构 VS 前后端完全分离架构](https://www.cnblogs.com/nykuo/p/10967560.html)

* 可以实现真正的前后端解耦，前端服务器使用nginx。前端/WEB服务器放的是css，js，图片等等一系列静态资源（甚至你还可以css，js，图片等资源放到特定的文件服务器，例如阿里云的oss，并使用cdn加速），前端服务器负责控制页面引用&跳转&路由，前端页面异步调用后端的接口，后端/应用服务器使用tomcat（把tomcat想象成一个数据提供者），加快整体响应速度。（这里需要使用一些前端工程化的框架比如nodejs，react，router，react，redux，webpack）。
* 前后端分离并非仅仅只是一种开发模式，而是一种架构模式（前后端分离架构）。千万不要以为只有在撸代码的时候把前端和后端分开就是前后端分离了，需要区分前后端项目。前端项目与后端项目是两个项目，放在两个不同的服务器，需要独立部署，两个不同的工程，两个不同的代码库，不同的开发人员。前后端工程师需要约定交互接口，实现并行开发，开发结束后需要进行独立部署，前端通过ajax来调用http请求调用后端的restful api。前端只需要关注页面的样式与动态数据的解析&渲染，而后端专注于具体业务逻辑。

[Nodejs VS 前端Javascript](https://segmentfault.com/a/1190000006154835) 

* JavaScript = ECMAScript(语言基础，如：语法、数据类型结构以及一些内置对象) + DOM(一些操作页面元素的方法) + BOM(一些操作浏览器的方法)
* Nodejs = ECMAScript(语言基础，如：语法、数据类型结构以及一些内置对象) + OS (操作系统) + file(文件系统) + net (网络系统) + database(数据库)
* 很容易看出，前端和后端的js相同点就是，他们的语言基础都是ECMAScript，只是他们所扩展的东西不同，前端需要操作页面元素，于是扩展了DOM，也需要操作浏览器，于是就扩展了BOM。而服务端的js则也是基于ECMAScript扩展出了服务端所需要的一些API，稍微了解后台的童鞋肯定知道，后台语音有操作系统的能力，于是扩展os，需要有操作文件的能力，于是扩展出file文件系统、需要操作网络，于是扩展出net网络系统，需要操作数据，于是要扩展出database的能力。

[Nodejs 本质](https://blog.csdn.net/myKurt/article/details/79914078)

* 类比Java中的JVM，和jvm同等地位的js运行环境，打开了前端人员走向后端的道路，然后js大佬开始封装MVC，各种模块
* nodejs 是一个平台 ，一个基于Chrome javascript 运行时建立的平台，它是对chrome V8引擎进行了封装的运行环境，简单说nodejs就是把浏览器的解释器封装起来作为服务器运行平台，用类似javascript的结构语法进行编程，在nodejs上运行。nodejs主要从事后台公共，可以系统操作，文件操作，网格系统，数据库操作，也可以建立http服务。nodejs 有个包管理工具 npm ,npm 是nodejs 编写的，npm下载的所有文件都会在一个交过node_modules 的文件夹内 [具体更多](https://www.jianshu.com/p/293d99158846)。


[React Vs Vue](https://www.jianshu.com/p/b7cd52868e95?from=groupmessage)

* 数据是不是可变的
* 通过js来操作一切，还是用各自的处理方式
* 类式的组件写法，还是声明式的写法 (在vue3.0支持类式写法之后就可以去掉)
* 什么功能内置，什么交给社区去做



