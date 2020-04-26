---
title: "Vue 之 PatchFlags"
entitle: vue-patch-flags
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
comments: true
date: 2020-04-26 14:58:38
timestamp:
tags:
  - JavaScript
keywords:
description:
photos:
  - /img/tags/javascript.jpg
---

在 4 月 21 日晚，Vue 作者尤雨溪在 B 站直播分享了 `Vue.js 3.0 Beta` 最新进展。

其中在介绍性能提升方面时，提到了编译模板的优化。例如下面的模板代码：

```html
<div>
  <span />
  <span class="highlight">{{ msg }}</span>
</div>
```

会被编译成：

```js
import { createVNode as _createVNode, toDisplayString as _toDisplayString, openBlock as _openBlock, createBlock as _createBlock } from "vue"

export function render(_ctx, _cache) {
  return (_openBlock(), _createBlock("div", null, [
    _createVNode("span"),
    _createVNode("span", { class: "highlight" }, _toDisplayString(_ctx.msg), 1 /* TEXT */)
  ]))
}
```

可以看到，创建第二个虚拟 DOM `span` 时，最后一个参数为 `1 /* TEXT */`。这个 `1` 就是一个 `PatchFlag`，代表具有动态的 `textContent`。在更新的时候，通过枚举值判断，只会比较该节点文字的变化，无需再去比较其它属性的变化。

> 在新版本的 Vue 中，只会对带有 PatchFlag 的节点进行真正的追踪。

