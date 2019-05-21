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

```
function defineReactive (data, key, val) {
    Object.defineProperty(data, key, {
        enumerable: true,
        configurable: true,
        get: function() {
            return val
        },
        ser: function(newVal) {
           if (val === newVal) {
               return 
           }
           val = newVal
        }
    })
}
```

此处用 defineReactive 对 Object.defineProperty 进行封装，其作用是定义一个响应式数据，在函数中对对象进行变化追踪。封装好之后，每当从 data 的 key 中读取数据时，get 函数被触发；每当在 data 的 key 中设置新数据时， set 函数就会执行。

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

该模板中有两个vm实例使用了text1，当它发生变化时，如果向使用了它的地方发送通知来更新视图？

对于上述的问题，我的回答是，先收集依赖，即把用到了数据 text1 的地方收集起来，然后当属性发生变化时，把之前收集好的依赖循环触发更新一遍。

即：在 getter 中收集依赖，在 setter 中触发依赖。

***
### 依赖收集到哪里（Dep）

 每个 key 都有一个数组，用来存储当前 key 的依赖。我们把依赖收集的代码封装成一个 Dep 类，用它来专门帮助我们管理依赖。使用它，我们可以收集依赖、删除依赖、向依赖发送通知等。

```javascript
let uid = 0
export default class Dep {
  static target: ?Watcher;
  id: number;
  subs: Array<Watcher>;

  constructor () {
    this.id = uid++ // 唯一id
    this.subs = [] // 观察者集合
  }
 // 添加观察者
  addSub (sub: Watcher) {
    this.subs.push(sub)
  }
 // 移除观察者
  removeSub (sub: Watcher) {
    remove(this.subs, sub)
  }
  
  depend () { // 如果存在 Dep.target，则进行依赖收集操作
    if (Dep.target) {
      Dep.target.addDep(this)
    }
  }

  notify () {
    const subs = this.subs.slice() // 避免污染原来的集合
    // 如果不是异步执行，先进行排序，保证观察者执行顺序
    if (process.env.NODE_ENV !== 'production' && !config.async) {
      subs.sort((a, b) => a.id - b.id)
    }
    for (let i = 0, l = subs.length; i < l; i++) {
      subs[i].update() // 更新
    }
  }
}

Dep.target = null // 用于闭包时，保存特定的值
const targetStack = []
// 入栈
export function pushTarget (target: ?Watcher) {
  targetStack.push(target)
  Dep.target = target
}
// 出栈
export function popTarget () {
  targetStack.pop()
  Dep.target = targetStack[targetStack.length - 1]
}
```

***
### 什么是依赖(Watcher)

当属性发生变化时，我们要通知用到数据的地方，用到数据的地方有很多，有可能是模板、也可能是用户写的函数等等，这时需要抽象出一个能集中处理这些情况的类。

然后，我们在依赖收集阶段只收集这个封装好的类的实例进来啊，通知也只通知它一个，然后，它在负责通知其它地方。

```javascript
let uid = 0
export default class Watcher {
  // ...
  constructor (
    vm: Component, // 组件实例
    expOrFn: string | Function, // 要观察的表达式，函数，或者字符串，只要能触发取值操作
    cb: Function, // 被观察者发生变化后的回调
    options?: ?Object, // 参数
    isRenderWatcher?: boolean // 是否是渲染函数的观察者
  ) {
    this.vm = vm // Watcher有一个 vm 属性，表明它是属于哪个组件的
    if (isRenderWatcher) {
      vm._watcher = this
    }
    vm._watchers.push(this) // 给组件实例的_watchers属性添加观察者实例
    // options
    if (options) {
      this.deep = !!options.deep // 深度发现对象内部值得变化
      this.user = !!options.user
      this.lazy = !!options.lazy
      this.sync = !!options.sync // 同步执行
      this.before = options.before
    } else {
      this.deep = this.user = this.lazy = this.sync = false
    }
    this.cb = cb // 回调
    this.id = ++uid // uid for batching // 唯一标识
    this.active = true // 观察者实例是否激活
    this.dirty = this.lazy // for lazy watchers
    // 避免依赖重复收集的处理
    this.deps = []
    this.newDeps = []
    this.depIds = new Set()
    this.newDepIds = new Set()
    
    this.expression = process.env.NODE_ENV !== 'production'
      ? expOrFn.toString()
      : ''
    // parse expression for getter
    if (typeof expOrFn === 'function') {
      this.getter = expOrFn
    } else { // 类似于 Obj.a 的字符串
      this.getter = parsePath(expOrFn)
      if (!this.getter) {
        this.getter = noop // 空函数
        process.env.NODE_ENV !== 'production' && warn(
          `Failed watching path: "${expOrFn}" ` +
          'Watcher only accepts simple dot-delimited paths. ' +
          'For full control, use a function instead.',
          vm
        )
      }
    }
    this.value = this.lazy
      ? undefined
      : this.get()
  }

  get () { // 触发取值操作，进而触发属性的getter
    pushTarget(this) // Dep 中提到的：给 Dep.target 赋值
    let value
    const vm = this.vm
    try {
      // 核心，运行观察者表达式，进行取值，触发getter，从而在闭包中添加watcher
      value = this.getter.call(vm, vm)
    } catch (e) {
      if (this.user) {
        handleError(e, vm, `getter for watcher "${this.expression}"`)
      } else {
        throw e
      }
    } finally {
      if (this.deep) { // 如果要深度监测，再对 value 执行操作
        traverse(value)
      }
      // 清理依赖收集
      popTarget()
      this.cleanupDeps()
    }
    return value
  }

  addDep (dep: Dep) {
    const id = dep.id
    if (!this.newDepIds.has(id)) { // 避免依赖重复收集
      this.newDepIds.add(id)
      this.newDeps.push(dep)
      if (!this.depIds.has(id)) {
        dep.addSub(this) // dep 添加订阅者
      }
    }
  }

  update () { // 更新
    /* istanbul ignore else */
    if (this.lazy) {
      this.dirty = true
    } else if (this.sync) {
      this.run() // 同步直接运行
    } else { // 否则加入异步队列等待执行
      queueWatcher(this)
    }
  }
```

