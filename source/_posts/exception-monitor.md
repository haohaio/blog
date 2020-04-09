---
title: "前端异常监控"
entitle: exception_monitor
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1586335615
comments: true
date: 2020-04-08 16:46:55
tags:
keywords:
description:
photos:
---

对于前端应用来说， 对异常的监控是很有必要的。异常也有很多种，比如 js 运行时异常，加载静态资源异常，接口请求异常等等。今天就来大概了解一下如何进行异常监控。

### JavaScript 运行时异常

#### try...catch

一般我们写代码时，会使用 `try...catch` 来捕获异常：

```js
try {
  throw "出错了！";
} catch (e) {
  console.log(e); // "出错了！"
}
```

在 `try` 代码块中，我们可以通过 `throw` 手动抛出异常，异常我们可以自定义，`catch` 代码块的参数是就是我们抛出来的异常。

当我们代码运行时发生错误时，会自动抛出来一个异常，这个异常是一个 `Error` 对象，会带有 `name` 和 `message` 两个标准属性。

> 现在大部分浏览器都支持了 `stack` 属性，包含错误的完整信息，包括了 `name` 、 `message` 和发生错误的文件和行号等。

```js
try {
  console.log(a);
  const a = 123;
} catch (e) {
  console.log(e.name); // 'ReferenceError'
  console.log(e.message); // 'Cannot access 'a' before initialization'
  console.log(e.stack); // ReferenceError: Cannot access 'a' before initialization   at test.html:23
}
```

但是我们并不能使用 `try...catch` 来做整个 Web 应用的异常监控，因为我们不可能给所有的 js 代码都加上 `try...catch`。

#### window.onerror

> 当 JavaScript 运行时错误（包括语法错误）发生时，window 会触发一个 ErrorEvent 接口的 error 事件，并执行 window.onerror()。

因此我们可以使用 `window.onerror` 来监控运行时的代码异常：

```js
/**
 * message: 错误信息
 * source: 出错文件：
 * lineno: 出错行号
 * colno: 出错列号：
 * error: 错误详情
 */
window.onerror = function (message, source, lineno, colno, error) {
  // Todo: 异常上报
  return true; // 函数返回true，阻止执行默认事件处理函数，即浏览器控制台不会再打印错误信息
};
```

需要注意的是，当加载自不同域的脚本中发生语法错误时，为避免信息泄露，语法错误的细节将不会报告，而代之简单的"Script error."。onerror 捕获的错误信息为：

```code
message: "Script error."
source: ""
lineno: 0
colno: 0
error: null
```

如果需要解决这个问题的话，我们需要做两件事：

- 引用跨域 js 文件时加上 `crossorigin` 属性

```html
<script type="text/javascript" src="http://b.com/b.js" crossorigin></script>
```

- 服务器发送相关跨域 js 文件时需要发送适当的 CORS HTTP 响应头(`Access-Control-Allow-Origin`)

### 静态资源异常

> 当一项资源（如 `<img>` 或 `<script>`）加载失败，并执行该元素上的 onerror() 处理函数。加载资源的元素会触发一个 Event 接口的 error 事件，并执行该元素上的 onerror() 处理函数。这些 error 事件不会向上冒泡到 window，不过能被单一的 window.addEventListener 捕获。

因此只用 `window.onerror` 来监测前端项目异常的话，功能还不够完善。但我们可以使用 `window.addEventListener('error', function(event) {}, true)` 来捕获静态资源异常

```js
window.addEventListener(
  "error",
  function (event) {
    console.log(event);
  },
  true
);
```

由于 `window.addEventListener('error', function(event) {}, true)` 也能捕获 js 运行时异常，因此我们使用了 `window.onerror` 后，为了避免重复上报，在监听静态资源异常时，需要做一些处理：

```js
window.addEventListener(
  "error",
  function (event) {
    let target = event.target || event.srcElement;
    // 判断是否是静态资源
    let isElementTarget =
      target instanceof HTMLScriptElement ||
      target instanceof HTMLLinkElement ||
      target instanceof HTMLImageElement;
    // 过滤非静态资源异常
    if (!isElementTarget) return;
    // 需要上报的资源文件 url
    let url = target.src || target.href;
    // Todo: 异常上报
  },
  true
);
```

### 未处理的 promise 异常

> 当 Promise 被 reject 且没有 reject 处理器的时候，会触发 `unhandledrejection` 事件。

也就是说当 promise 被 reject 但是没有进行 catch 处理时，会抛出异常。 但是这个异常不会被 `window.onerror` 和 `window.addEventListener('error', function(event) {}, true)` 捕获。

```js
function foo() {
  Promise.reject("出错了！");
}
foo(); // 控制台会打印 "Uncaught (in promise) 出错了！"
```

我们可以通过监听 `unhandledrejection` 事件来捕获未处理的 promise 异常：

```js
window.addEventListener("unhandledrejection", (event) => {
  console.log(event.reason); // "出错了！"
  // Todo: 异常上报
});
```

### 网络请求异常

#### XMLHttpRequest

