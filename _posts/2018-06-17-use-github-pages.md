---
layout: post
title: 使用GitHub Pages和Jekyll搭建自己的博客
date:   2018-06-17 21:24:35 +0800
categories: github github-pages jekyll
tags: [jekyll,github-pages]
---

本文将介绍一种免费、高效的搭建博客方案，通过使用Github Pages及Jekyll工具，可以依托于Github生成自己的博客站点。  

## 一、Github Pages介绍

首先我们先来了解一下Github和Github Pages。  

Github是全球知名的代码托管网站，无数的开源、闭源项目代码被托管在这个网站上，相信大部分在软件行业有经验的同学都会多少知道这个网站，这里就不详细介绍了。  

同时Github还开放了Github Pages功能，实质上它提供了一个托管静态页面的功能，并且可以使用username.github.io这样的链接来访问这些页面。  

Github规定我们可以使用Github Pages建立用户/组织页面或项目页面。

因此你可以放心地使用Github Pages功能建立个人站点，在站点里你可以为自己的开源项目建立一些介绍页面，或是作为博客记录一些有价值的文字，Github鼓励我们在正确的范围内随意使用Github Pages，所以如果你想搭建自己的博客，就放心的去用吧！

在使用之前，我们先来了解一下Github Pages的一些使用限制：  
> 资源上，Github Pages提供了：  
> 每个仓库1GB的空间大小，因此我们的博客只能在Github上存储不超过1GB的内容  
> 每个月100GB的流量限制  
> 每小时10次build（限制频繁的push新页面到仓库，和Jekyll生成静态站点有关）  
>   
> 使用范围上，Github Pages要求我们避免如下情况：  
> Github规定的违法或其他禁止的内容或行为    
> 暴力、恐吓的内容或行为   
> 过度的批量自动化功能，比如发送垃圾邮件   
> 损害Github用户或服务的行为  
> 快速致富指南  
> 淫秽内容  
> 伪造身份或站点目的的内容（原文如下：Content that misrepresents your identity or site purpose，有点不理解这句）  

使用Github Pages作为个人博客，还要接受下面的缺点：  

> 无法进行后端开发工作（没有后端）  
> 尽量在本地编辑好后再上传，不能频繁地push    
> Github被墙（国内可以使用Coding Pages替代）    

介绍完Github Pages，下面我们来实际建立一个自己的Github个人页面。  

## 二、建立Github Pages项目

Github Pages可以构建两种站点：用户/组织页面或项目页面，它们的用法几乎一样，但在细节上会有一些区别，比如用户/组织页面需要把静态文件推到master分支，而项目页面则要推到项目仓库的gh-pages分支下，本文将实际地建立一个用户页面的Github Pages项目并访问它，但如果你在别的文章里看到gh-pages分支这个东西，那么也别慌，这只是项目页面的不同点而已。  

Github规定，我们建立一个名称类似username.github.io的项目仓库时，该仓库会自动成为用户页面的仓库，我们上传到这个仓库里的静态页面就可以通过username.github.io来进行访问（这里的username是你的帐户名）。  

这里是我的账号：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image.png)  

因此我需要建立一个名为mcace.github.io的仓库：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image2.png)  

进入创建仓库的界面，下图中“根据喜好”表示的是可选填的内容，另外仓库虽然是可以私有的，但Github Pages相关的页面会公开在互联网，所以个人、组织站点的仓库是没有必要私有的： 

![](http://mcace.me/assets/images/2018/use-github-pages/Image14.png)  

下面就是创建好的博客仓库了  

![](http://mcace.me/assets/images/2018/use-github-pages/Image3.png)  

在浏览器里输入[https://mcace.github.io/](https://mcace.github.io/)，进入的页面默认显示的是README.md的内容：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image4.png)  

另外我们还可以选择一些Github Pages准备好的样式，首先选择仓库设置：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image5.png)  

该界面向下拉，到Github Pages项，点击Choose a theme按钮：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image6.png)  

在跳转的页面中，选择一个我们喜欢的主题，点击Select theme按钮应用主题：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image7.png)  

稍等片刻后，会回到仓库并进入README.md文件的编辑界面，先不管他，再来看我们的主页，主题已经应用了：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image8.png)  

