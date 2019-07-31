---
title: 'TypeScript 之 Generics'
entitle: typescript-generics
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1564540553
comments: true
date: 2019-07-31 10:35:53
tags: TypeScript
keywords: Generics
description: 
photos:
- /img/2019/typescript.jpg
---

## 泛型变量

我们可以在不知道函数参数类型的情况下，使用泛型来保证返回值的类型与传入参数的类型相同：

```ts
// 为函数添加类型变量 T。T 帮助我们捕获用户传入的类型（比如：number），之后我们再次使用了 T 当做返回值类型。
function identity<T>(arg: T): T {
  return arg;
}
```

我们使用泛型创建了泛型函数后，可以这样调用：

```ts
// 传入所有的参数，包括类型参数。明确的指定了 T 是 string 类型
let output = identity<string>("myString");
```

或者：

```ts
// 利用了类型推论，编译器会根据传入的参数自动地帮助我们确定 T 的类型
let output = identity("myString");
```

我们还可以把我们把泛型变量 T 当做类型的一部分使用：

```ts
function loggingIdentity<T>(arg: Array<T>): Array<T> {
  console.log(arg.length);  // Array has a .length
  return arg;
}
```

```ts
function loggingIdentity<T>(arg: T[]): T[] {
  console.log(arg.length);  // Array has a .length
  return arg;
}
```

## 泛型接口

我们可以使用接口来定义函数类型：

```ts
interface GenericIdentityFn {
  <T>(arg: T): T;
}

function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: GenericIdentityFn = identity;
```

我们还可以把泛型参数当作整个接口的一个参数，这样我们就能清楚的知道使用的具体是哪个泛型类型：

```ts
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;

myIdentity(123); // OK
myIdentity("123"); // Error: 类型“"123"”的参数不能赋给类型“number”的参数
```

## 泛型类

泛型类看上去与泛型接口差不多。 泛型类使用（ <>）括起泛型类型，跟在类名后面。

```ts
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) { return x + y; };
```

可以看到，类中只能使用同一种类型。

### 泛型约束

可以看到下面这个例子中：我们想访问 arg 的 length 属性，但是编译器并不能证明每种类型都有 length 属性，所以就报错了。

```ts
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // Error: T doesn't have .length
    return arg;
}
```

当我们想要限制函数去处理任意带有.length属性的所有类型。 我们需对传入的类型进行约束，要求传出的属性至少包含 length 这一属性。我们可以使用接口和 extends 关键字来实现约束：

```ts
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);  // OK
  return arg;
}

loggingIdentity({ length: 3, value: "123" }); // OK
```

### 使用类型参数进行约束

声明一个类型参数，且它被另一个类型参数所约束：

```ts
// 约束 K 是 T 的 key
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a"); // OK
getProperty(x, "m"); // Error: Argument of type 'm' isn't assignable to 'a' | 'b' | 'c' | 'd'.
```
