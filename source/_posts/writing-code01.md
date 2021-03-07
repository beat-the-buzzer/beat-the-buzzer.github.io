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
```js
// 思路：将要改变this指向的方法挂到目标this上执行并返回
Function.prototype.mycall = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('not funciton')
  }
  context = context || window
  context.fn = this
  let arg = [...arguments].slice(1)
  let result = context.fn(...arg)
  delete context.fn
  return result
}
```

### apply

```js
// 思路：将要改变this指向的方法挂到目标this上执行并返回
Function.prototype.myapply = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('not funciton')
  }
  context = context || window
  context.fn = this
  let result
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }
  delete context.fn
  return result
}
```

### bind

```js
// 思路：类似call，但返回的是函数
Function.prototype.mybind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  let _this = this
  let arg = [...arguments].slice(1)
  return function F() {
    // 处理函数使用new的情况
    if (this instanceof F) {
      return new _this(...arg, ...arguments)
    } else {
      return _this.apply(context, arg.concat(...arguments))
    }
  }
}
```

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

### 排序

关于排序，在之前一篇文章里已经写了：[算法系列——排序算法](/2019/12/03/sort/)

如果是求稳，可以使用选择排序或者冒泡排序这样逻辑简单的；

如果想更进一步，可以使用递归方式的归并排序；

还想更进一步，就可以使用稍微有点复杂的快速排序；

当然，这些排序算法的原理还是需要去弄清楚的。

### 去重

去重的方法，之前也有总结过：[数组去重的N个方法](2020/01/11/unique/)

其实面试主要考察的是思路，如果你的试卷上用Set一行解决，其实是很难得到高分的；

求稳的大思路，应该是定义新的数组，然后遍历原数组，如果在新的数组里没有遍历的值的时候，就把数据放到新数组里面；

更进一步的思路，是利用对象的Key、Map 的唯一性；

新奇的思路，寻找元素首次出现的位置是否和当前位置的元素相等；

面试的过程中，我们不仅仅是要提供问题的解决思路，也可以说一下我们这个思路的局限性。面试和工作的思路不一样，面试是为了展示能力，工作是为了解决问题。