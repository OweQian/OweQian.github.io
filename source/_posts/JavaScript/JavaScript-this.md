---
title: JavaScript-this
img: https://miro.medium.com/max/2560/1*UvWOnSlWP7AbbOtSVUeHMg.png
date: 2020-01-07 18:10:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> JavaScript 中的 this 就是一个对象，本文旨在分析各种情况下 this 的指向问题。

<!--more-->           
***

this 就是一个对象，不同情况下，this 的指向不同，主要有以下几种情况：

* 直接调用，指向全局对象 window

```javascript
function foo() {
  console.log(this)
}

foo() // window
```

* 对象调用，指向该对象

```javascript
var obj = {
    name: 'a',
    foo: function() {
      console.log(this.name)
    }
}

obj.foo() // 'a'
```

* new 方式，指向新创建的对象

```javascript
function Foo(name) {
  this.name = name
  console.log(this)
}

var wangxiaobai = new Foo('wangxiaobai') // 'wangxiaobai'
```

* 箭头函数的 this

由于箭头函数没有单独的 this 值，箭头函数的 this 与其声明所在的上下文相同(取决于包裹箭头函数的第一个普通函数的 this)，并且不会改变。

```javascript
const obj = {
    a:()=>{
        console.log(this);
    }
}
// 对象调用箭头函数
obj.a(); // window
```

## 如何改变 this 的指向

我们可以通过 call、apply、bind 改变 this 的指向：

```javascript
var obj = {
   name: 'wangxiaobai'
}

function foo() {
  console.log(this)
}

// 通过 call 改变 this 指向
foo.call(obj); // obj

// 通过 apply 改变 this 指向
foo.apply(obj); // obj

// 通过 bind 改变 this 指向
foo.bind(obj)()
```

总结一下三者的共同点和不同点。

共同点：

* 三者都能改变 this 的指向，且第一个传递的参数都是 this 指向的对象

* 三者都采用的后续传参的形式

不同点：

* call 的传参是单个传递，apply 的传参是数组形式，bind 没有明确规定。

* call 和 apply 函数的执行是直接执行，bind 函数会返回一个函数，然后想要调用的时候才会执行。

最后，当多个规则同时出现时，根据优先级来决定 this 最终指向哪里。

new > 显示绑定(call, apply, bind) > 对象绑定 > 隐式绑定
