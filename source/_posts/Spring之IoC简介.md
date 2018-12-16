---
title: Spring 之 IoC 简介
entitle: 'spring-ioc'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 后端
timestamp: 1544927895
comments: true
date: 2018-12-16 10:38:15
tags:
- Spring
keywords:
description: IoC 简介
photos:
- /img/2018/spring.jpg
---

IoC (Inversion of Control)，中文通常翻译为“控制反转”。

> 好莱坞原则 “Don’t call us, we will call you.” 恰如其分地表达了“反转”的意味，是用来形容 IoC 最多的一句话。

## 为什么需要 IoC ？

一般情况下，如果我们依赖于某个类或服务，最简单而有效的方式就是直接在类的构造函数中新建相应的依赖类。即我们自己主动地去获取依赖的对象。但其实我们最终要做的只是想要调用依赖对象的某项服务而已。只要用到这个依赖对象的时候，它能够准备就绪，我们完全可以不管这个对象是自己找来的还是别人送过来的。不需要自己去折腾。

实际上，IoC 就是为了帮助我们避免之前的“大费周折”，而提供了更加轻松简洁的方式。

IoC 的反转，就反转在让你从原来的事必躬亲，转变为现在的享受服务。简单点儿说，IoC 的理念就是，让别人为你服务!

![IoC.png](https://upload-images.jianshu.io/upload_images/1692994-49668210bfb3649f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 通常情况下，被注入对象会直接依赖于被依赖对象。但是，在IoC的场景中，二者之间通过 IoC Service Provider 来打交道，所有的被注入对象和依赖对象现在由IoC Service Provider统一管理。
- 被注入对象需要什么，直接跟 IoC Service Provider 招呼一声，后者就会把相应的被依赖对象注入到被注入对象中，从而达到 IoC Service Provider 为被注入对象服务的目的。
- IoC Service Provider 在这里就是通常的 IoC 容器所充当的角色。
- 从被注入对象的角度看，与之前直接寻求依赖对象相比，依赖对象的取得方式发生了反转，控制也从被注入对象转到了 IoC Service Provider 那里。

## 依赖注入 (Dependency Injection)

IoC 和 依赖注入 其实是同一个概念的不同角度描述。相对 IoC 而言，依赖注入明确描述了 `被注入对象依赖 IoC 容器配置依赖对象`。

> Java 有一个重要特征是反射（reflection），它允许程序在运行的时候动态的生成对象、执行对象的方法、改变对象的属性，Spring 就是通过反射来实现注入的。

依赖注入主要有三种方式：

### 构造方法注入

构造方法注入，就是被注入对象可以通过在其构造方法中声明依赖对象的参数列表， 让外部(通常是IoC容器)知道它需要哪些依赖对象。IoC Service Provider 会检查被注入对象的构造方法，取得它所需要的依赖对象列表，进而为其注入相应的对象。

优点：对象在构造完成之后，即已进入就绪状态，可以马上使用。

缺点：当依赖对象比较多的时候，构造方法的参数列表会比较长。而通过反 射构造对象的时候，对相同类型的参数的处理会比较困难，维护和使用上也比较麻烦。而且在 Java 中，构造方法无法被继承，无法设置默认值。对于非必须的依赖处理，可能需要引入多个构造方法，而参数数量的变动可能造成维护上的不便。

### setter 方法注入

当前对象只要为其依赖对象所对应的属性添加 setter 方法，就可以通过 setter 方法将相应的依赖对象设置到被注入对象中。

优点：因为方法可以命名，所以 setter 方法注入在描述性上要比构造方法注入好一些。另外，setter 方法可以被继承，允许设置默认值，而且有良好的IDE支持。

缺点：对象无法在构造完成后马上进入就绪状态。

### 接口注入

接口注入比较死板和烦琐，如果需要注入依赖对象，被注入对象就必须声明和实现另外的接口。

接口注入是现在不甚提倡的一种方式。因为它强制被注入对象实现不必要的接口，带有侵入性。