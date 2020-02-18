---
title: JS-类型判断
date: 2020-01-02 10:27:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 类型判断在 Web 开发中有非常广泛的作用，简单的有判断数字、字符串，复杂的有判断数组还是对象等。

<!--more-->

# typeof

typeof 是一元操作符，放在其单个操作数的前面，操作数可以是任意类型。返回值为表示操作数类型的一个字符串。

除了 null 类型和 object 类型不能准确判断外，其它数据类型都可以返回正确的类型。

```javascript
typeof undefined // 'undefined
typeof '10' // 'string'
typeof 10 // 'number'
typeof false // 'boolean'
typeof Symbol() // 'symbol'
typeof Function // 'function'
typeof null // 'object'
typeof [] // 'object'
typeof {} // 'object'
```

# Object.prototype.toString

当 toString 方法被调用的时候，下面的步骤会被执行：

* 如果 this 值是 undefined，就返回 [object Undefined]

* 如果 this 的值是 null，就返回 [object Null]

* 让 O 成为 ToObject(this) 的结果

* 让 class 成为 O 的内部属性 [[Class]] 的值

* 最后返回由 "[object " 和 class 和 "]" 三个部分组成的字符串

```javascript
Object.prototype.toString.call(1) // "[object Number]"
Object.prototype.toString.call('123') // "[object String]"
Object.prototype.toString.call(true) // "[object Boolean]"
Object.prototype.toString.call(undefined) // "[object Undefined]"
Object.prototype.toString.call(null) // "[object Null]"
Object.prototype.toString.call({a: 1}) // "[object Object]"
Object.prototype.toString.call([1, 2]) // "[object Array]"
Object.prototype.toString.call(new Date()) // "[object Date]"
Object.prototype.toString.call(new Error()) // "[object Error]"
Object.prototype.toString.call(/a/g) // "[object RegExp]"
Object.prototype.toString.call(function a() {}) // "[object Function]"
Object.prototype.toString.call(Symbol()) // "[object Symbol]"
Object.prototype.toString.call(new Set()) // "[object Set]"
Object.prototype.toString.call(new Map()) // "[object Map]"
Object.prototype.toString.call(new Math) // "[object Math]"
Object.prototype.toString.call(new JSON) // "[object JSON]"
```

# instanceof

instanceof 运算符用来判断一个对象在其原型链中是否存在一个构造函数的 prototype 属性。

```javascript
var a = []
console.log(a instanceof Array) // true

class A {
  
}
console.log(A instanceof Function) // true
```
