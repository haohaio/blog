---
title: "JavaScript 之 继承"
entitle: javascript-extend
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
comments: true
date: 2020-05-07 14:38:38
timestamp:
tags:
  - JavaScript
keywords:
description:
photos:
  - /img/tags/javascript.jpg
---

## 构造函数继承

### 原型链继承

原型链继承就是将父类的实例作为子类的原型

```js
function Animal() {
  this.species = "Animal";
  this.color = ["black", "white"];
}

Animal.prototype.sayHello = function () {
  console.log("Hello, my species is Animal.");
};

function Cat(name) {
  this.name = name;
}

// 将父类的实例作为子类的原型
Cat.prototype = new Animal();

let cat1 = new Cat("Tom");
console.log(cat1.species); // Animal
console.log(cat1.name); // Tom
console.log(cat1.color); // ["black", "white"]
cat1.sayHello(); // Hello, my species is Animal.

let cat2 = new Cat("Jack");
cat2.color.push("blue");

// 父类的引用属性会被所有子类实例共享
console.log(cat2.color); // ["black", "white", "blue"]
console.log(cat1.color); // ["black", "white", "blue"]
```

- 优点

1. 简单，易于实现
2. 父类新增原型方法、原型属性，子类都能访问到

- 缺点

1. 无法实现多继承，因为原型一次只能被一个实例更改
2. 父类的引用属性会被所有子类实例共享
3. 子类构建实例时不能向父类传递参数

### 构造函数继承

```js
function Animal(species) {
  this.species = species;
  this.color = ["black", "white"];
}

Animal.prototype.sayHello = function () {
  console.log(`Hello, my species is ${this.species}.`);
};

function Cat(name) {
  Animal.call(this, "Cat");
  this.name = name;
}

let cat1 = new Cat("Tom");
console.log(cat1.species); // Cat
console.log(cat1.name); // Tom
console.log(cat1.color); // ["black", "white"]
cat1.sayHello(); // Uncaught TypeError: cat1.sayHello is not a function

let cat2 = new Cat("Jack");
cat2.color.push("blue");
console.log(cat2.color); // ["black", "white", "blue"]
console.log(cat1.color); // ["black", "white"]
```

- 优点

1. 解决了原型链继承中子类实例共享父类引用属性的问题
2. 创建子类实例时，可以向父类传递参数
3. 可以实现多继承（call 多个父类对象）

- 缺点

1. 只能继承父类实例的属性和方法，不能继承其原型上的属性和方法
2. 父类的方法不能复用，子类实例的方法每次都是单独创建的。

### 组合继承

```js
function Animal(species) {
  this.species = species;
  this.color = ["black", "white"];
}

Animal.prototype.sayHello = function () {
  console.log(`Hello, my species is ${this.species}.`);
};

function Cat(name) {
  Animal.call(this, "Cat");
  this.name = name;
}
Cat.prototype = new Animal();

let cat1 = new Cat("Tom");
cat1.sayHello(); // Hello, my species is Cat.

console.log(cat1 instanceof Animal); // true
console.log(cat1 instanceof Cat); // true
console.log(cat1.constructor === Animal); // true
console.log(cat1.constructor === Cat); // false
```

- 优点

1. 父类的方法可以被复用
2. 父类的引用属性不会被共享
3. 子类构建实例时可以向父类传递参数

- 缺点

调用了两次父类的构造函数，第一次通过将父类的实例作为子类的原型，给子类的原型添加了父类构造函数的属性以及原型上的属性；第二次调用子类构造函数时又执行了父类的构造函数覆盖了子类原型中的同名参数，这种被覆盖的情况造成了性能上的浪费。

### 实例继承

为父类实例添加新特征，作为子类实例返回。

```js
function Animal(species) {
  this.species = species;
  this.color = ["black", "white"];
}

Animal.prototype.sayHello = function () {
  console.log(`Hello, my species is ${this.species}.`);
};

function Cat(name) {
  let animal = new Animal("Cat");
  animal.name = "Tom";
  return animal;
}

let cat1 = new Cat("Tom");
console.log(cat1.species); // Cat
console.log(cat1.name); // Tom
console.log(cat1.color); // ["black", "white"]
cat1.sayHello(); // Hello, my species is Cat.

let cat2 = new Cat("Jack");
cat2.color.push("blue");
console.log(cat2.color); // ["black", "white", "blue"]
console.log(cat1.color); // ["black", "white"]
```

