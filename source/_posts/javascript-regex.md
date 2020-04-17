---
title: "如何在 JavaScript 中使用正则表达式"
entitle: javascript-regex
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1587134839
comments: true
date: 2020-04-17 22:47:19
tags: JavaScript
keywords:
description:
photos:
  - /img/tags/javascript.jpg
---

之前我们已经了解了如何写正则表达式，今天就来具体了解一下正则表达式在 JavaScript 中的使用。

### 正则表达式的创建

在 JavaScript 中，正则表达式就是 `RegExp` 对象。有两种方法来创建正则表达式，一种是使用字面量创建，另一种是使用 `RegExp` 构造函数来创建了一个正则表达式对象。

```js
// 使用字面量创建正则表达式
var regex = /ab+c/i;
regex instanceof RegExp; // true

// 使用 `RegExp` 构造函数创建正则表达式
var regex = new RegExp("ab+c", "i");
var regex = new RegExp(/ab+c/, "i");
```

两种创建方式对于结果来说，没什么区别。所以一般我们都是语法采用更为简单的字面量创建方法。

### RegExp 方法

#### test()

`test()` 方法用来测试当前正则是否能匹配目标字符串。

直接举个栗子看一下：

```js
var regex = /ab+c/i;
regex.test("Abbc"); // true
```

有一点需要注意的是，如果正则表达式设置了全局搜索，`test()` 的执行会改变正则表达式 `lastIndex` 属性。连续的执行 `test()` 方法，后续的执行将会从 `lastIndex` 处开始匹配字符串，来看下栗子：

```js
var regex = /foo/g;

// regex.lastIndex is at 0
regex.test("foo"); // true

// regex.lastIndex is now at 3
regex.test("foo"); // false
```

#### exec()

`exec` 方法用来在目标字符串中执行一次正则匹配操作。返回一个结果数组或 null。

```js
var url = "https://www.google.com/";
var regex = /[^.]+/;
console.log(regex.exec(url)); // ["https://www", index: 0, input: "https://www.google.com/", groups: undefined]
```

和 `test()` 方法一样，当表达式设置了全局搜索时，`exec()` 的执行也会改变正则表达式 `lastIndex` 属性。使用此特性，`exec()` 可用来对单个字符串中的多次匹配结果进行逐条的遍历。还是直接看个栗子：

```js
var url = "https://www.google.com/";
var regex = /[^.]+/g;
var arr;

while ((arr = regex.exec(url)) !== null) {
  console.log(`Found ${arr[0]} Next starts at ${regex.lastIndex}.`);
}

// Found https://www Next starts at 11.
// Found google Next starts at 18.
// Found com/ Next starts at 23.
```

### 字符串相关正则方法

#### match()

在上一篇文章中，我们已经多次使用 `match()` 方法了，这里我们再说一下。

使用字符串的 `match()` 方法时，如果未使用 `g` 标志，则仅返回第一个完整匹配及其相关的结果数组。如果正则使用了 `g` 标志，则将返回与完整正则表达式匹配的所有结果。

#### search()

使用字符串的 `search()` 方法，会返回匹配到的第一个字符串起始位置的下标（无论是否使用了 `g` 标志）：

```js
var url = "https://www.google.com/";
var regex = /[^.]+/;
console.log(url.search(regex)); // ["https://www", index: 0, input: "https://www.google.com/", groups: undefined]
```

#### split()

我们经常使用 `split()` 方法来分割字符串，其实我们也可以传入一个正则表达式作为参数，来分割字符串：

```js
var classNameStr = `container user-info    flex-container`;
var regex = /\s+/;
var classNames = classNameStr.split(regex);
console.log(classNames); // ["container", "user-info", "flex-container"]
```

#### replace()

我们在开发中会使用 `replace()` 方法来替换字符串中子串，先来看看语法：

```js
str.replace(regexp|subStr, newSubStr|function)
```

接下里看个栗子，使用空字符串替换非数字和空格的字符：

```js
var str = "- -The car parked in the garage.@123";
var regex = /[^A-Za-z\s]/g;
str = str.replace(regex, "");
console.log(str); // The car parked in the garage
```

最后看一下 `replace()` 方法第二个参数为函数的情况。

先看一下该函数的参数：

| 变量名       | 代表值                                                                            |
| ------------ | --------------------------------------------------------------------------------- |
| `match`      | 匹配的子串                                                                        |
| `p1,p2, ...` | 假如 `replace()` 方法的第一个参数是一个 RegExp 对象，则代表第 n 个括号匹配的字符串。例如，如果是用 `/(\a+)(\b+)/` 这个来匹配，`p1` 就是匹配的 `\a+`，p2 就是匹配的 `\b+`。 |
| `offset`      | 匹配到的子字符串在原字符串中的偏量。                                                                 |
| `string`      | 被匹配的原字符串。                                                                 |

接下来看个栗子吧：

```js
var str = '<div>{{ name }}</div>'
var regex = /\{\{\s*(.+?)\s*\}\}/
str.replace(regex, (...args) => {
  console.log(args)
})

// ["{{ name }}", "name", 5, "<div>{{ name }}</div>"]
```

可以看到 `args` 中第以个元素 `{{ name }}` 是匹配到的子串，`args` 中第二个元素 `name` 就是 `(.+?)` 匹配到的内容。

接下来我们我们可以简单模拟一下 Vue 中的模板解析功能：

```js
var data = {
  name: 'haohaio'
};
var str = '<div>{{ name }}</div>';
var regex = /\{\{\s?(.+?)\s?\}\}/
str = str.replace(regex, (...args) => {
  return data[args[1]]
});
console.log(str); // <div>haohaio</div>
```

`replace()` 方法参数中的正则如果使用了 `g` 标志，那么函数将会多次调用：

```js
var data = {
  name: 'haohaio',
  age: 28
};
var str = '<div>{{ name }} - {{ age }}</div>';
var regex = /\{\{\s*(.+?)\s*\}\}/g;
str = str.replace(regex, (...args) => {
  return data[args[1]]
});
console.log(str); // <div>haohaio - 28</div>
```

### 小结

正则表达式在我们的开发过程中，还是很重要的。本篇文章介绍的 JavaScript 中的一些常用方法，一定要掌握哦 ~~
