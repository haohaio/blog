---
title: '设计模式之 Factory'
entitle: design-patterns-factory
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1561557950
comments: true
date: 2019-06-26 22:05:50
tags: DesignPatterns
keywords:
description: 工厂设计模式
photos:
- /img/tags/design.png
---

## 简单工厂模式

简单工厂其实并不算是一种设计模式，反而比较像是一种编程习惯。让我们先来看一下它的概念：

> 定义一个工厂类，根据传入的参数不同返回不同的实例，被创建的实例具有共同的父类或接口。

直接来个栗子说明一下，下面是两个汽车类，都实现了 Car 的接口：

```java
public interface Car {
    public void run();
}

public class AudiCar implements Car {
    @Override
    public void run() {
        System.out.println("奥迪");
    }
}

public class BenzCar implements Car {
    @Override
    public void run() {
        System.out.println("奔驰");
    }
}
```

下面是工厂类的具体实现：

```java
public class CarFactory {
    public static Car createCar(String carType) {
        Car car = null;
        if (carType.equals("Audi")) {
          car = new CarAudi();
        } else if (carType.equals("Benz")) {
          car = new CarBenz();
        }
        return car;
    }
}
```

在这个工厂类中通过传入不同的 carType 可以 new 出不同的 Car，结果返回 Car 类型。我们可以这样进行使用：

```java
// 创建一辆奥迪类汽车
Car audi = CarFactory.createCar("Audi");
audi.run();
// 创建一辆奔驰类汽车
Car benz = CarFactory.createCar("Benz");
benz.run();
```

简单工厂模式的最大优点在于工厂类中包含了必要的逻辑判断，根据客户端的选择条件动态实例化相关的类，对于客户端来说，去除了与具体产品的依赖。

这就是简单工厂模式了，是不是 so easy ~~

但是当我们需要扩展的时候，例如我们需要创建宝马类的汽车时，不仅需要新增一个 BMWCar 类，还需要对 CarFactory 内部的 createCar 方法，很明显这是违背了开放-封闭原则的。这就引出了工厂方法模式~~

## 工厂方法模式 (Factory Method)

> 定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类。

还是直接来个栗子：

```java
public interface CarFactory {
    public Car createCar();
}

public class AudiCarFactory implements CarFactory {
    @Override
    public Car createCar() {
        return new AudiCar();
    }
}

public class BenzCarFactory implements CarFactory {
    @Override
    public Car createCar() {
        return new BenzCar();
    }
}
```

```java
CarFactory carFactory;

carFactory = new AudiCarFactory();
Car audi = carFactory.createCar();
audi.run();

carFactory = new BenzCarFactory(); // 只需要替换工厂类，就可以创建其它种类的汽车了
Car benz = carFactory.createCar();
benz.run();
```

此时当我们需要创建宝马类的汽车时，我们只需要新增一个 BMWCar 类和一个 BMWCarFactory 类就行了，不会再对原有的代码进行修改了。

简单工厂模式和工厂方法模式都是集中封装的对象的，使得创建时要更换对象时，不需要做大的改动就可实现，降低了客户端与具体产品的耦合。工厂方法模式是简单工厂模式的进一步抽象和推广。工厂方法模式保持了简单工厂模式的优点，而且克服了简单工厂模式违背开放-封闭原则的缺点。但缺点是由于每增加一个产品，就需要加一个产品工厂的类，增加了额外的开发量。

## 抽象工厂模式 (Abstract Factory)

> 提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。

当我们的汽车工厂不仅仅生产汽车，还需要生产各种汽车配件时。按照工厂方法模式来设计的话，当我们要生产各个品牌的发动机时，就新增一个 EngineFactory 以及相关类，当我们需要生成各个品牌的轮胎时，就新增一个 TyreFactory 以及相关类...

很明显这又违背了我们的依赖倒置原则。依赖倒置原则是说抽象不应该依赖于细节，细节应当依赖于抽象。换言之，要针对抽象（接口）编程，而不是针对实现细节编程。

从用户的角度来看就是, 通过尽量少的接口变化, 来尽量多地满足需求变化。因为用户只关心调用创建对象的接口, 而不关心对象是如何创建出来的。这时候就引出来抽象工厂模式了，还是举个栗子说明一下：

```java
// 新增 Engine 接口、AudiEngine 类、BenzEngine 类以及 Tyre 接口、AudiTyre 类、BenzTyre 类
```

```java
public interface CarFactory {
    public Car createCar();

    // 创建发动机接口
    public Engine createEngine();

    // 创建轮胎接口
    public Tyre createTyre();
}
```

```java
// AudiCarFactory 类、BenzCarFactory 类实现 createEngine 和 createTyre 接口
```

```java
CarFactory carFactory;

carFactory = new AudiCarFactory();
Car audi = carFactory.createCar();
Engine audiEngine = carFactory.createEngine();
Tyre audiTyre = carFactory.createTyre();

carFactory = new BenzCarFactory();
Car audi = carFactory.createCar();
Engine benzEngine = carFactory.createEngine();
Tyre benzTyre = carFactory.createTyre();
```

可以看到，抽象工厂模式是就是对工厂方法模式的进一步优化。简单来讲，工厂方法只提供了一个方法用来创建一个产品，而抽象工厂提供了一组接口可用来创建一组相关的产品。

> 但是不知道大家注意了没有，当我们需要扩展的时候，比如我们要生产一种其它的汽车配件时，就会发现我们需要对原有的代码进行修改，这又违背了开放-封闭原则。。。
