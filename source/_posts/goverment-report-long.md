---
title: 数据角度看政府精神世界变迁—详细版
date: 2018-01-02 10:10:42
tags: R语言 文本挖掘 政府工作报告
toc: TRUE
---

突然来了一个紧急任务，我的毕业论文要开始写了，两个图像识别的模型先放一放，虽然我其实也做的差不多了。

## 准备工作
### 获取数据
政府工作报告[百度网盘地址](https://pan.baidu.com/s/1hs0CpgC) 密码:i667

### 数据说明
自新中国成立以来，国务院并非每年都发布政府工作汇报。从1950年开始，应该每年都有一次政府工作汇报，但是
1953年之前，政府处于初创，百废待兴，更重要的是人民代表大会还没有成立，政府结构和程序还处于摸索阶段，
所以这段时间政府并没有成型的工作汇报文本。另外1961-1963年国家经历的所谓的三年自然灾害，政府工作会议
因此无法正常举行，1965-1977年，国务院仅在1975年由国务院总理周恩来发布过一次工作汇报，这一阶段一共发
生了十年动乱的文革，76年和77年毛周朱相继去世，政府工作处于非正常时期，因此也没进行政府工作汇报。

### 数据处理

下面长长的一段代码其实就做了一件事，加载项目需要的包。为了贴心的让包一定能加载成功，这个包不存在的话
我就顺便把它下载了。让我头疼的是require函数不能传参，我始终没有找到批量加载包的方法。

```r
###载入数据包
if (!suppressWarnings(require("jiebaR"))) {
  install.packages("jiebaR")
  require("jiebaR")
}
if (!suppressWarnings(require("tm"))) {
  install.packages("tm")
  require("tm")
}

if (!suppressWarnings(require("slam"))) {
  install.packages("slam")
  require("slam")
}

if (!suppressWarnings(require("topicmodels"))){
  install.packages("topicmodels")
  require("topicmodels")
}

if (!suppressWarnings(require("wordcloud2"))) {
  install.packages("wordcloud2")
  require("wordcloud2")
}

if (!suppressWarnings(require("igraph"))) {
  install.packages("igraph")
  require("igraph")
}

if (!suppressWarnings(require("dplyr"))) {
  install.packages("dplyr")
  require("dplyr")
}

if (!suppressWarnings(require("reshape2"))) {
  install.packages("reshape2")
  require("reshape2")
}

if (!suppressWarnings(require("ggplot2"))) {
  install.packages("ggplot2")
  require("ggplot2")
}

if (!suppressWarnings(require("ggdendro"))) {
  install.packages("ggdendro")
  require("ggdendro")
}

if (!suppressWarnings(require("grid"))) {
  install.packages("grid")
  require("grid")
}
if (!suppressWarnings(require("ape"))) {
  install.packages("ape")
  require("ape")
}
```

这里入手了我们第一个工作，批量读取文件，原理就是获取目录下所有的文件名，然后自动执行读取函数。
```r
# 批量读取数据
get.msg <- function(path) {
  con <- file(path, open = "rt")#打开文件其他平台下需要注明编码方式
  msg <- readLines(con)#分行读取txt
  n <- grep("各位代表", msg)#每次正文都是以各位代表开头
  msg <- msg[seq(n[1],length(msg),1)]#提取相关内容
  close(con)#关闭路径
  return(paste(msg, collapse=""))#我们将第一次出现各位代表的行和最后一行连接在一起
}

# 上面我们构造了一个读取数据函数
path = "your path" #设定文件路径
docs <- dir(path) #找出路径中的所有文件
docs <- docs[which(docs != "cmds")] #去除文件中的系统文件
msg <- sapply(docs, function(p) get.msg(paste(path,p,sep="/")))
```


## 主题模型确定精神变迁话题线


  

### 选择统计量
主题模型主要以以下三个统计量为基础来进行统计和建模过程，词频（TF）
#### TF
TF 是英语单词 Term Frequency的缩写，用中文可以表达为词频，和字面上的意思一样，这个统计量的作用就统计每个词在文档中出现的频率。
数学公式为：
#### IDF
IDF既 Inverse Document Frequency，中文翻译为逆文档频率，概括来讲， IDF反应了一个词在所有文本中出现的频率，如果一个词在很多的文本中出现，那么它的IDF值应该低。
比如在两会的开始，主讲人经常会说“各位代表”这样的敬语，那“各位代表”这个词在语料库中的IDF就非常低。
#### TF-IDF

