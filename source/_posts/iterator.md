---
title: "ES6 之 Iterator"
entitle: javascript-iterator
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1586933228
comments: true
date: 2020-04-15 14:47:08
tags: JavaScript
keywords:
description:
photos:
  - /img/tags/javascript.jpg
---

ES6 新增了一个数据类型：`Symbol`，我们可以用它定义一个独一无二的值。此外，ES6 还提供了 11 个内置的 Symbol 值，其中有一个值 `Symbol.iterator`， 它可以作为对象的属性来使用，我们可以在对象该属性上添加一个迭代器函数，对象就可以完成遍历操作了，今天我们就来详细了解一下。

先来简单看一下代码：

```js
const obj = {
  data: ["hello", "world"],
  [Symbol.iterator]: function () {
    const self = this;
    let index = 0;
    return {
      next: function () {
        if (index < self.data.length) {
          return {
            value: self.data[index++],
            done: false,
          };
        } else {
          return { value: undefined, done: true };
        }
      },
    };
  },
};
```

上面代码中，对象 `obj` 具有 `Symbol.iterator` 属性, 该属性对应的值，是一个函数，执行这个函数就会返回一个迭代器对象，该对象的根本特征就是具有 `next` 方法，每次调用 `next` 方法，都会返回返回一个对象，表示当前数据成员的信息。这个对象具有 `value` 和 `done` 两个属性，`value` 属性返回当前位置的成员，`done` 属性是一个布尔值，表示遍历是否结束，即是否还有必要再一次调用 `next` 方法。

ES6 新增了 `for...of` 方法来进行遍历，Iterator 主要就是为 `for...of` 服务的，我们可以用 `for...of` 遍历一下上面我们的定义的 `obj` 对象看一下：

```js
for (let item of obj) {
  console.log(item);
}
// hello
// world
```

可以看到我们给 `obj` 对象定义了 `Symbol.iterator` 属性，并实现了迭代器函数后，它就可以进行遍历了。

> `done: false` 和 `value: undefined` 属性都是可以省略的。

### 默认 Iterator 接口

Iterator 可以说是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作。

ES6 规定，默认的 Iterator 接口部署在数据结构的 `Symbol.iterator` 属性，或者说，一个数据结构只要具有 `Symbol.iterator` 属性，就可以认为是“可遍历的”。

ES6 的一些数据结构原生具备了 Iterator 接口：`Array`、`Map`、`Set`、`String`、`TypedArray`、`函数的 arguments 对象`、`NodeList 对象`。

我们可以看一下数组的 `Symbol.iterator` 属性

```js
let arr = ["a", "b", "c"];
let iter = arr[Symbol.iterator]();

iter.next(); // { value: 'a', done: false }
iter.next(); // { value: 'b', done: false }
iter.next(); // { value: 'c', done: false }
iter.next(); // { value: undefined, done: true }
```

基于此，我们可以给类似数组的对象的 `Symbol.iterator` 属性，赋值为数组的 `Symbol.iterator`，来让其可以遍历：

```js
let iterable = {
  0: "a",
  1: "b",
  2: "c",
  length: 3,
  [Symbol.iterator]: Array.prototype[Symbol.iterator],
};
for (let item of iterable) {
  console.log(item);
}
// a
// b
// c
```

但是，为普通对象部署数组的 `Symbol.iterator` 方法，并无效果：

```js
let iterable = {
  a: "a",
  b: "b",
  c: "c",
  length: 3,
  [Symbol.iterator]: Array.prototype[Symbol.iterator],
};
for (let item of iterable) {
  console.log(item);
}
// undefined
// undefined
// undefined
```

### 调用 Iterator 接口的场合

#### 解构赋值

```js
let set = new Set(["a", "b", "c"]);
let [x, y] = set; // // x='a'; y='b'
let [first, ...rest] = set; // first='a'; rest=['b','c'];
```

### 扩展运算符

