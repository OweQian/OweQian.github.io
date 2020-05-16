---
title: JavaScript-词法作用域和动态作用域
img: https://miro.medium.com/max/2560/1*UvWOnSlWP7AbbOtSVUeHMg.png
date: 2020-05-15 15:50:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 作用域规定了当前执行代码对变量的访问权限。JavaScript 采用词法作用域，即静态作用域。

<!--more-->           
***

# 静态作用域

静态作用域，指函数的作用域在函数定义的时候就决定了。

```javascript
var value = 1;

function foo() {
    console.log(value);
}

function bar() {
    var value = 2;
    foo();
}

bar();
```

JavaScript采用静态作用域，让我们分析下执行过程：

执行 foo 函数，先从 foo 函数内部查找是否有局部变量 value，如果没有，就根据书写的位置，查找上面一层的代码，也就是 value 等于 1，所以结果会打印 1。

假设JavaScript采用动态作用域，让我们分析下执行过程：

执行 foo 函数，依然是从 foo 函数内部查找是否有局部变量 value。如果没有，就从调用函数的作用域，也就是 bar 函数内部查找 value 变量，所以结果会打印 2。

前面我们已经说了，JavaScript采用的是静态作用域，所以这个例子的结果是 1。

参考：

[[译] JavaScript深入之词法作用域和动态作用域}]: https://github.com/mqyqingfeng/Blog/issues/3
