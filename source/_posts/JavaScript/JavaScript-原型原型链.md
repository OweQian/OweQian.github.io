---
title: JavaScript-原型原型链
img: https://miro.medium.com/max/2560/1*UvWOnSlWP7AbbOtSVUeHMg.png
date: 2020-04-03 17:10:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> JavaScript 中的 原型和原型链是面试中常考的点，本文旨在分析原型和原型链是什么。

<!--more-->           
***

# 原型对象

JavaScript 通过 new 一个构造函数生成一个对象实例，但是每个对象实例的属性和方法并不是共享的，为了各个对象实例可以共享一些属性和方法，要设计一个对象专门用来存储对象共享的属性，那么我们叫它「原型对象」。

在 JS 所有对象中，只要是对象，都会有一个内置属性叫做_proto_，而且这个属性是系统自动生成的，只要你创建一个对象，这个对象就有这个属性。这个_proto_属性指向的是原型对象。

构造函数的 prototype 属性指向原型对象，原型对象的 constructor 属性指回构造函数。

# 原型链

每个对象都一个 __proto__ 指向原型对象，原型对象也有一个 __proto__ 指向它的原型对象，直到原型链的顶端 null。

![](https://user-gold-cdn.xitu.io/2019/10/24/16dfb64428ee0ce4?imageslim)

总结：

* 所有的实例的 __proto__ 都指向该构造函数的原型对象（prototype）。
* 所有的函数（包括构造函数）是 Function() 的实例，所以所有函数的 __proto__ 的都指向 Function() 的原型对象。
* 所有的原型对象（包括 Function 的原型对象）都是 Object 的实例，所以 __proto__ 都指向 Object （构造函数）的原型对象。而 Object 构造函数的 __proto__  指向 null。
* Function 构造函数本身就是 Function 的实例，所以 __proto__ 指向 Function 的原型对象。
