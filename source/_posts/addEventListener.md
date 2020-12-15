---
title: 探索addEventListener
date: 2020-12-15
tags: [JavaScript,函数式编程]
categories: 
- JavaScript
series: JavaScript
---

最近使用了 `default-passive-events` 解决滑动卡顿的问题，看源码的时候，发现这个node包是修改 `addEventListener` 的第三个参数的默认值，所以有必要先总结一下 `addEventListener` 的相关知识。

### addEventListener的定义

EventTarget.addEventListener() 方法将指定的监听器注册到 `EventTarget` 上，当该对象触发指定的事件时，指定的回调函数就会被执行。 先看一段`EventTarget` 在TypeScript中的接口定义：

```ts
interface EventTarget {
  /**
    * Appends an event listener for events whose type attribute value is type. The callback argument sets the callback that will be invoked when the event is dispatched.
    * The options argument sets listener-specific options. For compatibility this can be a
    * boolean, in which case the method behaves exactly as if the value was specified as options's capture.
    * When set to true, options's capture prevents callback from being invoked when the event's eventPhase attribute value is BUBBLING_PHASE. When false (or not present), callback will not be invoked when event's eventPhase attribute value is CAPTURING_PHASE. Either way, callback will be invoked if event's eventPhase attribute value is AT_TARGET.
    * When set to true, options's passive indicates that the callback will not cancel the event by invoking preventDefault(). This is used to enable performance optimizations described in §2.8 Observing event listeners.
    * When set to true, options's once indicates that the callback will only be invoked once after which the event listener will
    * be removed.
    * The event listener is appended to target's event listener list and is not appended if it has the same type, callback, and capture.
    */
  addEventListener(type: string, listener: EventListenerOrEventListenerObject | null, options?: boolean | AddEventListenerOptions): void;
  /**
    * Dispatches a synthetic event event to target and returns true
    * if either event's cancelable attribute value is false or its preventDefault() method was not invoked, and false otherwise.
    */
  dispatchEvent(event: Event): boolean;
  /**
    * Removes the event listener in target's event listener list with the same type, callback, and options.
    */
  removeEventListener(type: string, callback: EventListenerOrEventListenerObject | null, options?: EventListenerOptions | boolean): void;
}
```

addEventListener可以接收三个参数：事件类型字符串、事件绑定的回调以及其他配置项。我们在这里主要是研究一下第三个参数。

### 第三个参数可以是bool类型

```
……
boolean, in which case the method behaves exactly as if the value was specified as options's capture.
……
```

根据文档，我们知道，这里的 boolean 的值，就是和下面的capture是一样的。为什么支持 boolean 类型，主要是为了兼容早期的浏览器实现方式。

### 第三个参数可以是对象

```ts
interface EventListenerOptions {
  capture?: boolean;
}
interface AddEventListenerOptions extends EventListenerOptions {
  once?: boolean;
  passive?: boolean;
}
```

我们可以从接口的定义里了解到，addEventListener的第三个参数是对象的时候，可以有三个属性：

capture: 默认值为false（即 使用事件冒泡），是否使用事件捕获；

once: 默认值为false. 是否只调用一次，if true，会在调用后自动销毁listener。

passive:  默认值为false。如果是true, 意味着listener永远不远调用preventDefault方法，如果又确实调用了的话，浏览器只会console一个warning，而不会真的去执行preventDefault方法。 

通常为了滑动的性能，我们都需要把 滑动元素的`touchmove`事件的`passive`设置成true，保证了在页面滚动时不会因为自定义事件中调用了 preventDefault 而阻塞页面渲染。

### 相关应用

