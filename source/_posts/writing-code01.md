---
title: 前端面试之手写代码系列（基础篇）
date: 2020-12-28
tags: [面试]
categories: 
- 面试
---

下面的几个是JS里面比较基础和常用的东西，通过用代码实现，可以让我们更深刻地了解这些方法的底层原理，以及解决了哪些问题。大部分的方法实现在《你不知道的JavaScript》里面都有。

疫情的存在，使得我们几乎都是线上面试，几乎无法现场手写代码。但是，手写代码是面试中很重要的一环，因为这个比较综合、全面、深刻。

### new

 - 先创建一个新的、空的实例对象
 - 将实例对象的原型指向构造函数的原型
 - 将构造函数内部的this，修改为指向实例
 - 返回实例对象：判断构造函数的返回值是否为对象，如果是对象，就使用构造函数的返回值，否则返回创建的对象

 ```js
 function New(func) {
  // 声明一个中间对象，该对象为最终返回的实例
  var res = {};
  if (func.prototype !== null) {
    // 将实例的原型指向构造函数的原型
    res.__proto__ = func.prototype;
  }

  // ret为构造函数的执行结果
  // 将构造函数内部的this指向修改为指向res 
  // 通过apply实现
  var ret = func.apply(res, [].slice.call(arguments, 1));

  // 当我们在构造函数中指定了返回的对象时，new执行的结果就是这个对象
  if ((typeof ret === 'object' || typeof ret === 'function') && ret !== null) {
    return ret;
  }
  // 默认返回res
  return res;
}

var Person = function (name) {
  this.name = name;
}
Person.prototype.getName = function () {
  return this.name;
}
var p1 = New(Person, 'Tom');
var p2 = New(Person, 'Jerry');

p1.getName(); // Tom
p2.getName(); // Jerry
 ```

### call

### apply

### bind

### instanceof

这个原理就是查找原型链，看看实例的原型是否在我们希望判断的对象的原型链上。调用的内置的函数`[[HasInstance]]`

```js
// 参数都是两个对象
function instanceOf(leftObj, rightObj) {
  let proto = leftObj.__proto__;
  let prototype = rightObj.prototype
  while(true) {
    if(proto === null) return false
    if(proto === prototype) return true
    proto = proto.__proto__;
  }
}
```

### 继承

### 排序

### 去重