---
title: 'TypeScript 之 Enum'
entitle: typescript-enum
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1564543428
comments: true
date: 2019-07-31 11:23:48
tags: TypeScript
keywords: Enum
description:
photos:
- /img/tags/typescript.jpg
---

TypeScript支持基于数字和字符串的枚举。

## 数字枚举

```ts
// Direction.Up的值为 0, 其余的成员会从 0 开始递增
enum Direction {
  Up,
  Down,
  Left,
  Right
}

// 从 1 开始递增
enum Direction {
  Up = 1,
  Down,
  Left,
  Right
}
```

### 枚举的使用

我们可以通过枚举的属性来访问枚举成员

```ts
enum Result {
  No,
  Yes
}

function respond(recipient: string, message: Result): void {
  // ...
}

respond("Princess Caroline", Result.Yes)
```

## 字符串枚举

相对于数字枚举而言，字符串枚举的可读性更强，但字符串枚举没有自增长的行为。

```ts
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT",
}
```

## 异构枚举（Heterogeneous enums）

从技术的角度来说，枚举可以混合字符串和数字成员，但是一般也不会这么做。

```ts
enum BooleanLikeHeterogeneousEnum {
  No = 0,
  Yes = "YES",
}
```

## 常量成员和计算成员

### 常量成员

枚举成员使用 常量枚举表达式 初始化。常数枚举表达式是 TypeScript 表达式的子集，它可以在编译阶段求值。 当一个表达式满足下面条件之一时，它就是一个常量枚举表达式：

- 一个枚举表达式字面量（主要是字符串字面量或数字字面量）
- 一个对之前定义的常量枚举成员的引用（可以是在不同的枚举类型中定义的）
- 带括号的常量枚举表达式
- 一元运算符 +, -, ~其中之一应用在了常量枚举表达式
- 常量枚举表达式做为二元运算符 +, -, *, /, %, <<, >>, >>>, &, |, ^的操作对象。 若常数枚举表达式求值后为 NaN 或 Infinity，则会在编译阶段报错。

### 计算成员

所有非常量成员情况的枚举成员被当作是计算成员。

```ts
enum FileAccess {
  // 常量成员
  None,
  Read = 1 << 1,
  Write = 1 << 2,
  ReadWrite = Read | Write,
  // 计算成员
  G = "123".length
}
```

## const 枚举

为了避免在额外生成的代码上的开销和额外的非直接的对枚举成员的访问，我们可以使用 const枚举。

```ts
const enum Directions {
  Up,
  Down,
  Left,
  Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]
```

常量枚举在编辑阶段会被删除，在使用的地方会被内联进来，之所以可以这么做是因为，常量枚举不允许包含计算成员。可以看到编译后的代码是这样的：

```ts
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

常量枚举内包含计算成员时，会报错：

```ts
const enum FileAccess {
  None,
  Read = 1 << 1,
  Write = 1 << 2,
  ReadWrite = Read | Write,
  G = "123".length // Error: const enum member initializers can only contain literal values and other computed enum values.
}
```

再看下非 const 枚举编译后的代码：

```ts
var Directions;
(function (Directions) {
    Directions[Directions["Up"] = 0] = "Up";
    Directions[Directions["Down"] = 1] = "Down";
    Directions[Directions["Left"] = 2] = "Left";
    Directions[Directions["Right"] = 3] = "Right";
})(Directions || (Directions = {}));
var directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```
