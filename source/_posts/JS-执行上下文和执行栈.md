---
title: JS-执行上下文和执行栈
date: 2019-06-03 17:08:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 执行上下文是当前 JavaScript 被解析和执行时所在环境的抽象概念。执行栈用于存储在代码执行期间创建的所有执行上下文。

<!--more-->

### 执行上下文的类型

执行上下文总共有三种类型：

* 全局执行上下文：只有一个，浏览器中的全局对象就是 window 对象，this 会指向这个全局对象。

* 函数执行上下文：存在无数个，只有在函数被调用的时候才会被创建，每次调用函数都会创建一个新的执行上下文。

* Eval 函数执行上下文：运行在 Eval 函数中的代码。

***
### 执行上下文栈

接下来问题来了，我们写了那么多函数，如何管理创建的那么多执行上下文呢？

Javascript 创建了执行上下文栈，也叫调用栈，来管理在代码执行期间创建的所有执行上下文。

首次运行 Javascript 代码时，会创建一个全局执行上下文并 Push 到当前的执行栈中。每当发生函数调用，引擎都会为该函数创建一个新的函数执行上下文并 Push 到当前执行栈的栈顶。

根据执行栈 LIFO 规则，当栈顶函数运行完成后，其对应的函数执行上下文将会从栈中 Pop 出，上下文控制权将会移到当前执行栈的下一个执行上下文。

```javascript
var a = 'Hello World!';

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

![]('https://user-gold-cdn.xitu.io/2018/11/5/166e258e1d0281a6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1')

***
### 执行上下文的创建

执行上下文分为两个阶段创建：1） 创建阶段 2）执行阶段

#### 创建阶段

* 确定 this 的值，也被称为 This Binding

* LexicalEnvironment-词法环境被创建

* VariableEnvironment-变量环境被创建

```javascript
ExecutionContext = {
    ThisBinding: <this value>,
    LexicalEnvironment: {...},
    VariableEnvironment: {...}
}
```

##### This Binding

* 全局执行上下文中，this 指向全局对象，在浏览器中 this 的值指向 window 对象，而在 nodejs 中指向这个文件的 module 对象。

* 函数执行上下文中，this 的值取决于函数的调用方式。具体有：默认绑定、隐式绑定、显示绑定、new 绑定等。

##### 词法环境

词法环境有两个组成部分：

* 环境记录：存储变量和函数声明的实际位置

* 对外部环境的引用：可以访问其外部词法环境

词法环境有两种类型：

* 全局环境：是一个没有外部环境的词法环境，其外部环境引用为 null。拥有一个全局对象（window）及其关联的方法和属性以及任何用户自定义的全局变量， this 的值指向这个全局对象。

* 函数环境：用户在函数中定义的变量被存储在环境记录中，包含了 arguments 对象。对外部环境的引用可以是全局环境，也可以是包含内部函数的外部函数环境，

```javascript
GlobalExectionContext = {  // 全局执行上下文
  LexicalEnvironment: {          // 词法环境
    EnvironmentRecord: {          // 环境记录
      Type: "Object",                // 全局环境
      // 标识符绑定在这里 
      outer: <null>                 // 对外部环境的引用
  }  
}

FunctionExectionContext = { // 函数执行上下文
  LexicalEnvironment: {     // 词法环境
      EnvironmentRecord: {      // 环境记录
        Type: "Declarative",        // 函数环境
        // 标识符绑定在这里          // 对外部环境的引用
        outer: <Global or outer function environment reference>  
    }  
  }
  ```
  
  ##### 变量环境
  
  变量环境也是一个词法环境，因此它具有上面定义的词法环境的所有属性。
  
  在ES6中，词法环境和变量环境的区别在于前者用于存储函数声明和变量(let const)绑定，后者仅用于存储变量(var)绑定。
  
  ```javascript
  GlobalExectionContext = {
  
    ThisBinding: <Global Object>,
  
    LexicalEnvironment: {  
      EnvironmentRecord: {  
        Type: "Object",  
        // 标识符绑定在这里  
        a: < uninitialized >,  
        b: < uninitialized >,  
        multiply: < func >  
      }  
      outer: <null>  
    },
  
    VariableEnvironment: {  
      EnvironmentRecord: {  
        Type: "Object",  
        // 标识符绑定在这里  
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
        // 标识符绑定在这里  
        Arguments: {0: 20, 1: 30, length: 2},  
      },  
      outer: <GlobalLexicalEnvironment>  
    },
  
    VariableEnvironment: {  
      EnvironmentRecord: {  
        Type: "Declarative",  
        // 标识符绑定在这里  
        g: undefined  
      },  
      outer: <GlobalLexicalEnvironment>  
    }  
  }
  ```
  
  ##### 执行阶段
  
  此阶段，完成对所有变量的分配，然后执行代码。
  
  ### 参考
  
  * [理解JavaScript 中的执行上下文和执行栈](https://juejin.im/post/5bf3d20ff265da61776b95da)
