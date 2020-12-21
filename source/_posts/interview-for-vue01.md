---
title: 面试题系列——vue相关（基础篇）
date: 2020-11-16
tags: [面试]
categories: 
- 面试
series: 面试
---

> Vue、React相关的东西，我们如果仅仅停留在使用的层面，那么我们将无法通过任何一场面试。下面的一些关于Vue的问题，是我在阅读源码的过程中整理出来的，可能会遇到的问题。

### Vue2.x 使用了哪种类型检查工具，有什么好处

Flow 在运行之前就可以检测出一些由于类型错误可能会出现的问题，类似的还有 TypeScript。

### Vue的构建工具是什么，你还了解哪些其他的构建工具

rollup 适用于打包成一个JS库。

Rollup官方：Rollup 是一个 JavaScript 模块打包器，可以将小块代码编译成大块复杂的代码，例如 library 或应用程序

webpack官方：webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

Gulp官方：基于流的自动化构建工具

### Vue的核心思想是什么

数据驱动：数据和视图建立一种映射关系，如果期望页面改变，只需要改变数据。

组件化：把页面拆分成多个组件 (component)，每个组件依赖的 CSS、JavaScript、模板、图片等资源放在一起开发和维护。组件是资源独立的，组件在系统内部可复用，组件和组件之间可以嵌套。

### new Vue()的时候发生了什么

合并配置：一般 Vue 都会带一些配置参数，第一步就是把这个配置参数与默认参数进行合并；

初始化生命周期，

初始化事件中心，

初始化渲染，

初始化 data、props、computed、watcher 等等

中间调用了生命周期函数，beforeCreate、created

### 实例挂载在DOM上是如何实现的

vm._render 生成虚拟DOM

vm._update 把虚拟DOM更新到页面上

### render方法有什么作用

vm._render 最终是通过执行 createElement 方法并返回的是 vnode，它是一个虚拟 Node，这个是 Vue2.0 相对于 1.0 的重要更新。

### 虚拟DOM是什么，有什么好处，如何创建

其实 VNode 是对真实 DOM 的一种抽象描述，它的核心定义无非就几个关键属性，标签名、数据、子节点、键值等，其它属性都是用来扩展 VNode 的灵活性以及实现一些特殊 feature 的。由于 VNode 只是用来映射到真实 DOM 的渲染，不需要包含操作 DOM 的方法，因此它是非常轻量和简单的。

### update方法的更新机制是什么样子的

![https://ustbhuangyi.github.io/vue-analysis/assets/new-vue.png](https://ustbhuangyi.github.io/vue-analysis/assets/new-vue.png)

### 如何将虚拟DOM更新成真实DOM

patch

### Vue组件的生命周期

![https://ustbhuangyi.github.io/vue-analysis/assets/lifecycle.png](https://ustbhuangyi.github.io/vue-analysis/assets/lifecycle.png)

### Vue响应式的原理

1. 响应式对象

data 的初始化主要过程也是做两件事，一个是对定义 data 函数返回对象的遍历，通过 proxy 把每一个值 vm._data.xxx 都代理到 vm.xxx 上；另一个是调用 observe 方法观测整个 data 的变化，把 data 也变成响应式，可以通过 vm._data.xxx 访问到定义 data 返回函数中对应的属性。

Observer 是一个类，它的作用是给对象的属性添加 getter 和 setter，用于依赖收集和派发更新：

一般情况下，都是在初始化组件的时候会把我们定义的数据转化成“可观测对象”，所以我们如果给data里面的对象添加一条属性，这个属性不会在页面上刷新。

2. 依赖收集

收集依赖的目的是为了当这些响应式数据发生变化，触发它们的 setter 的时候，能知道应该通知哪些订阅者去做相应的逻辑处理，主要是 Watcher 和 Dep，核心思想就是使用 getter()

3. 派发更新

就是当数据发生变化的时候，触发 setter 逻辑，把在依赖过程中订阅的的所有观察者，也就是 watcher，都触发它们的 update 过程

### nextTick 有什么作用

```js
for (macroTask of macroTaskQueue) {
  // 1. Handle current MACRO-TASK
  handleMacroTask();
    
  // 2. Handle all MICRO-TASK
  for (microTask of microTaskQueue) {
    handleMicroTask(microTask);
  }
}
```

数据的变化到 DOM 的重新渲染是一个异步过程。如果需要在数据变化之后取到变化后的DOM，可以使用 nextTick 方法。这个方法和React中的 this.setState的第二个参数类似。

### 有没有一些情况，当你改变了数据，但是DOM没有更新

1. 给一个对象新增属性

2. 通过下标、length去改变数组

解决方法：

对于对象，在定义data的时候，就要把相关的属性定义好；

对于数组的更新，可以使用push、pop等方法，可以触发notify

更新的时候，赋值一个新数组或者新对象；

使用 $set 方法

使用forceUpdate