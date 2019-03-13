---
title: Vue-源码系列随读笔记之认识Flow
date: 2019-03-01 17:02:00
tags: [Vue]
categories: 前端
---

#### 导语
> Flow是facebook出品的javascript静态类型检查工具。Vue.js源码利用了Flow做了静态类型检查。优先了解Flow有助于我们阅读源码。

<!--more-->

***
### 为什么要使用Flow

javascript是动态类型语言，它的灵活性有目共睹。但过于灵活的副作用就是容易写出非常隐蔽的隐患代码，在编译期看上去不会报错，但在运行阶段就可能出现各种奇怪的bug。

类型检查是当前动态类型语言的发展趋势。所谓类型检查，就是在编译期尽早发现bug，又不影响代码运行，使编写javascript具有和编写java等强类型语言相近的体验。

***
### Flow的工作方式

通常类型检查分为2中方式：

* 类型推断：通过变量使用的上下文来推断出变量类型，然后根据这些推断来检查类型。

* 类型注释：事先注释好我们期待的类型，Flow会基于这些注释来判断。

#### 类型推断

无需任何代码修改即可进行类型检查，最小化开发者的工作量。不会强制你改变开发习惯，因为它会自动推断出变量的类型。

```
/*@flow*/

function split(str) {
    return str.split(' ')
}

split(11)
```

#### 类型注释

在某些特定的场景下，添加类型注释可以提供更好更明确的检查依据。

考虑如下代码：

```
/*@flow*/
function add(x, y) {
    return x + y
}
add('hello', 11)
```

Flow检查上述代码时检查不出任何错误，因为从语法层面考虑， + 既可以用在字符串上，也可以用在数字上，我们并没有明确指出 add() 的参数必须为数字。

在这种情况下，我们可以借助类型注释来指明期望的类型。类型注释是以 : 开头，可以在函数参数、返回值、变量声明中使用。

如果我们在上段代码中添加类型注释，就会变成如下：

```
/*@flow*/
function add(x: number, y: number) : number {
    return x + y
}
add('hello', 11)
```

现在Flow就能检查出错误，因为函数参数的期待类型为数字，而我们提供了字符串。

上面的例子是针对函数的类型注释。接下来我们来看看Flow能支持的一些常见的类型注释。

##### 数组

```
/*@flow*/
var arr: Array<number> = [1, 2, 3]
arr.push('hello)
```

数组类型注释的格式为Array<T>，T表示数组中每项的数据类型。在上述代码中，arr是每项均为数字的数组。如果我们给这个数组添加了一个字符串，Flow就能检查出错误。

##### 类和对象

```
/*@flow*/
class Bar {
    x: string
    y: string | number
    z: boolean
    constructor(x: string, y: string | number) {
        this.x = x
        this.y = y
        this.z = z
    }
}

var bar: Bar = new Bar('hello', 11)

var obj: { a: string, b: number, c: Array<string>, d: Bar} = {
    a: 'hello',
    b: 11,
    c: ['hello', 'world'],
    d: new Bar('hello', 11)
}
```

类的类型注释可以对类自身的属性做类型检查，也可以对构造函数的参数做类型检查。

对象的类型注释类似于类，需要指定对象属性的类型。

##### Null

若想任意类型 T 可以为 null 或 undefined ，只需类似如下写成 ?T 的格式即可。

```
/*@flow*/

var foo ?string = null
```

此时，foo 可以为字符串，也可以为 null 。

***
### Flow在Vue.js源码中的应用

有时我们想引用第三方库，或者自定义一些类型，但 Flow 并不认识，检查时就会报错。为了解决这类问题，Flow 提出了一个 libdef 的概念，可以用来识别这些第三方库或者自定义类型。而 Vue.js 也利用了这一特性。

在 Vue.js 的主目录下有 .flowconfig 文件，它是 Flow 的配置文件。这其中的 [libs] 部分用来描述包含指定库定义的目录，默认是名为 flow-typed 的目录。

这里的 [libs] 配置的是 flow ，表示指定的库定义都在 flow 文件夹内。我们打开这个目录，就会发现文件如下：

```
flow
|-- compiler.js   # 编译相关
|-- component.js   # 组件数据结构
|-- global.api.js   # Global API 结构
|-- modules.js   # 第三方库协议
|-- options.js   # 选项相关
|-- ssr.js   # 服务端渲染相关
|-- vnode.js   # 虚拟node相关
|-- weex.js   # weex相关
```

***
### 总结

通过对Flow的认识，有助于阅读Vue源码，并且这种静态类型检查的方式非常有利于大型项目源码的开发和维护。