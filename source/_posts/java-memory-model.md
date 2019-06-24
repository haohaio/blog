---
title: 'Java内存模型简介'
entitle: java-memory-model
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 后端
timestamp: 1561361812
comments: true
date: 2019-06-24 15:36:52
tags: Java
keywords:
description: Java内存模型简介
photos: 
- /img/2019/java.png
---

## 简介

> 可见性是指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。

> Java 线程之间的通信由 Java 内存模型（本文简称为 JMM）控制，JMM 决定一个线程对共享变量的写入何时对另一个线程可见。从抽象的角度来看，JMM 定义了线程和主内存之间的抽象关系：线程之间的共享变量存储在主内存（main memory）中，每个线程都有一个私有的本地内存（local memory），本地内存中存储了该线程以读 / 写共享变量的副本。本地内存是 JMM 的一个抽象概念，并不真实存在。它涵盖了缓存，写缓冲区，寄存器以及其他的硬件和编译器优化。

Java 内存模型的抽象示意图如下：

![jmm.png](https://upload-images.jianshu.io/upload_images/1692994-42601b49e7a7034a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图来看，线程 A 与线程 B 之间如要通信的话，必须要经历下面 2 个步骤：

1. 线程 A 把本地内存 A 中更新过的共享变量刷新到主内存中去。
2. 然后，线程 B 到主内存中去读取线程 A 之前已更新过的共享变量。

从整体来看，这两个步骤实质上是线程 A 在向线程 B 发送消息，而且这个通信过程必须要经过主内存。JMM 通过控制主内存与每个线程的本地内存之间的交互，来为 java 程序员提供内存可见性保证。

## 重排序

在执行程序时为了提高性能，编译器和处理器常常会对指令做重排序。但是这也会带来一些线程安全问题。

在进行重排序后，处理器对内存的读/写操作的执行顺序，不一定与内存实际发生的读/写操作顺序一致。例如：

```code
初始状态：a = b = 0

Processor A:    Processor B:
a = 1; // A1    b = 2; // B1
x = b; // A2    y = a; // B2

处理器允许执行后得到结果：x = y = 0
```

具体原因如下图所示：
![re_order.png](https://upload-images.jianshu.io/upload_images/1692994-a509278c29a15b6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由于写缓冲区仅对自己的处理器可见，它会导致处理器执行内存操作的顺序可能会与内存实际的操作执行顺序不一致。由于现代的处理器都会使用写缓冲区，因此现代的处理器都会允许对读/写操作重排序。

> 如上所示，当多个线程同时访问一个数据的时候，可能本地内存的修改没有及时刷新到主内存，所以就会发生线程安全问题。

### happens-before

从 JDK5 开始，java 使用新的 JSR -133 内存模型。JSR-133 提出了 happens-before 的概念，通过这个概念来阐述操作之间的内存可见性。如果一个操作执行的结果需要对另一个操作可见，那么这两个操作之间必须存在 happens-before 关系。与程序员密切相关的 happens-before 规则如下：

- 程序顺序规则：一个线程中的每个操作，happens- before 于该线程中的任意后续操作。
- 监视器锁规则：对一个监视器锁的解锁，happens- before 于随后对这个监视器锁的加锁。
- volatile 变量规则：对一个 volatile 域的写，happens- before 于任意后续对这个 volatile 域的读。
- 传递性：如果 A happens- before B，且 B happens- before C，那么 A happens- before C。

> 基于 volatile 变量规则，可以在 Java 中可以使用 volatile 关键字来修饰多线程间共享变量，使该变量在多线程之间可见。

### 参考

[深入理解 Java 内存模型（一）——基础](https://www.infoq.cn/article/java-memory-model-1/)
