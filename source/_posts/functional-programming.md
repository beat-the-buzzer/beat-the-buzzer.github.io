---
title: 函数式编程
date: 2019-12-01
tags: [JavaScript,函数式编程]
categories: 
- 前端开发
---

Git地址： [https://github.com/beat-the-buzzer/functional-programming.git](https://github.com/beat-the-buzzer/functional-programming.git)

#### 抛砖引玉

从一道容易出错的题开始：

```js
['1', '2', '3'].map(parseInt);
```

看到这样的代码，我们就能瞬间理解代码这样写的意图，就是把数组中的所有元素都调用parseInt方法，输出一个纯数字的数组。但是我们在控制台上打印出来的结果，和预期不符：

```js
[1, NaN, NaN]
```
造成这种情况的原因是，我们想当然地认为了map方法是让数组的值都执行一次map传入的函数，所以，我们的思路是这样的：

```js
parseInt('1');
parseInt('2');
parseInt('3');
```

但事实上，map方法给处理函数传的参数有三个，分别是value,index，array，另外，parseInt这个方法可以接受的参数有两个，第一个是待转换的字符串，第二个是进制。因此，上面的语句转换一下，就是这样：

```js	
['1','2','3'].map((value,index,arr) => parseInt(value,index,arr));
```

所以，我们实际上调用了这样的方法：

```js
parseInt('1',0);
parseInt('2',1);
parseInt('3',2);
```

parseInt第二个参数是0的时候，表示按十进制转换；
parseInt第二个参数是1的时候，没听说过一进制，所以是NaN；
parseInt第二个参数是2的时候，表示按二进制转换，但是很遗憾，二进制的世界里只有0和1，没有3，如果我们把数组的最后一个元素改成字符串'11'，转换之后，就会得到3；

所以最开始的问题，得到了[1, NaN, NaN]的结果。

正确的打开方式是这样：

```js
['1','2','3'].map((value) => {
  return parseInt(value);
});
```

我们使用下面的方法，也就是**函数式编程**的思想：

```js
const unary = (fn) => fn.length === 1 ? fn : (arg) => fn(arg)
```

这是一个函数，它的参数是另一个函数，我们检查传入的函数的参数列表，如果传入的函数只有一个参数，那么就不做处理，如果有多个参数，那么就把这个函数转换成接收一个参数的函数。因此：

```js	
['1','2','3'].map(unary(parseInt));
```

就能得到[1, 2, 3]的结果了。

> 《JavaScript ES6函数式编程入门经典》，文中的所有内容都来自于这本书。其实，我研究一下函数式编程，有两个原因，一个是这是我的知识盲点，虽然偶尔听到过类似“函数式编程、柯里化”等术语，但是我依旧没有好好地学习这方面的知识；另一个原因是在学习Redux的时候，里面有一个connect函数，这是一个高阶函数，但是redux里面的源码我看不懂，所以我想先充实一下自己的知识，然后再去学习更深层次的东西。

#### 什么是函数式编程

言归正传，**高阶函数是接受函数作为参数并且/或者返回函数作为输出的函数。**

**函数式编程的核心思想是：把操作抽象为函数。**

举个例子：我们要遍历数组，最初级的方式是什么，我们不经过大脑也能写出来：

```js
var array = [1, 2, 3];
for(var i = 0; i < array.length; i++) {
  console.log(array[i]);
}
```

我们需要把遍历的操作抽象出来：

```js
const forEach = (array, fn) => {
  let i;
  for(i = 0; i < array.length; i++) {
    fn(array[i]);
  }
}
```

#### 柯里化和偏函数应用

##### 术语介绍

 - 一元函数——只接受一个参数的函数
 - 二元函数——接受两个参数的函数
 - 变参函数——接受可变数量参数的函数

```js
const variadic = (a,...variadic) => {
  console.log(a);
  console.log(variadic);
}
variadic(1,2,3);
// 1
// [2,3]
```

##### 柯里化

柯里化其实并不是什么太复杂的概念，只是因为我们对它陌生罢了。

> 柯里化是把一个多参数函数转换为一个嵌套的一元函数的过程。

例如：

```js
const add = (x,y) => x + y;
const addCurried = x => y => x + y;
```

我们可以这样调用函数：

```js
addCurried(4)(4); // 8
```

我们用es5写一个柯里化函数，这样可以更好地看到嵌套的效果：

```js
// 这个函数名看起来好眼熟啊！
const curry = (binaryFn) => {
  return function(firstArg) {
    return function(secondArg) {
      return binaryFn(firstArg,secondArg);
    }
  }
}

let autoCurriedAdd = curry(add);
autoCurriedAdd(2)(2); // 4
```

##### 为什么需要柯里化？

实际上，平时工作中很少用到柯里化函数，但是，柯里化和“闭包、arguments、apply、call、bind”有很强的联系。比起运用柯里化，我们更需要注意的是在学习柯里化的过程中强化我们的基础知识。

```js
const curry = (fn) => {
  if (typeof fn !== 'function') {
    throw Error('No function Provided');
  }
  return function curriedFn(...args) {
    if (args.length < fn.length) {
      return function() {
        return curriedFn.apply(null,args.concat(
          [].slice.call(arguments)
        ))
      }
    };
    return fn.apply(null,args);
  };
};
```

使用curry函数包裹我们需要调用的函数，就可以完成柯里化了：

```js
const add = (a, b, c) => a + b + c;
```

正常调用方式是：

```js
add(1, 2, 3);
```

柯里化的调用方式是：

```js
curry(add)(1)(2)(3);
curry(add)(1)(2,3);
```

很遗憾，上面的柯里化调用方法只能处理三个参数，因为我们的add函数有三个参数。我试图写一个可变参数的add函数：

```js
const add = (...args) => args.reduce((x, y) => x + y);
```

但是，这个函数不能使用上面的柯里化方法，原因是，这个函数的length值始终是0。

##### 偏函数应用

> Partial Application(偏函数应用) 是指使用一个函数并将其应用一个或多个参数，但不是全部参数，在这个过程中创建一个新函数。

我们可以通过bind来实现这个效果：

```js
function add3(a, b, c) { return a + b + c; }  
add3(2, 4, 8);  // 14

var add6 = add3.bind(this, 2, 4);  
add6(8);  // 14 
```

上面这个例子中，我们定义了add6这个函数，这个函数是add3固定了两个参数之后生成的，add6只接受一个参数，并且计算的结果是给add6的参数加上6。

如果你已经有了 curry() ，那么意味着你也已经有偏函数应用！

```js
var add6 = curry(add3)(2)(4);  
add6(8); // 14
```

更新add方法：

我在书上看到了一个add方法，可以计算出任意参数的add

```js
add(1,2,3,4);
add(1)(2)(3)(4);
add(1,2)(3,4);
add(1,2,3)(4);
```

代码如下：[https://github.com/beat-the-buzzer/functional-programming/blob/master/add.js](https://github.com/beat-the-buzzer/functional-programming/blob/master/add.js)

add方法必须返回一个函数，但是我们的目标是计算累加值，所以使用了重写toString的方式，来计算累加值。

```js
function add() {
  var args = [].slice.call(arguments);
  var adder = function() {
    // 将参数用闭包捕获 args
    var adder_temp = function() {
      args.push(...arguments);
      return adder_temp;
    };
    adder_temp.toString = function() {
      return args.reduce(function(a, b) {
        return a + b;
      });
    }
    return adder_temp;
  }
  return adder(...args);
}
var a = add(1, 2, 3, 4);
var b = add(1)(2)(3)(4);
var c = add(1, 2)(3, 4);

console.log(+a); // 10
console.log(b.toString()); // 10
console.log(`${c}`); // 10
```

#### 应用Demo——处理请求返回

下面来实现一个高阶函数，这是我平时工作的时候遇到的一个新需求。

看下面这段代码，是我们项目中已有的调用接口的方式，我们在调用接口的时候，需要在我们自己的业务模块里面处理数据，例如，把GRID0的每一项进行分割。

```js
function getData(onSend, success, error, other) {
  var res = {
    data: {
      success: true,
      msg: '假设返回的数据',
      GRID0: [
        '123|456|789',
        'abc|def|ghi'
      ],
      KEY1INDEX: 0,
      KEY2INDEX: 1,
      KEY3INDEX: 2
    }
  };
  success(res);
}
getData({}, function(data) {
  // 处理数据
});
```

现在我们要把处理数据的代码写在公共的部分，也就是说，在调用接口的时候，成功的回调里面直接就能拿到处理后的数据。我觉得这样的需求，很适合用高阶函数。我们的目标是：`HOFgetData(getData)`这个函数，第二个参数是个回调函数，我们要让这个回调函数的参数变成我们想要的数据格式。下面我将一一实现。

1、我们定义的HOFgetData传入一个函数，返回一个新的函数：

```js
function HOFgetData () {
  var args = [].slice.call(arguments);
  var newFunc = function (obj, success) {
    args[0].call(null, obj, success);
  }
  return newFunc;
}
```

这段代码，就是实现不做任何操作的高阶函数。

2、我们要对成功的回调函数进行处理，说具体点，就是成功的回调函数的参数。所以，我又写了一个高阶函数，来处理成功的回调函数，使得`hofsuccess(success)`的参数是我们需要的数据格式。

hofsuccess也是传入一个函数，返回一个新函数：

```js
var hofsuccess = function () {
  var tempFunc = [].slice.call(arguments); // 传入的success函数
  return function () {
    var temp = [].slice.call(arguments); // 得到success的参数，就是返回值
    var data = temp[0].data;
    // 处理数组GRID0
    var LIST0 = data.GRID0.map(value => {
      var item = value.split('|');
      return {
        KEY1: item[data.KEY1INDEX],
        KEY2: item[data.KEY2INDEX],
        KEY3: item[data.KEY3INDEX]
      }
    });
    var obj = {
      LIST0: LIST0,
      msg: data.msg
    };
    tempFunc[0](obj);
  }
};
```

3、HOFgetData里面取调用新的成功的回调：

```js
var newFunc = function (obj, success) {
  args[0].call(null, obj, hofsuccess(success));
}
```

4、调用新的函数

```js
var obj = {
  param: 'param'
};

/*
* 高阶函数赋值，得到的newGetData是个函数，并且这个函数的参数个getData一样
* 这里第二个参数在HOFgetData内部做了高阶函数处理，使得返回的结果是处理后的数据结构
*/
var newGetData = HOFgetData(getData);

newGetData(obj, function (data) {
  console.log(data);
});
```

返回的结果是：

```js
{ 
  LIST0: [{
    KEY1: "123", 
    KEY2: "456", 
    KEY3: "789"
  }, {
    KEY1: "abc", 
    KEY2: "def", 
    KEY3: "ghi"
  }], 
  msg: "假设返回的数据"
}
```

[点击查看完整代码整合](https://github.com/beat-the-buzzer/functional-programming/blob/master/hof.js)

得到的newGetData就是我们想要的新的调用函数的方法，并且，这个方法对原有的功能不会造成影响。

#### 应用Demo——缓存效果

下面还有一个高阶函数的应用，可以应用在平时的工作中。

经常会有这样的业务场景：查询某一天的历史数据。下面，我将实现一个缓存效果，曾经访问过的值暂时缓存起来。当然，不是使用localStorage或者sessionStorage，而是高阶函数的方式。

```js
var data = 0; // 模拟数据
function postData(date) {
  // ... 省略逻辑
  console.log('发送请求，得到数据');
  var result = (++data) + ':' + date; // 模拟数据
  return result;
}

// 定义的高阶函数，传入的参数是一个函数，返回一个新的函数
function superPostData(fn) {
  var cache = {}; // 缓存的对象
  return function() {
    var args = [].slice.call(arguments);
    var cachedItem = cache[args[0]];
    if(cachedItem) {
      console.log('从缓存中取数据');
      return cachedItem;
    } else {
      cache[args[0]] = fn.apply(fn, args); // 缓存结果
      return cache[args[0]];
    }
  }
}

var newPostData = superPostData(postData); // 使用高阶函数，产生一个新的函数
newPostData('20190101'); // 发送请求，得到数据
newPostData('20190102'); // 发送请求，得到数据
newPostData('20190101'); // 从缓存中取数据
```

[点击查看完整代码整合](https://github.com/beat-the-buzzer/functional-programming/blob/master/cache.js)

从例子中，我们可以看到，第一次查询20190101的数据，缓存里没有，于是调用接口，得到数据并缓存；然后，查询20190102的数据，缓存里也没有，于是调用接口，得到数据并缓存。当我们再次查询20190101的数据，发现缓存里有这条数据，于是直接取出来，这样就减少了http请求，提升了性能。

以上就是我想要讲的关于函数式编程的所有内容，如果能弄懂上面的add函数，相信你会对函数、闭包这一类的概念有了更深的理解。建议大家在以后学习React的时候，试着去写一些高阶组件，相信不会太难理解。

其他应用：

[防抖节流操作总结](https://beat-the-buzzer.github.io/2020/09/12/debounce-throttle/)
