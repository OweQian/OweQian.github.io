---
title: JS-事件的节流(throttle)和防抖(debounce)
date: 2019-05-31 15:13:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 窗口的resize、scroll、mousemove、mouseover等事件被频繁触发时，其对应的回调函数也会被频繁触发而导致页面抖动甚至卡顿。

<!--more-->

为了规避这种情况，我们需要一些手段来控制事件被触发的频率。此时，事件节流(throttle)和事件防抖(debounce)出现了。

### 节流和防抖的本质

这两个东西都以“闭包”的形式存在。

它们通过对事件对应的回调函数进行包裹、以自由变量的形式缓存时间信息，来判断是否执行回调函数，以此来控制事件的触发频率。

***
### 节流-throttle

函数节流指某个函数在一定时间间隔内 ( 例如 3 秒 ) 只执行一次，在这 3 秒内无视后来产生的函数调用请求，也不会延长事件间隔。3 秒间隔结束后第一次遇到新的函数调用会触发执行，然后在新的 3 秒内依旧无视后来产生的函数调用请求，以此类推。

实现方案有以下两种：

* 第一种是用时间戳来判断是否已到执行时间，记录上次执行的时间戳，然后每次触发事件执行回调，回调中判断当前时间戳距离上次执行时间戳的间隔是否已经到达时间差，如果是则执行，并更新上次执行的时间戳，如此循环。

* 第二种是使用定时器，比如当 scroll 事件刚触发时，打印一个 hello word ，然后设置个 1000ms 的定时器，此后每次触发 scroll 事件回调，如果已经存在定时器，则回调不执行方法，直到定时器触发，handler 被清除，然后重新设置定时器。

理解了大致思路，我们用第一种方法实现一个 throttle:

 ```javascript
/**
 * 节流 throttle 时间戳
 * @param fn {function} 回调函数
 * @param wait {number} 时间间隔
 * @author wangxiaobai
 * @date 2019/5/30
 */
export function throttle (fn, wait) {
  // 上一次函数执行的时间
  let previous = 0
  return function (...args) {
    let now = +new Date() // 本次触发事件
    if (now - previous > wait) {
      previous = now
      // 执行fn
      fn.apply(this, args)
    }
  }
}
```

***
### 防抖-debounce

函数防抖指某个函数在一定时间间隔内 ( 例如 3 秒 ) 只执行一次，在这 3 秒内无视先前产生的函数调用请求，也不会延长事件间隔。3 秒间隔结束后第一次遇到新的函数调用暂时不会触发执行，然后在新的 3 秒内依旧无视先前产生的函数调用请求，以此类推。

实现方案：

* 使用定时器，比如当 scroll 事件刚触发时，首先清除掉旧计时器，然后设立新的计时器，等待3 秒，打印一个 hello word，此后每次触发 scroll 事件回调，如果已经存在定时器，则清除掉旧计时器，然后重新设置定时器。

理解了大致思路，我们用第一种方法实现一个 debounce:

```javascript
/**
 * 防抖 debounce 定时器
 * @param fn {function} 回调函数
 * @param wait {number} 时间间隔
 * @author wangxiaobai
 * @date 2019/5/30
 */
export function debounce (fn, wait) {
  let timer = null
  return function (...args) {
    // 清除上次执行得定时器
    if (timer) {
      clearTimeout(timer)
    }
    // 设立新定时器
    timer = setTimeout(() => {
      fn.apply(this, args)
    }, wait)
  }
}
```

***

throttle 和 debounce 不仅是我们日常开发中的常用优质代码片段，更是前端面试中不可不知的高频考点。“看懂了代码”、“理解了过程”在本节都是不够的，重要的是把它写到自己的项目里去，亲自体验一把节流和防抖带来的性能提升。

### 参考

* [前端性能优化原理与实践](https://juejin.im)
* [深入浅出节流函数throttle](https://github.com/yygmind/blog/issues/38)