```js
let str = "hello";

[...str]; // ["h", "e", "l", "l", "o"]

let arr = ["b", "c"];
["a", ...arr, "d"]; // ['a', 'b', 'c', 'd']
```

#### yield\*

Generator 函数中的 `yield*` 后面跟的是一个可遍历的结构，它会调用该结构的迭代器接口。

```js
let generator = function* () {
  yield 1;
  yield* [2, 3];
  yield 4;
};

let iterator = generator();
iterator.next(); // { value: 1, done: false }
iterator.next(); // { value: 2, done: false }
iterator.next(); // { value: 3, done: false }
iterator.next(); // { value: 4, done: false }
iterator.next(); // { value: undefined, done: true }
```

#### 其他场合

由于数组的遍历会调用迭代器接口，所以任何接受数组作为参数的场合，其实都调用了迭代器接口。比如：`for...of`、`Array.from()`、`Map()`、`Set()`、`Promise.all()`、`Promise.race()` 等。

#### 迭代器对象的 return()，throw()

迭代器对象除了具有 `next` 方法，还可以具有 `return` 方法和 `throw` 方法。我们自己写迭代器对象生成函数时，那么 `next` 方法是必须部署的， `return` 方法和 `throw` 方法是否部署是可选的。

`return` 方法的使用场合是，如果 `for...of` 循环提前退出（通常是因为出错，或者有 `break` 语句），就会调用 `return` 方法。

```js
function readLinesSync(file) {
  return {
    [Symbol.iterator]() {
      return {
        next() {
          return { done: false };
        },
        return() {
          file.close();
          return { done: true };
        },
      };
    },
  };
}

// 情况一
for (let line of readLinesSync(fileName)) {
  console.log(line);
  break;
}

// 情况二
for (let line of readLinesSync(fileName)) {
  console.log(line);
  throw new Error();
}
```

`throw` 方法主要是配合 Generator 函数使用。

### for...of 循环

ES6 新增了 `for...of` 方法来进行遍历，`for...of` 循环内部调用的是数据结构的 `Symbol.iterator` 方法。

#### 数组

数组原生具备 `iterator` 接口（即默认部署了 `Symbol.iterator` 属性），for...of 循环本质上就是调用这个接口产生的迭代器，可以用下面的代码证明：

```js
const arr = ["red", "green", "blue"];

for (let v of arr) {
  console.log(v); // red green blue
}

const obj = {};
obj[Symbol.iterator] = arr[Symbol.iterator].bind(arr);

for (let v of obj) {
  console.log(v); // red green blue
}
```

上面代码中，空对象 `obj` 部署了数组 `arr` 的 `Symbol.iterator` 属性，结果 `obj` 的 `for...of` 循环，产生了与 `arr` 完全一样的结果。

#### for...in & for...of

`for...in` 循环读取键名，`for...of` 循环读取键值。

```js
const arr = ["a", "b", "c", "d"];

for (let a in arr) {
  console.log(a); // 0 1 2 3
}

for (let a of arr) {
  console.log(a); // a b c d
}
```

如果要通过 `for...of` 循环，获取数组的索引，可以借助数组实例的 `entries` 方法和 `keys` 方法

```js
const arr = ["a", "b"];

for (let index of arr.keys()) {
  console.log(index);
}
// 0
// 1

for (let [index, elem] of arr.entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```

#### Set 和 Map

`Set` 和 `Map` 结构也原生具有 `Iterator` 接口，可以直接使用`for...of`循环。

```js
var engines = new Set(["Gecko", "Trident", "Webkit", "Webkit"]);
for (var e of engines) {
  console.log(e);
}
// Gecko
// Trident
// Webkit

var es6 = new Map();
es6.set("edition", 6);
es6.set("committee", "TC39");
es6.set("standard", "ECMA-262");
for (var [name, value] of es6) {
  console.log(name + ": " + value);
}
// edition: 6
// committee: TC39
// standard: ECMA-262
```

可以看到，`Set` 结构遍历时，返回的是一个值，`Map` 结构遍历时，返回的是一个数组。

