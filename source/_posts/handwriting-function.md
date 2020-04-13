---
title: "常见手写函数实现"
entitle: handwriting-function
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1586739427
comments: true
date: 2020-04-13 08:57:07
tags: JavaScript
keywords:
description:
photos:
  - /img/tags/javascript.jpg
---

### apply

```js
Function.prototype._apply = function (context = window, args = []) {
  const fnKey = "tmp_" + Date.now();
  context[fnKey] = this;
  const res = context[fnKey](...args);
  Reflect.deleteProperty(context, fnKey);
  return res;
};
```

### call

```js
Function.prototype._call = function (context = window, ...args) {
  const fnKey = "tmp_" + Date.now();
  context[fnKey] = this;
  const res = context[fnKey](...args);
  Reflect.deleteProperty(context, fnKey);
  return res;
};
```

### bind

```js
Function.prototype._bind = function (context = window, ...args) {
  const self = this;

  return function () {
    return self.apply(context, args);
  };
};
```

### new

```js
function _new(fn, ...args) {
  let obj = {};
  obj.__proto__ = fn.prototype;
  let ret = fn.apply(obj, args);
  return ret instanceof Object ? ret : obj;
}
```

### debounce

```js
function debounce(fn) {
  let timeout = null;

  return function () {
    clearTimeout(timeout);

    timeout = setTimeout(() => {
      fn.apply(this, arguments);
    }, 1000);
  };
}
```

### throttle

```js
function throttle(fn) {
  let canRun = true;

  return function () {
    if (!canRun) return;

    canRun = false;
    setTimeout(() => {
      fn.apply(this, arguments);
      canRun = true;
    }, 1000);
  };
}
```

### 数值千分位

```js
let num = 2333333333333;
num.toLocaleString(); // "2,333,333,333,333"
```

```js
let num = 2333333333333;
num
  .toString()
  .split("")
  .reverse()
  .reduce((acc, cur, idx) => {
    return (idx % 3 ? cur : cur + ",") + acc;
  }); // "2,333,333,333,333"
```

### url 参数获取

```js
function getQuery() {
  let searchParams = new URLSearchParams(location.search);
  let query = Object.fromEntries(searchParams);

  return query;
}
```

```js
function getQuery() {
  const searchStr = location.search;
  if (!searchStr.startsWith("?")) return null;

  let query = {};
  searchStr
    .substring(1)
    .split("&")
    .forEach((item) => {
      let queryItem = item.split("=");
      query[queryItem[0]] = queryItem[1];
    });

  return query;
}
```
