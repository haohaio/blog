---
title: 'vue-property-decorator 使用简介'
entitle: vue-property-decorator
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
comments: true
date: 2020-09-18 15:06:20
timestamp:
tags: 
  - JavaScript
keywords:
description:
photos:
  - /img/tags/javascript.jpg
---

最近接手了一个新项目，是用 TypeScript 进行开发的。项目里用了两个库 [vue-class-component](https://github.com/vuejs/vue-class-component) 和 [vue-property-decorator](https://github.com/kaorun343/vue-property-decorator)，写法与我们平常开发 Vue 项目时还是有很大区别的，今天就来看下具体如何使用的。

> 现在使用 Vue CLI 3 创建新项目时，选择 "Manually select features (手动选择特性)" 选项，勾选上 TypeScript 时，就可以默认引入 vue-class-component 和 vue-property-decorator 这两个库了。

## Vue Class Component

这个库是 Vue 官方团队开发维护的。接下来就看下 `vue-class-component` 这个库如何使用的吧。

### Overview

我们先大概看一下如何用 vue-class-component 这个库来定义我们的组件的：

```html
<template>
  <div>
    <button v-on:click="decrement">-</button>
    {{ count }}
    <button v-on:click="increment">+</button>
  </div>
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'

// Define the component in class-style
@Component
export default class Counter extends Vue {
  // Class properties will be component data
  count = 0

  // Methods will be component methods
  increment() {
    this.count++
  }

  decrement() {
    this.count--
  }
}
</script>
```

可以看到，通过 `@Component` 这个注解，我们用 ES6 的 class 语法，就可以定义我们的组件了。接下来具体看下如何使用的。

### Data

```html
<template>
  <div>{{ message }}</div>
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class HelloWorld extends Vue {
  // Declared as component data
  message = 'Hello World!'
}
</script>
```

可以看到，我们在定义组件的 data 数据时，可以像定义类属性一样进行定义，不再像原来一样使用 data hook 了。

> 需要注意的是，如果初始值为 undefined 时，该属性不会被变化侦测。可以通过将该属性设为 null，或者使用 data hook。

### Methods

```html
<template>
  <button v-on:click="hello">Click</button>
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class HelloWorld extends Vue {
  // Declared as component method
  hello() {
    console.log('Hello World!')
  }
}
</script>
```

定义组件的 methods 时，可以像定义类原型方法一样进行定义，不再像原来一样使用 methods hook 了。

> 需要注意的是，不要使用箭头函数进行定义。因为我们在使用 this 时，一般都需要 this 指向调用它的 Vue 实例，而因为箭头函数并没有 this，this 会作为变量一直向上级词法作用域查找，直至找到为止，所以这时的 this 就不是我们想要的 this 了。

### Computed Properties

```html
<template>
  <input v-model="name">
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class HelloWorld extends Vue {
  firstName = 'John'
  lastName = 'Doe'

  // Declared as computed property getter
  get name() {
    return this.firstName + ' ' + this.lastName
  }

  // Declared as computed property setter
  set name(value) {
    const split = value.split(' ')
    this.firstName = split[0]
    this.lastName = split[1] || ''
  }
}
</script>
```

可以看到，我们在定义计算属性时通过声明属性的 setter 和 getter 来定义。

### Hooks

像 `data`、`render` 以及 Vue 其他生命周期的方法我们还是像以前一样使用。

```js
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class HelloWorld extends Vue {
  // Declare mounted lifecycle hook
  mounted() {
    console.log('mounted')
  }

  // Declare render function
  render() {
    return <div>Hello World!</div>
  }
}
```

### Other Options

组件内其他选项，比如 `components`、`props`、`watch`等，我们需要将其写在装饰器函数中：

```html
<template>
  <OtherComponent />
</template>

<script>
import Vue from 'vue'
import Component from 'vue-class-component'
import OtherComponent from './OtherComponent.vue'

@Component({
  components: {
    OtherComponent
  }
})
export default class HelloWorld extends Vue {}
</script>
```

### Additional Hooks

当我们使用类似 `Vue Router` 这样的类库时，想要使用其组件内的路由导航守卫时，需要在声明组件前注册一下这些 hooks。

```js
// class-component-hooks.js
import Component from 'vue-class-component'

// Register the router hooks with their names
Component.registerHooks([
  'beforeRouteEnter',
  'beforeRouteLeave',
  'beforeRouteUpdate'
])
```

```js
// main.js

// Make sure to register before importing any components
import './class-component-hooks'

import Vue from 'vue'
import App from './App'

new Vue({
  el: '#app',
  render: h => h(App)
})
```

```js
import Vue from 'vue'
import Component from 'vue-class-component'

@Component
export default class HelloWorld extends Vue {
  // The class component now treats beforeRouteEnter,
  // beforeRouteUpdate and beforeRouteLeave as Vue Router hooks
  beforeRouteEnter(to, from, next) {
    console.log('beforeRouteEnter')
    next()
  }

  beforeRouteUpdate(to, from, next) {
    console.log('beforeRouteUpdate')
    next()
  }

  beforeRouteLeave(to, from, next) {
    console.log('beforeRouteLeave')
    next()
  }
}
```

### Extend

现在我们可以实现组件的继承了：

```js
// super.js
import Vue from 'vue'
import Component from 'vue-class-component'

// Define a super class component
@Component
export default class Super extends Vue {
  superValue = 'Hello'
}
```

```js
import Super from './super'
import Component from 'vue-class-component'

// Extending the Super class component
@Component
export default class HelloWorld extends Super {
  created() {
    console.log(this.superValue) // -> Hello
  }
}
```

### Mixins

现在我们使用 mixins 时，需要使用特殊的 mixins 辅助函数了：

```js
// mixins.js
import Vue from 'vue'
import Component from 'vue-class-component'

// You can declare mixins as the same style as components.
@Component
export class Hello extends Vue {
  hello = 'Hello'
}

@Component
export class World extends Vue {
  world = 'World'
}
```

```js
import Component, { mixins } from 'vue-class-component'
import { Hello, World } from './mixins'

// Use `mixins` helper function instead of `Vue`.
// `mixins` can receive any number of arguments.
@Component
export class HelloWorld extends mixins(Hello, World) {
  created () {
    console.log(this.hello + ' ' + this.world + '!') // -> Hello World!
  }
}
```

> 需要注意的是，写 mixins 时也需要定义成类组件。

## Vue Property Decorator

vue-property-decorator 是对  vue-class-component 的很好的补充。它可以让vue的某些属性和方法，通过装饰器的写法让它也写到vue组件实例的类里面。接下来介绍下 @Prop 和 @Watch 这两个常用装饰器：

### @Prop

```js
import { Vue, Component, Prop } from 'vue-property-decorator'

@Component
export default class YourComponent extends Vue {
  @Prop(Number) propA: number | undefined
  @Prop({ default: 'default value' }) readonly propB!: string
  @Prop([String, Boolean]) readonly propC: string | boolean | undefined
}
```

相当于：

```js
export default {
  props: {
    propA: {
      type: Number,
    },
    propB: {
      default: 'default value',
    },
    propC: {
      type: [String, Boolean],
    },
  },
}
```

### @Watch

```js
import { Vue, Component, Watch } from 'vue-property-decorator'

@Component
export default class YourComponent extends Vue {
  @Watch('child')
  onChildChanged(val: string, oldVal: string) {}

  @Watch('person', { immediate: true, deep: true })
  onPersonChanged1(val: Person, oldVal: Person) {}

  @Watch('person')
  onPersonChanged2(val: Person, oldVal: Person) {}
}
```

相当于：

```js
export default {
  watch: {
    child: [
      {
        handler: 'onChildChanged',
        immediate: false,
        deep: false,
      },
    ],
    person: [
      {
        handler: 'onPersonChanged1',
        immediate: true,
        deep: true,
      },
      {
        handler: 'onPersonChanged2',
        immediate: false,
        deep: false,
      },
    ],
  },
  methods: {
    onChildChanged(val, oldVal) {},
    onPersonChanged1(val, oldVal) {},
    onPersonChanged2(val, oldVal) {},
  },
}
```

关于在 Vue 项目中使用 vue-class-component 和 vue-property-decorator 进行开发，大概了解这么多就差不多了，后续了解更深的话再继续补充~
