---
title: Vue-从头到尾撸官方文档系列(Vue实例篇)
date: 2018-6-20 22:52:00
tags: [Vue]
categories: 前端
---

#### 导语
> 从2017年3月份开始接触到Vue，至今前前后后已经使用了1年多，一直希望有机会能从头开始认真地撸一遍官方文档，现在终于有充足的时间了。

<!--more-->
## Vue实例
### 创建一个Vue实例
每个 Vue 应用都是通过用 Vue 函数创建一个新的 Vue 实例开始的：

```
var vm = new Vue({
  // 选项
})
```

当创建一个 Vue 实例时，你可以传入一个选项对象。一个 Vue 应用由一个通过 new Vue 创建的根 Vue 实例，以及可选的嵌套的、可复用的组件树组成。
所有Vue组件都是Vue实例，并接受相同的选项对象，一些跟实例特有的选项除外。

***
### 数据与方法
当一个 Vue 实例被创建时，它向 Vue 的响应式系统中加入了其 data 对象中能找到的所有的属性。当这些属性的值发生改变时，视图将会产生“响应”，即匹配更新为新的值。

```
// 我们的数据对象
var data = { a: 1 }

// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})

// 获得这个实例上的属性
// 返回源数据中对应的字段
vm.a == data.a // => true

// 设置属性也会影响到原始数据
vm.a = 2
data.a // => 2

// ……反之亦然
data.a = 3
vm.a // => 3
```

值得注意的是只有当实例被创建时data中存在的属性才是响应式的。

如果你知道你会在晚些时候需要一个属性，但是一开始它为空或不存在，那么你仅需要设置一些初始值，如：

```
data: {
  newTodoText: '',
  visitCount: 0,
  hideCompletedTodos: false,
  todos: [],
  error: null
}
```

还有一个例外是使用Object.freeze()，这会阻止修改现有的属性，也意味着响应系统无法再追踪变化。

```
var obj = {
  foo: 'bar'
}

Object.freeze(obj)

new Vue({
  el: '#app',
  data: obj
})
```

```
<div id="app">
  <p>{{ foo }}</p>
  <!-- 这里的 `foo` 不会更新！ -->
  <button v-on:click="foo = 'baz'">Change it</button>
</div>
```

***
### 实例生命周期钩子

每个Vue实例在被创建时都要经过一系列的初始化过程。在实例生命周期的不同阶段，可以调用一些钩子，如 mounted、updated 和 destroyed。

注：不要在选项属性或回调上使用箭头函数，比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。

***
#### 生命周期图示

![](http://ow43yt5wd.bkt.clouddn.com/lifecycle.png)


