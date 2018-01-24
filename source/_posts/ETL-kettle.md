---
title: 简单的kettle的数据抽取过程
date: 2017-11-24 13:54:21
toc: true
tags: kettle ETL
comment: true
---


## 准备工作
kettle是一款开源免费的TL工具，kettle的功能就像它的中文名字‘水壶’一样，他可以将不同来源的数据抽取过来处
理，然后以目标格式倒出。
我们这次测试的就是一个简单的抽取功能。kettle需要配置好jdk才能打开，亲测jdk9我们这次测试的就是一个简单的抽取功能。kettle需要配置好jdk才能打开，亲测jdk9.0.1会有问题，我这里用的是1.8.0。
配置成功以后打开kettle文件夹下的spoon.bat文件就会弹出下图的kettle界面。
![pic](https://raw.githubusercontent.com/XufengHan/hanxufeng.github.io/master/img/kettle.png)

### 准备工具
-[kettle下载](http://community.pentaho.com)
-[java下载](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
-[java的环境配置](https://jingyan.baidu.com/article/6dad5075d1dc40a123e36ea3.html)

## 操作步骤

### 设计事件
接下来就可以进行我们的数据抽取过程了，新建一个转换，在输入输出里分别选择表输入和插入更新，再按住shift键连接，完成的结果如下图。
![pic1](https://raw.githubusercontent.com/XufengHan/hanxufeng.github.io/master/img/cqsj.png)

### 配置数据源
双击这两个图标，完成数据源配置。主机名称是你的服务器或主机地址，数据库名称是你的SID，端口号要看连接的数据库种类，比如ORACLE就是1521，用户名和密码就是你的数据库的用户名和密码了。
![pic2](https://raw.githubusercontent.com/XufengHan/hanxufeng.github.io/master/img/fwqpz.png)
数据源配置成功后，分别选择对应的表，点击运行就可以开始抽取过程了。

## 总结
我的理解里kettle是一款流程设计软件，不管是要完成表的抽取，数据库的抽取，或者各种数据源的抽取，都是先设计
好事件节点，再完成配置，最终得到我们想要的结果这个过程。总的来说，在学习这种交互式的工具过程中，我认为明
确需求比了解功能更重要。


