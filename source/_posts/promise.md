---
title: "Promise 简单实现"
entitle: promise
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1586495644
comments: true
date: 2020-04-10 13:14:04
tags: JavaScript
keywords:
description:
photos:
  - /img/tags/javascript.jpg
---

> Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6 将其写进了语言标准，统一了用法，原生提供了 Promise 对象。

### 基本结构

话不多说，我们先实现 Promise 的基本结构，我们需要注意以下几点：

- 构造函数 Promise 接受一个函数作为参数，该函数包含 resolve 和 reject 两个参数，它们是两个函数。
- Promise 对象有三种状态：Pending(进行中)、Fulfilled(已成功)、Rejected(已失败)。状态只能由 Pending 变为 Fulfilled 或由 Pending 变为 Rejected ，且状态改变之后不会在发生变化，会一直保持这个状态。
- resolve 方法将 Promise 对象的状态从 Pending 变为 Fulfilled。
- reject 方法将 Promise 对象的状态从 Pending 变为 Rejected。
- resolve 和 reject 都可以传入任意类型的值作为实参，表示 Promise 对象成功（Fulfilled）和失败（Rejected）的值

基于以上几点，我们实现代码如下：

```js
const isFunction = (variable) => typeof variable === "function";
// 定义Promise的三种状态常量
const PENDING = Symbol("PENDING");
const FULFILLED = Symbol("FULFILLED");
const REJECTED = Symbol("REJECTED");

class MyPromise {
  constructor(handle) {
    if (!isFunction(handle)) {
      throw new Error("MyPromise must accept a function as a parameter");
    }

    this._status = PENDING;
    this._value = undefined;

    // 执行handle
    try {
      handle(this._resolve.bind(this), this._reject.bind(this));
    } catch (err) {
      this._reject(err);
    }
  }
  // 添加 resolve 时执行的函数
  _resolve(val) {
    if (this._status !== PENDING) return;
    this._status = FULFILLED;
    this._value = val;
  }
  // 添加reject时执行的函数
  _reject(err) {
    if (this._status !== PENDING) return;
    this._status = REJECTED;
    this._value = err;
  }
}
```

### Promise 的 then 方法

Promise 的核心就是 then 方法。接下来，基于以下几点我们来实现一下 then 函数

- Promise 对象的 then 方法接收两个参数：onFulfilled（状态变为成功后的回调函数）、onRejected（状态变为失败后的回调函数），两个参数都是可选的。当状态为 Pending 时，会等待状态变化后再执行相应回调函数，当状态已经变化时，会立即执行相应回调函数。
- then 方法可以被调用多次，当状态变化后，会按照 then 的调用顺序，依次执行其相应回调。
- then 方法会返回一个新的 Promise 对象，以支持链式调用。
- then 方法的回调函数参数的返回值时 Promise 对象时，需要等它的状态变化后，再去执行下一个 then 的回调函数；返回值不是 Promise 对象时，直接执行下一个 then 的回调函数。

