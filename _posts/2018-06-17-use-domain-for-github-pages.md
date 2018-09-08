---
layout: post
title: 配置域名到Github Pages
date:   2018-06-17 15:27:35 +0200
categories: github-pages 建站
tags: [github-pages]
---

本文将介绍在GoDaddy上购买域名后，配置到Github Pages的方法。  

我已经在 [GoDaddy](https://sg.godaddy.com/zh)上购买了mcace.me的域名。首先登录 [GoDaddy](https://sg.godaddy.com/zh)，到后台配置DNS处：  
![](http://mcace.me/assets/images/2018/use-github-pages/Image36.png)  
![](http://mcace.me/assets/images/2018/use-github-pages/Image37.png)  
![](http://mcace.me/assets/images/2018/use-github-pages/Image38.png)  
添加A记录，及一条CNAME记录，其中“名称”对应配置里的“主机”，“值”对应配置里的“指向”。这里A记录主机为@，表示当前域名即mcace.me，CNAME记录表示类似xx.mcace.me，这里配置的www表示为通过www.mcace.me访问时，会指向当前域名，用@表示。这样配置之后通过mcace.me或www.mcace.me都可以访问到我的博客。具体配置类似下图。  
![](http://mcace.me/assets/images/2018/use-github-pages/Image40.png)  
其中A记录的IP在[GitHub Pages帮助页面](https://help.github.com/articles/setting-up-an-apex-domain/)查看，如下图，你配置时可以看一下IP地址有没有变动。 
![](http://mcace.me/assets/images/2018/use-github-pages/Image39.png)  
有的教程会教你创建两个CNAME记录，分别为从@和www指向你个人页面地址，goDaddy不允许添加@主机到CNAME中，因为会影响该域名下其他的类似邮件等功能，因此这里我研究了半天得出了使用A记录来提交到DNS的办法。A记录必须指向一个固定的IP，CNAME可以指向一个域名，看起来CNAME更灵活一点，但实际上正确的用法是A记录配置的是example.com这个裸域(naked domain / root domain)的记录，而CNAME记录配置的是类似www.example.com的子域名的记录。  
这里配置完成后，来到Github的个人页面仓库，进入Settings标签下，找到Github Pages区域，将我们的域名配置进去，并点Save按钮：  
![](http://mcace.me/assets/images/2018/use-github-pages/Image41.png)  
![](http://mcace.me/assets/images/2018/use-github-pages/Image42.png)  
在点击Save按钮后，Github Pages会自动在仓库根目录下生成一个CNAME文件，内容为我们配置的域名，有的教程会教你自己建立这个文件，也是没有问题的。  
![](http://mcace.me/assets/images/2018/use-github-pages/Image44.png)  
一般来说我们配置的DNS要过一小会才能生效，Github Pages的帮助页面表示有可能会需要一天的时间，但我等了几分钟之后，就可以通过[http://mcace.me](http://mcace.me/)来访问了，同时访问[http://mcace.github.io](http://mcace.github.io/)时也会自动跳转到mcace.me。  
![](http://mcace.me/assets/images/2018/use-github-pages/Image43.png)  