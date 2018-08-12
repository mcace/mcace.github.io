---
layout: post
title: Java序列化数据分析
date: 2018-08-12 19:03:34 +0800
categories: java,java-core,java-core-serialize
tags: [java,java-core,java-core-serialize]
---
学习Java序列化时，对Java实现的序列化数据存储格式感兴趣，简单了解了一下。

了解这个数据存储结构，间接地可以窥到Java序列化是如何实现的，分析时不是看着数据说话，而是对着数据去debug程序，来看每段数据是如何插进去的，这样对了解Java序列化很有帮助。

Java序列化数据分为两部分，一部分是类信息，一部分是具体的数据，大概的实现方式就是把一个类的信息，包括类名、类的字段信息转为二进制数据输出，再将具体的数据输出。

这些分析主要是通过debug java.io.ObjectOutputStream#writeObject(Object obj)方法运行，以及博客[深入学习 Java 序列化](http://www.importnew.com/24490.html)学习得到的知识。

##### 类信息、对象信息：
```
类：com.mcsoft.SerializeDemo.SerialVersionUIDDemo.SerialClass
UID：3952546287202085578L
fields：
    private String name;
    private int age;
    private int height;
data:
    name = "MC"
    age = 18
    height = 170

SerialClass父类：com.mcsoft.SerializeDemo.SerialVersionUIDDemo.SerialFatherClass
UID:1237807120365710903L
no fields.
```

##### 序列化后的原始数据：
```
aced 0005 7372 0039 636f 6d2e 6d63 736f
6674 2e53 6572 6961 6c69 7a65 4465 6d6f
2e53 6572 6961 6c56 6572 7369 6f6e 5549
4444 656d 6f2e 5365 7269 616c 436c 6173
7336 da43 e81e f82a ca02 0003 4900 0361
6765 4900 0668 6569 6768 744c 0004 6e61
6d65 7400 124c 6a61 7661 2f6c 616e 672f
5374 7269 6e67 3b78 7200 3f63 6f6d 2e6d
6373 6f66 742e 5365 7269 616c 697a 6544
656d 6f2e 5365 7269 616c 5665 7273 696f
6e55 4944 4465 6d6f 2e53 6572 6961 6c46
6174 6865 7243 6c61 7373 112d 9304 8dd8
ee37 0200 0078 7000 0000 1200 0000 aa74
0002 4d43 
```

##### 简单解析：
再往下拉有详细解释版
```
aced                    //Magic Num，表示序列化数据开始
0005                    //序列化版本号
73                      //TC_OBJECT，表示定义新对象
72                      //TC_CLASSDESC，开始一段Class的描述(DESC=description)
0039                    //类名长度(字节数)
636f 6d2e 6d63 736f 6674 2e53 6572 6961 //com.mcsoft.SerializeDemo.
6c69 7a65 4465 6d6f 2e53 6572 6961 6c56 //SerialVersionUIDDemo.Seri
6572 7369 6f6e 5549 4444 656d 6f2e 5365 //alClass
7269 616c 436c 6173 73
36 da43 e81e f82a ca    //序列号：3952546287202085578的16进制格式
02                      //flag，程序生成，标识可序列化等信息
0003                    //SerialClass的字段个数，3个
49                      //TypeCode='I'，表示Integer
00 03                   //字段名长度
61 6765                 //字段名UTF-8格式"age"
49                      //TypeCode='I'
00 06                   //字段名长度
68 6569 6768 74         //字段名"height"
4c                      //TypeCode='L'，表示这是一个引用类型
0004                    //字段名长度
6e61 6d65               //字段名"name"
74                      //TC_STRING，表示下面是一段String类型的数据
00 12                   //字段名长度
4c 6a61 7661 2f6c 616e 672f 53
74 7269 6e67 3b         //UTF-8解码后，得到"Ljava/lang/String;"
78                      //TC_ENDBLOCKDATA，和上面的72:TC_CLASSDESC对应，结束类定义
72                      //TC_CLASSDESC，新的Class定义
00 3f                   //类名长度(字节数)
63 6f6d 2e6d 6373 6f66 742e 5365 7269 616c //com.mcsoft.SerializeDemo.
697a 6544 656d 6f2e 5365 7269 616c 5665 //SerialVersionUIDDemo.Seri
7273 696f 6e55 4944 4465 6d6f 2e53 6572 //alFatherClass
6961 6c46 6174 6865 7243 6c61 7373 
112d 9304 8dd8 ee37 0200 00
78                      //TC_ENDBLOCKDATA，结束Class定义
70                      //TC_NULL
00 0000 12              //age的数据
00 0000 aa              //height的数据
74                      //TC_STRING，下面是一个String数据
0002                    //String长度2字节
4d43                    //"MC"的UTF-8编码
```

