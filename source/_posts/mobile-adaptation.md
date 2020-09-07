---
title: 移动端适配之淘宝方案分析
date: 2020-08-26
tags: [CSS,CSS3,JavaScript,移动端适配]
categories: 
- 移动端适配
series: 移动端适配
---

这里主要是对淘宝适配方案：flexible进行源码分析。

```js
// 封装全局对象的常用方法，减少变量污染
; (function (win, lib) {
  var doc = win.document;
  var docEl = doc.documentElement;
  var metaEl = doc.querySelector('meta[name="viewport"]'); // 获取标签推荐使用querySelector
  var flexibleEl = doc.querySelector('meta[name="flexible"]');
  var dpr = 0;
  var scale = 0;
  var tid;
  var flexible = lib.flexible || (lib.flexible = {});

  // doc取文档的document对象

  // docEl取到了我们html为根的整个dom树，后期我们需要向html插入dpr和font-size就是用这个属性

  // metaEl取meta标签里面name＝viewport的元素，没有返回空，为了判断是否有自己设置的meta值来做一些逻辑

  // flexibleEl取meta标签里面name＝flexible的元素，没有返回空，为了判断用户是否自己手动的设置了一些meta值

  // dpr表示的是取你手机屏幕的dpr值 设备像素比

  // scale表示取你meta里面的scale，会根据不同的scale设置dpr

  // 先定义好变量，尤其是获取dom元素的地方，都要缓存在变量里，不推荐每次都用document.documentElement去访问DOM

  if (metaEl) {
    // 如果设置了meta viewport，就读取设置的缩放比例
    console.warn('将根据已有的meta标签来设置缩放比例');
    var match = metaEl.getAttribute('content').match(/initial\-scale=([\d\.]+)/);
    if (match) {
      scale = parseFloat(match[1]);
      dpr = parseInt(1 / scale);
    }
  } else if (flexibleEl) {
    // 如果设置了meta flexible
    var content = flexibleEl.getAttribute('content');
    if (content) {
      var initialDpr = content.match(/initial\-dpr=([\d\.]+)/);
      var maximumDpr = content.match(/maximum\-dpr=([\d\.]+)/);
      if (initialDpr) {
        dpr = parseFloat(initialDpr[1]);
        scale = parseFloat((1 / dpr).toFixed(2));
      }
      if (maximumDpr) {
        dpr = parseFloat(maximumDpr[1]);
        scale = parseFloat((1 / dpr).toFixed(2));
      }
    }
  }

  // 如果没有获取到了这两个值，就使用系统参数获取到的设备像素比
  if (!dpr && !scale) {
    var isAndroid = win.navigator.appVersion.match(/android/gi);
    var isIPhone = win.navigator.appVersion.match(/iphone/gi);
    var devicePixelRatio = win.devicePixelRatio;
    if (isIPhone) {
      // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
      if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) {
        dpr = 3;
      } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)) {
        dpr = 2;
      } else {
        dpr = 1;
      }
    } else {
      // 其他设备下，仍旧使用1倍的方案
      dpr = 1;
    }
    scale = 1 / dpr;
    // 这一行十分关键，如果是2倍屏，一切都放大两倍的时候，由于这一行的存在
  }

  docEl.setAttribute('data-dpr', dpr);
  // 这个设置，可以用于定义不同的样式，如果没有动态修改initial-scale 其实也不需要动态修改

  if (!metaEl) {
    metaEl = doc.createElement('meta');
    metaEl.setAttribute('name', 'viewport');
    metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
    if (docEl.firstElementChild) {
      docEl.firstElementChild.appendChild(metaEl);
    } else {
      var wrap = doc.createElement('div');
      wrap.appendChild(metaEl);
      doc.write(wrap.innerHTML);
    }
  }
  // 当我们之前没有设置metaEl标签的话，那么需要我们手动的去创建meta标签，实现移动端的适配，也就是创建一个默认的meta

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

  // 屏幕宽度发生改变的时候，重新设置根元素的font-size
  win.addEventListener('resize', function () {
    clearTimeout(tid);
    tid = setTimeout(refreshRem, 300);
  }, false);
  win.addEventListener('pageshow', function (e) {
    if (e.persisted) {
      clearTimeout(tid);
      tid = setTimeout(refreshRem, 300);
    }
  }, false);
  // 监听window里面的resize和pageshow方法来实现css样式的重绘

  if (doc.readyState === 'complete') {
    doc.body.style.fontSize = 12 * dpr + 'px';
  } else {
    doc.addEventListener('DOMContentLoaded', function (e) {
      doc.body.style.fontSize = 12 * dpr + 'px';
    }, false);
  }
  // 设置body的font-size，是为了给页面元素一个初始的font-size


  refreshRem();

  flexible.dpr = win.dpr = dpr;
  flexible.refreshRem = refreshRem;
  flexible.rem2px = function (d) {
    var val = parseFloat(d) * this.rem;
    if (typeof d === 'string' && d.match(/rem$/)) {
      val += 'px';
    }
    return val;
  }
  flexible.px2rem = function (d) {
    var val = parseFloat(d) / this.rem;
    if (typeof d === 'string' && d.match(/px$/)) {
      val += 'rem';
    }
    return val;
  }

})(window, window['lib'] || (window['lib'] = {}));
```

从代码里我们可以看到，这里dpr的值就是viewport中的initial-scale属性，一般情况下，这个值是不会变的。

最终我们得到的结果就是，根据屏幕宽度，动态计算出了根元素的font-size值。

当然，我们还有其他的方法动态修改根元素的font-size：

 - 媒体查询

 优点：CSS的方式，不会出现闪动

 缺点：代码冗余，适配不够精确

 - 用calc(vw / x)计算

 优点：简单、代码量少

 缺点：需要支持vm（未来会逐步支持）
