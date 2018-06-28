---
title: Vue-从头到尾撸官方文档系列(介绍篇)
date: 2018-6-20 20:52:52
tags: [Vue]
categories: 前端
---

#### 导语
> 从2017年3月份开始接触到Vue，至今前前后后已经使用了1年多，一直希望有机会能从头开始认真地撸一遍官方文档，现在终于有充足的时间了。

<!--more-->
## 介绍
### vue.js是什么
Vue是一套用于构建用户界面的渐进式框架，其被设计为可以自底向上逐层应用。Vue的核心库只关心视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与现代化的工具链及各种支持类库结合使用时，Vue也完全能够为复杂的单页应用提供驱动。------官网摘录~~~

***
### 声明式渲染
Vue.js的核心是一个允许采用简洁的模板语法来声明式将数据渲染进DOM的系统：

```
<div id="app">
  {{ message }}
</div>
```

```
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

```
Hello Vue!
```

除了文本插值，我们还可以像这样来绑定元素特性：

```
<div id="app-2">
  <span v-bind:title="message">
    鼠标悬停几秒钟查看此处动态绑定的提示信息！
  </span>
</div>
```

```
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: '页面加载于 ' + new Date().toLocaleString()
  }
})
```

```
鼠标悬停几秒钟查看此处动态绑定的提示信息！
```

***
### 条件与循环
控制切换一个元素是否显示也相当简单：

```
<div id="app-3">
  <p v-if="seen">现在你看到我了</p>
</div>
```

```
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```

```
现在你看到我了
```

还有其它很多指令，每个都有特殊的功能。例如，v-for 指令可以绑定数组的数据来渲染一个项目列表：

```
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```

```
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: '学习 JavaScript' },
      { text: '学习 Vue' },
      { text: '整个牛项目' }
    ]
  }
})
```

```
1.学习 JavaScript
2.学习Vue
3.整个牛项目
```

***
### 处理用户输入
为了让用户和你的应用进行交互，我们可以用 v-on 指令添加一个事件监听器，通过它调用在 Vue 实例中定义的方法：

```
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">逆转消息</button>
</div>
```

```
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```

Vue 还提供了 v-model 指令，它能轻松实现表单输入和应用状态之间的双向绑定。

```
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```

```
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```

***
### 组件化应用构建
组件系统是 Vue 的另一个重要概念，允许我们使用小型、独立和通常可复用的组件构建大型应用。几乎任意类型的应用界面都可以抽象为一个组件树：

![](http://ow43yt5wd.bkt.clouddn.com/components.png)

在 Vue 里，一个组件本质上是一个拥有预定义选项的一个 Vue 实例。在 Vue 中注册组件很简单：

```
// 定义名为 todo-item 的新组件
Vue.component('todo-item', {
  template: '<li>这是个待办项</li>'
})
```

但是这样会为每个待办项渲染同样的文本，我们应该能从父作用域将数据传到子组件才对。让我们来修改一下组件的定义，使之能够接受一个 prop：

```
Vue.component('todo-item', {
  // todo-item 组件现在接受一个
  // "prop"，类似于一个自定义特性。
  // 这个 prop 名为 todo。
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

现在，我们可以使用 v-bind 指令将待办项传到循环输出的每个组件中：

```
<div id="app-7">
  <ol>
    <!--
      现在我们为每个 todo-item 提供 todo 对象
      todo 对象是变量，即其内容可以是动态的。
      我们也需要为每个组件提供一个“key”，稍后再
      作详细解释。
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id">
    </todo-item>
  </ol>
</div>
```

```
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { id: 0, text: '蔬菜' },
      { id: 1, text: '奶酪' },
      { id: 2, text: '随便其它什么人吃的东西' }
    ]
  }
})
```

```
1.蔬菜
2.奶酪
3.随便其它什么人吃的东西
```

在一个大型应用中，有必要将整个应用程序划分为组件，以使开发更易管理。

***


