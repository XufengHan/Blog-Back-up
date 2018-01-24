---
title: github+hexo构建博客
subtitle: 简洁版  
date: 2017-11-20 17:58:21
tags: github blog
toc: true
---

## 前言
[参考链接](https://www.cnblogs.com/liuxianan/p/build-blog-website-by-hexo-github.html)
我构建博客的过程主要是参考的连接上的那篇博客，博客的主题用的是[yilia](https://github.com/litten/hexo-theme-yilia)。
鉴于链接的资料已经很详细了，构建博客的基本内容我就写的比较简洁，主要是补充一下我碰到的问题。

## 准备工作
[git下载](https://git-scm.com/download/win)
[node.js下载](https://nodejs.org/zh-cn/)
git是我们的博客开发环境,node.js是为了配置博客构建工具hexo，我的域名是在[腾讯云](https://cloud.tencent.com/)这里买的，有没有觉得.club后缀逼格赛高，好吧，其实我就是贪便宜。域名其实也可以不要，但是这样你的博客就没有自定义地址了。


## 配置工作
太麻烦，懒得写，不管啦，配置过程你们好好看看[参考链接](https://www.cnblogs.com/liuxianan/p/build-blog-website-by-hexo-github.html)吧，亲测是没有问题的，有什么问题可以发我邮箱。
配置这个的作用是在git bash里能下载hexo
``` bash
$ git config --global user.name "liuxianan"// 你的github用户名，非昵称
$ git config --global user.email  "xxx@qq.com"// 填写你的github注册邮箱
``` 

这个命令能在本地生成一个网页，访问 http://localhost:4000 就可以查看
``` bash
hexo s 
``` 
自己按照需求改_config.yml文件，记得hexo根目录和yilia根目录下都有这个文件。在yilia下的_config.yml文件里的menu下添加'目录： /archives'（不包括''号）就可以有你的文章列表栏了，tag的不知道是干嘛的，注释掉了。
支付宝，微信，和头像的图片我都自己放在yilia->source->img文件夹下了，路径直接写’/img/pic.png'就行了。

config.yml 具体怎么写看[litten](https://github.com/litten/)的[博客备份](https://github.com/litten/BlogBackup)。

## 搭建评论系统
这个就是我自己捣鼓出来的了，多说目前已经倒闭了，我们的评论系统用的是畅言。大家可能发现注册畅言需要备案号，而我们只有一个域名，没有服务器，是无法备案并且有备案号的，这里需要一个骚操作。

### 搞定畅言评论系统的备案号
怎么搞定很简单，随便找一个有备案的网站，拉倒网站底部，复制它们的备案号，然后在域名白名单里填上我们的域名就行了。
![pic1](https://raw.githubusercontent.com/XufengHan/Month-Cost/master/pic/beanhao.png)

畅言审核通过以后，拿到APP ID 和 APP KEY，如下图
![pic2](https://raw.githubusercontent.com/XufengHan/Month-Cost/master/pic/changyan.png)

在yilia下的_config.yml文件里的changyan_appid和changyan_conf 后面填上我们的APP ID，和 APP KEY，更新一下博客，我们的评论系统就出来了。

## 结语
虽然还有些功能没实现，到这里我们建github博客的攻略还是要告一段落了。


最后我再总结一下<font size=15>腾讯云</font>，<font size=15>斗鱼</font>，快来给我结一下广告费。
