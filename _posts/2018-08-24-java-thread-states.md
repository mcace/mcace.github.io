---
layout: post
title: 图解Java线程状态转换
date: 2018-08-24 20:28:49 +0800
categories: Java并发
tags: [Java并发]
---

Java线程有多种状态，状态之间的转换也是有多种方式，本文用图解方式来说明Java线程状态转换的过程。

![]({{ site.url }}/assets/images/2018/java-thread-states/image1.jpg)

需要注意以下几点：

1. Java线程有六种状态，这一点在Thread的内部枚举类Thread.State可以看到：

   ```java
   public enum State {
        NEW,
        RUNNABLE,
        BLOCKED,
        WAITING,
        TIMED_WAITING,
        TERMINATED;
    }
   ```
   但事实上，传统意义的线程状态只有三种：就绪、运行、阻塞。（新建和结束个人认为只是Java定义的线程状态，而并非传统意义的线程状态）。  
   这里存在对应关系，在图中左上角可以看到。
2. WAITING、TIMED_WAITING两种状态，大部分情况下都是由同步代码内锁对象调用wait()方法来达到的，在这种情况下，结束这两种状态后，由于wait方法会释放锁资源，因此线程会进入BLOCKED状态，也就是网上大部分教程里说的“锁池”“等锁”的状态，归根结底实际上就是线程进入BLOCKED状态然后等待获取锁对象的锁。
3. 但也有如sleep(long)这样的方法，会使线程进入TIMED_WAITING状态，当时间过去或调用interrupt()方法后，就会唤醒线程，直接进入RUNNABLE状态，因为sleep(long)方法不会释放锁资源，因此也不会进入BLOCKED状态。
4. wait()方法的注释上有这么一段

   ```java
   /* A thread can also wake up without being notified, interrupted, or
   * timing out, a so-called <i>spurious wakeup</i>.  While this will rarely
   * occur in practice, applications must guard against it by testing for
   * the condition that should have caused the thread to be awakened, and
   * continuing to wait if the condition is not satisfied.  In other words,
   * waits should always occur in loops, like this one:
   * <pre>
   *     synchronized (obj) {
   *         while (&lt;condition does not hold&gt;)
   *             obj.wait(timeout);
   *         ... // Perform action appropriate to condition
   *     }
   * </pre>
   * (For more information on this topic, see Section 3.2.3 in Doug Lea's
   * "Concurrent Programming in Java (Second Edition)" (Addison-Wesley,
   * 2000), or Item 50 in Joshua Bloch's "Effective Java Programming
   * Language Guide" (Addison-Wesley, 2001).*/
   ```

   表明线程也可能在特殊情况下苏醒，因此比较好的实践经验是用while(条件不符合)来包住wait()方法。我自己暂时没有在实践里见过别人这么写的，所以是见仁见智的一个提示。