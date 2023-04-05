---
title: iOS微信自动播放视频的处理方案
date: 2023-04-05
tags: [疑难问题, 视频]
categories: 
- 疑难问题
---

### iOS微信自动播放视频的处理方案

现象：iOS的微信会阻止视频和音频的加载，因此我们设置的autoplay属性是不会生效的，并且在iOS微信上，视频的duration也不会被加载出来。

处理方式：

```js
document.addEventListener('WeixinJSBridgeReady', play, false)

function play() {
	// 如果视频出现在视口内，并且设置了自动播放，就去执行播放操作
	if (isElementInView(videoDom) && autoplay) {
		const promise = videoDom.play() // 返回一个Promise
		if (promise != undefined) {
			promise
				.then(() => {
					videoDom.play()
				})
				.catch(error => {
					videoDom.muted = false;
				})
		}
	}
}

function isElementInView() {
	const rect = el.getBoundingClientRect()
	const inView =
		rect.top >= 0 &&
		rect.left >= 0 &&
		rect.bottom <= (window.innerHeight || document.documentElement.clientHeight) &&
		rect.right <= (window.innerWidth || document.documentElement.clientWidth)
	return inView
}
```

这种方式可以成功的两个重要原理：

1. WeixinJSBridgeReady和用户的真实操作来（touchend、click、doubleclick 或 keydown 事件等标准的事件）触发调用video.play()，才能自动播放视频。

2. video.play()是一个异步操作，返回一个Promise，并不是一个瞬时的操作。因此，一个src为空的video触发了play，会等到src赋值正确之后再去进行播放。

最终页面的加载流程：

video的Dom结构渲染 ===> 监听weixinJsBridgeReady ===> 触发weixinJsBridgeReady，执行video的play ===> 拿到video的src，进行赋值 ===> 播放