##### 数据解析
里面每段数据的注释中提及的方法，是写该段数据的代码所在
```
aced                    //Magic Num，表示序列化数据开始
0005                    //序列化版本号
//上面两条在创建java.io.ObjectOutputStream时就被插入到流中，所以可以在构造方法里找到代码

//下面开始定义序列化的对象，代码在java.io.ObjectOutputStream#writeOrdinaryObject
//调用栈：java.io.ObjectOutputStream#writeObject->
//java.io.ObjectOutputStream#writeObject0->
//java.io.ObjectOutputStream#writeOrdinaryObject
//（最开始的writeObject方法就是我们调用的ObjectOutputStream写对象的方法。）

73                      //TC_OBJECT，表示定义新对象
                        //TC_XX可以在java.io.ObjectStreamConstants中找到对应常量

//下面开始写序列化的类描述，代码在java.io.ObjectOutputStream#writeNonProxyDesc
//调用栈：java.io.ObjectOutputStream#writeObject->
//java.io.ObjectOutputStream#writeObject0->
//java.io.ObjectOutputStream#writeOrdinaryObject->
//java.io.ObjectOutputStream#writeClassDesc->
//java.io.ObjectOutputStream#writeNonProxyDesc
72                      //TC_CLASSDESC，表示开始一段Class的描述(DESC=description)
0039                    //类名长度(字节数)
636f 6d2e 6d63 736f 6674 2e53 6572 6961 //com.mcsoft.SerializeDemo.
6c69 7a65 4465 6d6f 2e53 6572 6961 6c56 //SerialVersionUIDDemo.Seri
6572 7369 6f6e 5549 4444 656d 6f2e 5365 //alClass
7269 616c 436c 6173 73
36 da43 e81e f82a ca    //序列号：3952546287202085578的16进制格式

02                      //java.io.ObjectStreamClass#writeNonProxy方法添加的数据
                        //标识类实现了Serializable还是Enternalizable接口
                        //以及有没有自定义的writeObject方法等数据

0003                    //SerialClass的字段个数，3个

//第一个字段
49                      //TypeCode，用于标识下面这条数据的类型
                        //这里是'I'，表示Integer
                        //具体可在java.io.ObjectStreamField#ObjectStreamField(
                        //java.lang.String, java.lang.String, boolean)
                        //及java.io.ObjectStreamField#getClassSignature方法看到。
00 03                   //字段名长度
61 6765                 //字段名编码，这个byte数组可以尝试拿UTF-8格式转换为String，结果为"age"

//第二个字段
49                      //TypeCode='I'
00 06                   //字段名长度
68 6569 6768 74         //字段名"height"

//第三个字段
4c                      //TypeCode='L'，表示这是一个引用类型，除了name还要输出Class名
0004                    //字段名长度
6e61 6d65               //字段名"name"
74                      //TC_STRING，表示下面是一段String类型的数据
                        //这段数据标明了第三个字段的引用类型对应的类名
00 12                   //字段名长度
4c 6a61 7661 2f6c 616e 672f 53
74 7269 6e67 3b         //UTF-8解码后，得到"Ljava/lang/String;"

78                      //TC_ENDBLOCKDATA，表示一段数据块结束，这里是和上面的72:TC_CLASSDESC对应，结束类定义

//下面是写SerializeDemo的父类，写父类的代码可以在java.io.ObjectOutputStream#writeNonProxyDesc方法的最后一行找到
//writeNonProxyDesc方法会递归地输出所有父类，直到getSuperClass返回null为止
//这个顺序是：java.io.ObjectOutputStream#writeOrdinaryObject->
//java.io.ObjectOutputStream#writeClassDesc(ObjectStreamClass desc, boolean unshared)->  
//(先判断desc，决定如何写Class信息)
//if(desc == null) java.io.ObjectOutputStream#writeNull()
//(递归结束)
//else java.io.ObjectOutputStream#writeNonProxyDesc->
//(一般的Class信息通过该方法写)
//writeNonProxyDesc方法最后一句:writeClassDesc(desc.getSuperDesc(), false);
//获取到父类的ObjectStreamClass对象，递归调用java.io.ObjectOutputStream#writeClassDesc
//又回到上面，继续写Class信息了。
//这个递归调用的结束标识是writeNonProxyDesc方法最后一句，desc.getSuperDesc()返回null时
72                      //TC_CLASSDESC，新的Class定义，这里是SerialClass的父类SerialFatherClass的定义
00 3f                   //类名长度(字节数)
63 6f6d 2e6d 6373 6f66 742e 5365 7269 616c //com.mcsoft.SerializeDemo.
697a 6544 656d 6f2e 5365 7269 616c 5665 //SerialVersionUIDDemo.Seri
7273 696f 6e55 4944 4465 6d6f 2e53 6572 //alFatherClass
6961 6c46 6174 6865 7243 6c61 7373 
112d 9304 8dd8 ee37 0200 00
78                      //TC_ENDBLOCKDATA，结束Class定义

70                      //TC_NULL，输出了一个NULL数据
                        //输出NULL的原因：在父类写完后，会继续递归调用
                        //java.io.ObjectOutputStream#writeNonProxyDesc
                        //方法的最后一行找父类的父类，直到父类没有显式地extends时，
                        //getSuperClass会返回一个null值，
                        //接着这个null值就会通过
                        //java.io.ObjectOutputStream#writeNull打印出来，
                        //然后递归调用结束，开始输出数据

//下面是具体的数据了，反序列化时会赋给对象
//写数据的方法在java.io.ObjectOutputStream#writeSerialData，具体要看序列化类是否自己实现了
//private void writeObject(ObjectOutputStream)方法
//如果没有则调用java.io.ObjectOutputStream#defaultWriteFields方法
//如果有则通过反射调用writeObject方法来写数据
//调用栈：java.io.ObjectOutputStream#writeObject->
//java.io.ObjectOutputStream#writeObject0->
//java.io.ObjectOutputStream#writeOrdinaryObject->
//java.io.ObjectOutputStream#writeSerialData
00 0000 12              //按顺序来，默认序列化先输出基本类型数据，这个是age的数据
00 0000 aa              //这个是height的数据
74                      //下面输出引用类型数据，74表示TC_STRING，下面是一个String数据
0002                    //String长度2字节
4d43                    //"MC"的UTF-8编码
```

--------------------------EOF--------------------------