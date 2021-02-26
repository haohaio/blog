---
title: 'React Hooks'
entitle: react-hooks
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
comments: true
date: 2021-02-25 10:14:49
timestamp:
tags:
  - React
keywords:
description:
photos:
  - /img/tags/react.jpeg
---

好长时间没写 React 的项目了，发现 React 发生了很大的变化，v16.8 版本引入了全新的 API，叫做 React Hooks，颠覆了以前的用法。现在就来了解一下相关用法。

之前 React 写组件时，都是通过类（class）来写的。比如：

```js
import React, { Component } from 'react'

export default class Button extends Component {
  constructor() {
    super()
    this.state = { buttonText: 'Click me, please' }
    this.handleClick = this.handleClick.bind(this)
  }
  handleClick() {
    this.setState(() => {
      return { buttonText: 'Thanks, been clicked!' }
    })
  }
  render() {
    const { buttonText } = this.state
    return <button onClick={this.handleClick}>{buttonText}</button>
  }
}
```

现在我们使用 React Hooks 来写组件的话，是这样的：

```js
import React, { useState } from 'react'

export default function Button() {
  const [buttonText, setButtonText] = useState('Click me,   please')

  function handleClick() {
    return setButtonText('Thanks, been clicked!')
  }

  return <button onClick={handleClick}>{buttonText}</button>
}
```

直观的就可以看到，代码量减少了一些。更重要的一点是使用函数实现的组件。

其实 React 早就支持函数组件:

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>
}
```

但是这种写法有重大限制，必须是纯函数，不能包含状态，也不支持生命周期方法，因此无法取代类。

React Hooks 的出现，就解决了这些问题，对函数组件进行了加强，现在不使用类，也能写出全功能的组件。

### Hook 的 含义

Hook 这个单词的意思是“钩子”。

React Hooks 的意思是，组件尽量写成纯函数，如果需要外部功能和副作用，就用钩子把外部代码"钩"进来。 React Hooks 就是那些钩子。

你需要什么功能，就使用什么钩子。React 默认提供了一些常用钩子，你也可以封装自己的钩子。

下面介绍一些 React 默认提高的常用钩子。

### useState()：状态钩子

useState()用于为函数组件引入状态（state）。纯函数不能有状态，所以把状态放在钩子里面。

再看一下我们上面用函数实现的组件，这个组件里就使用了 useState()。

```js
import React, { useState } from 'react'

export default function Button() {
  const [buttonText, setButtonText] = useState('Click me,   please')

  function handleClick() {
    return setButtonText('Thanks, been clicked!')
  }

  return <button onClick={handleClick}>{buttonText}</button>
}
```

useState()这个函数接受状态的初始值，作为参数，上例的初始值为按钮的文字。该函数返回一个数组，数组的第一个成员是一个变量，指向状态的当前值。第二个成员是一个函数，用来更新状态，约定是 `set` 前缀加上状态的变量名。

### useContext()：共享状态钩子

如果需要在组件之间共享状态，可以使用 useContext()。

下面代码中， 有两个组件 Navbar 和 Messages 需要共享状态，可以看看通过 useContext() 是如何实现的：

```js
import React, { useContext } from 'react'
import ReactDOM from 'react-dom'

// 使用 React Context API，在组件外部建立一个 Context。
const AppContext = React.createContext({})

const Navbar = () => {
  // useContext()钩子函数用来引入 Context 对象，从中获取username属性。
  const { username } = useContext(AppContext)

  return (
    <div className="navbar">
      <p>AwesomeSite</p>
      <p>{username}</p>
    </div>
  )
}

const Messages = () => {
  // useContext()钩子函数用来引入 Context 对象，从中获取username属性。
  const { username } = useContext(AppContext)

  return (
    <div className="messages">
      <h1>Messages</h1>
      <p>1 message for {username}</p>
      <p className="message">useContext is awesome!</p>
    </div>
  )
}

function App() {
  // AppContext.Provider提供了一个 Context 对象，这个对象可以被子组件共享。
  return (
    <AppContext.Provider
      value={{
        username: 'superawesome',
      }}
    >
      <div>
        <Navbar />
        <Messages />
      </div>
    </AppContext.Provider>
  )
}

const rootElement = document.getElementById('root')
ReactDOM.render(<App />, rootElement)
```

### useReducer()：action 钩子

React 本身不提供状态管理功能，通常需要使用外部库。这方面最常用的库是 Redux。

Redux 的核心概念是，组件发出 action 与状态管理器通信。状态管理器收到 action 以后，使用 Reducer 函数算出新的状态，Reducer 函数的形式是`(state, action) => newState`。

`useReducers()`钩子用来引入 Reducer 功能。

```js
const [state, dispatch] = useReducer(reducer, initialState)
```

useReducer() 接受 Reducer 函数和状态的初始值作为参数，返回一个数组。数组的第一个成员是状态的当前值，第二个成员是发送 action 的 dispatch 函数。

```js
const initialState = { count: 0 }

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 }
    case 'decrement':
      return { count: state.count - 1 }
    default:
      throw new Error()
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState)
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
    </>
  )
}
```

### useEffect()：副作用钩子

useEffect()用来引入具有副作用的操作，最常见的就是向服务器请求数据。useEffect()的用法如下：

```js
useEffect(() => {
  // Async Action
}, [dependencies])
```

useEffect()接受两个参数。第一个参数是一个函数，异步操作的代码放在里面。第二个参数是一个数组，用于给出 Effect 的依赖项，只要这个数组发生变化，useEffect()就会执行。第二个参数可以省略，这时每次组件渲染时，就会执行 useEffect()。

如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（[]）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循输入数组的工作方式。

> React 会等待浏览器完成画面渲染之后才会延迟调用 useEffect

具体可以看个栗子：

```js
const Person = ({ personId }) => {
  const [loading, setLoading] = useState(true)
  const [person, setPerson] = useState({})

  useEffect(() => {
    setLoading(true)
    fetch(`https://swapi.co/api/people/${personId}/`)
      .then((response) => response.json())
      .then((data) => {
        setPerson(data)
        setLoading(false)
      })
  }, [personId])

  if (loading === true) {
    return <p>Loading ...</p>
  }

  return (
    <div>
      <p>You're viewing: {person.name}</p>
      <p>Height: {person.height}</p>
      <p>Mass: {person.mass}</p>
    </div>
  )
}
```

上面代码中，每当组件参数 personId 发生变化，useEffect()就会执行。组件第一次渲染时，useEffect()也会执行。

通常，组件卸载时需要清除 effect 创建的诸如订阅或计时器 ID 等资源。要实现这一点，useEffect 函数需返回一个清除函数。

```js
useEffect(() => {
  const subscription = props.source.subscribe()
  return () => {
    // 清除订阅
    subscription.unsubscribe()
  }
})
```

> 为防止内存泄漏，清除函数会在组件卸载前执行。另外，如果组件多次渲染（通常如此），则在执行下一个 effect 之前，上一个 effect 就已被清除。

最后需要注意的事，使用 Hook 其中一个目的就是要解决 class 中生命周期函数经常包含不相关的逻辑，但又把相关逻辑分离到了几个不同方法中的问题。所以我们不应该把多个副效应合并写在一个 useEffect() 里，应该使用多个 effect，将不相关逻辑分离到不同的 effect 中。
