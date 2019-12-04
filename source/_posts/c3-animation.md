---
title: CSS3 动画
date: {{ date }}
tags: [CSS, CSS3, 动画]
categories: 
- CSS
series: CSS
---

项目Git地址：[https://github.com/beat-the-buzzer/c3-animation.git](https://github.com/beat-the-buzzer/c3-animation.git)

### 什么是动画

1、动画片、漫画、视频、flash等等，会动的画面都是动画

2、颜色高度等属性的变化（过渡）也是动画

3、CSS3对于动画的实现由过渡和帧动画

### 初识CSS3动画

1、CSS3动画包括transition和animation

2、动画常常和transform属性配合使用

3、transform不是动画属性

### CSS3动画的兼容性

- CSS3动画效果兼容

1、IE10 2012-09-04 开始兼容动画

2、Chrome 4 2010-01-25 -webkit

3、Firefox 5 2011-01-25

- CSS3事件兼容

1、IE 10 2013-10-17

2、Chrome 4 2010-01-25

3、Firefox 5 2011-01-25

### CSS3动画的应用

1、功能性的菜单按钮

2、宣传用的轮播图、页面效果的缓冲

3、页面的切换过渡、小游戏

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/c3-animation/c3-animation-01.png)

### CSS3动画的学习目标

1、熟练使用transition和animation

2、熟练使用动画库，观察动画，得到更好的实现方案

3、复杂动画的开发和实现

### transition的使用

#### Transition基础

1、属性名称（property）

2、过渡时间（duration）和延迟时间（delay）

3、时间函数（timing-function）

```css
.box {
  width: 100px;
  height: 100px;
  background: #000;
}
.demo-1 {
  /* 属性名 过渡时间 时间函数 延时*/
  /* 时间函数 linear ease */
  /* 鼠标放在元素上延时两秒后，宽度匀速从100px增长到500px */
  transition: width 2s linear 2s;
}
.demo-1:hover {
  width: 500px;
}
```

上面的例子中，矩形宽度平缓增长，看起来有一点卡顿，我们可以去修改时间函数。实际上，linear和easa都是`贝塞尔曲线（cubic-bezier）`的特殊情况，我们可以在控制台上去调试运动的曲线，从而达到我们想要的运动，如下图所示：

我们可以点击控制台上的时间函数：

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/c3-animation/c3-animation-02.png)

我们可以调整运动曲线，得到bezier属性值：

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/c3-animation/c3-animation-03.png)

#### 注意事项

1、display: none;不能和transition一起使用

transition需要读取元素的初始属性值，display为none的情况下，很多属性都读不到

2、transition后面尽量不要跟all

transition需要去检查元素属性的变化，如果使用all，就会把所有的属性都去检查一遍，造成大量资源的浪费

3、解决闪动问题，使用3D属性

perspective、backface-visibility、translate3d(0,0,0)

### animation的使用

#### Animation基础

1、动画名称（name）--@keyframes

2、过渡时间（duration）和延迟时间（delay）

3、时间函数（timing-function）

```css
.box {
  width: 100px;
  height: 100px;
  background: #000;
}
.demo-1 .cell {
  width: 200px;
  height: 200px;
  background: red;
}

.demo-1:hover .cell{
  animation: move 2s linear;
}

@keyframes move{
  100% {
    transform: translateX(200px);
  }
}
```

4、播放次数（iteration-count）

5、播放方向（direction），是否轮流播放和反向播放

6、停止播放的状态（fill-mode），是否暂停（play-state）

#### 注意事项：

1、解决了transition和display:none的冲突问题

2、实现跳动的元素

#### Demo——自由落体

下面是一个Demo，实现物体的自由落体运动。

自由落体运动的关键在于时间函数，选择合适的时间函数会让物体运动得更加逼真。

首先，我观察了一下系统自带的时间函数：

linear：动画从头到尾的速度是相同的

![linear效果](https://gitee.com/beat-the-buzzer/pictures/raw/master/c3-animation/c3-animation-04.png)

ease：默认。动画以低速开始，然后加快，在结束前变慢

![ease效果](https://gitee.com/beat-the-buzzer/pictures/raw/master/c3-animation/c3-animation-05.png)

ease-in: 动画以低速开始

![ease-in效果](https://gitee.com/beat-the-buzzer/pictures/raw/master/c3-animation/c3-animation-06.png)

ease-out: 动画以低速结束

![ease-out效果](https://gitee.com/beat-the-buzzer/pictures/raw/master/c3-animation/c3-animation-07.png)

ease-in-out: 动画以低速开始和结束

![ease-in-out效果](https://gitee.com/beat-the-buzzer/pictures/raw/master/c3-animation/c3-animation-08.png)

观察这些轨迹，我发现，贝塞尔曲线最终就是物体的位移轨迹，我们可以通过修改控制点的方式来修改位移。

显然，自由落体运动是初速度为0，加速度为g的匀加速直线运动，所以最终的位移曲线应该是一个定点在原点，开口向上的抛物线。

大致在浏览器上画了一个差不多的曲线，值得注意的是，由于自由落体的初速度为0，初始位置的切线必然和x轴平行，得到了贝塞尔曲线的函数方程：

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/c3-animation/c3-animation-09.png)

最终运行效果：

[点击查看运行效果](https://beat-the-buzzer.github.io/c3-animation/)

#### Demo——红包雨效果

[点击查看项目源码](https://github.com/beat-the-buzzer/c3-animation/tree/master/demo)

[点击查询红包雨效果](https://beat-the-buzzer.github.io/c3-animation/demo/rain.html)