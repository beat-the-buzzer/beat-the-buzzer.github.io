---
title: Vue修饰符总结（上篇）
date: 2020-06-27
tags: [Vue]
categories: 
- Vue
series: Vue
---

Demo演示地址： [https://beat-the-buzzer.github.io/my-demos/#/index/my-decorator-01](https://beat-the-buzzer.github.io/my-demos/#/index/my-decorator-01)

Vue的修饰符为我们解决了很多通用的问题，如果我们了解这些修饰符，就能在开发的过程中节约大量的时间。

我们都知道使用`v-on`或者`@`在Vue中绑定事件，关于事件的修饰符，其实也是十分实用的。如果我们不了解这些修饰符，就会自己去写一些原生的实现方式，实际上Vue已经在内部有实现了。

上篇主要是总结一下各种事件的修饰符，基本上都是Vue官方文档上面的。

#### .stop - 调用 event.stopPropagation()

由于事件冒泡的机制，在点击子元素的时候，也会触发父级的点击事件。只要在绑定事件的时候加上`.stop`，就可以阻止事件冒泡。

```html
<div @click="showMsg('父元素触发')">
  <button @click="showMsg('子元素触发')">未阻止冒泡</button">
</div>

<div @click="showMsg('父元素触发')">
  <button @click.stop="showMsg('子元素触发')">阻止冒泡</button>
</div>
```

#### .prevent - 调用 event.preventDefault()

用于阻止事件的默认行为，例如，当给a标签绑定事件时，加了这个修饰符会阻止默认跳转，相当于调用了event.preventDefault()方法。

```html
<a href="https://github.com/beat-the-buzzer/" @click="showMsg('未阻止默认')">未阻止默认</a>
<a href="https://github.com/beat-the-buzzer/" @click.prevent="showMsg('阻止默认')">阻止默认</a>
```

#### .capture - 添加事件侦听器时使用 capture 模式

完整的事件机制是：捕获阶段--目标阶段--冒泡阶段，默认的是事件触发是从目标开始往上冒泡。当我们加了这个修饰符之后，事件触发从包含这个元素的顶层开始往下触发。

```html
<div @click="showMsg('父元素触发')">
  <div @click="showMsg('子元素触发')">子元素先触发</div>
</div>
<div @click.capture="showMsg('父元素触发')">
  <div @click="showMsg('子元素触发')">父元素先触发</div>
</div>
```

#### .self - 只当事件是从侦听器绑定的元素本身触发时才触发回调

只当事件是从事件绑定的元素本身触发时才触发回调，也就是说，通过冒泡的方式不会触发点击事件。

```html
<div @click="showMsg('父元素触发')">
  <div @click="showMsg('子元素触发')">触发两次</div>
</div>
<div @click.self="showMsg('父元素触发')">
  <div @click="showMsg('子元素触发')">父元素只触发父元素上面的点击</div>
</div>
```

#### .{keyCode | keyAlias} - 只当事件是从特定键触发时才触发回调

```html
<input type="text" @keyup.enter="showMsg('Enter被按下')">
<input @keyup.13="showMsg('Enter被按下(编码13)')">
```

```
//普通键
.enter
.tab
.delete //(捕获“删除”和“退格”键)
.space
.esc
.up
.down
.left
.right

//系统修饰键
.ctrl
.alt
.meta
.shift
```

#### .native - 监听组件根元素的原生事件

我们直接在组件上绑定一个点击事件是没有效果的，加这个修饰符可以让组件像DOM元素一样绑定事件。

```html
<My-Component @click="showMsg('无法触发')"></My-Component>
<My-Component @click.native="showMsg('触发组件的点击事件')"></My-Component>
```

#### .once - 只触发一次回调

只能用一次，绑定了事件以后只能触发一次，第二次就不会触发。

```html
<div @click="showMsg('多次触发')">多次触发</div>
<div @click.once="showMsg('只触发一次')">只触发一次</div>
```

#### .left - (2.2.0) 只当点击鼠标左键时触发 .right - (2.2.0) 只当点击鼠标右键时触发 .middle - (2.2.0) 只当点击鼠标中键时触发

键盘的左、中、右键点击的时候触发

```html
<div @click.left="showMsg('左键点击触发')">左键点击触发</div>
<div @click.right="showMsg('右键点击触发')">右键点击触发</div>
<div @click.middle="showMsg('滚轮点击触发')">滚轮点击触发</div>
```

> 小结： 或许以上的总结在实际项目中可能用不到，或者我们习惯了使用原生的解决方法，例如手动阻止默认事件，但是我们的目的不仅仅只是为了了解这个知识，更多的是通过了解这些用法，来重新温习一下JS的基础，在后面，还需要去阅读源码，了解这些装饰器在底层是如何实现的，这样才能真正走上进阶之路。