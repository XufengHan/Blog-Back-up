---
title: tensorflow入门
subtitle: goole深度学习框架
date: 2017-12-11 16:01:23
tags: python tensorflow 深度学习 神经网络
toc: true
---
## goole深度学习框架

## 前言
我在这之前并没有接触过tensorflow，不过我深感时间太少，还是督促自己早点把TensorFlow的学习提上日程，努力进步。

## 准备工具
### python环境下安装
如果下载了anaconda,tensorflow的安装就很方便了，在CMD环境下输入下面的代码就能搞定了
```python
pip install tensorflow
```
使用这种方法下载的是CPU版本，tensorflow还有一个GPU版本，安装起来会更复杂，GPU版本tensorflow支持并行运算。
### R环境下的安装
[TensorFlow for R](https://tensorflow.rstudio.com/)
[github介绍](https://tensorflow.rstudio.com/)
从下图可以看到，R是通过python的环境运行的tensorflow，还是得先安装好python和anaconda。调用Python的接口的确是个好方法，很有开源社区资源共享的特色。
![pic1](https://raw.githubusercontent.com/XufengHan/hanxufeng.github.io/master/img/tensor-rumen/tensorflow%20for%20R.png)
下面的tensorflow我还是会用python运行，主要是因为我对R的环境和操作已经比较熟悉了，而python需要更多练习，不过我也会补充一下R的案例。

### 参考链接
[参考链接](https://github.com/BinRoot/TensorFlow-Book) -
  我还是会在网上找一些参考文档，毕竟工作的时候捧着一本书看太张扬。
[官方文档中文版](http://wiki.jikexueyuan.com/project/tensorflow-zh/) -
  开源框架这种东西，还是得对着官方文档一步一步来。
[tensorflow游乐场](http://playground.tensorflow.org/)-
  名字怪怪的，事实上它是tensorflow的一款可视化开发工具。
  
## tensorflow对象 
使用 TensorFlow, 我们需要明白 TensorFlow:
  • 使用 tensor 表示数据.
  • 在被称之为 会话 (Session) 的上下文 (context) 中执行图.
  • 通过 变量 (Variable) 维护状态
  • 使用图 (graph) 来表示计算任务
  • 使用 feed 和 fetch 可以为任意的操作(arbitrary operation) 赋值或者从其中获取数据.
### 创建一个tensor
```python
a=tf.constant([1.0,2.0],name="a")
b=tf.constant([2.0],name="b")
result=a+b
```

### 创建一个会话
```python

```
## 未完待续
不急不急，我还在怼案例。