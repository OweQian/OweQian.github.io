---
title: JavaScript-基础之this
date: 2020-01-07 18:10:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> new 关键字，首先想到的是 new 一个实例对象。

<!--more-->           
***

# new 内部发生了什么

new 的过程包括以下四个阶段：

* 创建一个新对象

* 这个新对象的 __proto__ 属性指向构造函数的 prototype 属性

* 将这个新对象绑定到此函数的 this 上

* 如果这个函数没有返回其它对象，就返回这个新对象

# 手动实现一个 new 操作符

```javascript
// 创建一个新对象
// 链接到原型链
// 绑定 this
// 返回对象

function create(Con, ...args) {
    
  let obj = {}
  
  obj.__proto__ = Con.prototype
  
  let result = Con.apply(obj, args)
  
  return result instanceof Object ? result : obj
}
```

# 有几种创建对象的方式

最常用的常见对象的两种方式：

* 对象字面量

* new 构造函数

其它创建对象的方式：

* Object.create

Object.create(proto, [propertiesObject])

* proto: 新创建对象的原型对象

* 为创建的新对象设置属性和值

## 字面量相对于 new 创建对象有哪些优势

* 代码量少，易阅读

## new / 字面量 与 Object.create(null)创建对象的区别？

* new / 字面量创建的对象的原型指向 Object.prototype，会继承 Object 属性和方法

* Object.create(null) 创建的对象，其原型指向 null，null 作为原型链的顶端，没有继承任何属性和方法。

* 对象字面量运行速度快，可以在解析时被优化。new 一个对象，解析器需要顺着作用域链从当前作用域开始查找，如果在当前作用域找到名为 Object() 的函数就执行，如果一直没找到，就会顺着作用链继续查找，直到找到为止。

* Object() 构造函数可以接收参数，通过这个参数可以把实例的创建过程委托给其它内置构造函数，并返回另外一个对象实例。


