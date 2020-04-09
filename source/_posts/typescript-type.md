---
title: 'TypeScript 之 类型'
entitle: typescript-type
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1563952168
comments: true
date: 2019-07-24 15:09:28
tags: TypeScript
keywords: TypeScript
description:
photos:
- /img/tags/typescript.jpg
---

## 基础类型

### boolean

```ts
let isDone: boolean = false;
```

### number

```ts
let count: number = 6;
```

### string

```ts
let name: string = "bob";
```

### 数组

有两种方式可以定义数组

```ts
// 在元素类型后面接上 []
let list: number[] = [1, 2, 3];
// 使用数组泛型，Array<元素类型>
let list: Array<number> = [1, 2, 3];
```

### Tuple（元组）

表示一个已知元素数量和类型的数组，各元素的类型不必相同

```ts
// Declare a tuple type
let x: [string, number];
x = ['hello', 10]; // OK
x = [10, 'hello']; // Error

// 当访问一个已知索引的元素，会得到正确的类型：
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'

// 当访问一个越界的元素，会使用联合类型替代：
x[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型
console.log(x[5].toString()); // OK, 'string' 和 'number' 都有 toString
x[6] = true; // Error, 布尔不是(string | number)类型
```

### enum

```ts
enum Color {Red, Green, Blue}

// 默认情况下，从0开始为元素编号，也可以手动的指定成员的数值。
enum Color {Red = 1, Green, Blue}

// 或者全部手动指定成员的数值
enum Color {Red = 1, Green = 2, Blue = 4}

// 我们可以由枚举值得到它的名字
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2];
console.log(colorName);  // 显示'Green'因为上面代码里它的值是2
```

### any

```ts
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false;

let list: any[] = [1, true, "free"];
```

### void

表示没有任何类型。

```ts
// 当一个函数没有返回值时，其返回值类型是 void。
function warnUser(): void {
    console.log("This is my warning message");
}

// 声明一个void类型的变量没有什么大用，因为你只能为它赋予undefined和null
let unusable: void = undefined;
```

### undefined 和 null

默认情况下 null 和 undefined 是所有类型的子类型，即可以将它们赋值为其它的类型。其本身的类型用处不是很大。

```ts
let u: undefined = undefined;
let n: null = null;

// 但当指定了--strictNullChecks 标记，null 和 undefined 只能赋值给 void 和它们各自
let count: number = undefined;
```

### never

表示的是那些永不存在的值的类型。

```ts
// 返回 never 的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为 never
function fail() {
    return error("Something failed");
}

// 返回 never 的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```

### object

表示非原始类型，也就是除number，string，boolean，symbol，null或undefined之外的类型。

```ts
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error
```

## 高级类型

### 交叉类型

交叉类型是将多个类型合并为一个类型：

```ts
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U>{};
    for (let id in first) {
        (<any>result)[id] = (<any>first)[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            (<any>result)[id] = (<any>second)[id];
        }
    }
    return result;
}

class Person {
    constructor(public name: string) { }
}
interface Loggable {
    log(): void;
}
class ConsoleLogger implements Loggable {
    log() {
        // ...
    }
}
var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();
```

### 联合类型（Union Types）

```ts
function padLeft(value: string, padding: string | number) {
  // ...
}

let indentedString = padLeft("Hello world", true); // Error: 类型“true”的参数不能赋给类型“string | number”的参数
```

## 类型断言

类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你已经进行了必须的检查。

类型断言有两种形式：

```ts
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;

// 在JSX中，只能使用 as 语法断言。
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;

let someValue: number = 123;
let strLength: number = (someValue as string).length; // Error: Conversion of type 'number' to type 'string' may be a mistake

let someValue: any = 123;
let strLength: number = (someValue as string).length; // OK

let someValue: any = [1, 2, 3];
let strLength: number = (someValue as string).length; // OK
```

## 类型推论

TypeScript 里，在有些没有明确指出类型的地方，类型推论会帮助提供类型。

### 最佳通用类型

最终的通用类型取自候选类型，计算通用类型算法会考虑所有的候选类型，并给出一个兼容所有候选类型的类型。

```ts
let x = 3; // x: number
let x = [0, 1, null]; // x: number[]
let x = [0, 1, "hello"]; // x: (string | number)[]
```

当候选类型共享相同的通用类型，但类型推论并不能帮我们推论出这个通用类型，此时我们可以手动指出这个通用类型

```ts
// Rhino、Elephant、Snake 均继承自 Animal
let zoo = [new Rhino(), new Elephant(), new Snake()]; // (Rhino | Elephant | Snake)[]

// 指出通用类型
let zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()];

```

### 上下文类型

TypeScript类型推论也可能按照相反的方向进行。 这被叫做“按上下文归类”:

```ts
// mouseEvent 会被推断为 MouseEvent 类型
window.onmousedown = function (mouseEvent) {
  console.log(mouseEvent.number);  // Error: 类型 MouseEvent 上不存在属性 number
};
```

```ts
// 当上下文类型表达式包含了明确的类型信息，上下文的类型被忽略
window.onmousedown = function (mouseEvent: any) {
  console.log(mouseEvent.number);  // OK
};
```
