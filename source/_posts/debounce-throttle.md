---
title: 防抖节流操作总结
date: 2020-09-12
tags: [JavaScript, 函数式编程]
categories: 
- 前端开发
---

防抖和节流是我们平时开发的时候非常常见的一个功能，比如，按钮点击提交表单、输入框变化的时候需要发请求、浏览器窗口发生变化的时候做一些操作等等。每次用户快速操作的时候，经常会出现一些问题，比如重复提交等等，这个时候我们就需要对这样的业务场景进行优化。

一般我们的优化方式就是防抖和节流。

#### 防抖节流概念

**所谓防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。**

**所谓节流，就是指连续触发事件但是在 n 秒中只执行一次函数。节流会稀释函数的执行频率。**

下面介绍几个常见的例子来帮助理解防抖节流的概念。

#### 输入框变化发请求

```html
<input type="text" onchange="changeInput(this.value)" />
```

我们如果不做任何处理的话，每次输入一个字符，就会去触发这个函数，然后发请求，渲染页面。发请求的操作还有可能出现异步的情况，就是先发的请求后返回，导致页面渲染错误。事实上，我们如果快速输入了四个字符，前三次其实是没有必要去执行的。

```js
var timeout;

// 下面的代码应该是在输入框改变的时候触发
if(timeout) {
  // 如果有一个方法正在等待执行，就直接清理掉，因为这个是没有必要执行的 后面会新建一个新的
  clearTimeout(timeout);
}
// 最新的执行队列，一秒内如果没有输入，就执行
timeout = setTimeout(function() {
  changeInput();
}, 1000);
```

这两种方法就是`防抖`。如果你一直快速输入，持续10秒，那么只会执行最后一次。

#### 按钮的防重复点击

```html
<button onclick="submit">提交</button>
```

很显然，我们不希望在提交表单的时候发多次请求，事实上，如果我们不做任何处理，在快速点击的时候，就会多次执行submit方法。

下面这个方法就是在第点击，提交方法执行的同时把状态变成不可操作，然后一秒之后再重置，这样的话，用户就不可能再一秒内提交两次。

```js
var flag = true; // 表示按钮可以点击，可以提交表单

// 下面的代码应该是在点击的时候去执行
if(flag) {
  var flag = false; // 在执行submit之前，先把状态改成不可点击
  submit(); 
  // 一秒之后才可以再次提交
  setTimeout(function() {
    flag = true;
  }, 1000)
}
```

还有一种思路是用两次点击的时间来进行计算，计算点击的瞬间的时间和上次点击时间的间隔，如果超过一定值，我们才允许函数执行。

```js
var pre = 0;

// 下面的代码应该是在点击的时候去执行
var now = Date.now();
if(now - pre > 1000) {
  pre = now;
  submit()
}
```

这两种方法就是`节流`。如果你每0.1秒点击一次，不做节流处理的情况下，10秒就需要执行100次。做了节流操作之后，一秒最多执行一次，10秒最多执行10次。

> 总结：防抖是减小执行数量，节流是减小执行频率。

#### 逐步优化

上面的几种方法解决了我们开发过程中遇到的实际问题，但是，我们不能仅仅满足于解决问题，还需要考虑通用的方案，把防抖和节流做成公共方法。我们要避免复制代码，要多复用代码。

之前有一篇文章，讲的是函数式编程，这个可以完美解决我们的问题，我们的目标是把我们原本调用的changeInput、submit改成func(changeInput)、func(submit)这样的形式。func就是我们的公共方法，在其他地方直接调用就行，不用复制防抖和节流的具体实现。参考文章：[函数式编程](https://beat-the-buzzer.github.io/2019/12/01/functional-programming/)

函数入参：执行函数、时间间隔。

执行函数就是我们原本要绑定的事件，在上面的例子中就是changeInput和submit，时间间隔应该是可传的参数，我们可以给个默认值。

函数返回：一个新的函数，用法、参数和changeInput、submit一样。

```js
// 防抖函数
function debounce(func, wait=1000) {
  var timeout;
  return function() {
    var that = this; // 函数的执行上下文
    var args = [].slice.call(arguments); // 函数的参数转成数组
    if(timeout) {
      clearTimeout(timeout);
    }
    timeout = setTimeout(function() {
      func.apply(that, args); // 这里的this是函数调用时的上下文
    }, wait);
  }
}

// 节流函数1
function throttle1(func, wait = 1000) {
  var flag = true;
  return function() {
    var that = this;
    var args = [].slice.call(arguments);
    if(flag) {
      flag = false;
      func.apply(that, args);
       setTimeout(function() {
        flag = true;
      }, wait)
    }
  }
}

// 节流函数2
function throttle2(func, wait = 1000) {
  var pre = 0;
  return function() {
    var now = Date.now();
    var that = this;
    var args = [].slice.call(arguments);
    if(now - pre > wait) {
      pre = now;
      func.apply(that, args);
    }
  }
}
```

经过上面的改造之后，我们就可以在项目中使用这些公共方法了：

```html
<input type="text" onchange="debounce(changeInput, 1500)(this.value)" />
<button onclick="throttle1(submit)">提交</button>
<button onclick="throttle2(submit, 2000)">提交</button>
```

至此，一个公共方法就完成了。

其实我们还可以进一步进行封装，比如我们使用Vue或者React的时候，可以自己对button组件进行封装，新增参数控制是否需要防抖节流，这样，我们就可以直接在组件内部解决了这个问题。