#### 计算生成的数据结构

ES6 的数组、Set、Map 都部署了 `keys`、`values` 和 `entries` 方法，此外对象可以使用 `Object.keys()`、`Object.values()` 和 `Object.entries()` 来进行遍历。

```js
let arr = ["a", "b", "c"];
for (let pair of arr.entries()) {
  console.log(pair);
}
// [0, 'a']
// [1, 'b']
// [2, 'c']

let obj = {
  a: "hello",
  b: "world",
};

for (let [key, value] of Object.entries(obj)) {
  console.log(key + ": " + value);
}
// a: hello
// b: world
```

#### 类似数组的对象

类似数组的对象包括好几类。下面是 `for...of` 循环用于字符串、DOM NodeList 对象、`arguments` 对象的例子。

```js
// 字符串
let str = "hello";

for (let s of str) {
  console.log(s); // h e l l o
}

// DOM NodeList对象
let paras = document.querySelectorAll("p");

for (let p of paras) {
  p.classList.add("test");
}

// arguments对象
function printArgs() {
  for (let x of arguments) {
    console.log(x);
  }
}
printArgs("a", "b");
// 'a'
// 'b'
```

并不是所有类似数组的对象都具有 `Iterator` 接口，一个简便的解决方法，就是使用 `Array.from` 方法将其转为数组。

```js
let arrayLike = { length: 2, 0: "a", 1: "b" };

// 报错
for (let x of arrayLike) {
  console.log(x);
}

// 正确
for (let x of Array.from(arrayLike)) {
  console.log(x);
}
```

#### Iterator & Generator

`Symbol.iterator` 方法的最简单实现就是使用 Generator 函数：

```js
let myIterable = {
  [Symbol.iterator]: function* () {
    yield 1;
    yield 2;
    yield 3;
  },
};
[...myIterable]; // [1, 2, 3]

// 或者采用下面的简洁写法

let obj = {
  *[Symbol.iterator]() {
    yield "hello";
    yield "world";
  },
};

for (let x of obj) {
  console.log(x);
}
// "hello"
// "world"
```

> 我们可以对 Generator 函数这样简单理解：其是一个分段执行的函数，`yield` 表达式是暂停执行的标记，而 `next` 方法可以恢复执行。`return` 表达式表示结束执行，如果没有 `return` 语句，就执行到函数结束。

执行 Generator 函数就会返回一个迭代器对象。我们可以通过代码再详细看一下：

```js
function* helloWorldGenerator() {
  yield "hello";
  yield "world";
  return "ending";
}

var hw = helloWorldGenerator();
hw.next(); // { value: 'hello', done: false }
hw.next(); // { value: 'world', done: false }
hw.next(); // { value: 'ending', done: true }
hw.next(); // { value: undefined, done: true }
```

此外，还有一个有趣的地方可以了解一下，Generator 函数执行后，返回一个迭代器对象。该对象本身也具有 `Symbol.iterator` 属性，执行后返回自身。

```js
function* gen() {
  yield "hello";
  yield "world";
}

let g = gen();
g[Symbol.iterator]() === g; // true
```

所以，Generator 函数执行后，返回的迭代器对象，是可以直接遍历的：

```js
function* foo() {
  yield 1;
  yield 2;
  yield 3;
  return 4;
  yield 5;
  yield 6;
}

for (let v of foo()) {
  console.log(v); // 1 2 3
}
```

ES6 还提供了 `yield*` 表达式，在之前提到会调用迭代器接口的场景时，就提到了这个表达式。起作用就是用来在一个 Generator 函数里面执行另一个 Generator 函数。

```js
function* foo() {
  yield "a";
  yield "b";
}

function* bar() {
  yield "x";
  yield* foo();
  yield "y";
}

// 等同于
function* bar() {
  yield "x";
  yield "a";
  yield "b";
  yield "y";
}
```

### 参考

[ECMAScript 6 入门 - Iterator 和 for...of 循环](https://es6.ruanyifeng.com/#docs/iterator)
