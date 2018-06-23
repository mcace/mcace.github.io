---
layout: post
title:  初尝Spring-Boot
date:   2018-06-20 20:47:11 +0800
categories: spring spring-boot
tags: [spring,spring-boot,spring-boot学习笔记]
---

Spring-Boot是Pivotal团队贡献的全新后端Java开发框架，它主要整合了Spring、Spring MVC及各种第三方框架，并且以“约定大于配置”的思想，解决了以往Spring开发中需要面对大量配置文件的状况。

使用Spring-Boot，我们以往需要配置的application.xml、web.xml等都可以省略了，框架会自动帮我们引入类似<component-scan>、Spring Listener及Dispatch Servlet等配置，因为这些都是约定好的，极少需要变动的配置。

并且Spring-Boot内嵌了Tomcat、Jetty这样的Web应用服务器，因此一个Spring-Boot项目可以在打包后单独部署在任意位置，通常Spring-Boot项目也和容器技术(Docker)、持续集成技术(Jenkins)合在一起，搭建一套完整的持续集成、持续部署、持续交付的开发模式。

简言之，Spring-Boot可以  

1. 极大地简化Web应用的开发流程，更快地应用Spring、Spring MVC等框架，使开发人员可以更专注于代码工作。
2. 减少配置，不再重复地进行复制粘贴配置的工作。
3. 内嵌Web服务器，可以以独立部署的jar包形式存在（Standalone Jars），更加契合微服务的开发模式。

学习Spring-Boot，可以说是我们进入分布式、微服务化大门的第一步，因此从今天起我将持续更新一套Spring-Boot的学习笔记，其中大部分内容都是从网上其他博主的文章中参考整理而来，间或也有我自己的一些想法在里面，因此本文只能算是半原创、记录学习过程的一个笔记。

## 构建Spring-Boot Hello World项目

要构建一个Spring-Boot新项目，你可以手动在项目里用Maven或Gradle引入Spring-Boot的依赖，但Spring官网提供了一个更便捷的构建办法：

1. 访问[https://start.spring.io/](https://start.spring.io/)
2. 如图中所示，指定好Group、Artifact，Spring-Boot版本选择当前稳定的1.5.14
![](https://mcace.me/assets/images/2018/spring-boot-chapter1/img1.jpg)
3. 点击Generate Project，浏览器就会自动下载一个zip包，命名为你设定的Artifact，解压后就是新建的项目
4. 解压后的项目，直接导入IDE中即可，并且需要添加Maven支持，我使用的IDEA可以直接按照Maven项目进行添加，这里就不再赘述了

导入后，项目目录如下：

![](https://mcace.me/assets/images/2018/spring-boot-chapter1/img2.jpg)

其中包含了一些自动生成的目录和类：  

-   `src\main\java`下是程序源代码，其中Chapter1Application.java是整个Spring-Boot的启动入口。  
-   `src\test\java`下是测试类源代码，其中Chapter1ApplicationTests.java是启动入口的测试类，当前里面并没有什么内容。
-   `src\main\resources`下是各种配置文件所在的目录，其中自动生成的application.properties是应用于Spring-Boot各个集成框架的配置文件，目前也没有什么内容。

这个目录结构是Maven编译项目的约定结构，我们不需要再配置什么，只要按照各个目录的用法使用即可。

## 快速入门——写一个简单的web应用

#### 添加web模块

因为我们在构建时没有选定spring-web模块，因此当前的项目还不能直接使用spring-mvc，Maven项目按照如下步骤添加spring-web模块：

1. 打开pom.xml
2. 在`<dependencies>`内添加如下内容：
~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
~~~

#### 编写Hello服务

新建HelloController，内容如下：

~~~java
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello() {
        return "Hello World";
    }

}
~~~

#### 运行

在IDE里直接运行Chapter1Application.java即可，运行完成后访问`http://localhost:8080/hello`就可以看到返回的字符串了。

#### 编写测试用例

本节使用MockMvc发送Http请求，来验证接口正常：

1. 在src\test\java目录下的添加一个HelloControllerTest.java文件，使用IDEA可以将光标放在HelloController类里的类名上，然后按Alt+Enter，选择Create Test来自动生成测试类。
2. 测试类内容如下：
~~~java
@RunWith(SpringRunner.class)
@SpringBootTest
public class HelloControllerTest {

    MockMvc mvc;

    @Before
    public void setUp() throws Exception {
        mvc = MockMvcBuilders.standaloneSetup(new HelloController()).build();
    }

    @Test
    public void hello() throws Exception {
        mvc.perform(get("/hello").accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().string(equalTo("Hello World")));
    }

}
~~~
3. 在import区引入静态方法，很多教程都没有写这一步，但是我本机实践下来不写是找不到上面的get()、status()等静态方法的。
~~~java
import static org.hamcrest.CoreMatchers.equalTo;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
~~~
4. 运行测试类或测试方法，查看有无异常，这一步之后，可以将静态字符串`Hello World`改成其他不同字符串，运行测试类查看有无异常，来看看测试出异常时会是什么样的效果。


###### 到这里，Spring-Boot应用基本就入门了，本章我们简单地构建了一个提供返回`Hello World`字符串的Http协议接口方法，这个过程中如果你曾经用过Spring+Spring MVC，就一定能感受到这个过程有多方便快捷，在后面的学习中，我将继续深入Spring-Boot的实践及原理解析。