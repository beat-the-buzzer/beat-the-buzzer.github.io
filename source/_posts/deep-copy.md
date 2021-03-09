---
title: 逐步实现深拷贝和浅拷贝
date: 2021-03-08
tags: [JavaScript]
categories: 
- 前端开发
---

### 数据类型和存储方式

JavaScript的数据类型分为**基本数据类型**和**引用数据类型**。

基本数据类型：number，string，boolean，null，undefined，symbol

引用数据类型：统称为Object类型，细分的话，有：Object，Array，Date，Function等。

基本数据类型保存在栈内存，栈内存中分别存储着变量的标识符以及变量的值。

```js
let a = 1; 
```

引用数据类型保存在栈内存，变量名存在栈内存中，值存在于堆内存中，但是栈内存会提供一个引用的地址指向堆内存中的值。

```js
let obj = {
  a: 1
}; // 假设存的地址是xxx
```

所以这两种类型，赋值的时候就能体现出不同之处了：

```js
let b = a; // 重新开辟了一个空间存储b，重新开辟了一个空间存储b的值，也就是1
let obj1 = obj; // 重新开辟了一个空间存储obj1,重新开辟了一个空间存储obj1的值，也就是xxx(上面的假设)
```

所以我们现在得出，obj存的xxx指向的值发生变化的时候，xxx是不变的，此时通过obj2去访问属性，也是会变化的。

### 浅拷贝和深拷贝

浅拷贝：创建一个新的数据，这个数据有着原始数据属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址，所以如果其中一个数据改变了这个地址，就会影响到另一个数据。

深拷贝：深拷贝会拷贝所有的属性，并拷贝属性指向的动态分配的内存。当对象和它所引用的对象一起拷贝时即发生深拷贝。深拷贝相比于浅拷贝速度较慢并且花销较大。在堆中重新分配内存，拥有不同的地址，且值是一样的，复制后的对象与原来的对象是完全隔离，互不影响。

### 深拷贝的实现方式

1. 先 stringify 再 parse

缺点：忽略了undefined、symbol和函数；对象循环引用时，会报错；

2. Object#assign 扩展运算符

缺点：只能实现一层深拷贝

3. jQuery的 $.extends 方法

缺点：需要引用第三方库

3. 使用递归的方式，遍历对象中的属性

step1: 遍历对象中的属性，先创建一个新的对象。遍历属性的时候，如果这个属性值是一个对象，那么我们需要把这个属性值再次做拷贝操作。如果属性值是基本类型，就直接赋值给前面创建的新对象。

```js
function deepClone(obj){
  let objClone = Array.isArray(obj) ? []: {};
  if(obj && typeof obj === "object"){
    for(key in obj){
      if(obj.hasOwnProperty(key)){
        //判断obj子元素是否为对象，如果是，递归复制
        if(obj[key] && typeof obj[key] === "object"){
          objClone[key] = deepClone(obj[key]);
        }else{
          //如果不是，简单复制
          objClone[key] = obj[key];
        }
      }
    }
  }
  return objClone;
}
``` 

step2: 我们需要解决循环引用的问题，如果我们对象里其中一个属性指向了上一层的那个对象，那么我们上面的操作就会死循环。对于循环引用的问题，我们需要在进行拷贝之前，先判断这个对象之前是否被拷贝过，如果被拷贝过，就不需要再进行拷贝，直接返回这个对象就行了。

```js
function clone(target, map = new Map()) {
  if (target && typeof target === 'object') {
    let cloneTarget = Array.isArray(target) ? [] : {};
    if (map.get(target)) {
      // 先去存储的对象里找一下，该对象有没有被拷贝过，如果有 直接返回
      return map.get(target);
    }
    // 下面的代码标识该对象没有被拷贝过，此时我们要先把该对象存起来，然后再进行进一步的拷贝操作
    map.set(target, cloneTarget);
    for (const key in target) {
      cloneTarget[key] = clone(target[key], map);
    }
    return cloneTarget;
  } else {
    return target;
  }
};
```

Map数据结构，允许字符串以外的类型的数据作为Map的key，并且这个key是唯一的。

```js
var myMap = new Map();
var keyObj = {};
myMap.set(keyObj, "和键 keyObj 关联的值");
myMap.get(keyObj); // "和键 keyObj 关联的值"
myMap.get({}); // undefined, 因为 keyObj !== {}
```

step3: 对于一些不可继续拷贝的对象，例如函数、Date()等，我们需要判断具体的类型，使用`Object#toString`方法。

### 小结一下

实现深拷贝的方式，上面说了很多种，有些可能是项目中使用的，有些是比较完整，但是项目中为了图省事没有这么写的。这里注意的是，如果我们是在面试的过程中被问到了这个问题，我们需要回答比较完整的那种方案，可以循循善诱，先把其他几种简单的说一下，然后说出这些方式的缺点，最后为了解决这些缺点，我们使用了XXX方案，这样才是面试官希望听到的答案。

回答要点：

step1: 其他几种方式极其缺点

step2: 描述递归：先判断目标的类型是否是可拷贝的值，然后创建一个新的空对象，再进行遍历属性，如果属性值也是一个可拷贝的值，就继续调用这个拷贝函数，如果不是，就直接返回这个值。然后可以解释一下什么叫做可拷贝的值，以及如何判断该值是可拷贝的值。

step3: 解决循环引用的问题，我们需要一个新的空间来存储已经被拷贝过的对象，每次在遍历对象之前，先去这个空间找一下该对象是否曾经被拷贝过。

