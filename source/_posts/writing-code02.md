---
title: 前端面试之手写代码系列（应用篇）
date: 2020-12-28
tags: [面试]
categories: 
- 面试
---

### 防抖与节流

关于防抖与节流，在之前一篇文章里已经写了：[防抖节流操作总结](/2020/09/12/debounce-throttle/)

文章里还做了科里化的操作，就是下面即将要写的，目的是把防抖和节流做成一个公共的方法。

### 科里化与函数式编程

关于科里化与函数式编程，在之前一篇文章里已经写了：[函数式编程](/2019/12/01/functional-programming/)

文章里有大量的举例和应用场景。

### 深拷贝

简单版本1：先`stringfy`再`parse`，无法处理值为日期、函数等类型的数据；

简单版本2：ES6扩展运算符，只能处理一层数据，对于深层次的对象无法处理；

简单版本3：ES6 `Object.assign({}, ...)`

简单版本4：Jquery `$.extends()`方法

算法版本：主要是用了递归的思想

```js
const deepClone = (target, cache = new WeakMap()) => {
  if(target === null || typeof target !== 'object') {
    return target
  }
  if(cache.get(target)) {
    return target
  }
  const copy = Array.isArray(target) ? [] : {}
  cache.set(target, copy)
  Object.keys(target).forEach(key => copy[key] = deepClone(obj[key], cache))
  return copy
}
```

### Rem基本设置

关于Rem基本设置，在之前一篇文章里已经写了：[移动端适配之淘宝方案分析](/2020/08/26/mobile-adaptation/)

```js
function refreshRem() {
  var width = docEl.getBoundingClientRect().width;
  if (width / dpr > 540) {
    width = 540 * dpr;
  }
  var rem = width / 10;
  // 意味着100%的宽度就是10rem
  docEl.style.fontSize = rem + 'px';
  flexible.rem = win.rem = rem;
}
window.addEventListener('resize', function () {
  clearTimeout(tid);
  tid = setTimeout(refreshRem, 300);
}, false);
```

### Promise
