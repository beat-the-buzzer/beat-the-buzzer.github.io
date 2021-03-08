---
title: CSS——display详解
date: 2021-03-08
tags: [CSS]
categories: 
- 前端开发
---

display有很多属性值，我们在这里只介绍几个常用的，重要的，以及他们之间可能存在的一些问题和差异。

### block

设置了block元素，那么这个元素就遵循盒子模型的规则，宽高、边距都是可以设置的。

块元素独占一行。

### inline

inline元素理解为盒子内部的“流”元素，他的形状完全取决于容器，手动设置宽高都是无效的。

### inline-block

外部看是“流”，内部看是“块”，比如一行内有多个按钮，如果都是inline-block，那么这些按钮可以在一行内进行布局，并且可以设置他们的宽高。

### inline-block 容易产生的问题

空白间隙

```html
<style>
.inline-block {
  display: inline-block;
}
</style>
<div class="wrapper">
  <div class="inline-block"></div>
  <div class="inline-block"></div>
  <div class="inline-block"></div>
</div>
```

显然，每个inline-block元素中间，都会有回车/Tab/空格，这些会造成三个inline-block元素之间存在一点间隙。

解决方案1：设置容器的字体大小：`font-size: 0`

解决方案2：在HTML中不换行，当然这会降低代码的可读性，所以我们还有另外的方法，就是使用HTML中的注释：

```html
<div class="wrapper"><!--
  --><div class="inline-block"></div><!--
  --><div class="inline-block"></div><!--
  --><div class="inline-block"></div><!--
--></div>
```

解决方案3：使用`Webpack`或者`Gulp`这样的工具，对代码进行压缩处理，消除空格、回车、Tab。

[使用gulp-htmlmin压缩html](https://github.com/beat-the-buzzer/gulp-test)