***
### 递归侦测所有Key

前面的实例代码只能侦测数据中的一个属性，我们希望把数据中的所有属性都侦测到，所以要封装一个Observer类。Observer的作用是将一个数据内的所有属性（包括子属性）都转换成 getter/setter 的形式，然后去追踪它们的变化：

```
// observer/index.js
// Observer 前的预处理方法
export function observe (value: any, asRootData: ?boolean): Observer | void {
  if (!isObject(value) || value instanceof VNode) { // 是否是对象或者虚拟dom
    return
  }
  let ob: Observer | void
  // 判断是否有 __ob__ 属性，有的话代表有 Observer 实例，直接返回，没有就创建 Observer
  if (hasOwn(value, '__ob__') && value.__ob__ instanceof Observer) {
    ob = value.__ob__
  } else if ( // 判断是否是单纯的对象
    shouldObserve &&
    !isServerRendering() &&
    (Array.isArray(value) || isPlainObject(value)) &&
    Object.isExtensible(value) &&
    !value._isVue
  ) {
    ob = new Observer(value) // 创建Observer
  }
  if (asRootData && ob) {
    ob.vmCount++
  }
  return ob
}

// Observer 实例
export class Observer { 
  value: any;
  dep: Dep;
  vmCount: number; // number of vms that have this object as root $data

  constructor (value: any) {
    this.value = value
    this.dep = new Dep() // 给 Observer 添加 Dep 实例，用于收集依赖，辅助 vm.$set/数组方法等
    this.vmCount = 0
    // 为被劫持的对象添加__ob__属性，指向自身 Observer 实例。作为是否 Observer 的唯一标识。
    def(value, '__ob__', this)
    if (Array.isArray(value)) { // 判断是否是数组
      if (hasProto) { // 判断是否支持__proto__属性，用来处理数组方法
        protoAugment(value, arrayMethods) // 继承
      } else {
        copyAugment(value, arrayMethods, arrayKeys) // 拷贝
      }
      this.observeArray(value) // 劫持数组成员
    } else {
      this.walk(value) // 劫持对象
    }
  }

  walk (obj: Object) { // 只有在值是 Object 的时候，才用此方法
    const keys = Object.keys(obj)
    for (let i = 0; i < keys.length; i++) {
      defineReactive(obj, keys[i]) // 数据劫持方法
    }
  }
}
```

现在，我们重新封装一下 defineReactive ：

```
export function defineReactive (
  obj: Object,
  key: string,
  val: any,
  customSetter?: ?Function,
  shallow?: boolean
) {
  const dep = new Dep() // 实例一个 Dep 实例

  const property = Object.getOwnPropertyDescriptor(obj, key) // 获取对象自身属性
  if (property && property.configurable === false) { // 没有属性或者属性不可写就没必要劫持了
    return
  }

  // 兼容预定义的 getter/setter
  const getter = property && property.get
  const setter = property && property.set
  if ((!getter || setter) && arguments.length === 2) { // 初始化 val
    val = obj[key]
  }
  // 默认监听子对象，从 observe 开始，返回 __ob__ 属性 即 Observer 实例
  let childOb = !shallow && observe(val)
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter () {
      const value = getter ? getter.call(obj) : val // 执行预设的getter获取值
      if (Dep.target) { // 依赖收集的关键
        dep.depend() // 依赖收集，利用了函数闭包的特性
        if (childOb) { // 如果有子对象，则添加同样的依赖
          childOb.dep.depend() // 即 Observer时的 this.dep = new Dep();
          if (Array.isArray(value)) { // value 是数组的话调用数组的方法
            dependArray(value)
          }
        }
      }
      return value
    },
    set: function reactiveSetter (newVal) {
      const value = getter ? getter.call(obj) : val
      // 原有值和新值比较，值一样则不做处理
      // newVal !== newVal && value !== value 这个比较有意思，但其实是为了处理 NaN
      if (newVal === value || (newVal !== newVal && value !== value)) {
        return
      }
      if (process.env.NODE_ENV !== 'production' && customSetter) {
        customSetter()
      }
      if (getter && !setter) return
      if (setter) { // 执行预设setter
        setter.call(obj, newVal)
      } else { // 没有预设直接赋值
        val = newVal
      }
      childOb = !shallow && observe(newVal) // 是否要观察新设置的值
      dep.notify() // 发布，利用了函数闭包的特性
    }
  })
}
// 处理数组
function dependArray (value: Array<any>) {
  for (let e, i = 0, l = value.length; i < l; i++) {
    e = value[i]
    e && e.__ob__ && e.__ob__.dep.depend() // 如果数组成员有 __ob__，则添加依赖
    if (Array.isArray(e)) { // 数组成员还是数组，递归调用
      dependArray(e)
    }
  }
}
```

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