[1. npm: https://www.npmjs.com/package/default-passive-events](https://www.npmjs.com/package/default-passive-events)

[2. GitHub: https://github.com/zzarcon/default-passive-events](https://github.com/zzarcon/default-passive-events)

### default-passive-events 源码解读

其实这个代码只做了一件事，就是给 addEventListener 的第三个参数设定一个默认值。

```js
import { eventListenerOptionsSupported } from './utils';

const defaultOptions = {
  passive: true,
  capture: false
};
const supportedPassiveTypes = [
  'scroll', 'wheel',
  'touchstart', 'touchmove', 'touchenter', 'touchend', 'touchleave',
  'mouseout', 'mouseleave', 'mouseup', 'mousedown', 'mousemove', 'mouseenter', 'mousewheel', 'mouseover'
];
const getDefaultPassiveOption = (passive, eventName) => {
  if (passive !== undefined) return passive;

  return supportedPassiveTypes.indexOf(eventName) === -1 ? false : defaultOptions.passive;
};

const getWritableOptions = (options) => {
  const passiveDescriptor = Object.getOwnPropertyDescriptor(options, 'passive');
    
  return passiveDescriptor && passiveDescriptor.writable !== true && passiveDescriptor.set === undefined
    ? Object.assign({}, options)
    : options;
};

const overwriteAddEvent = (superMethod) => {
  EventTarget.prototype.addEventListener = function (type, listener, options) {
    const usesListenerOptions = typeof options === 'object' && options !== null;
    const useCapture          = usesListenerOptions ? options.capture : options;

    options         = usesListenerOptions ? getWritableOptions(options) : {};
    options.passive = getDefaultPassiveOption(options.passive, type);
    options.capture = useCapture === undefined ? defaultOptions.capture : useCapture;

    superMethod.call(this, type, listener, options);
  };

  EventTarget.prototype.addEventListener._original = superMethod;
};

const supportsPassive = eventListenerOptionsSupported();

if (supportsPassive) {
  const addEvent = EventTarget.prototype.addEventListener;
  overwriteAddEvent(addEvent);
}
```

```js
// 判断浏览器是否支持 passive， 在get()里面去拦截属性的访问
export const eventListenerOptionsSupported = () => {
  let supported = false;

  try {
    const opts = Object.defineProperty({}, 'passive', {
      get() {
        supported = true;
      }
    });

    window.addEventListener('test', null, opts);
    window.removeEventListener('test', null, opts);
  } catch (e) {}

  return supported;
}
```

#### 1. 兼容处理

我们需要先判断浏览器是否支持 passive，这里就提供了一个不错的思路，使用 Object.defineProperty 的 get() 去进行拦截，我们比较熟悉的 Vue 的实现思路其实和这个类似。

#### 2. 函数式编程

这应该是我第三次提到函数式编程了，前两次分别是[函数式编程](https://beat-the-buzzer.github.io/2019/12/01/functional-programming/)和[防抖防抖节流操作总结](https://beat-the-buzzer.github.io/2020/09/12/debounce-throttle/)。反复提到这个思想，是因为在使用的时候，我们可以把新增的逻辑写在高阶函数里面，这样就不用去处理原方法了，并且方便移植代码。

#### 3. 最终使用这个库的效果

如果 passive 和 capture没有设置的时候，一律默认 passive: true, capture: false

```js
document.addEventListener('mouseup', onMouseUp); // {passive: true, capture: false}
document.addEventListener('mouseup', onMouseUp, true); // {passive: true, capture: true}
document.addEventListener('mouseup', onMouseUp, false); // {passive: true, capture: false}
document.addEventListener('mouseup', onMouseUp, {passive: false}); // {passive: false, capture: false}
document.addEventListener('mouseup', onMouseUp, {passive: false, capture: false}); // {passive: false, capture: false}
document.addEventListener('mouseup', onMouseUp, {passive: false, capture: true}); // {passive: false, capture: true}
document.addEventListener('mouseup', onMouseUp, {passive: true, capture: false}); // {passive: true, capture: false}
document.addEventListener('mouseup', onMouseUp, {passive: true, capture: true}); // {passive: true, capture: true}
```

> 小结：看似只总结了 addEventListener，实际上关联了很多其他的知识。前端技术总会互相联系的，当你发现了这个联系，你就会获得探索的乐趣。