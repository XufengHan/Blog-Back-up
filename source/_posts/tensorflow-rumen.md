---
title: tensorflow入门-识别验证码
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
[参考链接-tensorflow官方文档中文版](http://wiki.jikexueyuan.com/project/tensorflow-zh/) -
  我对tensorflow的学习主要是参考极客学院上tensorflow的官方文档。

 ## tensorflow对象 
使用 TensorFlow, 我们需要明白 TensorFlow:
  • 使用 tensor 表示数据.
  • 在被称之为 会话 (Session) 的上下文 (context) 中执行图.
  • 通过 变量 (Variable) 维护状态
  • 使用图 (graph) 来表示计算任务
  • 使用 feed 和 fetch 可以为任意的操作(arbitrary operation) 赋值或者从其中获取数据。

## tensorflow使用示例

### 启动tensorflow
下面用一个简单的例子介绍了tensorflow的启动流程，定义两个常量(tf.constant)a和b，并将a,b相加。
tensorflow里不能直接输出相加的结果，需要创建一个会话，在会话里运行这个求和运算，才能输出结
果。

## tensorflow训练模型实例
下面是一个tensoeflow训练一元回归模型的样例，这个简单的案例包含了深度学习模型几个基本的要素。
包括：
1.学习函数：我们用来拟合数据的函数，这里使用的是y=weight*x+bias来拟合x_data,y_data
2.优化函数：下面的方法用的是梯度下降方法，梯度是参数的偏导，梯度下降的算法会不断更新参数，不
断沿着梯度的反方向让参数朝着损失更小的方向更新。
3.损失函数：定义模型结果的损失程度，下面是以y_data-y_prediction的方差为损失函数

其实下面的模型还少了一个关键的步骤，那就是构造测试集验证，不过那是评估模型的内容了，不是我们
这个样例的主题。

```python
import tensorflow as tf
import numpy as np

## 输入数据
with tf.name_scope('data'):
     x_data = np.random.rand(100).astype(np.float32)
     y_data = 0.3*x_data+0.1
## 参数设置
with tf.name_scope('parameters'):
     weight = tf.Variable(tf.random_uniform([1],-1.0,1.0)) #取-1到1之间的随机数
     bias = tf.Variable(tf.zeros([1])) #线性回归的偏置项
## 构造学习函数
with tf.name_scope('y_prediction'):
     y_prediction = weight*x_data+bias
## 定义损失函数
with tf.name_scope('loss'):
     loss = tf.reduce_mean(tf.square(y_data-y_prediction))
## 优化函数
optimizer = tf.train.GradientDescentOptimizer(0.5)
#creat train ,minimize the loss 
with tf.name_scope('train'):
     train = optimizer.minimize(loss)
# 初始化变量
with tf.name_scope('init'): 
     init = tf.global_variables_initializer()

##creat a Session 
sess = tf.Session()
## 保存为日志文件
writer = tf.summary.FileWriter('logs', sess.graph)
sess.run(init)
## 定义训练次数
for step in range(101):
  sess.run(train)
  if(step%10)==0:
    print (step ,'weight:',sess.run(weight),'bias:',sess.run(bias))#输出每一步的结果
```
可能有人注意到上面模型的许多部分我都使用了with tf.name_scope()这串代码，这是在调用tensorboard
记录我们的训练过程，在cmd环境里敲入 tensorboard --logdir logs就可以启动tensorboard了。输入提示
的地址http://host:6066（host是自己的主机名）就可以看到下面的图形了。
![pic1](https://raw.githubusercontent.com/XufengHan/Month-Cost/master/pic/line_near_test.png)

## 用tensorflow识别验证码
## 未完待续
不急不急，我还在怼。