可以看下源码中对 `PatchFlag` 枚举值是如何定义的：[vue-next patchFlags](https://github.com/vuejs/vue-next/blob/c11905fe36adbcbb58c4fc02144e81ccdfaceda6/packages/shared/src/patchFlags.ts)

```ts
export const enum PatchFlags {
  TEXT = 1, // 表示具有动态textContent的元素
  CLASS = 1 << 1, // 表示有动态Class的元素
  STYLE = 1 << 2, // 表示有动态样式的元素
  PROPS = 1 << 3, // 表示具有非类/样式动态道具的元素。(组件定义的 class 和 style 也属于 PROPS)
  FULL_PROPS = 1 << 4, // 表示带有动态键的道具的元素，与上面 CLASS, STYLE and PROPS 相斥
  HYDRATE_EVENTS = 1 << 5, // 表示带有事件监听器的元素
  STABLE_FRAGMENT = 1 << 6, // 表示子元素顺序不变的 fragment。(在React中，有一个React.Fragments，其作用就是说在一个组件返回多个元素，意味着组件下根节点不只一个。新版本的 Vue 中 template 支持多个根节点了，不需要用一个根节点包裹所有的元素了，render 可以返回一个数组。新版本的 Vue 会把这些自动的变成 fragment，特别在模板语法中时无感知的)
  KEYED_FRAGMENT = 1 << 7, // 表示子元素有 key或部分子元素有 key 的 fragment
  UNKEYED_FRAGMENT = 1 << 8, // 表示带有无key绑定的 fragment
  NEED_PATCH = 1 << 9, // 表示只需要非属性比较的元素，例如 ref 或 directives
  DYNAMIC_SLOTS = 1 << 10, // 表示具有动态插槽的元素

  // 特殊 FLAGS： 负整数表示永远不会用作diff,只需检查 patchFlag === FLAG.

  HOISTED = -1, // 表示静态节点
  BAIL = -2, // 与 renderSlot() 相关
}
```

### 枚举与位运算

可以看到上面源码中使用了位操作符对枚举值进行定义，接下来我们来了解一下为什么这么写。

#### 位运算概览

位运算就是直接对整数在内存中的二进制位进行操作。接下来我们先来系统的了解下位运算的各种操作符。

##### 按位与运算符（&）

`&` 运算符表示按位与，即两个位都为 1 时，结果才为 1，例如 `3 & 5`，即 `0000 0011 & 0000 0101`，结果为 `0000 0001`。即 `3 & 5` 的值为 1。

##### 按位或运算符（|）

`|` 运算符表示按位或，即两个位有一位为 1 时，结果就为 1，例如 `3 | 5`，即 `0000 0011 & 0000 0101`，结果为 `0000 0111`，即 `3 | 5` 的值为 7。

##### 按位异或运算符（^）

`^` 运算符表示按位异或，即两个位相同时结果为 0，相异时结果为 1。例如 `3 ^ 5`，即 `0000 0011 & 0000 0101`，结果为 `0000 0110`，即 `3 & 5` 的值为 6。

##### 按位取反运算符（~）

`~` 运算符表示按位取反，即对一个二进制数按位取反，即将 0 变 1， 1 变 0。例如 `~3`，即将 `0000 0011` 变成 `1111 1100`。很明显是一个负数。现在计算机普遍使用补码表示负数，补码转换成原码就是等于其绝对值取反然后末尾加 1，先绝对值取反就是 `1000 0011`，末尾加 1，变成`1000 0100`，即 `~3` 的值为 ``-4`

##### 左移运算符（<<）

`<<` 表示把各二进位全部左移若干位，高位丢弃，低位补0。例如二进制 `1010 1110` 左移两位就是 `1011 1000`。

##### 右移运算符（>>）

`>>` 表示把各二进位全部右移若干位，对无符号数，高位补0，有符号数，各编译器处理方法不一样，有的补符号位（算术右移），有的补0（逻辑右移）。

### PatchFlags 中的枚举值

再来看一下 `PatchFlags`  中定义的枚值就很清楚了：

```ts
export const enum PatchFlags {
  TEXT = 1, // 1  二进制: 0000 0001 (1)
  CLASS = 1 << 1, // 二进制: 0000 0010 (2)
  STYLE = 1 << 2, // 二进制: 0000 0100 (4)
  PROPS = 1 << 3, // 二进制:  0000 1000 (8)
  FULL_PROPS = 1 << 4 //  二进制: 0001 0000 (16)
```

通过观察可以看到，每个枚举值的二进制数中都只有一个 1，通过左移的位数不同来进行区分。就是通过这个特点，我们可以方便的进行多枚举值情况的处理。

接下来我们看下当同时需要对 TEXT 和 PROPS 需要进行 patching 时，`PatchFlag` 的枚举值是多少

```html
<div>
  <span />
  <span :id="123">{{ msg }}</span>
</div>
```

```ts
import { createVNode as _createVNode, toDisplayString as _toDisplayString, openBlock as _openBlock, createBlock as _createBlock } from "vue"

export function render(_ctx, _cache) {
  return (_openBlock(), _createBlock("div", null, [
    _createVNode("span"),
    _createVNode("span", { id: _ctx.id }, _toDisplayString(_ctx.msg), 9 /* TEXT, PROPS */, ["id"])
  ]))
}
```

可以看到 `PatchFlag` 的值为 `9 /* TEXT, PROPS */`。

- 那么 9 是如何得来的呢？

其实就是通过按位或运算得来的。`TEXT` 对应的二进制为 `0000 0001`，`PROPS` 对应的二进制为 `0000 1000`，执行按位或运算后结果就是 `0000 1001`，也就是 9 了。

- 那我们如何通过 9 来判断包含了枚举值 `TEXT` 和 `PROPS` 呢？

其实是通过按位与运算来判断的。`TEXT` 的值为 1，`9 & 1` 的值，二进制为 `0000 0001`，值为 1。`PROPS` 的值为 8，`9 & 8` 的值，二进制为 `0000 1000`，值为 8。值都不为 0，那么转换成布尔值的话就都是 `true`。

现在再来看下 9 的二进制表示： `0000 1001`。可以看到有 2 个 `1`，第一个 1 与 `PROPS`(8) 二进制数 `0000 1000` 中的 1 位置相同，第二个 1 与 `TEXT`(1) 二进制数 `0000 0001` 中的 1 位置相同。如果第一次接触位运算的话，到这里是不是恍然大悟了呢。通过按位或运算和按位与运算，可以很方便的处理多枚举的情况哦 ~~

在源码 `vue-next/packages/runtime-core/src/componentRenderUtils.ts` 的 `shouldUpdateComponent` 方法中，有这样一段代码，我们来看一下：

```js
if (patchFlag > 0) {
  if (patchFlag & PatchFlags.DYNAMIC_SLOTS) {
    // slot content that references values that might have changed,
    // e.g. in a v-for
    return true
  }
  if (patchFlag & PatchFlags.FULL_PROPS) {
    // presence of this flag indicates props are always non-null
    return hasPropsChanged(prevProps!, nextProps!)
  } else if (patchFlag & PatchFlags.PROPS) {
    const dynamicProps = nextVNode.dynamicProps!
    for (let i = 0; i < dynamicProps.length; i++) {
      const key = dynamicProps[i]
      if (nextProps![key] !== prevProps![key]) {
        return true
      }
    }
  }
}
```

可以看到 Vue 中就是使用按位与运算（&）来判断枚举值的 ~~

### 位运算编程小技巧

#### 奇偶数判断

当一个数为奇数时，那么其二进制末位肯定为 1。当其与 1 执行按位与运算时，则结果肯定为 1。反之，偶数的二进制末位肯定为 0，当其与 1 执行按位与运算时，则结果肯定为 0。因此，可以通过位运算很方便的写一个判断奇偶数的方法：

```js
const isOdd = (num) => {
  return !!(num & 1)
}
```

#### 乘以2 和 除以 2

看到上面枚举值可以看到左移一位就是乘以2，那么右移一位就是除以2了，但是这个除以 2 和 JavaScript 中的除以 2 还是有些不同的，比如 3 的二进制为 `0011`，那么右移一位为 `0001`，等于 1，相当于 JavaScript 中除以 2 后向下取整了。

### 小结

本文对 Vue 3.0 中的 PatchFlags 做了一些了解，更多的是对位运算重新学习了一遍 ~~
