---
title: '设计模式之 Singleton'
entitle: design-patterns-singleton
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1561368574
comments: true
date: 2019-06-24 17:29:34
tags: DesignPatterns
keywords:
description: 单例设计模式
photos:
- /img/2019/design.png
---

> 保证一个类仅有一个实例，并提供一个访问它的全局访问点。

Singleton 可以说是设计模式中最简单的一个模式了，但是我们要想用好它的话，也不简单~~

## Java 的单例

下面我们先来看在 Java 中如何实现单例模式：

### 懒汉式（需要的时候才会去实例化），教科书版本

```java
class Singleton {

    private static Singleton instance = null;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

}
```

但是很明显，这样实现单例会有线程安全的问题，当有多个线程并行调用 getInstance() 的时候，就会创建多个实例。

### 懒汉式，同步方法

为了解决上面的问题，最简单的方法是将整个 getInstance() 方法设为同步（synchronized）。

```java
class Singleton {

    private static Singleton instance = null;

    private Singleton() {
    }

    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

}
```

虽然做到了线程安全，并且解决了多实例的问题，但是它并不高效。因为在任何时候只能有一个线程调用 getInstance() 方法。但是同步操作只需要在第一次调用时才被需要，即第一次创建单例实例对象时。

### 懒汉式，同步代码块

上面的同步方法 synchronized 的范围是整个方法, 而同步代码块方法将 synchronized 的范围缩小为 Block

```java
class Singleton {

    private static Singleton instance = null;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized(Singleton.class) {
                instance = new Singleton();
            }
        }
        return instance;
    }

}
```

看起来我们进行了改进，但是又造成了线程不安全的问题。当同步代码块结束后立即被其它线程抢占了，也可以执行同步代码块内的代码，这就会和教科书版本一样创建了多个实例。

### 懒汉式，双重检验锁

双重检验锁模式（double checked locking pattern），之所以称其为双重检查锁，是因为会有两次检查 instance == null，一次是在同步块外，一次是在同步块内。

```java
class Singleton {

    private volatile static Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }

        return singleton;
    }

}
```

现在看起来已经线程安全了，但是还是有问题的。以为 `instance = new Singleton()` 这句，这并非是一个原子操作。事实上在 JVM 中这句话大概做了下面 3 件事情：

1. 给 instance 分配内存
2. 调用 Singleton 的构造函数来初始化成员变量
3. 将instance对象指向分配的内存空间（执行完这步 instance 就为非 null 了）

但是在 JVM 的即时编译器中存在指令重排序(Re-Order)的优化。也就是说上面的第二步和第三步的顺序是不能保证的，最终的执行顺序可能是 1-2-3 也可能是 1-3-2。

如果是后者，则在 3 执行完毕、2 未执行之前，被其它线程抢占了，这时 instance 已经是非 null 了（但却没有初始化），所以其它线程会直接返回 instance，但是得到的instance是还没有完全构造的对象，然后就会顺理成章地报错。

### 懒汉式，双重检验锁 + volatile

在 JDK5 以后，我们可以使用 volatile 关键字来解决这个问题，将变量声明成 volatile 以禁止 JVM 指令重排序(Re-Order)优化。

```java
public class Singleton {

    // 声明成 volatile
    private volatile static Singleton instance;

    private Singleton (){
    }

    public static Singleton getSingleton() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }

}
```

这样终于是和同步方法版本一样可靠了~~

## 饿汉式 (当 class 文件被加载的时候，初始化)

```java
class Singleton {

    private static final Singleton instance = new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return instance;
    }

}
```

看起来非常简单，并且天生是线程安全的。如果不考虑 Lazy Loading 带来的这些微小的内存消耗和优化的话，看起来这是一个很不错的选择。

## iOS 的单例

再让我们看一下 iOS 中是怎么实现单例模式的：

> 这里就不考虑 使用 new、alloc] init]、copy 等方法可以创造出不同对象的问题了。

```objc
+ (instancetype)getInstance {
    static Singleton *instance = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
    });
    return instance;
}
```

可以看到，是使用 GCD 里的 dispatch_once (保证在多个线程同时调用的时候，dispatch_once 只会执行一次) 来保证线程安全的。

## JavaScript 的单例

### 模拟传统面向对象语言实现的单例

```javascript
var Singleton  = function() {
  this.instance = null;
}

Singleton.getInstance = function() {
  if(!this.instance) {
    this.instance = new Singleton();
  }
  return this.instance;
}
```

### 全局变量

全局变量不是单例模式，但是一个全局变量确实是独一无二的。但是全局变量存在很多问题，它很容易造成命名空间污染。

我们有必要尽量的减少全局变量的使用，即使需要，也要把它的污染将至最低。

#### 使用命名空间

```javascript
var MyApp = {
  event: {},
  dom: {
    style: {}
  },
}
```

适当的使用命名空间，并不会杜绝全局变量，但可以减少全局变量的数量。

#### 使用闭包封装私有变量

将变量封装在闭包的内部，只暴露一些接口与外界通讯。

```javascript
var user = (function(){
  var _name = 'xiaoming';
  var _age = 26;
  return {
    getUserName: function() {
      return _name;
    },
    getUserAge: function() {
      return _age;
    }
  }
})()
```