```js
class MyPromise {
  constructor(handle) {
    if (!isFunction(handle)) {
      throw new Error("MyPromise must accept a function as a parameter");
    }

    this._status = PENDING;
    this._value = undefined;
    // 由于 then 方法可以多次调用，我们需要维护两个数组，将 then 方法中的 onFulfilled 和 onRejected 添加到对应数组，等待执行
    this._fulfilledQueues = [];
    this._rejectedQueues = [];

    // 执行handle
    try {
      handle(this._resolve.bind(this), this._reject.bind(this));
    } catch (err) {
      this._reject(err);
    }
  }
  // 添加 resolve 时执行的函数，需要考虑 val 是 Promise 对象的情况
  _resolve(val) {
    if (this._status !== PENDING) return;

    // 依次执行成功队列中的函数，并清空队列
    const runFulfilled = (value) => {
      let cb;
      while ((cb = this._fulfilledQueues.shift())) {
        cb(value);
      }
    };

    // 依次执行失败队列中的函数，并清空队列
    const runRejected = (error) => {
      let cb;
      while ((cb = this._rejectedQueues.shift())) {
        cb(error);
      }
    };

    if (val instanceof MyPromise) {
      val.then(
        (value) => {
          this._value = value;
          this._status = FULFILLED;
          runFulfilled(value);
        },
        (err) => {
          this._value = err;
          this._status = REJECTED;
          runRejected(err);
        }
      );
    } else {
      this._value = val;
      this._status = FULFILLED;
      runFulfilled(val);
    }
  }
  // 添加reject时执行的函数
  _reject(err) {
    if (this._status !== PENDING) return;
    this._status = REJECTED;
    this._value = err;
    let cb;
    while ((cb = this._rejectedQueues.shift())) {
      cb(err);
    }
  }

  then(onFulfilled, onRejected) {
    const { _value, _status } = this;

    // 返回一个新的Promise对象
    return new MyPromise((onFulfilledNext, onRejectedNext) => {
      // 封装一个成功时执行的函数
      let fulfilled = (value) => {
        try {
          // 如果 then 函数传入的成功回调参数不是一个函数，直接执行新 Promise 对象的 resolve 函数
          if (!isFunction(onFulfilled)) {
            onFulfilledNext(value);
          } else {
            let res = onFulfilled(value);
            // 如果当前回调函数返回的是 Promise 对象，调用它自己的 then 函数，必须等待其状态改变后再执行下一个 then 的回调函数
            if (res instanceof MyPromise) {
              res.then(onFulfilledNext, onRejectedNext);
            } else {
              // 否则会将返回结果直接作为参数，传入下一个then的回调函数，并立即执行下一个 then 的回调函数
              onFulfilledNext(res);
            }
          }
        } catch (err) {
          // 如果函数执行出错，新的Promise对象的状态为失败
          onRejectedNext(err);
        }
      };

      // 封装一个失败时执行的函数
      let rejected = (error) => {
        try {
          if (!isFunction(onRejected)) {
            onRejectedNext(error);
          } else {
            let res = onRejected(error);
            if (res instanceof MyPromise) {
              // 如果当前回调函数返回MyPromise对象，必须等待其状态改变后在执行下一个回调
              res.then(onFulfilledNext, onRejectedNext);
            } else {
              //否则会将返回结果直接作为参数，传入下一个then的回调函数，并立即执行下一个then的回调函数
              onFulfilledNext(res);
            }
          }
        } catch (err) {
          // 如果函数执行出错，新的Promise对象的状态为失败
          onRejectedNext(err);
        }
      };

      switch (_status) {
        // 当状态为 Pending 时，将 then 方法回调函数加入执行队列等待执行
        case PENDING:
          this._fulfilledQueues.push(fulfilled);
          this._rejectedQueues.push(rejected);
          break;
        // 当状态已经改变时，立即执行对应的回调函数
        case FULFILLED:
          fulfilled(_value);
          break;
        case REJECTED:
          rejected(_value);
          break;
      }
    });
  }
}
```

下面我们用两个栗子来测试一下实现的 then 函数：

```js
var promise = new MyPromise(resolve => {
  console.log('Done');
  resolve('同步 Promise');
}).then((data) => {
  console.log(data);
  return '同步 then 1'
}).then((data) => {
  console.log(data);
})

// Done
// 同步 Promise
// 同步 then 1
```

```js
var promise = new MyPromise(resolve => {
  setTimeout(() => {
    console.log('Done');
    resolve('2秒');
  }, 2000);
}).then((data) => {
  console.log(data);

  return new MyPromise((resolve, reject) => {
    setTimeout(() => {
      resolve('第一个 then 返回一个Promise')
    }, 2000)
  })
}).then((data) => {
  console.log(data);
})

// Done
// 2秒
// 第一个 then 返回一个Promise
```

没什么问题~~

### 扩展

接下来我们添加一些 Promise 常用的方法

#### catch

相当于调用 then 方法, 但只传入 Rejected 状态的回调函数

```js
catch (onRejected) {
  return this.then(undefined, onRejected)
}
```

#### 静态 all 方法

```js
static all (list) {
  return new MyPromise((resolve, reject) => {
    let values = []
    let count = 0
    for (let [i, p] of list.entries()) {
      this.resolve(p).then(res => {
        values[i] = res
        count++
        // 所有子 Promise 状态都变成 fulfilled 时，返回的 Promise 状态就变成 fulfilled
        if (count === list.length) resolve(values)
      }, err => {
        // 有一个子 Promise 状态被 rejected 时, 返回的 Promise 状态就变成 rejected
        reject(err)
      })
    }
  })
}
```

#### 静态 race 方法

```js
static race (list) {
  return new MyPromise((resolve, reject) => {
    for (let p of list) {
      // 只要有一个子 Promise 先改变状态，返回的 Promise 的状态就跟着改变
      this.resolve(p).then(res => {
        resolve(res)
      }, err => {
        reject(err)
      })
    }
  })
}
```

这样，Promise 的基本功能算是实现了~~

### 参考

[Promise实现原理](https://juejin.im/post/5b83cb5ae51d4538cc3ec354)
