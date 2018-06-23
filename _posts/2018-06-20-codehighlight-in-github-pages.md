---
layout: post
title:  在Github Pages中让列表内的代码块也支持高亮
date:   2018-06-23 18:35:15 +0800
categories: github github-pages jekyll
tags: [github-pages,jekyll,markdown,kramdown]
---

我在写作之前的日志时，发现代码块在列表中很难写，大部分在列表中使用```包围的代码块都会出现解析错误的现象，因此我参考了别人的方法，找到了解决办法。

##### 下面是一段格式错误的列表内嵌代码块：

文本格式如下：
~~~
1. 列表项1
2. 列表项2（包含代码）
```java
public static void main(String[] args) {
    System.out.println("Hello Code Blocks");
}
```
3. 列表项3
~~~

显示效果如下：

1. 列表项1
2. 列表项2（包含代码）
```java
public static void main(String[] args) {
    System.out.println("Hello Code Blocks");
}
```
3. 列表项3

实际上这么写是错误的，虽然在Sublime Text 3的插件Markdown Preview中可以正常显示，但经过Github Pages的Jekyll生成的静态页面就会出现错误

##### 要解决这个问题很简单，正确的写法是：

~~~
1. 列表项1
2. 列表项2（包含代码）

   ```java
   public static void main(String[] args) {
       System.out.println("Hello Code Blocks");
   }
   ```

   由于本条列表项首行缩进了3个字符（'2''.'' '三个字符），因此代码块也整体缩进3个字符，同时代码块前后要有空白行
3. 列表项3
~~~

显示效果如下：

1. 列表项1
2. 列表项2（包含代码）

   ```java
   public static void main(String[] args) {
       System.out.println("Hello Code Blocks");
   }
   ```

3. 列表项3

##### 正确写法遵循的规则为：

1. 列表项中，代码块插到哪个条目里时，当该条目首行缩进了x个字符时，代码块就要缩进x个字符。
2. 列表项中，代码块前后要有空行。

这个问题的根源是由于Jekyll 3默认使用rouge作为markdown代码高亮器，，并且Github Pages自2016年1月起只支持kramdown + rouge的组合，因此我们写作在Github Pages上的markdown文本必须遵循rouge的语法，也是很蛋疼了，并且Sublime Text 3的MarkdownPreview插件在对这种类型的列表进行预览时，代码块所在条目的下一条目会从起始数字开始而不是继续下去，这应该是MarkdownPreview的markdown引擎问题，有空我要研究一下怎么修改。