一般情况下，我们都是基于 `XMLHttpRequest` 发送网络请求的：

```js
let xhr = new XMLHttpRequest();
xhr.open("GET", "/test");
xhr.send();
xhr.onreadystatechange = function () {
  if (xhr.readyState == 4 && xhr.status == 200) {
    console.log(xhr.responseText);
  } else {
    console.log(xhr.status); // 404
    console.log(xhr.statusText); // Not Found
  }
};
```

我们可以通过劫持 `XMLHttpRequest` 的 `open` 和 `send` 方法来进行异常上报：

```js
(function handleXMLHttpRequestException() {
  if (window.location.protocol === "file:") return;
  if (!window.XMLHttpRequest) return;

  const xmlHttp = window.XMLHttpRequest;

  // 当请求完全无法执行的时候（比如无法跨域请求），XMLHttpRequest 会收到 status=0 和 statusText="" 的返回，此时 responseURL 也为 ""，因此我们可以通过劫持 open 方法，把请求地址加到 XMLHttpRequest 对象上。
  let oldOpen = xmlHttp.prototype.open;
  xmlHttp.prototype.open = function (method, url) {
    this.ajaxUrl = url;
    oldOpen.apply(this, arguments);
  };

  // 保存原生 send 方法
  let oldSend = xmlHttp.prototype.send;
  let handleExceptionEvent = function (event) {
    if (event && event.currentTarget && event.currentTarget.status !== 200) {
      console.log(event.type); // load
      console.log(
        event.currentTarget.responseURL || event.currentTarget.ajaxUrl
      );
      console.log(event.currentTarget.status); // 404
      console.log(event.currentTarget.statusText); // Not Found
      // Todo: 异常上报
    }
  };

  // 劫持原生 send 方法，进行异常上报
  xmlHttp.prototype.send = function () {
    this.addEventListener("error", handleExceptionEvent);
    this.addEventListener("load", handleExceptionEvent); // 404 时会触发 load 事件
    return oldSend.apply(this, arguments);
  };
})();

let xhr = new XMLHttpRequest();
xhr.onreadystatechange = function () {
  if (xhr.readyState === 4) {
    if (xhr.status === 200) {
      // success
    } else {
      // error
    }
  }
};
xhr.open("GET", "http://www.baidu.com");
xhr.send();
```

#### fetch

当我们使用 `fetch` 发送网络请求时，可以这样做：

```js
(function handleFetchException() {
  if (!window.fetch) return;
  let oldFetch = window.fetch;
  window.fetch = function () {
    return (
      oldFetch
        .apply(this, arguments)
        .then((res) => {
          if (!res.ok) {
            // Todo: 异常上报
          }
          return res;
        })
        // 当fetch方法错误时上报
        .catch((error) => {
          // error.message,
          // error.stack
          // Todo: 异常上报
          throw error;
        })
    );
  };
})()

fetch('http://url')
```

### Vue 内部发生的错误

Vue 内部发生的错误会被 Vue 拦截，官方提供了一个处理函数，我们可以在这个函数内部进行异常上报：

```js
Vue.config.errorHandler = function (err, vm, info) {
  // handle error
  // `info` 是 Vue 特定的错误信息，比如错误所在的生命周期钩子
  // 只在 2.2.0+ 可用
}
```

### React 内部发生的错误

我们可以通过 React16+ 中的 `Error Boundaries` 特性来处理组件内部的错误：

```js
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  componentDidCatch(error, info) {
    // Display fallback UI
    this.setState({ hasError: true });
    // Todo: 异常上报
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

`Error Boundaries` 即错误边界，其可以拦截子组件生命周期内的错误，我们可以使用它来保证页面可以正常渲染UI，而不是一片空白。我们可以使用`ErrorBoundary` 包裹我们的子组件，在 `componentDidCatch` 函数中进行异常上报。

### 上报异常

当我们上报异常时，为了更准确的分析定位，我们需要不仅仅要上报捕获到的异常基本信息，还需要上报用户信息、环境信息（操作系统信息、浏览器信息、网络环境、应用版本等）等。

上报异常时我们还需要考虑上报的量和频率问题。有需要的情况下，我们可以先把异常信息存储到 IndexedDB 中，在 Web Worker 线程中控制异常上报。

上报异常时，Ajax 请求本身也有可能会发生异常，一般情况下更推荐使用动态创建 img 标签的形式进行上报。

现在前端代码大部分情况都是经过压缩后发布的，上报的 stack 信息需要还原为源码信息，才能快速定位源码进行修改。因此我们可以发部署时，将 sourcemap 文件上传到监控系统，在监控系统中展示 stack 信息时，利用 sourcemap 文件对 stack 信息进行解码，得到源码中的具体信息。

### 小结

现在我们算是基本了解了如何进行异常监控，但要实现完整的异常监控功能，还任重而道远。[Sentry](https://github.com/getsentry/sentry) 是一项非常出色的服务，可以帮助我们进行异常监控，并且是开源的。后面会研究下，如何使用 Sentry 进行异常监控。
