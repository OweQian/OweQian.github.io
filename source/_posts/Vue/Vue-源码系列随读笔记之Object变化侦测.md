---
title: Vue-源码系列随读笔记之Object变化侦测
date: 2019-5-16 18:34:52
tags: [Vue]
categories: 前端
---

#### 导语
> Vue最独特的特性之一是看起来并不显眼的响应式系统。数据模型仅仅是普通的JS对象。而当你修改它们时，视图会进行更新。这使得状态管理非常简单、直接。不过理解其工作原理同样重要，这样你可以回避一些常见问题 ---官方文档

<!--more-->
### 什么是变化侦测

从状态生成DOM，再输出到用户界面显示的一整套流程叫做渲染，应用在运行时会不断地重新渲染。响应式系统赋予框架重新渲染的能力，其重要组成部分是变化侦测。

简单来说，变化侦测的作用就是侦测数据的变化，当数据变化时，会通知视图进行相应的更新。

Vue.js 2.0 引入了虚拟 DOM，收集每一个状态所绑定的依赖 (组件实例) ，当状态改变后，会通知到组件，组件内部再使用虚拟 DOM 进行对比。

***
### 如何追踪变化

首先，在 JS 中，通过 Object.defineProperty 侦测对象变化，这也是响应式最根本的依赖。

```javascript
function defineReactive (data, key, val) {
  Object.defineProperty(data, key, {
     enumerable: true,
     configurable: true,
     get: function() {
        return val
     },
     set: function(newVal) {
        if (val === newVal) {
           return 
        }
        val = newVal
     }
  })
}
```

此处用 defineReactive 对 Object.defineProperty 进行封装，其作用是定义一个响应式数据，在函数中对对象进行变化追踪。封装好之后，每当从 data 的 key 中读取数据时，getter 函数被触发；每当在 data 的 key 中设置新数据时， setter 函数就会执行。

由此可见，Object.defineProperty 是对已有属性进行的劫持操作，所以 Vue 才要求事先将需要用到的数据定义在 data 中，同时也无法响应对象属性的添加和删除。被劫持的属性会有相应的 get、set 方法。

***
### 如何收集依赖

思考以下问题：

```javascript
let globalObj = {
  text1: 'text1'
};

let o1 = new Vue({
  template:
    `<div>
       <span>{{text1}}</span> 
    <div>`,
  data: globalObj
});

let o2 = new Vue({
  template:
     `<div>
        <span>{{text1}}</span> 
     <div>`,
  data: globalObj
});
```

该模板中有两个vm实例使用了text1，当它发生变化时，如何向使用了它的地方发送通知来更新视图？

对于上述的问题，我的回答是，先收集依赖，即把用到了数据 text1 的地方收集起来，然后当属性发生变化时，把之前收集好的依赖循环触发更新一遍。

即：在 getter 中收集依赖，在 setter 中触发依赖。

***
### 依赖收集到哪里（Dep）

 每个 key 都有一个数组，用来存储当前 key 的依赖。我们把依赖收集的代码封装成一个 Dep 类，用它来专门帮助我们管理依赖。使用它，我们可以收集依赖、删除依赖、向依赖发送通知等。

```javascript
export default class Dep {
  
  constructor () {
    this.subs = [] // 观察者集合
  }
 // 添加观察者
  addSub (sub) {
    this.subs.push(sub)
  }
 // 移除观察者
  removeSub (sub) {
    remove(this.subs, sub)
  }
  
  depend () { // 如果存在 Dep.target，则进行依赖收集操作
    if (Dep.target) {
      this.addSub(Dep.target)
    }
  }

  notify () {
    const subs = this.subs.slice() // 避免污染原来的集合
    for (let i = 0, l = subs.length; i < l; i++) {
      subs[i].update() // 更新
    }
  }
}

function remove(arr, item) {
  if (arr.length) {
    const index = arr.indexOf(item)
    if (index > -1) {
      return arr.splice(index, 1)
    }
  }
}
```

改造 defineReactive

```javascript
function defineReactive (data, key, val) {
  let dep = new Dep()
  Object.defineProperty(data, key, {
     enumerable: true,
     configurable: true,
     get: function() {
        dep.depend()
           return val
     },
     set: function(newVal) {
        if (val === newVal) {
           return 
        }
        val = newVal
        dep.notify()
     }
  })
}
```
***
### 什么是依赖

当属性发生变化时，我们要通知用到数据的地方，用到数据的地方有很多，有可能是模板、也可能是用户写的函数等等，这时需要抽象出一个能集中处理这些情况的类。

然后，我们在依赖收集阶段只收集这个封装好的类的实例进来啊，通知也只通知它一个，然后，它在负责通知其它地方。

收集谁？Watcher!

***
### 什么是 Watcher

Watcher 是一个中介的角色，数据发生变化时通知它，它再通知其它地方。

```javascript
vm.$watch('a.b.c', function(newVal, oldVal) {
  // do something
})
```

这段代码表示当 data.a.b.c 属性发生变化时，触发第二个参数中的函数。

把这个 watcher 实例添加到 data.a.b.c 属性的 Dep 中去就行了。然后，当 data.a.b.c 的值发生变化时，通知 watcher。接着，watcher 在执行参数中的回调函数。

