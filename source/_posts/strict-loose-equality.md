---
title: 宽松相等和严格相等
date: 2019-12-02
tags: [JavaScript, Vue]
categories: 
- 前端开发
---

这篇文章有一个Vue标签，是因为做了一个Vue相关的项目，列举了几乎所有宽松相等和严格相等的情况，项目地址：[https://github.com/beat-the-buzzer/vue-equality-table.git](https://github.com/beat-the-buzzer/vue-equality-table.git)

#### 提出问题

宽松相等（loose equals） == 和严格相等（strict equals）=== 一直困扰着众多开发者。和Java这样的语言不同，JavaScript更加自由，但是自由的代价就是容易出错。下面就来深入研究一下宽松相等和严格相等。

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/imooc/imooc008-1.jpg)

> 误解：== 检查值是否相等，=== 除了检查值之外，还检查了类型是否相同。

听起来好像很有道理，但是，上面的说法并不准确。

> 正解：== 允许在比较的过程中进行强制类型转换， === 不允许。

ESLint中有一个规则：

> Use === and !== instead of == and !=. This avoids type coercion errors.

这就是所谓的“严师出高徒”。严格相等可以避免一些意外的错误，下面我们来踩一下宽松相等带来的坑。

#### 类型相同的情况

类型相同，就好办了，我们只需要比较两边的值是否相等就行了，例如：

```js
110 == 110;
'abc' == 'abc';
```

这里有一些特殊情况：

```js
NaN == NaN; // false
+0 == -0; // true
{} == {}; // false
```

JavaScript中，对象是引用类型，所以，两个对象字面量分别属于两个不同的存储空间，所以是不相等的。

#### 类型不同，强制转换

##### string VS number

```js
var a = '45';
var b = 45;
a == b; // true
```

其实这个是我们经常会遇到的情况，尤其是在前后端交互的时候。有时候，后台人员自己也不知道什么时候就把数字转成字符串了。规范中，这种情况的比较是把字符串转成了数字，类似于：

```js
Number(a) == b
```

##### boolean VS others

 == 最容易出现错误的地方就是和布尔类型的值进行相等比较

```js
var a = '45';
var b = true;
a == b; // false
```

我们知道，'45'是个真值，但是'45'却不等于true。

从规范上说，如果布尔类型和其他类型比较，就需要把布尔类型转成数字类型，类似于

```js
a == Boolean(b);
=> '45' == 1 // false
```

但是，为什么说这个容易出错呢？那是因为我们经常写这样的代码：

```js
if(a) {
  console.log('a是真值');
}
```

我们受这样的代码的影响，自然而然地认为'45' == true 成立。事实上，上面的代码应该写成这样：

```js
if(Boolean(a)) {
  console.log('a是真值');
}
```

知识的学习最后都需要去实践，根据上面说的，我们在平时写代码的时候，就需要自己格外注意：

```js
var a = '45';
// 错误的示范
if(a == true) {
  // do something
}
// 也是错误的示范
if(a === true) {
  // do something
}
// 可以这么写
if(a) {
  // do something
}
// 优化写法1
if(Boolean(a)) {
  // do something
}
// 优化写法2
if(!!a) {
  // do something
}
```

为了减少出错的概率，我们在平时写代码的时候，坚决不要写类似 `a == true` 这样的代码。

##### null & undefined

null 和 undefined 都是假值，它们存在这样的关系：

```js
null == undefined // true
```

我们知道，还有其他的假值，false、''、0，这些值和null、undefined有什么关系呢？

答案是没有什么关系，null和undefined互相相等或者与其本身相等。

```js
null == false    // false
undefined == 0    // false
```

##### object VS others

对象和基本类型的比较，都是把对象转成基本类型。例如：

```js
[45] == 45
```

首先，[45]调用valueOf方法，得到的结果还是[45]，不是基本类型，然后调用了toString方法，得到了"45"，然后就把问题转化成了"45" == 45

来看一个问题，这个问题在开发中几乎不会出现，只是为了加深理解：

[1] == 0 在什么情况下成立？

根据上面的结论，我们重写valueOf方法就行了：

```js
Array.prototype.valueOf = function() {
  return 0;
}

[1] == 0; // true 
```

这就是传说中的指鹿为马！

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/imooc/imooc008-2.jpg)

##### 特殊情况

 - '' == 0

上面这种情况，其实之前也说到了，我们要弄清楚被转换的是哪一个。我们都知道，'0' == 0是成立的，但是，'' == 0也成立，这就有点颠覆了认知。但是，我们只需要明白一点，就是：

> 字符串和数字的相等比较，要把字符串转换成数字。Number('')的结果是0

 - [] == ![]

这个更加颠覆认知了，似乎这个值既是真值又是假值。

我们还是一步一步来：

```js
[] == ![]
<=> [] == false
<=> [] == 0
<=> '0' == 0
<=> 0 == 0（显然成立）
```

这样，我们就得到了 [] == ![] 为 true的结论。

#### Demo

GitHub用户dorey制作了几个图表，其中包括了各种 == === 和 if()的值。我自己使用了Vue-cli把项目重构了一遍，希望能有所收获。

项目运行截图：

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/imooc/imooc008-3.png)

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/imooc/imooc008-4.png)

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/imooc/imooc008-5.png)

相信看到这里，第一张图片里面，派大星的问题应该可以轻松解决了。

项目演示地址：

[https://beat-the-buzzer.github.io/vue-equality-table/#/](https://beat-the-buzzer.github.io/vue-equality-table/#/)


