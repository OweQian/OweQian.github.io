---
title: JS-this、call、apply
date: 2019-06-13 13:00:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 在 Javascript 编程中，this、Function.prototype.call、Function.prototype.apply 有着广泛的运用，理解它们的用法至关重要。

<!--more-->

### this

Javascript 中的 this 总是指向一个对象，而具体指向哪个对象是在运行时基于函数的执行环境动态绑定的。

#### this 的指向

在具体实际应用中，this 的指向通常可分为以下4种:

* 作为对象的方法调用

* 作为普通函数调用

* 作为构造器调用

* Function.prototype.call、Function.prototype.apply

##### 作为对象的方法调用

当函数作为对象的方法调用时，this 指向该对象：

```javascript
var obj = {
  a: 1,
  getA: function () {
    console.log(this.a)
  }
}

obj.getA()

// 1
```

##### 作为普通函数调用

函数作为普通函数调用，此时的 this 总是指向全局对象。在浏览器中，这个全局对象是 window 对象。

```javascript
var a = 1

var getName = function () {
  console.log(this.a)
}

getName()
// 1
```

##### 作为构造器调用

当用 new 运算符调用函数时，该函数会返回一个对象。通常情况下，构造器里的 this 就指向返回的这个对象。

```javascript
var MyClass = function () {
  this.name = 'sven'
}

var obj = new MyClass()
console.log(obj.name)
// 'sven'
```

如果构造器显式地返回了一个对象，那么此次运算结果最终会返回这个对象，而不是我们之前期待的 this。

```javascript
var MyClass = function () {
  this.name = 'sven'
  return {
    name: 'wang'
  }
}

var obj = new MyClass()
console.log(obj.name)
// wang
```

如果构造器不显式地返回任何数据，或者返回一个非对象类型的数据，就不会造成上述问题。

```javascript
var MyClass = function () {
  this.name = 'sven'
  return 'wang'
}

var obj = n
console.log(obj.name)
// 'sven'
```

##### Function.prototype.call、Function.prototype.apply

用 Function.prototype.call、Function.prototype.apply 可以动态改变传入函数的 this。

```javascript
var obj1 = {
  name: 'wang',
  getName: function () {
    console.log(this.name)
  }
}

var obj2 = {
  name: 'zhang'
}

obj1.getName()
obj1.getName.call(obj2)

// 'wang'
// 'zhang'
```

***
#### 丢失的 this

举个例子：

```javascript
var obj = {
  name: 'wang',
  getName: function () {
    console.log(this.name)
  }
}

obj.getName()
getName = obj.getName
getName()

// 'wang'
// undefined
```

当调用 obj.getName 时，此时 this 指向 obj 对象，所以输出 'wang'。

当用另外一个变量 getName 来引用 obj.getName，并且调用时，它是被当作普通函数来调用，此时 this 指向 window 对象，所以输出 undefined。

***
#### call 和 apply

在实际开发中，特别是在一些函数式风格的代码编写中，call 和 apply 方法尤为重要。

##### 区别

call 和 apply 作用一模一样，区别在于传入参数形式的不同。

apply 接受两个参数，第一个参数指定了函数体内的 this 对象的指向，第二个参数为一个带下标的集合，可以为数组，也可以为类数组。

call 传入的参数数量不固定，跟 apply 不同的是，第一个参数也是代表函数体内 this 对象的指向，从第二个参数开始，每个参数依此传入函数。

当使用 call 或者 apply 时，如果我们传入的第一个参数为 null，则函数体内的 this 会指向默认的宿主对象。

但如果在严格模式下，函数体内的 this 还是为 null。

##### call 和 apply 的用途

下面将详细介绍 call 和 apply 在实际开发中的用途。

###### 改变 this 指向

```javascript
var obj1 = {
  name: 'wang'
}

var obj2 = {
  name: 'zhang'
}

var name = 'qian'

var getName = function () {
  console.log(this.name)
}

getName () // 'qian'
getName.call(obj1)  // 'wang'
getName.call(obj2) // 'zhang'
```

###### Function.prototype.bind

大部分高级浏览器都实现了内置的 Function.prototype.bind，用来指定函数内部的 this 指向。即使没有原生的 Function.prototype.bind ，我们也可以自己来模拟：

```javascript
Function.prototype.bind = function (context) {
  let self = this // 保存原函数
  console.log(this)
  return function () { // 返回一个新的函数
    return self.apply(context, arguments) // 执行新的函数时，会把之前传入的 context 当作新函数体内的 this
  }
}

var obj = {
  name: 'seven'
}

var func = function () {
  console.log(this.name)
}.bind(obj)

func()
```

上述为一个简化版的 Function.prototype.bind 实现，通常我们还会把它实现得稍微复杂一些，使得可以在 func 函数中预先填入一些参数：

```javascript
Function.prototype.bind = function () {
  console.log(arguments)
  var self = this, // 保存原函数
      context = [].shift.call(arguments), // 需要绑定的 this 上下文
      args = [].slice.call(arguments) // 剩余的参数转化为数组
  console.log(context)
  console.log(args)
  return function () { // 返回一个新韩淑
    return self.apply(context, [].concat(args, [].slice.call(arguments))) // 执行新的函数时，把之前传入的 context 当作新函数体内的 this，并且组合两次分别传入的参数，作为新函数的参数
  }
}

var obj = {
  name: 'seven'
}

var func = function (a, b, c, d) {
  console.log(this.name)
  console.log([a, b, c, d])
}.bind(obj, 1, 2)
func(3, 4)
```

###### 借用其他对象的方法

借用方法的第一种场景是”借用构造函数“，通过这种技术，可以实现一些类似继承的效果：

```javascript
var A = function (name) {
  this.name = name
}

var B = function () {
  A.apply(this, arguments)
}

B.prototype.getName = function () {
  return this.name
}

var b = new B('wang')

console.log(b.getName()) // 'wang'
```

附：

函数的参数列表 arguments 是一个类数组对象，并不是真正的数组。

如果想在 arguments 中添加一个元素，通常会借用 Array.prototype.push

如果想把 arguments 转换为真正的数组，通常会借用 Array.prototype.slice

如果想截取 arguments 中的第一个元素，通常会借用 Array.prototype.shift
