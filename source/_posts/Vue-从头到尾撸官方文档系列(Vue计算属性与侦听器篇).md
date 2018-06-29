---
title: Vue-从头到尾撸官方文档系列(Vue计算属性与侦听器篇)
date: 2018-6-21 20:28:00
tags: [Vue]
categories: 前端
---

#### 导语
> 从2017年3月份开始接触到Vue，至今前前后后已经使用了1年多，一直希望有机会能从头开始认真地撸一遍官方文档，现在终于有充足的时间了。

<!--more-->
## 计算属性和侦听器
### 计算属性
对于任何复杂逻辑，你都应当使用计算属性。

***
#### 基础例子
```
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
```

```
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
```

```
结果：

Original message: "Hello"

Computed reversed message: "olleH"
```

vm.reversedMessage 的值始终取决于 vm.message 的值。

***
#### 计算属性缓存 vs 方法

在表达式中调用方法可达到同样的效果：

```
<p>Reversed message: "{{ reversedMessage() }}"</p>
```

```
// 在组件中
methods: {
  reversedMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```

然而，最重要的是：

计算属性是基于它们的依赖进行缓存的。计算属性只有在它的相关依赖发生改变时才会重新求值。

相比之下，每当触发重新渲染时，调用方法将总会再次执行函数。

***
#### 计算属性 vs 侦听属性

当你有一些数据需要随着其它数据变动而变动时，你很容易滥用 watch。

然而，通常更好的做法是使用计算属性而不是命令式的 watch 回调。

```
<div id="demo">{{ fullName }}</div>
```

```
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

```
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

***
#### 计算属性的setter
计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter ：

```
/ ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

现在再运行 vm.fullName = 'John Doe' 时，setter 会被调用，vm.firstName 和 vm.lastName 也会相应地被更新。

***
### 侦听器

 Vue 通过 watch 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。

