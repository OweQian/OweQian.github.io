---
title: JavaScript-闭包
img: https://miro.medium.com/max/2560/1*UvWOnSlWP7AbbOtSVUeHMg.png
date: 2020-01-08 14:26:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 闭包，面试中的重难点，要从很多方面讲起。

<!--more-->           
***

# 什么是作用域

规定函数和变量的可使用范围叫做作用域。

```javascript
function foo() {
    let a = 1
}
```

声明一个函数，创建一个私用作用域。其它函数不能直接访问私有作用域 foo 的变量 a。一个函数就是一个作用域。

# 什么是作用域链

每个函数都会有一个作用域，查找变量或函数时，由局部作用域到全局作用域依次查找，这些作用域的集合就称为作用域链。

```javascript
let a = 1
function fn() {
    function fn1() {
        function fn2() {
            let c = 3;
            console.log(a);
        }
        // 执行 fn2
        fn2();
    }
    // 执行 fn1
    fn1();
}
// 执行函数
fn();
```

在 fn2 作用域中打印 a，首先在自己所在作用域搜索，如果没有就向上级作用域搜索，直到搜索到全局作用域，a = 1，找到了打印出值。整个搜索的过程，就是基于作用域链搜索的。

# 什么是闭包

闭包是指有权访问另一个函数作用域中的变量的函数。

闭包产生的本职就是当前环境中存在指向父级作用域的引用。

```javascript
function foo() {
  let a = 2
  function bar() {
     console.log(a);
  }
  return bar
}

let x = foo()
x() // 2
```

# 闭包有哪些表现形式？

* 返回一个函数，如上例

* 函数作为参数传递

```javascript
function foo() {
  let a = 2
  function baz() {
     console.log(a);
  }
  bar(baz)
}

function bar(fn) {
    fn()
}

foo() // 2
```

* 定时器，ajax 请求，事件监听等，只要使用了回调函数，实际上就是在使用闭包

* IIFE (立即执行函数表达式) 创建了闭包

# 如何解决下面的循环输出问题？

```javascript
for(var i = 1; i <= 5; i ++){
  setTimeout(function timer(){
    console.log(i)
  }, 0)
}
```

上面全部输出 6，如何输出 1，2，3，4，5？

解决方法：

* IIFE，传入此时的 i 变量到定时器中

```javascript
for(var i = 1; i <= 5; i ++){
  (function (j) {setTimeout(function timer(){
    console.log(i)
  }, 0)})(j)
}
```

* 给定时器传入第三个参数

```javascript
for(var i = 1; i <= 5; i ++){
  setTimeout(function timer(){
    console.log(i)
  }, 0, i)
}
```

* 使用 ES6 中的 let

```javascript
for(let i = 1; i <= 5; i ++){
  setTimeout(function timer(){
    console.log(i)
  }, 0)
}
```


