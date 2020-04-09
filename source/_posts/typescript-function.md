---
title: 'TypeScript 之 Function'
entitle: typescript-function
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1564109126
comments: true
date: 2019-07-26 10:45:26
tags: TypeScript
keywords: TypeScript
description:
photos:
- /img/tags/typescript.jpg
---

### 函数类型

和 JavaScript 中的函数一样，我们可以用函数声明和函数表达式两种方式来定义函数。在 TypeScript 中，我们可以为函数定义类型（参数类型和返回值类型）。

```ts
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x + y; };
```

在使用函数表达式来定义函数时，我们还可以写出函数的完整类型：

```ts
let myAdd: (x: number, y: number) => number = function (x: number, y: number): number { return x + y; };
```

### 推断类型

如果你在赋值语句的一边指定了类型但是另一边没有类型的话， TypeScript 编译器会自动识别出类型：

```ts
// The parameters x and y have the type number
let myAdd: (baseValue: number, increment: number) => number =
  function (x, y) { return x + y; };
```

这叫做“按上下文归类”，是类型推论的一种。 它帮助我们更好地为程序指定类型。

### 函数参数

在正常情况下，TypeScript 里的每个函数参数都是必须的。 这不是指不能传递  null 或 undefined 作为参数，而是说编译器检查用户是否为每个参数都传入了值。 
```ts
function buildName(firstName: string, lastName: string) {
  return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // Error: An argument for 'lastName' was not provided.
let result2 = buildName("Bob", "Adams", "Sr.");  // Error: 应有 2 个参数，但获得 3 个。
let result3 = buildName("Bob", "Adams");         // OK
```

### 可选参数

JavaScript 里，每个参数都是可选的，可传可不传。 没传参的时候，它的值就是 undefined。 在 TypeScript 里我们可以在参数名旁使用 `?` 实现可选参数的功能。

```ts
function buildName(firstName: string, lastName?: string) {
  if (lastName) {
    return firstName + " " + lastName;
  } else {
    return firstName;
  }
}

let result1 = buildName("Bob");  // OK
let result2 = buildName("Bob", "Adams", "Sr.");  // Error: 应有 1-2 个参数，但获得 3 个。
let result3 = buildName("Bob", "Adams");  // OK
```

> 可选参数必须跟在必须参数后面。 如果上例我们只想让 firstName 是可选的，那么就必须调整它们的位置，把 firstName 放在后面。

### 默认参数

在 TypeScript 里，我们也可以为参数提供一个默认值，当用户没有传递这个参数或传递的值是 undefined 时，这个参数的值就为默认值。它们叫做有默认初始化值的参数。 

```ts
function buildName(firstName: string, lastName = "Smith") {
  return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // OK, returns "Bob Smith"
let result2 = buildName("Bob", undefined);       // OK, also returns "Bob Smith"
let result3 = buildName("Bob", "Adams", "Sr.");  // Error: 应有 1-2 个参数，但获得 3 个。
let result4 = buildName("Bob", "Adams");         // OK
```

### 剩余参数

在JavaScript里，我们可以使用 arguments来访问所有传入的参数。在TypeScript里，当我们想同时操作多个参数，或者并不知道会有多少参数传递进来时，我们可以把所有参数收集到一个变量里：

```ts
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```

剩余参数会被当做个数不限的可选参数，可以一个都没有，同样也可以有任意个。

### this

在 JavaScript 里，this 的值在函数被调用的时候才会指定。在 TypeScript 中也一样。具体可参考 [JavaScript 之 This
](https://nook.coding.me/front-end/javascript-this-1564103929.html)

> 关于 --noImplicitThis，等之后涉及了在后续补充。

### 重载

如下所示：重载的 pickCard 函数在调用的时候会进行正确的类型检查。查找重载列表时，JavaScript里的处理流程一样，是从上到下查找的，因此，在定义重载的时候，一定要把最精确的定义放在最前面。

> function pickCard(x): any 并不是重载列表的一部分，因此这里只有两个重载：一个是接收对象另一个接收数字。 以其它参数调用 pickCard会产生错误。

```ts
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: { suit: string; card: number; }[]): number;
function pickCard(x: number): { suit: string; card: number; };
function pickCard(x): any {
  // Check to see if we're working with an object/array
  // if so, they gave us the deck and we'll pick the card
  if (typeof x == "object") {
    let pickedCard = Math.floor(Math.random() * x.length);
    return pickedCard;
  }
  // Otherwise just let them pick the card
  else if (typeof x == "number") {
    let pickedSuit = Math.floor(x / 13);
    return { suit: suits[pickedSuit], card: x % 13 };
  }
}

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);

let pickedCard3 = pickCard("123"); // Error: 类型“"123"”的参数不能赋给类型“number”的参数。
```
