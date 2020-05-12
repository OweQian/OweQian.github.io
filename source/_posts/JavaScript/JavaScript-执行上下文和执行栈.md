---
title: JavaScript-执行上下文和执行栈
img: https://miro.medium.com/max/2560/1*UvWOnSlWP7AbbOtSVUeHMg.png
date: 2020-05-12 17:50:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 执行上下文是评估和执行 JavaScript 代码的环境的抽象概念。每当 Javascript 代码在运行的时候，它都是在执行上下文中运行。
<!--more-->           
***

# 执行上下文的类型

JavaScript 中有三种执行上下文类型：

* 全局执行上下文：任何不在函数内部的代码都在全局上下文中。它会执行两件事：创建一个全局的 window 对象（浏览器的情况下），并且设置 this 的值等于这个全局对象。一个程序中只会有一个全局执行上下文。

* 函数执行上下文：每当一个函数被调用时, 都会为该函数创建一个新的上下文。函数上下文可以有任意多个。每当一个新的执行上下文被创建，它会按定义的顺序执行一系列步骤。

* Eval 函数执行上下文：执行在 eval 函数内部的代码也会有它属于自己的执行上下文。

# 执行栈

执行栈，也叫"调用栈"，是一种 LIFO（后进先出）的数据结构，被用来存储代码执行过程中创建的所有执行上下文。

当 JavaScript 引擎第一次遇到你的脚本时，它会创建一个全局的执行上下文并且压入当前执行栈。每当引擎遇到一个函数调用，它会为该函数创建一个新的执行上下文并压入栈的顶部。

引擎会执行那些执行上下文位于栈顶的函数。当该函数执行结束时，执行上下文从栈中弹出，控制流程到达当前栈中的下一个上下文。

```javascript
let a = 'Hello World!';

function first() {
  console.log('Inside first function');
  second();
  console.log('Again inside first function');
}

function second() {
  console.log('Inside second function');
}

first();
console.log('Inside Global Execution Context');
```

![](https://user-gold-cdn.xitu.io/2018/9/20/165f539572076fe3?imageslim)

上述代码的执行上下文栈。

# 如何创建执行上下文

在 JavaScript 代码执行前，执行上下文将经历创建阶段。在创建阶段会发生三件事：

1. this 绑定
2. 创建词法环境组件
3. 创建变量环境组件

所以执行上下文在概念上表示如下：

```text
ExecutionContext = {
  ThisBinding = <this value>,
  LexicalEnvironment = {},
  VariableEnvironment = {}
}
```

## This 绑定

在全局执行上下文中，this 的值指向全局对象。

在函数执行上下文中，this 的值取决于该函数是如何被调用的。如果它被一个引用对象调用，那么 this 会被设置成那个对象，否则 this 的值被设置为全局对象或者 undefined

## 词法环境

简单来说词法环境是一种持有标识符—变量映射的结构。（这里的标识符指的是变量/函数的名字，而变量是对实际对象[包含函数类型对象]或原始数据的引用。

词法环境内部有两个组件：1）环境记录器 2）对外部环境的引用

* 环境记录器是存储变量和函数声明的实际位置。
* 外部环境的引用意味着它可以访问其父级词法环境（作用域）。

词法环境有两种类型：

* 全局环境（在全局执行上下文中）是没有外部环境引用的词法环境。全局环境的外部环境引用是 null。它拥有内建的 Object/Array/等、在环境记录器内的原型函数（关联全局对象，比如 window 对象）还有任何用户定义的全局变量。
* 在函数词法环境中，函数内部用户定义的变量存储在环境记录器中。并且引用的外部环境可能是全局环境，或者任何包含此内部函数的外部函数。

环境记录器也有两种类型：

1. 声明式环境记录器存储变量、函数和参数。
2. 对象环境记录器用来定义出现在全局上下文中的变量和函数的关系。

简而言之：

* 在全局环境中，环境记录器是对象环境记录器。
* 在函数环境中，环境记录器是声明式环境记录器。

注意 — 对于函数环境，声明式环境记录器还包含了一个传递给函数的 arguments 对象。

伪代码：

```text
GlobalExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 在这里绑定标识符
    }
    outer: <null>
  }
}

FunctionExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 在这里绑定标识符
    }
    outer: <Global or outer function environment reference>
  }
}
```

## 变量环境

变量环境也是一个词法环境，它与变量环境的一个不同就是前者被用来存储函数声明和变量（let 和 const）绑定，而后者只用来存储 var 变量绑定。

🌰：

```javascript
let a = 20;
const b = 30;
var c;

function multiply(e, f) {
 var g = 20;
 return e * f * g;
}

c = multiply(20, 30);
```

伪代码：

```text
GlobalExectionContext = {

  ThisBinding: <Global Object>,

  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 在这里绑定标识符
      a: < uninitialized >,
      b: < uninitialized >,
      multiply: < func >
    }
    outer: <null>
  },

  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 在这里绑定标识符
      c: undefined,
    }
    outer: <null>
  }
}

FunctionExectionContext = {
  ThisBinding: <Global Object>,

  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 在这里绑定标识符
      Arguments: {0: 20, 1: 30, length: 2},
    },
    outer: <GlobalLexicalEnvironment>
  },

VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 在这里绑定标识符
      g: undefined
    },
    outer: <GlobalLexicalEnvironment>
  }
}
```

可能你已经注意到 let 和 const 定义的变量并没有关联任何值，但 var 定义的变量被设成了 undefined。

这是因为在创建阶段时，引擎检查代码找出变量和函数声明，虽然函数声明完全存储在环境中，但是变量最初设置为 undefined（var 情况下），或者未初始化（let 和 const 情况下）。

这就是我们说的变量声明提升。

# 执行阶段

完成对所有这些变量的分配，最后执行代码，在执行阶段，如果 JavaScript 引擎不能在源码中声明的实际位置找到 let 变量的值，它会被赋值为 undefined。

参考：

[[译] 理解 JavaScript 中的执行上下文和执行栈}]: https://juejin.im/post/5ba32171f265da0ab719a6d7

