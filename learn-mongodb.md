---
title: Mac环境下MongoDB踩坑记
date: 2020-03-23 12:27:35
categories: MongoDB
tags: MongoDB
mathjax: true
---

* 坑：无法在系统根目录创建 /data/db

  原因：结果系统升级了catalina，不能这么干

  解决办法：[建立指定 --dbpath 参数的配置文件，以带 --config 参数的方式启动mongoDB服务](https://www.jianshu.com/p/c910b97105cf)<!--more-->

* 坑：Robo 3T，连接本地mongoDB，127.0.0.1:27017，出现  Error: Failed to execute "listdatabases" command

  原因：没有指定数据库的登录权限，因为mongodb安装以后只在cmd中进行本地访问，所以要使用图形界面就需要使用登录账号及密码

  解决办法：[配置账号和密码](https://www.cnblogs.com/mengyu/p/9071371.html) ，执行到 二.3 就可以了

* 坑：上述 [配置账号和密码](https://www.cnblogs.com/mengyu/p/9071371.html) 中 需要重新 带 --auth 参数启动，需要先关闭之前的mongod服务 

  解决办法：[四个路子对号入座](https://blog.csdn.net/github_38885296/article/details/80619191)

  我这里情况：

  第一条路子废了，原因是以配置文件在后台启动的，没办法 crtl + C，情理之中

  第二条路子废了，出现问题，莫名其妙

  ```
  (base) ➜  ~ mongo
  MongoDB shell version v4.2.3
  connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
  Implicit session: session { "id" : UUID("f2a46baa-dbd7-4b59-96cf-fe4c45c3d802") }
  MongoDB server version: 4.2.3
  > use admin
  switched to db admin
  > db.shutdownServer();
  2020-03-23T13:55:32.317+0800 E  QUERY    [js] Error: shutdownServer failed: {
  	"ok" : 0,
  	"errmsg" : "command shutdown requires authentication",
  	"code" : 13,
  	"codeName" : "Unauthorized"
  } :
  _getErrorWithCode@src/mongo/shell/utils.js:25:13
  DB.prototype.shutdownServer@src/mongo/shell/db.js:426:19
  @(shell):1:1
  >
  ```

  第三条路子废了，居然告诉我无法识别  --shutdown 参数，来了一波 --help ，真没有，惊呆了

  第四条路子终极必杀技，直接杀掉当时以配置文件启动mongod的进程，记录一下猛如虎操作

  ```bash
  ps -ef | grep mongo
  ---------------------------------
  0  2244     1   0 12:26下午 ??         0:16.55 mongod --config /Users/codeaspoetry/mongodb_workdir/etc/mongod.conf
    501  2511  2427   0  1:57下午 ttys002    0:00.00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox mongo
  ---------------------------------
  sudo kill 2244
  ```

  然后终于可以重新带着 --auth 和 --config启动 mongod 了

1. Robo 3T

2. 命令行愉快地玩耍 

   ```bash
   source .bash_profile
   # 启动服务
   sudo mongod --config /Users/codeaspoetry/mongodb_workdir/etc/mongod.conf
   # 启动客户端
   mongo
   ```

3.  将 json 文件导入 mongoDB 中指定的数据库，且作为一个表

   ```bash
   mongoimport --host=localhost --port=27017 --username=admin --password=password --authenticationDatabase admin --db Dialogue --collection text --file text.json
   ```

   

   