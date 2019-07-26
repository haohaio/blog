---
title: 'JavaScript 之 this'
entitle: javascript-this
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1564103929
comments: true
date: 2019-07-26 09:18:49
tags: JavaScript 之 this
keywords: JavaScript
description: JavaScript
photos:
- /img/2019/javascript.jpg
---

JavaScript 的 this 总是指向一个对象，而具体指向那个对象是在运行时基于函数的执行环境动态绑定的，而非函数被声明时的环境。

this 的指向大致可以分为以下4种：

### 作为对象的方法调用

当函数作为对象的方法被调用时，this 指向该对象

```javascript
var obj = {
    a: 1,
    getA: function() {
        console.log(this === obj); // 输出：true
        console.log(this.a); // 输出：1
    }
}
obj.getA();
```

### 作为普通函数调用

当函数不作为对象的属性被调用时，即普通函数方式，this 总是指向全局对象。在浏览器的 JavaScript 里，这个全局对象是 window 对象。

```javascript
window.name = 'globalName';
var getName = function() {
    return this.name;
}
console.log(getName()); // 输出：globalName
```

### 构造器调用

通常情况下，构造器里的 this 就指向返回的这个对象。但当 new 调用构造器时，如果构造器显式地返回了一个object类型的对象，那么运行结果最终会返回这个对象，而不是我们之前期待的this。

```javascript
var myClass = function() {
    this.name = 'sven';
    return {
        name: 'anne'
    }
}

var obj = new myClass();
console.log(obj.name); // 输出：anne
```

### Function.prototype.call 或 Function.prototype.apply 调用

```javascript
var obj1 = {
    name: 'sven',
    getName: function() {
        return this.name;
    }
}
var obj2 = {
    name: 'anne'
}
console.log(obj1.getName()); // 输出：sven
console.log(obj1.getName.call(obj2)); // 输出：anne
```

### 丢失的 this

```javascript
window.name = 'globalName';
var myObject = {
    name: 'sven',
    getName: function() {
        return this.name;
    }
}

// 对象方法调用
console.log(myObject.getName()); // 输出：sven
// 定义了一个 getName 的函数，此时为普通函数调用，this 指向了 window
var getName = myObject.getName;
console.log(getName()); // 输出：globalName
```
