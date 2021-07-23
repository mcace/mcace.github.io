---
layout: post
title: 使用Idea的Spring Initializr快速创建spring-boot项目
date: 2018-07-05 14:18:52 +0800
categories: spring-boot Idea
tags: [spring-boot]
---

文本将介绍使用Idea内嵌的Spring Initializr工具快速创建一个Spring-Boot项目。

我的Idea版本为2017.1.1，新版本可能会有变动，不再研究了。

首先打开Idea，选择创建新的项目（`[Create New Project]`，或是在打开项目后的菜单栏`[File]->[New]->[Project..]`）。

弹出的窗口中选择`[Spring Initializr]`项，如下图：

![]({{ site.url }}/assets/images/2018/idea-spring-initializr/1.jpg)

可见默认是通过Spring官网的[https://start.spring.io/](https://start.spring.io/)来进行Spring-Boot项目构建的。

在该界面可以设置JDK版本，如果你设置了1.7，但后面的Java Version设置为8的话，ide就会提示"Selected Java version 1.8 is not supported by SDK (maximum 1.7)"。

由于Spring-Boot最低支持的JDK就是1.8了，所以这里设置为1.8。然后进入下一个界面。

![]({{ site.url }}/assets/images/2018/idea-spring-initializr/2.jpg)

这里大部分东西看一眼就能了解是干什么的，其中需要说明的是`[Type]`项是你想创建一个基于哪个构建工具建立的项目，有Maven和Gradle，同时可以选择`[Generate a Maven(Gradle) based project archive]`，或是`[Generate a Maven pom.xml]`，前者是会创建一个附带Maven(Gradle)默认目录结构的项目，后者只是创建一个附带pom.xml(Gradle配置文件)的空项目，我们保持它默认就行。

在这里配置好你的Group、Artifact等内容，进入下一界面。

![]({{ site.url }}/assets/images/2018/idea-spring-initializr/3.jpg)

该界面用于增加各个Spring-Boot集成模块，同时在右上角可以指定Spring-Boot版本，没有什么太多需要介绍的，按需添加就好！

下一界面就是传统的指定存放目录界面了，选择好存放位置后，Finish即可。

![]({{ site.url }}/assets/images/2018/idea-spring-initializr/4.jpg)

现在一个Spring-Boot项目就创建完成了，我们可以看到Idea内嵌的Spring-Boot构建器使用的还是Spring官方提供的Web工具来进行构建，通过这个构建器，我们可以在IDE内部创建Spring-Boot项目，而不需要再使用Web工具创建、下载、导入IDE这么繁琐的步骤了，可以说是非常的方便。
