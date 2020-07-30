---
title: Vue修饰符总结（下篇）
date: 2020-07-13
tags: [Vue]
categories: 
- Vue
series: Vue
---

Demo演示地址： [https://beat-the-buzzer.github.io/my-demos/#/index/my-decorator-02](https://beat-the-buzzer.github.io/my-demos/#/index/my-decorator-02)

Vue的修饰符为我们解决了很多通用的问题，如果我们了解这些修饰符，就能在开发的过程中节约大量的时间。

我们都知道使用`v-on`或者`@`在Vue中绑定事件，关于事件的修饰符，其实也是十分实用的。如果我们不了解这些修饰符，就会自己去写一些原生的实现方式，实际上Vue已经在内部有实现了。

下篇主要是总结一下的修饰符，都是Vue官方文档上面的。

### v-bind相关修饰符

#### .prop - 作为一个 DOM property 绑定而不是作为 attribute 绑定。

```html
 <!-- 绑定一个全是 attribute 的对象 -->
<div v-bind="{ id: id, 'class': 'blue' }">blue</div>
<!-- 通过 prop 修饰符绑定 DOM attribute -->
<div v-bind:innerHTML.prop="'red'" :className.prop="'red'"></div>
```

#### .camel - (2.1.0+) 将 kebab-case 特性名转换为 camelCase. (从 2.1.0 开始支持)

下面的例子中，第二种写法是没有效果的，第三种方法可以正常展示。

```html
<svg style="width:50px; height:50px" :viewBox="viewBox">
  <circle cx="50" cy="50" r="50" fill="#fdd" stroke="none"></circle>
</svg>
<svg style="width:50px; height:50px" :view-box="viewBox">
  <circle cx="50" cy="50" r="50" fill="#fdd" stroke="none"></circle>
</svg>
<svg style="width:50px; height:50px" :view-box.camel="viewBox">
  <circle cx="50" cy="50" r="50" fill="#fdd" stroke="none"></circle>
</svg>
```

#### .sync (2.3.0+) 语法糖，会扩展成一个更新父组件绑定值的 v-on 侦听器

这种情况下暂时不举例说明了。

### v-model相关修饰符

#### .lazy - 取代 input 监听 change 事件

```html
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg">
```

#### .number - 输入字符串转为有效的数字

如果这个值无法被 parseFloat() 解析，则会返回原始的值。

```html
<input v-model.number="age" type="number">
```

#### .trim - 输入首尾空格过滤

```html
<input v-model="name0" type="text" placeholder="请输入姓名">
<input v-model.trim="name" type="text" placeholder="请输入姓名：trim">
```

> 小结： 或许以上的总结在实际项目中可能用不到，或者我们习惯了使用原生的解决方法，例如手动阻止默认事件，但是我们的目的不仅仅只是为了了解这个知识，更多的是通过了解这些用法，来重新温习一下JS的基础，在后面，还需要去阅读源码，了解这些装饰器在底层是如何实现的，这样才能真正走上进阶之路。