这里的主题就是Github Pages通过Jekyll工具支持构建的一个静态页面，我们也可以在本地使用Jekyll构建好HTML页面再上传到仓库，关于Jekyll我们后面再细谈。  

实际上我们也可以为自己的任意项目仓库建立Github Pages，但这不是本文的重点，有兴趣的同学可以自己去搜索一下。  

## 三、上传我们的第一个页面

仓库建好了，下面就要在本地写好HTML页面然后上传到仓库了。  

这一段我会使用终端操作git，实际上你也可以下载一个[Github Desktop](https://desktop.github.com/)桌面工具，有非常直观的交互界面，使用起来更方便。  

首先我们在本地任意位置建立一个文件夹，然后在终端进入该文件夹，使用git clone命令将Github上的仓库复制下来：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image9.png)  

可以看到我的Github Pages仓库已经被拉下来了（这里的连接一定要改成你自己的仓库，可别拉我的仓库哦）：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image11.png)  

![](http://mcace.me/assets/images/2018/use-github-pages/Image10.png)  

进入仓库目录后，我们先写一个首页，Github Pages默认会将index.html作为首页：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image12.png)  

接下来使用add、commit、push命令将index.html上传到仓库的master分支下（这里报错是要求我登录一下）：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image15.png)  

再访问我们的站点，主页就变为（这里我上传后立即访问时还是README.md的内容，稍微等了一会后才改变，应该是Github需要有一个构建跳转的过程）：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image13.png)  

到这里，我们基本上了解并学会使用Github Pages了，现在我们可以在本地写好HTML静态页面，上传到Github Pages仓库，（假设上传的文件名为page.html）就可以使用username.github.io/page.html或username.github.io/page这样的链接来访问那个页面啦！

虽然我们成功地建立起了自己的一个静态页面站点，但是这离一个博客还很远，大多数情况下我们希望有一套现成的博客系统部署，而我们只关注怎么写博客就好了。  
关于这一点Github Pages提供了Jekyll支持，我们刚刚使用的主题就是来自Jekyll，下面我将介绍什么是Jekyll，以及Jekyll众多主题中的Yummy-Jekyll主题的使用方法。

学会使用Yummy-Jekyll主题后，相信很快你也可以找到自己喜欢的Jekyll主题进行使用，甚至对它进行你个人的二次开发（几乎所有主题开发者都鼓励大家这么做），祝你好运！  

## 四、Jekyll是什么？Jekyll-theme又是什么？

