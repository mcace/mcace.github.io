---
layout: post
title: 配置域名到Github Pages
date:   2018-06-17 15:27:35 +0200
categories: github-pages 建站
tags: [github-pages]
---

本文将介绍在GoDaddy上购买域名后，配置到Github Pages的方法。  

我已经在 [GoDaddy](https://sg.godaddy.com/zh)上购买了mcace.me的域名。首先登录 [GoDaddy](https://sg.godaddy.com/zh)，到后台配置DNS处：  

1:

![](http://mcace.me/assets/images/2018/use-github-pages/Image36.png)  

2:

![](http://mcace.me/assets/images/2018/use-github-pages/Image37.png)  

3:进入DNS管理界面

![](http://mcace.me/assets/images/2018/use-github-pages/Image38.png) 

这里是我配置好的结果，初见可能不是这样的，可以清空后添加四条A记录及一条CNAME记录。

首先我们先看如何配置，见下图

![](http://mcace.me/assets/images/2018/use-github-pages/Image40.png) 

DNS管理中的“名称”项对应这里的“主机”，“值”项对应这里的“指向”。“主机”是用来配置域名的，“值”表示访问这个域名时，指向哪个IP或域名。

A记录用于配置你当前域名（mcace.me）的指向，CNAME记录用于配置你当前域名的子域名（形如www.mcace.me，hello.mcace.me这些）的指向。

DNS管理中，A记录“主机”为@，等于当前域名，即"mcace.me"，其中指向的IP会在下面讲在哪里得到；CNAME记录中“主机”配置为www，“值”配置为@，表示为通过www.mcace.me访问时，会指向当前域名，即通过www.mcace.me访问时会指向mcace.me这个域名，然后根据A记录解析到指定IP。这样配置之后通过mcace.me或www.mcace.me都可以访问到我的博客。

其中A记录的IP在[GitHub Pages帮助页面](https://help.github.com/articles/setting-up-an-apex-domain/)查看，如下图，你配置时可以看一下IP地址有没有变动。

![](http://mcace.me/assets/images/2018/use-github-pages/Image39.png)

---

###### 有的教程会教你创建两个CNAME记录，分别为从@和www指向你个人页面IP地址，goDaddy不允许添加@主机到CNAME中，因为会影响该域名下其他的类似邮件等功能，因此这里我研究了半天得出了使用A记录来提交到DNS的办法。A记录必须指向一个固定的IP，CNAME则可以指向一个域名或IP，看起来CNAME更灵活一点，但实际上正确的用法是A记录配置的是mcace.me这个裸域(naked domain / root domain)的记录，而CNAME记录配置的是类似www.mcace.com/hello.mcace.me的子域名的记录。

---

这里配置完成后，来到Github的个人页面仓库，进入Settings标签下，找到Github Pages的Custom domain区域，将我们的域名配置进去，并点Save按钮（这里可以把Enforce HTTPS勾上，以后博客就必须通过HTTPS来访问，HTTP访问也会强制转为HTTPS）：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image41.png)

![](http://mcace.me/assets/images/2018/use-github-pages/Image42.png)

在点击Save按钮后，Github Pages会自动在仓库根目录下生成一个CNAME文件，内容为我们配置的域名，有的教程会教你自己建立这个文件，也是没有问题的。

![](http://mcace.me/assets/images/2018/use-github-pages/Image44.png)

至此整个配置就结束了，一般来说我们配置的DNS要过一小会才能生效，Github Pages的帮助页面表示有可能会需要一天的时间，但我等了几分钟之后，就可以通过[https://mcace.me](https://mcace.me/)来访问了，同时访问[https://mcace.github.io](https://mcace.github.io/)时也会自动跳转到mcace.me。

![](http://mcace.me/assets/images/2018/use-github-pages/Image43.png)  