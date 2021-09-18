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
const PENDING = Symbol('pending')
const FULFILLED = Symbol('fulfilled')
const REJECTED = Symbol('rejected')

const resolvePromise = (promise2, x, resolve, reject) => {
  if (promise2 === x) {
    return reject(new TypeError('cycle error'))
  }

  if ((typeof x === 'object' && x !== null) || typeof x === 'function') {
    let called = false
    try {
      let then = x.then
      if (typeof then === 'function') {
        then.call(
          x,
          (y) => {
            if (called) {
              return
            }
            called = true
            resolvePromise(promise2, y, resolve, reject)
          },
          (e) => {
            if (called) {
              return
            }
            called = true
            reject(e)
          }
        )
      } else {
        resolve(x)
      }
    } catch (error) {
      if (called) {
        return
      }
      called = true
      reject(error)
    }
  } else {
    resolve(x)
  }
}

class Promise {
  constructor(executor) {
    this.status = PENDING
    this.value = undefined
    this.reason = undefined
    this.onFulfilledCallbacks = []
    this.onRejectedCallbacks = []
    const resolve = (value) => {
      if (this.status === PENDING) {
        this.value = value
        this.status = FULFILLED
        this.onFulfilledCallbacks.forEach((fn) => fn())
      }
    }

    const reject = (reason) => {
      if (this.status === PENDING) {
        this.reason = reason
        this.status = REJECTED
        this.onRejectedCallbacks.forEach((fn) => fn())
      }
    }

    try {
      executor(resolve, reject)
    } catch (error) {
      reject(error)
    }
  }

  then(onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : (val) => val
    onRejected =
      typeof onRejected === 'function'
        ? onRejected
        : (err) => {
            throw err
          }

    const promise2 = new Promise((resolve, reject) => {
      const fulfilled = () => {
        setTimeout(() => {
          try {
            let x = onFulfilled(this.value)
            resolvePromise(promise2, x, resolve, reject)
          } catch (error) {
            reject(error)
          }
        }, 0)
      }

      const rejected = () => {
        setTimeout(() => {
          try {
            let x = onRejected(this.reason)
            resolvePromise(promise2, x, resolve, reject)
          } catch (error) {
            reject(error)
          }
        }, 0)
      }

      switch (this.status) {
        case FULFILLED:
          fulfilled()
          break
        case REJECTED:
          rejected()
          break
        case PENDING:
          this.onFulfilledCallbacks.push(fulfilled)
          this.onRejectedCallbacks.push(rejected)
          break

        default:
          break
      }
    })

    return promise2
  }
}

Promise.defer = Promise.deferred = function () {
  const defer = {}
  defer.promise = new Promise((resolve, reject) => {
    defer.resolve = resolve
    defer.reject = reject
  })

  return defer
}

module.exports = Promise
```

这样，Promise 的基本功能算是实现了~~

最后我们可以用 `promises-aplus-tests` 库，来验证下我们写的 promise 是否符合 Promises/A+ 规范。

```bash
npm i promises-aplus-tests -g
promises-aplus-tests promise.js
```
