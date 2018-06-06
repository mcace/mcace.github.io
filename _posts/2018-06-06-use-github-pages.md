# 2018-06-06

本文将介绍一种免费、高效的搭建博客方案，通过使用Github Pages及Jekyll工具，可以依托于Github生成自己的博客站点。
## 一、Github Pages介绍
首先我们先来了解一下Github和Github Pages。

Github是全球知名的代码托管网站，无数的开源、闭源项目代码被托管在这个网站上，相信大部分在软件行业有经验的同学都会多少知道这个网站，这里就不详细介绍了。

同时Github还开放了Github Pages功能，实质上它提供了一个托管静态页面的功能，并且可以使用username.github.io这样的链接来访问这些页面。

Github规定我们可以使用Github Pages建立用户/组织页面或项目页面。因此你可以放心地使用Github Pages功能建立个人站点，在站点里你可以为自己的开源项目建立一些介绍页面，或是作为博客记录一些有价值的文字，Github鼓励我们在正确的范围内随意使用Github Pages，所以如果你想搭建自己的博客，就放心的去用吧！

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

![](http://mcace.me/assets/images/2018/use-github-pages/Image1.png)

进入创建仓库的界面，下图中“根据喜好”表示的是可选填的内容，另外仓库虽然是可以私有的，但Github Pages相关的页面会公开在互联网，所以个人、组织站点的仓库是没有必要私有的：

下面就是创建好的博客仓库了

在浏览器里输入https://mcace.github.io/，进入的页面默认显示的是README.md的内容：

另外我们还可以选择一些Github Pages准备好的样式，首先选择仓库设置：

该界面向下拉，到Github Pages项，点击Choose a theme按钮：

在跳转的页面中，选择一个我们喜欢的主题，点击Select theme按钮应用主题：

稍等片刻后，会回到仓库并进入README.md文件的编辑界面，先不管他，再来看我们的主页，主题已经应用了：

这里的主题就是Github Pages通过Jekyll工具支持构建的一个静态页面，我们也可以在本地使用Jekyll构建好HTML页面再上传到仓库，关于Jekyll我们后面再细谈。
实际上我们也可以为自己的任意项目仓库建立Github Pages，但这不是本文的重点，有兴趣的同学可以自己去搜索一下。

## 三、上传我们的第一个页面

仓库建好了，下面就要在本地写好HTML页面然后上传到仓库了。
这一段我会使用终端操作git，实际上你也可以下载一个Github Desktop桌面工具，有非常直观的交互界面，使用起来更方便。
首先我们在本地任意位置建立一个文件夹，然后在终端进入该文件夹，使用git clone命令将Github上的仓库复制下来：

可以看到我的Github Pages仓库已经被拉下来了（这里的连接一定要改成你自己的仓库，可别拉我的仓库哦）：


进入仓库目录后，我们先写一个首页，Github Pages默认会将index.html作为首页：

接下来使用add、commit、push命令将index.html上传到仓库的master分支下（这里报错是要求我登录一下）：

再访问我们的站点，主页就变为（这里我上传后立即访问时还是README.md的内容，稍微等了一会后才改变，应该是Github需要有一个构建跳转的过程）：

到这里，我们基本上了解并学会使用Github Pages了，现在我们可以在本地写好HTML静态页面，上传到Github Pages仓库，（假设上传的文件名为page.html）就可以使用username.github.io/page.html或username.github.io/page这样的链接来访问那个页面啦！

虽然我们成功地建立起了自己的一个静态页面站点，但是这离一个博客还很远，大多数情况下我们希望有一套现成的博客系统部署，而我们只关注怎么写博客就好了，关于这一点Github Pages提供了Jekyll支持，我们刚刚使用的主题就是来自Jekyll，下面我将介绍什么是Jekyll，以及Jekyll众多主题中的Yummy-Jekyll主题的使用方法，学会使用Yummy-Jekyll主题后，相信很快你也可以找到自己喜欢的Jekyll主题进行使用，甚至对它进行你个人的二次开发（几乎所有主题开发者都鼓励大家这么做），祝你好运！

## 四、Jekyll是什么？Jekyll-theme又是什么？

首先，本文旨在简单介绍并快速上手，如果你希望深入了解Jekyll的用法及Liquid语法，那么详细内容可以在这里找到。
Jekyll实质上是一个静态站点生成工具，最基本的，它可以生成一个完整可发布的静态站点，以及通过模板将markdown文本转换为静态页面。这个静态站点可以部署到任意位置，也就是说如果你有自己的服务器空间，你也可以部署到自己的服务器上。因为Github Pages提供了类似服务器空间的功能，因此我们可以将Jekyll和Github Pages结合起来使用。
使用Jekyll的优点在于你套用了模板后，就不需要再操心页面的布局了，你不再需要写前后端代码（当然如果你想也可以对模板进行开发），只需要专注于写markdown文本，然后在本地生成好站点后，推到Github即可。
Github本身提供了Jekyll功能，如前文我们的README.md文件，虽然它并不是一个HTML文件，在Github后端通过Jekyll处理后，我们就可以在前端页面上访问到这个md文件的内容。
但Github对Jekyll做出了限制，为了安全性Gibhub禁止运行Jekyll插件，如果需要使用Jekyll插件，那么就要在本地生成好静态页面后再推到博客仓库里，相对的，一般情况下我们只要写好markdown文件并推送上去，Github就会自动使用Jekyll生成我们的静态站点。
在本文中我们会将生成静态站点的素材（比如Jekyll配置文件、模板文件、css、js、markdown格式正文等）上传到Github上，Github会自动使用Jekyll工具生成静态站点，以此达到搭建博客的目的。
一般情况下我们使用Jekyll，需要自己动手写全站所有的东西，比如模板，这就有点麻烦了，不过不要紧，因为已经有大批热心的程序员开发出了非常多的Jekyll主题，通过使用这些主题，我们能很轻松地把模板套用到我们自己的博客上，你可以在这里找到Jekyll-theme，如果你懂前端，并且学习了Jekyll基本语法及Liquid语法，那么你就有能力对模板进行自己的个性化修改了，下面我们将从使用Yummy-Jekyll这个主题切入，介绍如何使用Jekyll和Jekyll主题。

## 五、Yummy-Jekyll安装

先简单介绍一下，Yummy-Jekyll这个主题是由DONGChuan开发的，你可以在这里看到具体应用后的效果，如果你觉得不满意，没有关系，学会使用后你可以自己找一些Jekyll主题使用！
由于Jekyll推荐运行在Linux系统下，因此下面的教程是基于CentOS 6.9系统进行的。CentOS安装软件包的命令为yum，如果你不是这个系统，请参照自己系统进行安装。另外Jekyll似乎也可以在Win下运行，如果你有需要可以去搜索一下。
首先我们需要在本地安装Jekyll，用来生成静态页面及预览，Jekyll是基于Ruby开发的，而Ruby模块（称为Gems）可以通过RubyGems工具进行安装，因此我们先安装Ruby及RubyGems。
由于CentOS 6.9使用yum默认安装的是1.8的ruby，但Yummy-Jekyll需要高版本Ruby，因此我采用RVM来安装Ruby，RVM是管理Ruby环境的工具，可以借助它来安装Ruby，本文假定你暂时没有安装过Ruby，且笔记撰写时Ruby稳定版本为2.5.1。

curl -sSL https://rvm.io/mpapis.asc | gpg --import -
curl -L get.rvm.io | bash -s stable
source /etc/profile.d/rvm.sh
rvm reload
rvm requirements run
rvm install 2.5.1
然后安装Git：

yum install git

基本的工具安装完成后，下面是Yummy-Jekyll安装环境的搭建，一般在Github上发布的Jekyll模板，作者都会将安装步骤教给你，所以当你使用其他模板时，请留意，下面的步骤不一定适用于你。
因为Yummy-Jekyll安装用到了Bundler，因此我们还需要使用RubyGems安装Bundler，Bundler可以看作是管理Gems的工具，当一个Ruby程序依赖了多个Gems，那么Bundler就可以管理这些依赖，提供合并下载等功能，一般情况下安装Jekyll只需要使用RubyGems即可，但Yummy-Jekyll的作者使用了一些其他Gems，并使用Bundler管理这些依赖，因此我们要按照作者提供的步骤进行安装。

gem install bundler

同时由于Yummy-Jekyll还用到了bower来管理前端插件，因此我们需要安装nodejs，并使用它的npm安装bower，这一步稍微有点麻烦，我的CentOS6.9需要添加yum源，这里我们使用NodeSource提供的安装方式，并安装当前稳定版本nodejs 8.x，如果你的系统不是CentOS 6.9，你可以尝试先安装nodejs，如果失败了再去搜索你的系统安装nodejs的方式。

curl https://rpm.nodesource.com/setup_8.x | bash -
yum -y install nodejs
安装完成后，使用命令查看版本号，确认完成安装：

npm -v
node -v

然后安装bower，再使用命令来确认bower安装完成：

npm install -g bower
bower -v

现在安装环境已经建立了，下面我们要做的是把Yummy-Jekyll部署到我们的Github Pages仓库里。

首先我们使用fork将Yummy-Jekyll添加到我们自己的库中（不强制，直接clone下来也行）：

Github会提示我们正在forking，稍等一会，页面就会自动跳转到我们fork下来的库中，这时这个项目当前的代码就存在于我们自己的库里了。


接下来在本地找到合适的位置，使用git clone命令，将fork下来的代码整个拉下来，记得把URL修改成你自己的哦，不要拉我的仓库，拉下来后会自动创建文件夹Yummy-Jekyll，我这里是虚拟机共享目录blog里拉代码的。

cd /mnt/hgfs/blog
git clone https://github.com/mcace/Yummy-Jekyll.git
接着，因为拉下的是Yummy-Jekyll仓库，我们需要将它切换为我们个人页面仓库，首先我们要删除.git目录：

cd Yummy-Jekyll
rm -rf ./.*
接着进行Yummy-Jekyll安装操作，非常简单，按顺序输入命令即可：

bower install （如果提示bower cannot run with sudo，而你又登陆了root，那么可以在确定安全的情况下加上--allow-root来解决这个问题）
bundler install
接下来，找到我们要存放博客的目录，将博客仓库拉下来，记得修改成你自己仓库的URL

cd /mnt/hgfs/blog
git clone https://github.com/mcace/mcace.github.io.git
拉取完成后，会生成mcace.github.io目录，现在的目录结构如下

-|..
 |-mcace.github.io
 |-Yummy-Jekyll
我们接下来把Yummy-Jekyll目录下的内容移动到博客目录下，如果有发生覆盖，那么根据你自己的情况选择是否用Yummy-Jekyll的文件覆盖你的文件，移动完成后删除Yummy-Jekyll目录并进入博客仓库目录中：

mv ./Yummy-Jekyll/* ./mcace.github.io/
rm -rf ./Yummy-Jekyll
cd ./mcace
下面就是将本地仓库PUSH到远程仓库的过程了：

git add .
git commit -m "yummy-jekyll theme"
git push -u origin master
push过程中提示我：

这是因为没有设置用户名的原因，修改当前目录下的 .git/config文件

[remote "origin"]  
    url = https://github.com/mcace/mcace.github.io.git
在URL中添加[用户名@]：

[remote "origin"]  
    url = https://mcace@github.com/mcace/mcace.github.io.git
这时再进行git push，就会提示输入密码，输入完成后，就会进行推送。
如果你觉得每次push输入密码很麻烦，也可以在URL中添加[用户名：密码@]：

[remote "origin"]  
    url = https://mcace:password@github.com/mcace/mcace.github.io.git
当push完成后，我们来访问https://mcace.github.io/，就可以看到效果啦！如果暂时还没有效果，那么可以稍微等待一会！


## 六、Yummy-Jekyll配置

虽然网页已经显示了，但实际上还是处于半成品状态的，下面几个部分我们将一步步把Yummy-Jekyll的所有内容改成我们自己的。
首先我们要对自己的博客做一些配置，主要是_config.yml这个文件的修改，这里我展示的是直接在仓库里修改，你也可以在本地仓库修改后push上去。
首先在仓库里找到_config.yml这个文件，它在根目录下，很快就可以找到：

接下来点击右边的编辑按钮：

这一部分要修改的主要是Site settings部分，里面已经有作者的注释了，内容就不详述了。其中要提一下的有：
1.url那一项，要改成你自己的Github个人页面地址，未来你注册了自己的域名，也可以改成你自己的域名。
2.Google Analytics部分，如果你有对应的tracking id，那么可以填一下，如果没有你就可以用#注释掉。
3.disqus部分，如果你还没有，可以注释掉。
4.以及其他一些要填写的部分，如果你不想填，也可以用#注释掉。
5.一定要记住每个KV对之间的冒号后面都要有个空格，否则配置是不会生效的。
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

提交后，稍微等几秒，再刷新我们的个人页面，就可以看到修改了：

最后我们将修改后的代码拉回到仓库：

cd /mnt/hgfs/blog/mcace.github.io/
git pull

## 七、Jekyll调试

虽然Github支持在其后台使用Jekyll生成静态站点，但很多时候我们更希望在本地调试好再上传到Github上，比如说我们自己修改模板，可以立即在本地看到效果，直到修改满意再上传，因此我们先看一下如何使用Jekyll Server调试我们的页面。
首先进入博客目录

cd /mnt/hgfs/blog/mcace.github.io
然后修改_config.yml文件的url项，这里我直接在Win系统下进行修改了，我是在虚拟机里运行的Linux系统，使用NAT模式联网，这里就设置为虚拟机的局域网IP地址，虚拟机的IP地址可以使用ifconfig进行查看，如果你本身就是Linux系统，那么使用http://localhost:4000或http://127.0.0.1:4000或http://本机IP:4000即可。

因为上传到Github上的url必须是你个人仓库的地址或你的域名，但本机调试url必须是本地地址，因此你每次上传_config.yml文件时，都要仔细看看url项的配置，如果只是为了本地调试而修改该文件，那么就不要再推送该文件了。
同时我的虚拟机开启了防火墙，那么就要打开4000端口的访问权限

vim /etc/sysconfig/iptables
在22条例下面添加一条4000端口的条例

-A INPUT -m state --state NEW -m tcp -p tcp --dport 4000 -j ACCEPT

下面在博客目录下使用jekyll命令打开服务器

jekyll server -w --host=192.168.249.130

这里的-w表示Jekyll服务器会监视新文件的改动，比如有新的博文进来，那么Jekyll就会自动生成相关文件，但如果是修改了_config.yml文件，那么就要重新运行Jekyll服务器了。
指定--host表示发布到哪个IP地址，默认发布到127.0.0.1，这样作为局域网内的宿主机是无法访问的，我这边要改成0.0.0.0或192.168.249.130才能访问，你可以根据你自己的情况尝试，我个人感觉发布到虚拟机的局域网IP地址访问速度要比发布到0.0.0.0要快。
另外这里的Jekyll Server是运行在终端的前台的，使用Ctrl-C可以快速关闭，如果你希望Jekyll Server运行在后台，那么就在命令后面加上-B或--detach即可，运行完成后Jekyll会提示你pid号，要暂停Jekyll Server则直接使用kill -9 [pid]即可。

现在在宿主机上访问http://192.168.249.130:4000，就能访问到博客页面了！

下面我们再来看到博客目录下，可以看到多出了一个_site的目录，点进去里面就是生成的静态站点。

关于这个_site目录，我们可以看一下_config.yml文件，其中有source和destination配置如下：

source就表示我们生成静态站点的素材所在的目录，这里是当前目录下的所有文件。
destination就表示生成的静态站点目录，这里设置为当前目录下的_site，因此Jekyll会自动生成该目录。

另外需要提一下的是，如果你只是想使用Jekyll生成你的静态站点（也许你想部署在你自己的服务器上），可以使用命令

jekyll build
创建_site目录下的内容。

因为我们使用的Github Pages会自动用Jekyll根据_config.yml文件构建静态站点，因此我们不需要将_site目录上传到仓库里，这里我们在博客目录下创建.gitignore文件,内容为"_site"，用来忽略掉_site目录

cd /mnt/hgfs/blog/mcace.github.io
echo _site > .gitignore
如果你用了Github Desktop，那么就按下面步骤添加即可


到这里Jekyll Server调试就讲完了，下面我们来初步个性化我们的模板，因为我使用了虚拟机共享文件夹，因此这一步我将不再在Linux下操作。

## 八、模板修改

首先修改一下about.html，把里面的“blablabla”改成你想说的话。
然后修改一下_config.yml，其中最下面以skill_为开头的几个配置，是about页面的个人技能几个栏位的展示文字。




## 九、发布文章













## 十、配置域名到Github Pages

我已经在 GoDaddy上购买了mcace.me的域名。首先登录 GoDaddy，到后台配置DNS处：



添加A记录，及一条CNAME记录，其中“名称”对应配置里的“主机”，“值”对应配置里的“指向”。这里A记录主机为@，表示当前域名即mcace.me，CNAME记录表示类似xx.mcace.me，这里配置的www表示为通过www.mcace.me访问时，会指向当前域名，用@表示。这样配置之后通过mcace.me或www.mcace.me都可以访问到我的博客。具体配置类似下图。

其中A记录的IP在GitHub Pages帮助页面查看，如下图，你配置时可以看一下IP地址有没有变动。

有的教程会教你创建两个CNAME记录，分别为从@和www指向你个人页面地址，goDaddy不允许添加@主机到CNAME中，因为会影响该域名下其他的类似邮件等功能，因此这里我研究了半天得出了使用A记录来提交到DNS的办法。A记录必须指向一个固定的IP，CNAME可以指向一个域名，看起来CNAME更灵活一点，但实际上正确的用法是A记录配置的是example.com这个裸域(naked domain / root domain)的记录，而CNAME记录配置的是类似www.example.com的子域名的记录。
这里配置完成后，来到Github的个人页面仓库，进入Settings标签下，找到Github Pages区域，将我们的域名配置进去，并点Save按钮：


在点击Save按钮后，Github Pages会自动在仓库根目录下生成一个CNAME文件，内容为我们配置的域名，有的教程会教你自己建立这个文件，也是没有问题的。

一般来说我们配置的DNS要过一小会才能生效，Github Pages的帮助页面表示有可能会需要一天的时间，但我等了几分钟之后，就可以通过http://mcace.me来访问了，同时访问http://mcace.github.io时也会自动跳转到mcace.me。