---
title: Python 异步任务队列 Celery 使用
date: 2020-03-15 15:53:50
categories: Python工程
tags: celery使用
mathjax: true
---

Celery 是一个简单灵活且可靠的，处理大量消息的分布式系统，专注于实时处理的异步任务队列，同时也支持任务调度的。celery 基本结构如下：<!--more-->

<img src="/images/celery.jpg"/>

task 是 celery 中执行单元，其中，task producer 作为生产者，产生 task ，将 task 送入到 broker 任务队列中，此任务队列也可以是 rabbitmq ，随后由调度器，即 celery beat 将任务分发给多个 worker，即消费者，得到结果之后保存在数据库，也可以是 redis 中。

celery 使用场景：

* 异步任务：将耗时操作任务提交给 Celery 去异步执行，比如发送短信/邮件、消息推送、音视频处理等等。比如用户通过网页上传视频并在线转码，采用 Celery ，前端页面就不需要一直等待，直至转为，才能知道结果，如果不采用 Celery，就会阻塞在前端页面上
* 定时任务：类似于 crontab ，比如每日数据统计

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=28815250&auto=0&height=66"></iframe>