- 优点

1. 不限制调用方式，不管是 new 子类()还是子类()，返回的对象具有相同的效果
2. 父类的引用属性不会被共享
3. 子类构建实例时可以向父类传递参数

- 缺点

1. 实例是父类的实例，不是子类的实例
2. 不支持多继承

### 拷贝继承

```js
function Animal(species) {
  this.species = species;
  this.color = ["black", "white"];
}

Animal.prototype.sayHello = function () {
  console.log(`Hello, my species is ${this.species}.`);
};

function Cat(name) {
  let f = new Animal("Cat");
  for (let k in f) {
    Cat.prototype[k] = f[k];
  }
  Cat.prototype.name = name;
}

let cat1 = new Cat("Tom");
console.log(cat1.species); // Cat
console.log(cat1.name); // Tom
console.log(cat1.color); // ["black", "white"]
cat1.sayHello(); // Hello, my species is Cat.

let cat2 = new Cat("Jack");
cat2.color.push("blue");
console.log(cat2.color); // ["black", "white", "blue"]
console.log(cat1.color); // ["black", "white", "blue"]
```

- 优点

支持多继承

- 缺点

1. 效率低，性能差，占用内存高（因为需要拷贝父类属性）
2. 使用的是浅拷贝的方式，父类的引用属性依然会被子类共享

### 寄生组合继承

```js
function Animal(species) {
  this.species = species;
  this.color = ["black", "white"];
}

Animal.prototype.sayHello = function () {
  console.log(`Hello, my species is ${this.species}.`);
};

function Cat(name) {
  Animal.call(this, "Cat");
  this.name = name;
}
Cat.prototype = Object.create(Animal.prototype);
// 修复构造函数指向（修改子类 prototype 时，会修改 constructor 的指向）
Cat.prototype.constructor = Cat;

let cat1 = new Cat("Tom");
cat1.sayHello(); // Hello, my species is Cat.
```

解决了组合继承会两次调用父类的构造函数造成浪费的缺点。是一种比较完美的继承方式。

### ES6 Class extends

```js
class Animal {
  constructor(species) {
    this.species = species;
    this.color = ["black", "white"];
  }
}

class Cat extends Animal {
  constructor(species, name) {
    super(species);
    this.name = name;
  }
}

let cat1 = new Cat("Cat", "Tom");
console.log(cat1.species); // Cat
console.log(cat1.name); // Tom
console.log(cat1.color); // ["black", "white"]

let cat2 = new Cat("Jack");
cat2.color.push("blue");
console.log(cat2.color); // ["black", "white", "blue"]
console.log(cat1.color); // ["black", "white"]
```

除了兼容性问题，完美。

## 非构造函数继承

### 原型式继承

利用一个空对象作为中介，将某个对象直接赋值给空对象构造函数的原型。其本质上是对参数对象的一个浅复制。

```js
function object(obj) {
  function F() {}
  F.prototype = obj;
  return new F();
}

let animal = {
  species: "Animal",
  color: ["black", "white"],
};
let cat1 = object(animal);
console.log(cat1.species); // Animal
```

ES5 还提供了 `Object.create()` 方法规范化了原型式继承，可以使用 `Object.create()` 方法替换 `object()` 方法：

```js
let animal = {
  species: "Animal",
  color: ["black", "white"],
};
let cat1 = Object.create(animal);
console.log(cat1.species); // Animal
```

优缺点与原型链继承相同。

### 拷贝继承

使用深拷贝的方式解决子类会共享父类引用属性的问题。

```js
let animal = {
  species: "Animal",
  color: ["black", "white"],
};

function deepCopy(p, c) {
  var c = c || {};
  for (var i in p) {
    if (typeof p[i] === "object") {
      c[i] = Array.isArray(p[i]) ? [] : {};
      deepCopy(p[i], c[i]);
    } else {
      c[i] = p[i];
    }
  }
  return c;
}

let cat1 = deepCopy(animal);
let cat2 = deepCopy(animal);
cat2.color.push("blue");
console.log(cat2.color); // ["black", "white", "blue"]
console.log(cat1.color); // ["black", "white"]
```