```javascript
export default class Watcher {
  // ...
  constructor (
    vm: Component, // 组件实例
    expOrFn: string | Function, // 要观察的表达式，函数，或者字符串，只要能触发取值操作
    cb: Function // 被观察者发生变化后的回调
  ) {
    this.vm = vm // Watcher有一个 vm 属性，表明它是属于哪个组件的
    this.cb = cb // 回调
    this.getter = parsePath(expOrFn)
    this.value = this.get()
  }

  get () { // 触发取值操作，进而触发属性的getter
    Dep.target = this
    let value = this.getter.call(this.vm, this.vm)
    Dep.target = null
    return value
  }

  update () { // 更新
    const oldValue = this.value
    this.value = this.get()
    this.cb.call(this.vm, this.value, oldValue)
  }
}
```

这段代码可以将自己主动添加到 data.a.b.c 的 Dep 中。

在 get 方法中先把 Dep.target 设置成了 this，也就是当前 watcher 实例，然后再读一下 data.a.b.c 的值，这就会触发 getter，触发了 getter，就会触发收集依赖的逻辑。

只要现在 Dep.target 赋一个 this，然后再读一下值，去触发 getter，就可以把 this 主动添加到 keypath 的 Dep 中。

依赖注入到 Dep 中后，每当 data.a.b.c 的值发生变化时，就会让依赖列表中所有的依赖循环触发 update 方法。

不管用户执行的是 vm.$watch('a.b.c', (value, oldValue) => {})，还是模板中的 data，都是通过 watcher 来通知自己是否需要变化。

parsePath函数：

```javascript
const bailRE = /[^\w.$]/

export function parsePath(path) {
  if (bailRE.test(path)) {
      return
  }
  const segments = path.split('.')
	return function(obj) {
	  for (let i = 0; i < segments.length; i++) {
	      if (!obj) return
	      obj = obj[segments[i]]
	  }
	  return obj
	}
}
```

***
### 递归侦测所有Key

前面的实例代码只能侦测数据中的一个属性，我们希望把数据中的所有属性都侦测到，所以要封装一个Observer类。Observer的作用是将一个数据内的所有属性（包括子属性）都转换成 getter/setter 的形式，然后去追踪它们的变化：

```javascript
export class Observer {
    constructor (value) {
        this.value = value
        if (!Array.isArray(value)) {
            this.walk(value)
        }
    }
    walk (obj) {
        const keys = Object.keys(obj)
        for (let i = 0; i < keys.length; i++) {
            defineReactive (obj, keys[i], obj[keys[i]])
        }
    }
}

function defineReactive(data, key, val) {
  if (typeof val === 'object') {
      new Observer(val)
  }
  let dep = new Dep()
    Object.defineProperty(data, key, {
       enumerable: true,
       configurable: true,
       get: function() {
          dep.depend()
             return val
       },
       set: function(newVal) {
          if (val === newVal) {
             return 
          }
          val = newVal
          dep.notify()
       }
    })
}
```

Observer 类，用于将一个正常的 object 转换成被侦测的 object。

判断数据的类型，只有 Object 类型的数据才会调用 walk 将每一个属性转换成 getter/setter 的形式来侦测变化。

最后，在 defineReactive 中新增 new Observer(val) 来递归子属性，这样我们就可以把 data 中的所有属性转换成 getter/setter 的形式来侦测变化。

当 data 中的属性发生变化时，与属性对应的依赖就会接收到通知。

***
### 总结

变化侦测就是侦测数据的变化。当数据发生变化时，要能侦测到并发出通知。

Object 可以通过 Object.defineProperty 将属性转换成 getter/setter 的形式来追踪变化，读取数据时会触发 getter，修改数据时会触发 setter。

我们需要在 getter 中收集有哪些依赖使用了数据。当 setter 被触发时，去通知 getter 中收集的依赖数据发生了变化。

收集依赖需要为依赖找一个存储的地方，为此创建了 Dep，它用来收集依赖、删除依赖、向依赖发送消息等。

所谓的依赖，其实就是 Watcher。把 Watcher 收集到 Dep 中。当数据发生变化时，会循环依赖列表，把所有的 Watcher 都通知一遍。

Watcher 的原理是先把自己设置到全局唯一的指定位置 (Dep.target)，然后读取这个数据。因为读取了这个数据，因此会触发这个数据的 getter 。接着，在 getter 中就会从全局唯一的位置读取正在读取数据的 Watcher，并把这个 Watcher 收集到 Dep 中去。通过这样的方式，Watcher 可以主动去订阅任意一个数据的变化。

由此，我们创建了 Observer 类，它的作用就是把一个 object 中的所有数据（包括子数据）都转换成响应式的，也就是它会侦测 object 中所有数据的变化。

在 Vue 中，对象新增属性或删除属性都无法被侦测到。

![](https://screenshot.net/zh/qv9nwto)

Object 通过 Observer 转换成了 getter/setter 的形式来追踪变化。

当外界通过 Watcher 读取数据时，会触发 getter 从而将 Watcher 添加到 Dep 中。

当数据发生了变化时，会触发 setter，从而向 Dep 中的依赖发送通知。

Watcher 接收到通知后，会像外界发送通知，变化通知到外界后可能会触发视图更新，也有可能会触发用户的某个回调函数等。
