---
title: 移动端适配前置知识学习
date: 2020-08-26
tags: [CSS,CSS3,JavaScript,移动端适配]
categories: 
- 移动端适配
series: 移动端适配
---

什么是移动端适配？

移动端页面，常见的有webview的H5页面，手机浏览器端的页面等等；目的是在不同尺寸的手机设备上，页面“相对性的达到合理的展示（自适应）”或者“保持统一效果的等比缩放（看起来差不多）”。

在讲移动端适配之前，我们需要弄清楚一些基本的概念。

#### viewport

参考资料： [https://www.cnblogs.com/2050/p/3877280.html#!comments](https://www.cnblogs.com/2050/p/3877280.html#!comments)

```
visual viewport：可见视口，即屏幕宽度；
layout viewport：布局视口，即DOM宽度；
idea viewport：理想适口，使布局视口就是可见视口；
设备宽度（visual viewport）与DOM宽度（layout viewport），scale的关系是：（visual viewport）= （layout viewport）* scale
获取屏幕宽度（visual viewport）的尺寸：window.innerWidth/Height
获取DOM宽度（layout viewport）的尺寸：document.documentElement.clientWidth/Height
```

移动设备上的viewport就是设备的屏幕上能用来显示我们的网页的那一块区域，在具体一点，就是浏览器上(也可能是一个app中的webview)用来显示网页的那部分区域，但viewport又不局限于浏览器可视区域的大小，它可能比浏览器的可视区域要大，也可能比浏览器的可视区域要小。在默认情况下，一般来讲，移动设备上的viewport都是要大于浏览器可视区域的，这是因为考虑到移动设备的分辨率相对于桌面电脑来说都比较小，所以为了能在移动设备上正常显示那些传统的为桌面浏览器设计的网站，移动设备上的浏览器都会把自己默认的viewport设为980px或1024px（也可能是其它值，这个是由设备自己决定的），但带来的后果就是浏览器会出现横向滚动条，因为浏览器可视区域的宽度是比这个默认的viewport的宽度要小的。下图列出了一些设备上浏览器的默认viewport的宽度。

![https://images0.cnblogs.com/blog/130623/201407/300958475557219.png](https://images0.cnblogs.com/blog/130623/201407/300958475557219.png)

我们新建一个空的html文件，里面什么都不加，把body的宽度设置为100vw，在浏览器的iPhone模式下查看，发现body的实际宽度是980px。实际上，真正的iPhone(以iPhone 6/7/8)为例，实际屏幕的宽度只有375px。如果我们什么都不做，直接开发页面，就会出现横向滚动条，这个明显是不好的体验。

#### 像素 CSS像素、物理像素、逻辑像素、设备像素比

参考资料：[https://github.com/jawil/blog/issues/21](https://github.com/jawil/blog/issues/21)

CSS中的px和设备中的px是不一样的。在iphone3，它的分辨率为320x480，此时css像素和设备的物理像素是一样的。iphone4开始，分辨率变成640x960，但屏幕尺寸却没变化，此时css中的1px可以展示两个物理像素，这样，屏幕上的元素会变得更清晰。

逻辑像素可以看做是CSS中的像素，物理像素代表的是设备内部计算使用的像素。

window对象有一个devicePixelRatio属性，它的官方的定义为：设备物理像素和设备独立像素的比例，也就是 devicePixelRatio = 物理像素 / 独立像素。

#### 用meta标签

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
```

  属性 | 定义 |
:----: | :-: | 
width | 设置layout viewport  的宽度，为一个正整数，或字符串"width-device" | 
initial-scale | 设置页面的初始缩放值，为一个数字，可以带小数 | 
minimum-scale | 允许用户的最小缩放值，为一个数字，可以带小数 | 
maximum-scale | 允许用户的最大缩放值，为一个数字，可以带小数 | 
height | 设置layout viewport  的高度，这个属性对我们并不重要，很少使用 | 
user-scalable | 是否允许用户进行缩放，值为"no"或"yes", no 代表不允许，yes代表允许 | 

```
屏幕尺寸、屏幕分辨率-->对角线分辨率/屏幕尺寸-->屏幕像素密度PPI
                                             |
              设备像素比dpr = 物理像素 / 设备独立像素dip(dp)
                                             |
                                       viewport: scale
                                             |
                                          CSS像素px
```

#### 字体大小的控制

首先不需要用户缩放和横向滚动条就能正常的查看网站的所有内容；第二，显示的文字的大小合适，比如一段14px大小的文字，不会因为在一个高密度像素的屏幕里显示得太小而无法看清，理想的情况是这段14px的文字无论是在何种密度屏幕，何种分辨率下，显示出来的大小都是差不多的。当然，不只是文字，其他元素像图片什么的也是这个道理，ppk把这个viewport叫做 ideal viewport，也就是第三个viewport——移动设备的理想viewport。

#### Rem

Rem是一个相对的长度单位，'R'是root的意思，也就是相对于根元素的字体大小来计算长度。如果我们让根元素和屏幕宽度有一个对应关系，那么我们的间距、字体等等任何使用Rem做单位的值，都会和屏幕宽度有对应关系，其实移动端适配的核心思想就在这里。

> 争议：字体是否需要使用Rem?

从我实际的使用效果来看，字体大小使用Rem做单位是没有太多问题的，可以满足平时的开发需求。

#### 小结
适配不同屏幕宽度以及不同dpr，通过动态设置viewport(scale=1/dpr) + 根元素fontSize + rem, 辅助使用vw/vh等来达到适合的显示；

若无需适配可显示1px线条，也可以不动态设置scale，只使用动态设置根元素fontSize + rem + 理想视口；

当视口缩放，计算所得的根元素fontSize也会跟着缩放，即若理想视口(scale=1), iPhone6根元素fontSize=16px; 若scale=0.5, iPhone6根元素fontSize=32px; 因此不必担心rem的计算；

全部用rem即可，包括字体大小，不用px；

px为单位的元素，需根据dpr有不同的大小，如大小12px, dpr=2则采用24px, 使用sass mixin简化写法；

配合scss函数，简化px2rem转换，且易于维护(若需修改$base-font-size, 无需手动重新计算所有rem单位)；

px2rem函数的base-font-size=32px，参数传值直接为设计图标注尺寸；

使用iPhone6(375pt)二倍设计图:宽度750px；

切图使用三倍精度图，以适应三倍屏

参考资料 [https://www.jianshu.com/p/b13d811a6a76](https://www.jianshu.com/p/b13d811a6a76)
