---
title: Vue-从头到尾撸官方文档系列(Vue基础篇)
date: 2017-4-02 22:52:00
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

***

## 模板语法
Vue.js 使用了基于 HTML 的模板语法，允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。

***
### 插值
#### 文本
数据绑定最常见的形式就是使用“Mustache”语法 (双大括号) 的文本插值：

```
<span>Message: {{ msg }}</span>
```

Mustache 标签将会被替代为对应数据对象上 msg 属性的值。无论何时，绑定的数据对象上 msg 属性发生了改变，插值处的内容都会更新。

通过使用 v-once 指令，你也能执行一次性地插值，当数据改变时，插值处的内容不会更新。

***
#### 原始HTML
双大括号会将数据解释为普通文本，而非 HTML 代码。为了输出真正的 HTML，你需要使用 v-html 指令：

```
<p>Using mustaches: {{ rawHtml }}</p >
<p>Using v-html directive: <span v-html="rawHtml"></span></p >
```

```
Using mustaches: <span style="color: red">This should be red.</span>
Using v-html directive: This should be red.
```

你的站点上动态渲染的任意 HTML 可能会非常危险，因为它很容易导致 XSS 攻击。请只对可信内容使用 HTML 插值，绝不要对用户提供的内容使用插值。

***
#### 特性
Mustache 语法不能作用在 HTML 特性上，遇到这种情况应该使用 v-bind 指令：

```
<div v-bind:id="dynamicId"></div>
```

***
#### 使用JavaScript表达式
对于所有的数据绑定，Vue.js 都提供了完全的 JavaScript 表达式支持。

```
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
<div v-bind:id="'list-' + id"></div>
```

模板表达式都被放在沙盒中，只能访问全局变量的一个白名单，如 Math 和 Date。你不应该在模板表达式中试图访问用户定义的全局变量。

***
### 指令
指令特性的值预期是单个 JavaScript 表达式（除v-for），当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。

```
<p v-if="seen">现在你看到我了</p>
```

这里，v-if 指令将根据表达式 seen 的值的真假来插入/移除 <p> 元素。

***
#### 参数
一些指令能够接收一个“参数”，在指令名称之后以冒号表示。
v-bind 指令可以用于响应式地更新 HTML 特性：

```
<a v-bind:href="url">...</a>
```

另一个例子是 v-on 指令，它用于监听 DOM 事件：

```
<a v-on:click="doSomething">...</a>
```

***
#### 修饰符
修饰符 (Modifiers) 是以半角句号 . 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。

```
<form v-on:submit.prevent="onSubmit">...</form>
```

***
### 缩写
Vue.js 为 v-bind 和 v-on 这两个最常用的指令，提供了特定简写：

#### v-bind 缩写

```
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>
```

***
#### v-on缩写

```
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>
```

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

***
## Class 与 Style 绑定
操作元素的 class 列表和内联样式是数据绑定的一个常见需求。因为它们都是属性，所以我们可以用 v-bind 处理它们：只需要通过表达式计算出字符串结果即可。表达式结果的类型除了字符串之外，还可以是对象或数组。

***
### 绑定HTML Class
#### 对象语法
我们可以传给 v-bind:class 一个对象，以动态地切换 class：

```
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>
```

绑定的数据对象不必内联定义在模板里：

```
<div v-bind:class="classObject"></div>
```

```
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

我们也可以在这里绑定一个返回对象的计算属性。这是一个常用且强大的模式：

```
<div v-bind:class="classObject"></div>
```

```
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

***
#### 数组语法
我们可以把一个数组传给 v-bind:class，以应用一个 class 列表：

```
<div v-bind:class="[activeClass, errorClass]"></div>
```

```
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

如果你也想根据条件切换列表中的 class，可以用三元表达式：

```
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

当有多个条件 class 时这样写有些繁琐。所以在数组语法中也可以使用对象语法：

```
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

***
#### 用在组件上
当在一个自定义组件上使用 class 属性时，这些类将被添加到该组件的根元素上面。这个元素上已经存在的类不会被覆盖。

```
<my-component class="baz boo"></my-component>
```

对于带数据绑定 class 也同样适用：

```
<my-component v-bind:class="{ active: isActive }"></my-component>
```

***
### 绑定内联样式
#### 对象语法
v-bind:style。CSS 属性名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用单引号括起来) 来命名：

```
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```
data: {
  activeColor: 'red',
  fontSize: 30
}
```

直接绑定到一个样式对象通常更好，这会让模板更清晰：

```
<div v-bind:style="styleObject"></div>
```

```
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

同样的，对象语法常常结合返回对象的计算属性使用。

***
#### 数组语法
v-bind:style 的数组语法可以将多个样式对象应用到同一个元素上：

```
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

***
#### 自动添加前缀
当 v-bind:style 使用需要添加浏览器引擎前缀的 CSS 属性时，如 transform，Vue.js 会自动侦测并添加相应的前缀。

***
#### 多重值
Vue.js 可以为 style 绑定中的属性提供一个包含多个值的数组，常用于提供多个带前缀的值，例如：

```
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

***

## 条件渲染
### v-if
在 Vue 中，我们使用 v-if 指令实现根据条件是否展示元素的功能：

```
<h1 v-if="ok">Yes</h1>
```

也可以用 v-else 添加一个“else 块”：

```
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```

***
#### 使用 <template> && v-if 条件渲染分组
如果想切换多个元素，此时可以把一个 <template> 元素当做不可见的包裹元素，并在上面使用 v-if。最终的渲染结果将不包含 <template> 元素。

```
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p >
  <p>Paragraph 2</p >
</template>
```

***
#### v-else
使用 v-else 指令来表示 v-if 的“else 块”：

```
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```

***
#### v-else-if
v-else-if，顾名思义，充当 v-if 的“else-if 块”，可以连续使用：

```
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

***
#### 用 key 管理可复用的元素
Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。

```
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

那么在上面的代码中切换 loginType 将不会清除用户已经输入的内容。因为两个模板使用了相同的元素，<input> 不会被替换掉——仅仅是替换了它的 placeholder。

所以 Vue 为你提供了一种方式来表达“这两个元素是完全独立的，不要复用它们”。只需添加一个具有唯一值的 key 属性即可：

```
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

***
### v-show
另一个用于根据条件展示元素的选项是 v-show 指令。用法大致一样：

```
<h1 v-show="ok">Hello!</h1>
```

不同的是带有 v-show 的元素始终会被渲染并保留在 DOM 中。v-show 只是简单地切换元素的 CSS 属性 display。

***
### v-if vs v-show
v-if 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

v-if 也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。

***
### v-if 与 v-for 一起使用
当 v-if 与 v-for 一起使用时，v-for 具有比 v-if 更高的优先级。
