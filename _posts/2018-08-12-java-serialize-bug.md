---
layout: post
title: 简单了解Java反序列化漏洞
date: 2018-08-12 21:18:38 +0800
categories: java,java-core,java-core-serialize
tags: [java,java-core,java-core-serialize]
---
Java反序列化漏洞：

来自博客：
- [序列化和反序列化漏洞的简单理解](https://blog.csdn.net/jameson_/article/details/73826384)
- [java反序列化漏洞原理研习](https://www.cnblogs.com/KevinGeorge/p/8448967.html)

##### 原理：

Apache Common Collections库有一个TransformedMap类，该Map在每次setValue时，都会调用transform方法，该方法对应一系列的Transformer类形成的ChainedTransformer，主要功能是将一个对象转换成另一个类的对象，Transformer类需要用户自己实现，但Apache也实现了一部分，其中就有一个叫InvokerTransformer的类，该类的transform方法中使用了反射调用方法，因此可以任意调用Java中的所有方法，甚至是通过Java间接调用系统函数。

比如：`Runtime.getRuntime().exec("command_string");`

##### 因此漏洞利用的方式是：

将一个Map转换成TransformedMap，并定义好InvokerTransformer对象，接着触发Map的setValue方法。由于要在远程执行，因此通过序列化的方式将Map数据传过去，在反序列化调用readObject时触发setValue，即可达到利用InvokerTransformer对象调用任意Java方法的目的。

那么问题在于如何在readObject时触发Map的setValue方法。

Java有一个类AnnotationInvocationHandler（和注解处理有关，对本文来说不重要），该类内部持有一个Map类型的成员变量，并且该类有自定义的readObject方法，在readObject时会对Map的值进行setValue。

##### 因此漏洞利用方案如下：

构造一个AnnotationInvocationHandler对象，内部持有一个Map类型，实际为TransformedMap的对象，在该TransformedMap对象的ChainedTransformer中设置一连串的InvokerTransformer对象，在这些InvokerTransformer对象中定义好要调用的方法名，这些对象就是调用Java方法乃至系统函数的关键。

构造完AnnotationInvocationHandler对象后，将其序列化，序列化的数据传入到目标开放的反序列化接口，这样在readObject时，触发setValue，触发InvokerTransformer，成功执行方法调用代码。

---
这个漏洞要求目标开放一个反序列化的网络接口（或是其他可以传入数据进行反序列化的方式），并且使用Apache Common Collections，并且有触发setValue的方案（上面用了Java的AnnotationInvocationHandler类，实际上还有别的方案）。

它结合了Java反射调用方法、远程传输数据执行方法，不需要在目标服务器有任何权限，只要满足条件就能控制目标机器。