首先，本文旨在简单介绍并快速上手，如果你希望深入了解Jekyll的用法及Liquid语法，那么详细内容可以在[这里](http://jekyllcn.com/docs/home/)找到。  

Jekyll实质上是一个静态站点生成工具，最基本的，它可以生成一个完整可发布的静态站点，以及通过模板将markdown文本转换为静态页面。这个静态站点可以部署到任意位置，也就是说如果你有自己的服务器空间，你也可以部署到自己的服务器上。  
因为Github Pages提供了类似服务器空间的功能，因此我们可以将Jekyll和Github Pages结合起来使用。  

使用Jekyll的优点在于你套用了模板后，就不需要再操心页面的布局了，你不再需要写前后端代码（当然如果你想也可以对模板进行开发），只需要专注于写markdown文本，然后在本地生成好站点后，推到Github即可。  

Github本身提供了Jekyll功能，如前文我们的README.md文件，虽然它并不是一个HTML文件，在Github后端通过Jekyll处理后，我们就可以在前端页面上访问到这个md文件的内容。  

但Github对Jekyll做出了限制，为了安全性Gibhub禁止运行Jekyll插件，如果需要使用Jekyll插件，那么就要在本地生成好静态页面后再推到博客仓库里，相对的，一般情况下我们只要写好markdown文件并推送上去，Github就会自动使用Jekyll生成我们的静态站点。  

在本文中我们会将生成静态站点的素材（比如Jekyll配置文件、模板文件、css、js、markdown格式正文等）上传到Github上，Github会自动使用Jekyll工具生成静态站点，以此达到搭建博客的目的。  

一般情况下我们使用Jekyll，需要自己动手写全站所有的东西，比如模板，这就有点麻烦了，不过不要紧，因为已经有大批热心的程序员开发出了非常多的Jekyll主题，通过使用这些主题，我们能很轻松地把模板套用到我们自己的博客上，你可以在[这里](http://jekyllthemes.org/)找到Jekyll-theme，如果你懂前端，并且学习了Jekyll基本语法及Liquid语法，那么你就有能力对模板进行自己的个性化修改了，下面我们将从使用[Yummy-Jekyll](https://github.com/DONGChuan/Yummy-Jekyll)这个主题切入，介绍如何使用Jekyll和Jekyll主题。

## 五、Yummy-Jekyll安装

先简单介绍一下，[Yummy-Jekyll](https://github.com/DONGChuan/Yummy-Jekyll)这个主题是由[DONGChuan](https://github.com/DONGChuan)开发的，你可以在[这里](http://dongchuan.github.io/)看到具体应用后的效果，如果你觉得不满意，没有关系，学会使用后你可以自己找一些Jekyll主题使用！  

由于Jekyll推荐运行在Linux系统下，因此下面的教程是基于CentOS 6.9系统进行的。CentOS安装软件包的命令为yum，如果你不是这个系统，请参照自己系统进行安装。另外Jekyll似乎也可以在Win下运行，如果你有需要可以去搜索一下。  

首先我们需要在本地安装Jekyll，用来生成静态页面及预览，Jekyll是基于Ruby开发的，而Ruby模块（称为Gems）可以通过RubyGems工具进行安装，因此我们先安装Ruby及RubyGems。  

由于CentOS 6.9使用yum默认安装的是1.8的ruby，但Yummy-Jekyll需要高版本Ruby，因此我采用RVM来安装Ruby，RVM是管理Ruby环境的工具，可以借助它来安装Ruby，本文假定你暂时没有安装过Ruby，且笔记撰写时Ruby稳定版本为2.5.1。  

```bash
curl -sSL https://rvm.io/mpapis.asc | gpg --import -
curl -L get.rvm.io | bash -s stable
source /etc/profile.d/rvm.sh
rvm reload
rvm requirements run
rvm install 2.5.1
```

然后安装Git：

```bash
yum install git
```

基本的工具安装完成后，下面是Yummy-Jekyll安装环境的搭建，一般在Github上发布的Jekyll模板，作者都会将安装步骤教给你，所以当你使用其他模板时，请留意，下面的步骤不一定适用于你。  
因为Yummy-Jekyll安装用到了Bundler，因此我们还需要使用RubyGems安装Bundler，Bundler可以看作是管理Gems的工具，当一个Ruby程序依赖了多个Gems，那么Bundler就可以管理这些依赖，提供合并下载等功能，一般情况下安装Jekyll只需要使用RubyGems即可，但Yummy-Jekyll的作者使用了一些其他Gems，并使用Bundler管理这些依赖，因此我们要按照作者提供的步骤进行安装。  

```bash
gem install bundler
```

![](http://mcace.me/assets/images/2018/use-github-pages/Image16.png)  

同时由于Yummy-Jekyll还用到了bower来管理前端插件，因此我们需要安装nodejs，并使用它的npm安装bower，这一步稍微有点麻烦，我的CentOS6.9需要添加yum源，这里我们使用NodeSource提供的安装方式，并安装当前稳定版本nodejs 8.x，如果你的系统不是CentOS 6.9，你可以尝试先安装nodejs，如果失败了再去搜索你的系统安装nodejs的方式。  

```bash
curl https://rpm.nodesource.com/setup_8.x | bash -
yum -y install nodejs
```

安装完成后，使用命令查看版本号，确认完成安装：  

```bash
npm -v
node -v
```

![](http://mcace.me/assets/images/2018/use-github-pages/Image17.png)  

然后安装bower，再使用命令来确认bower安装完成：  

```bash
npm install -g bower
bower -v
```

![](http://mcace.me/assets/images/2018/use-github-pages/Image18.png)  

现在安装环境已经建立了，下面我们要做的是把Yummy-Jekyll部署到我们的Github Pages仓库里。

首先我们使用fork将Yummy-Jekyll添加到我们自己的库中（不强制，直接clone下来也行）：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image19.png)  

Github会提示我们正在forking，稍等一会，页面就会自动跳转到我们fork下来的库中，这时这个项目当前的代码就存在于我们自己的库里了。  

![](http://mcace.me/assets/images/2018/use-github-pages/Image20.png)  

![](http://mcace.me/assets/images/2018/use-github-pages/Image21.png)  

接下来在本地找到合适的位置，使用git clone命令，将fork下来的代码整个拉下来，记得把URL修改成你自己的哦，不要拉我的仓库，拉下来后会自动创建文件夹Yummy-Jekyll，我这里是虚拟机共享目录blog里拉代码的。  

```bash
cd /mnt/hgfs/blog
git clone https://github.com/mcace/Yummy-Jekyll.git
```

接着，因为拉下的是Yummy-Jekyll仓库，我们需要将它切换为我们个人页面仓库，首先我们要删除.git目录：  

```bash
cd Yummy-Jekyll
rm -rf ./.*
```

接着进行Yummy-Jekyll安装操作，非常简单，按顺序输入命令即可：  

```bash
bower install （如果提示bower cannot run with sudo，而你又登陆了root，那么可以在确定安全的情况下加上--allow-root来解决这个问题）
bundler install
```

接下来，找到我们要存放博客的目录，将博客仓库拉下来，记得修改成你自己仓库的URL  

```bash
cd /mnt/hgfs/blog
git clone https://github.com/mcace/mcace.github.io.git
```

拉取完成后，会生成mcace.github.io目录，现在的目录结构如下  

> —|..  
> 　|——mcace.github.io  
> 　|——Yummy-Jekyll  

我们接下来把Yummy-Jekyll目录下的内容移动到博客目录下，如果有发生覆盖，那么根据你自己的情况选择是否用Yummy-Jekyll的文件覆盖你的文件，移动完成后删除Yummy-Jekyll目录并进入博客仓库目录中：  

```bash
mv ./Yummy-Jekyll/* ./mcace.github.io/
rm -rf ./Yummy-Jekyll
cd ./mcace
```

下面就是将本地仓库PUSH到远程仓库的过程了：  

```bash
git add .
git commit -m "yummy-jekyll theme"
git push -u origin master
```

push过程中提示我：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image22.png)  

这是因为没有设置用户名的原因，修改当前目录下的 .git/config文件  

```bash
[remote "origin"]  
    url = https://github.com/mcace/mcace.github.io.git
```

在URL中添加[用户名@]：  

```bash
[remote "origin"]  
    url = https://mcace@github.com/mcace/mcace.github.io.git
```

这时再进行git push，就会提示输入密码，输入完成后，就会进行推送。  

如果你觉得每次push输入密码很麻烦，也可以在URL中添加[用户名：密码@]：  

```bash
[remote "origin"]  
    url = https://mcace:password@github.com/mcace/mcace.github.io.git
```

当push完成后，我们来访问[https://mcace.github.io/](https://mcace.github.io/)，就可以看到效果啦！如果暂时还没有效果，那么可以稍微等待一会！  

![](http://mcace.me/assets/images/2018/use-github-pages/Image23.png)  

## 六、Yummy-Jekyll配置

虽然网页已经显示了，但实际上还是处于半成品状态的，下面几个部分我们将一步步把Yummy-Jekyll的所有内容改成我们自己的。  

首先我们要对自己的博客做一些配置，主要是_config.yml这个文件的修改，这里我展示的是直接在仓库里修改，你也可以在本地仓库修改后push上去。  

首先在仓库里找到_config.yml这个文件，它在根目录下，很快就可以找到：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image24.png)  

接下来点击右边的编辑按钮：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image25.png)  

这一部分要修改的主要是Site settings部分，里面已经有作者的注释了，内容就不详述了。其中要提一下的有：  

> 1.url那一项，要改成你自己的Github个人页面地址，未来你注册了自己的域名，也可以改成你自己的域名。   
> 2.Google Analytics部分，如果你有对应的tracking id，那么可以填一下，如果没有你就可以用#注释掉。    
> 3.disqus部分，如果你还没有，可以注释掉。   
> 4.以及其他一些要填写的部分，如果你不想填，也可以用#注释掉。  
> 5.一定要记住每个KV对之间的冒号后面都要有个空格，否则配置是不会生效的。 

最后我修改的样子为：  

```
# Welcome to Jekyll!
#
# This config file is meant for settings that affect your whole blog, values
# which you are expected to set up once and rarely need to edit after that.
# For technical reasons, this file is *NOT* reloaded automatically when you use
# 'jekyll serve'. If you change this file, please restart the server process.

# Site settings
title: 画中世界 # ex. I m Chuan
name: McAce # ex. DONGChuan
email: n0tmc8ce@gmail.com
#location: My location # ex. Nice, France
#company: MyCompany
#company_url: my_company_website
github_url: https://github.com/mcace # ex. https://github.com/DONGChuan
github_username: mcace # ex. DONGChuan
description: 这是我的博客
favicon: favicon.png # Image must be in assets/images
repository: mcace/mcace.github.io # ex. DONGChuan/DONGChuan.github.io

# The base hostname & protocol for your site
# For local test, please use http://localhost:4000
# For production, use your github page url. ex. http://dongchuan.github.io
url: http://mcace.me

## Gem
gems:
  - jekyll-paginate
  - jekyll-geo-pattern
  - rouge
  - kramdown
  - jekyll-github-metadata

# Build settings
markdown: kramdown
highlighter: rouge
lsi:         false
excerpt_separator: "\n\n"

source:      .
destination: ./_site

quiet:    false
defaults: []


kramdown:
  input: GFM
  enable_coderay: true

# Disque
#disque: my_disque_settings # ex. httpdongchuangithubio.disqus.com

# Pagination
paginate: 10
paginate_path: 'page:num'

# Navigation. If you do not like these modules, just remove them :D
navs:
  -
    href: /
    label: Home

  -
    href: /open-source
    label: Open-Source

  -
    href: /blog
    label: Blog

  -
    href: /bookmark
    label: Bookmark

  -
    href: /about
    label: About

# Sidebar to show popular repo
side_bar_repo_limit: 5 # Number of repos to show

# Google Analytics
#analytics :
#  provider : google
#  google :
#      tracking_id : 'tracking_id'  # ex. UA-72165600-X


# For about.html. Fell free to create your own!
skill_software_keywords: [Java, MySQL, Oracle, PL/SQL]
skill_j2ee_keywords: [Spring, Struct, Hibernet, MyBatis, JSP]
skill_web_keywords: [HTML, CSS, JS, JQuery, Ajax, MongoDB]
#skill_mobile_app_keywords: [Android, Sketch UI Desgin]
```

最后修改完成后，页面拉到底部，点击Commit changes按钮：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image26.png)  

提交后，稍微等几秒，再刷新我们的个人页面，就可以看到修改了：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image27.png)  

最后我们将修改后的代码拉回到仓库：  

```bash
cd /mnt/hgfs/blog/mcace.github.io/
git pull
```

## 七、Jekyll调试

虽然Github支持在其后台使用Jekyll生成静态站点，但很多时候我们更希望在本地调试好再上传到Github上，比如说我们自己修改模板，可以立即在本地看到效果，直到修改满意再上传，因此我们先看一下如何使用Jekyll Server调试我们的页面。  

首先进入博客目录  

```bash
cd /mnt/hgfs/blog/mcace.github.io
```

然后修改_config.yml文件的url项，这里我直接在Win系统下进行修改了，我是在虚拟机里运行的Linux系统，使用NAT模式联网，这里就设置为虚拟机的局域网IP地址，虚拟机的IP地址可以使用ifconfig进行查看，如果你本身就是Linux系统，那么使用http://localhost:4000或http://127.0.0.1:4000或http://本机IP:4000即可。  

![](http://mcace.me/assets/images/2018/use-github-pages/Image28.png)  

因为上传到Github上的url必须是你个人仓库的地址或你的域名，但本机调试url必须是本地地址，因此你每次上传_config.yml文件时，都要仔细看看url项的配置，如果只是为了本地调试而修改该文件，那么就不要再推送该文件了。  

同时我的虚拟机开启了防火墙，那么就要打开4000端口的访问权限  

```bash
vim /etc/sysconfig/iptables
```

在22条例下面添加一条4000端口的条例  

```bash
-A INPUT -m state --state NEW -m tcp -p tcp --dport 4000 -j ACCEPT
```

![](http://mcace.me/assets/images/2018/use-github-pages/Image29.png)  

下面在博客目录下使用jekyll命令打开服务器  

```bash
jekyll server -w --host=192.168.249.130
```

![](http://mcace.me/assets/images/2018/use-github-pages/Image30.png)  

这里的-w表示Jekyll服务器会监视新文件的改动，比如有新的博文进来，那么Jekyll就会自动生成相关文件，但如果是修改了_config.yml文件，那么就要重新运行Jekyll服务器了。  

指定--host表示发布到哪个IP地址，默认发布到127.0.0.1，这样作为局域网内的宿主机是无法访问的，我这边要改成0.0.0.0或192.168.249.130才能访问，你可以根据你自己的情况尝试，我个人感觉发布到虚拟机的局域网IP地址访问速度要比发布到0.0.0.0要快。  

另外这里的Jekyll Server是运行在终端的前台的，使用Ctrl-C可以快速关闭，如果你希望Jekyll Server运行在后台，那么就在命令后面加上-B或--detach即可，运行完成后Jekyll会提示你pid号，要暂停Jekyll Server则直接使用kill -9 [pid]即可。  

现在在宿主机上访问http://192.168.249.130:4000，就能访问到博客页面了！  

![](http://mcace.me/assets/images/2018/use-github-pages/Image31.png)  

下面我们再来看到博客目录下，可以看到多出了一个_site的目录，点进去里面就是生成的静态站点。  

![](http://mcace.me/assets/images/2018/use-github-pages/Image33.png)  

关于这个_site目录，我们可以看一下_config.yml文件，其中有source和destination配置如下：  

![](http://mcace.me/assets/images/2018/use-github-pages/Image32.png)  

source就表示我们生成静态站点的素材所在的目录，这里是当前目录下的所有文件。  

destination就表示生成的静态站点目录，这里设置为当前目录下的_site，因此Jekyll会自动生成该目录。

另外需要提一下的是，如果你只是想使用Jekyll生成你的静态站点（也许你想部署在你自己的服务器上），可以使用命令  

```
jekyll build
```

创建_site目录下的内容。

因为我们使用的Github Pages会自动用Jekyll根据_config.yml文件构建静态站点，因此我们不需要将_site目录上传到仓库里，这里我们在博客目录下创建.gitignore文件,内容为"_site"，用来忽略掉_site目录  

```bash
cd /mnt/hgfs/blog/mcace.github.io
echo _site > .gitignore
```

如果你用了Github Desktop，那么就按下面步骤添加即可  

![](http://mcace.me/assets/images/2018/use-github-pages/Image34.png)  

![](http://mcace.me/assets/images/2018/use-github-pages/Image35.png)  

到这里Jekyll Server调试就讲完了，下面我们来初步个性化我们的模板，因为我使用了虚拟机共享文件夹，因此这一步我将不再在Linux下操作。  

## 八、模板修改

首先修改一下about.html，把里面的“blablabla”改成你想说的话。  

然后修改一下_config.yml，其中最下面以skill_为开头的几个配置，是about页面的个人技能几个栏位的展示文字。  

模板修改暂时就只写这么多了，因为都是前端的东西，所以我这里暂时不研究了，如果你希望能进一步个性化站点，可以自己探索一下。

## 九、编辑、发布文章

现在我们已经有一套博客了，下面就是最重要的写文章部分了，在markdown文本编辑环境中我选择了[Sublime Text 3](https://www.sublimetext.com/3)+Markdown Editing插件，Sublime Text 3是一款免费的本文编辑器，支持安装多种插件，我也用过同类型的Notepad++，但始终觉得Sublime Text界面更加友好，插件更易用。  

#### 安装Markdown Editing插件  

首先Sublime Text 3在安装插件之前，要安装[Package Control](https://packagecontrol.io/installation)插件，这个插件可以用于安装各种Sublime Text的插件。    

打开Sublime Text 3，按Ctrl+`呼出控制台，输入下面这行命令后回车:

> import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)

稍等片刻，控制台会给出一串数字，这时重启Sublime Text 3即可。   

重启后按Ctrl+Alt+P打开包管理界面，在文本框中输入"pcip"，如果Package Controller被正确安装则会显示"Package Control： Install Package"，这时按下回车即可进入包安装界面（可能要稍微等一会才弹出），输入"Markdown Editing"即可找到插件，按回车即可进行安装。    

但不知什么原因，我的Package Control找不到Markdown Editing，因此这里再介绍另一种安装Sublime Text插件的方法： 
首先在这里下载[Markdown Editing](https://github.com/SublimeText-Markdown/MarkdownEditing/archive/master.zip)的官方打包。 

下载完成后用“解压到当前文件夹”进行解压缩，然后将"MarkdownEditing-master"文件夹改名为"MarkdownEditing"，再移动到Sublime Text 3的插件目录下，插件目录可以通过在Sublime Text 3下按Alt+N -> B来找到。  

这时重启Sublime Text 3，按Ctrl+Alt+P打开包管理界面，输入"Markdown Editing"即可找到该插件。

#### Markdown Editing在Windows下的一些操作：

>Ctrl+Alt+V：粘贴一个内嵌链接，如果选中了一段文字粘贴则自动设为链接文字，一般是复制一段链接然后使用该快捷键  
>Ctrl+1-6：添加标题，从小到大，文字比一般文字大一些，如果选中了一段文字则自动设置该段文字  
>Shift+Win+K：粘贴一个内嵌图片，一般是复制一张图片的链接然后使用该快捷键  
>Alt+B/I： __加粗__ 一段文字  

#### 同时我再介绍一些Markdown语法

-   首先是最重要的换行，在段落内换行时，单纯的回车换行是不行的，必须在每行末尾处增加至少两个空格才能实现换行。  
![](http://mcace.me/assets/images/2018/use-github-pages/Image49.png)
-   如果想实现两段文字中产生分段的效果，只要在两行文字中空一行即可实现。 
![](http://mcace.me/assets/images/2018/use-github-pages/Image50.png)
-   接着是和本段文字类似的列表，在列表每项前增加"-"、"+"或"*"再接三个空格即可。  
![](http://mcace.me/assets/images/2018/use-github-pages/Image51.png)
-   如果需要带数字的列表，则可以使用'1.'接一个空格来创建。  
![](http://mcace.me/assets/images/2018/use-github-pages/Image52.png)
-   如果需要输入代码，那么可以在代码首尾两行用四个空格或\`\`\`包围即可，使用\`\`\`包围代码时，可以在首行的\`\`\`后指定代码格式进行高亮，比如\```java，但是代码能不能高亮要看markdown支持的实际情况。  
![](http://mcace.me/assets/images/2018/use-github-pages/Image53.png)
-   如果要输入一段引用内容，可以使用'>'再接文字，比如下面这段就是`>测试文本`输入的。
   >测试文本

关于插入图片，我这里用Github仓库来存图片，文章内的图片使用了绝对地址来引用，步骤如下：

1. 将图片放在仓库内的`assets\images\2018\use-github-pages`目录下。
2. 在文章内插入图片链接，格式为`http://mcace.me/assets/images/2018/use-github-pages/图片名`

当然很多人会很介意这种Github的使用方式，所以如果你有靠谱的图床，也可以外链到图床来插入图片。

#### 下面是上传到github看效果的时候了，步骤如下：

1. 在文章头部添加下面这段yaml头信息，用于Jekyll生成页面
```
---
layout: post
title: 使用GitHub Pages和Jekyll搭建自己的博客
date:   2018-06-08 00:54:35 +0200
categories: github github-pages jekyll
tags: [jekyll,github-pages]
---
```
2. 将本文命名为`2018-06-06-use-github-pages.md`，放在`_posts`目录下  
3. 将图片文件放在`assets\images\2018\use-github-pages`目录下  
4. 上传文件到Github仓库里
5. 稍等片刻，页面生成完成后在自己的Github Pages页面查看文章

__注意Jekyll使用的md文件名，格式必须为[yyyy-mm-dd-title.md]__

到这里基本上接完成了Github Pages博客站的初步工作了，我们只要写好Markdown文本，放在_posts目录下再上传即可完成博文的写作和分享了！    

但是每一篇Markdown文本我们都要添加yaml头信息才能让Jekyll自动生成对应的Html页面，这是很麻烦的一件事情，下面我们简单地了解一下yaml头信息，以及使用SublimeTmpl构造模板实现yaml头的自动插入。

#### yaml头信息，SublimeTmpl实现日志模板

上述的yaml头信息表示了文章使用的模板、标题、创建日期、日志分类及标签，这些都是用于Jekyll生成静态站点及相关HTML的信息，并且这里的 配置都可以在我们定义的模板页面中获取，以便用于生成自定义页面，如果你感兴趣可以看一看`_layouts\post.html`页面的内容，这是Yummy-Jekyll作者自制的内容。

在Sublime Text下使用SublimeTmpl这个插件可以通过自定义模板来创建自带yaml头信息的文本，这样我们就不需要每次都手动去添加这些内容，首先来看插件的安装：

1. 在Sublime Text 3中按Ctrl+Alt+P打开包管理界面，输入pcip按回车，等待包安装界面弹出。
2. 在包安装界面输入SublimeTmpl，选择插件并回车或左键单击安装。
3. 安装完成后，查看菜单栏的[文件] /[File]下是否存在[New File(SublimeTmpl)]选项，如果没有就重启Sublime Text 3再看。

安装成功后如图：  
![](http://mcace.me/assets/images/2018/use-github-pages/Image45.png)

下面我们来添加模板文件：    

1. Alt+N -> B，或者在菜单的[首选项]下，选择[浏览插件目录]（我安装了中文插件）。
2. 找到目录`SublimeTmpl\templates`，可以看到里面有几个预置的模板文件了。
3. 新建文件`md_blog.tmpl`，使用文本编辑器打开
4. 添加如下内容    
> \---  
> layout: post  
> title:    
> date:   ${date}  
> categories:   
> tags: []  
> \---
5. 回到上一级目录，找到`Main.sublime-menu`文件，打开编辑，文件内容是json格式的，找到id为`sublimetmpl`的节点，在`children`数组格式内容中添加如下内容（其中caption是显示在菜单中的内容，type是模板的文件名）：
```
{
    "id": "md_blog",
    "caption": "md_blog",
    "command": "sublime_tmpl",
    "args": {
        "type": "md_blog"
    }
}
```
修改后内容如图：  
![](http://mcace.me/assets/images/2018/use-github-pages/Image46.png)
6. 修改完成后，在菜单中就可以看到我们设置的模板了，点击后就是我们设置的模板：    
![](http://mcace.me/assets/images/2018/use-github-pages/Image47.png)
7. 为了不使Jekyll在生成站点时因为时间设置导致错误，我们必须在生成的时间中添加时区信息，在插件目录中找到`SublimeTmpl.sublime-settings`文件，在`date_format`节点数据末尾添加" +0800"，如图：
![](http://mcace.me/assets/images/2018/use-github-pages/Image48.png)

到这里基本上就完成了模板的创建，另外关于SublimeTmpl的使用主要是参考博文：[Sublime Text tmpl插件快速生成md文件YAML头](http://www.shaoguoji.cn/2016/02/20/Sublime_Text_pakage_generate_md_file_YAML_head_information/)，这里还提供了设置创建新文件的快捷键的方法，你可以参考一下。

#### 博文预览

通过在Sublime Text 3中安装MarkdownPreview插件可以实现博文的预览，通过Package Control安装插件的过程不再赘述，这里仅介绍我经常使用的方式：

1. Alt+P打开网页预览博文
2. 博文更新后保存，刷新对应网页即可实现内容更新


#### 现在创建新文件并开始写博客的方式也就基本固定了：

1. 通过模板创建文件
2. 配置title,catagory,tags
3. 开始写博文
4. 如果有图片，则通过各种方法得到图片的外链，然后插入博文中
5. 通过MarkdownPreview插件预览效果，如有必要可以在本地运行Jekyll查看全站
6. 保存博文，文件名格式为[yyyy-mm-dd-title.md]，上传到Github仓库
7. 在Github Pages站点欣赏自己的大作

__另外需要注意：博文第一行会作为预览显示在博客网站的博文目录里，所以大家第一句一定要简单概括一下文章内容，不要上来就一整段主要内容！__

###### 至此，整篇文章也就算结束了吧，其中肯定会有不足之处，还望大家多多包含，不吝赐教，另外也请关注我的另一篇Github Pages博客相关的博文：[配置域名到Github Pages](http://mcace.me/github/github-pages/jekyll/2018/06/17/use-domain-for-github-pages.html)，该文以GoDaddy上购买的域名配置到Github Pages博客页面为例介绍域名配置。
##### 最后非常感谢您的阅读！
