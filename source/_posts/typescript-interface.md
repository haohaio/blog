---
title: 'TypeScript 之 Interface'
entitle: typescript-interface
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1564022051
comments: true
date: 2019-07-25 10:34:11
tags: TypeScript
keywords: TypeScript
description: TypeScript
photos:
- /img/tags/typescript.jpg
---

TypeScript 的核心原则之一是对值所具有的结构进行类型检查。 在 TypeScript 里，接口的作用就是为这些类型命名和以及定义校验规则。

```ts
// 使用接口来描述一个拥有 firstName 和 lastName 字段的对象 (firstName 和 lastName 两个字段是必须的)
interface Person {
  firstName: string;
  lastName: string;
}

function greeter(person: Person) {
  return "Hello, " + person.firstName + " " + person.lastName;
}

// 只要保证包含了接口要求的结构就可以（还可有其它字段），顺序也没有要求，也不必明确地使用 implements 语句
let user = { firstName: "Jane", lastName: "User" };

document.body.innerHTML = greeter(user);
```

### 可选属性

接口里的属性不全都是必需的。

> 当使用了可选属性时，不能引用接口内不存在的属性

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
  let newSquare = { color: "white", area: 100 };
  
  if (config.clor) {
    // Error：属性“clor”在类型“SquareConfig”上不存在
    newSquare.color = config.clor;
  }
  
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({ color: "black" });

// Error：类型“{ color: string; type: string; }”的参数不能赋给类型“SquareConfig”的参数
let mySquare = createSquare({ color: "black" });
```

### 只读属性

```ts
interface Point {
    readonly x: number;
    readonly y: number;
}

let p1: Point = { x: 10, y: 20 };
p1.x = 5; // Error
```

TypeScript 具有 `ReadonlyArray<T>` 类型，它与 `Array<T>` 相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改

```ts
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12; // Error: 类型“readonly number[]”中的索引签名仅允许读取
ro.push(5); // Error: 类型“readonly number[]”上不存在属性“push”
ro.length = 100; // Error: Cannot assign to 'length' because it is a read-only property.
a = ro; // Error: The type 'readonly number[]' is 'readonly' and cannot be assigned to the mutable type
```

### 额外的属性检查

先来看个栗子：

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
  let newSquare = { color: "white", area: 100 };
  
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

// Error：类型“{ color: string; type: string; }”的参数不能赋给类型“SquareConfig”的参数
let mySquare = createSquare({ color: "black", type: "simple" });
```

可以看到，传入 createSquare 的参数内，有 color 和 type 两个属性，type 属性不在 SquareConfig 定义的范围内，此时 TypeScript 会认为这段代码可能存在bug。

这是因为对象字面量会被特殊对待而且会经过额外属性检查，当将它们赋值给变量或作为参数传递的时候。 如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误。

我们可以通过类型断言来绕开检查：

```ts
let mySquare = createSquare({ color: "black", type: "simple" } as SquareConfig);
```

然而，最佳的方式是能够添加一个字符串索引签名（前提是我们能够确定这个对象可能具有某些做为特殊用途使用的额外属性） ：

```ts
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

还有一种方式可绕开这些检查：

```ts
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```

看上去有些奇怪，我们只是将这个对象赋值给一个另一个变，但由于变量不会经过额外属性检查，所以编译器不会报错。

> 但我们可能不应该绕开这些检查，而是去审查一下接口，看是否需要对其进行修改。

### 函数类型

我们还可以使用接口来描述函数类型。

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string): boolean {
  let result = source.search(subString);
  return result > -1;
}
```

对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配。参数类型和返回值类型也可以省略，TypeScript的类型系统会自行推断。

```ts
let mySearch: SearchFunc;
mySearch = function (src, sub) {
  let result = src.search(sub);
  return result > -1;
}
```

### 可索引的类型

我们还可以用接口描述那些能够“通过索引得到”的类型，比如a[10]或ageMap["daniel"]。

```ts
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```

> TypeScript支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值同类型或其子类型。 这是因为当使用 number来索引时，JavaScript会将它转换成string然后再去索引对象。 也就是说用 100（一个number）去索引等同于使用"100"（一个string）去索引，因此两者需要保持一致。

```ts
class Animal {
  name: string;
}
class Dog extends Animal {
  breed: string;
}

// Error：数字索引类型“Animal”不能赋给字符串索引类型“Dog”。
interface NotOkay {
  [x: number]: Animal;
  [x: string]: Dog;
}

// OK
interface NotOkay {
  [x: number]: Dog;
  [x: string]: Animal;
}
```

### 类静态部分与实例部分的区别

类是具有两个类型的：静态部分的类型和实例的类型。当一个类实现了一个接口时，只对其实例部分进行类型检查。 constructor存在于类的静态部分，所以不在检查的范围内。所以我们不能直接在接口内定义构造器方法，然后让类去实现。

```ts
interface ClockConstructor {
  new(hour: number, minute: number);
}

// Error
class Clock implements ClockConstructor {
  currentTime: Date;
  constructor(h: number, m: number) { }
}
```

### 类类型

和 Java 里接口的基本作用一样，TypeScript 也能明确的强制一个类去符合某种契约。

```ts
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date);
}

class Clock implements ClockInterface {
  currentTime: Date;
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) { }
}
```

### 继承接口

和类一样，接口也可以相互继承。

```ts
interface Shape {
  color: string;
}

interface PenStroke {
  penWidth: number;
}

interface Square extends Shape, PenStroke {
  sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

### 混合类型

直接看个栗子：一个对象可以同时做为函数和对象使用，并带有额外的属性

```ts
interface Counter {
  (start: number): string;
  interval: number;
  reset(): void;
}

function getCounter(): Counter {
  let counter = <Counter>function (start: number) { };
  counter.interval = 123;
  counter.reset = function () { };
  return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

### 接口继承类

当接口继承了一个类类型时，它会继承类的成员但不包括其实现。 接口同样会继承到类的private和protected成员。

```ts
class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control implements SelectableControl {
    select() { }
}

class TextBox extends Control {
    select() { }
}

// 错误：“Image”类型缺少“state”属性。
class Image implements SelectableControl {
    select() { }
}
```
