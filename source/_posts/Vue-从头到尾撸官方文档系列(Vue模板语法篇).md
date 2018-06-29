---
title: Vue-从头到尾撸官方文档系列(Vue模板语法篇)
date: 2018-6-21 20:28:00
tags: [Vue]
categories: 前端
---

#### 导语
> 从2017年3月份开始接触到Vue，至今前前后后已经使用了1年多，一直希望有机会能从头开始认真地撸一遍官方文档，现在终于有充足的时间了。

<!--more-->
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

