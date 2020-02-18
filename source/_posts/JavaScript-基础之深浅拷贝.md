---
title: JavaScript-基础之深浅拷贝
date: 2020-01-03 11:27:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 类型判断在 Web 开发中有非常广泛的作用，简单的有判断数字、字符串，复杂的有判断数组还是对象等。


#### 导语
> 当我们在JS中复制一个数据，在复制如string，number等基本数据类型时，看似没有出现问题。但是当复制如array，object类型时。出现了意想不到的结果。

<!--more-->

### 实例

🌰：

```
var a = {x: 10, y: 10}
var b = a
b.x = 100
b.y = 100
```

打印一下a和b的值：

```
console.log(a) // {x: 100, y: 200}
console.log(b) // {x: 100, y: 200}
```

发现改变b的值，同时也改了a的值。

🌰：

```
var a = 10
var b = a
b = 20
```

打印一下a和b的值：

```
console.log(a) // 10
console.log(b) // 20
```

??? b的值改变并没有引起a的值改变。

***
### 值类型 vs 引用类型

在JS中，变量传递方式分为值类型和引用类型。值类型包括String，Number，Null，Boolean，Undefined。引用类型包括Object类的所有，如Date，Array，Function。

在参数传递方式上，值类型是按值传递。引用类型是按共享传递(指向同一个内存地址，两者引用同一个值，当一者改变值，另一者也改变)。

JS这样设计的原因在于：按值传递的类型，复制一份存入栈内存，这类类型一般不会占用太多内存，而且按值传递保证了其访问速度。按共享传递的类型，是复制其引用，而不是整个复制其值，保证过大的对象等不会因为不停复制内容而造成内存的浪费。

深拷贝和浅拷贝最根本的区别在于是否真正获取了一个对象的拷贝实体，而不只是引用。

***
### 浅拷贝的实现方式
#### 直接赋值

🌰：

```
  let obj = {username: 'kobe', age: 39, sex: {option1: '男', option2: '女'}}
  let obj1 = obj
  obj1.sex.option1 = '不男不女' // 修改复制的对象会影响原对象
```

打印obj1的值

```
  console.log(obj1) // {username: 'kobe', age: 39, sex: {option1: '不男不女', option2: '女'}}
```

***
#### Object.assign

🌰：

```
  let obj = {
    username: 'kobe'
  };
  let obj1 = Object.assign(obj)
  obj1.username = 'wang'
```

打印obj的值

```
  console.log(obj) // { username: "wang" }
```

***
#### Array.prototype.concat

🌰：

```
let arr = [1, 3, {
    username: 'kobe'
}]
let arr2=arr.concat()
arr2[2].username = 'wang'
```

打印arr的值

```
   console.log(arr) // [1, 3, { username: "wade" }]
```

***
#### Array.prototype.slice

🌰：

```
let arr = [1, 3, {
    username: 'kobe'
}]
let arr2=arr.slice()
arr2[2].username = 'wang'
```

打印arr的值

```
   console.log(arr) // [1, 3, { username: "wade" }]
```

***
#### Array.prototype.concat vs Array.prototype.slice

关于Array的concat和slice补充说明：Array的concat和slice方法不改变原始数组，只是返回了一个浅复制原数组中的元素的一个新数组。

原数组中的元素会按照下述规则拷贝：

* 如果该元素是个引用类型，slice或者concat会拷贝这个引用到新的数组里，两个对象引用都引用了同一个对象。如果被引用的对象发生改变，则新数组和原数组中的这个元素都会发生改变。

* 如果该元素是个值类型，slice或者concat会复制一份这些值到新数组里。在别的数组里修改这些值，并不会影响另一个数组。

🌰：

```
let arr = [1, 3, {
    username: 'kobe'
}];
let arr3 = arr.slice();
arr3[1] = 2
console.log(arr, arr3)
```

打印arr和arr3的值

```
console.log(arr, arr3)
// ---result
// [1, 3, { username: "kobe" }]
// [1, 2, { username: "kobe" }]
```

***
### 深拷贝的实现方式
#### Object.assign

Object.assign可以进行一层深度拷贝

🌰：

```
var obj = {
        name: 'wang',
        test1: null,
        test2: undefined,
        test3: function(){alert(1)},
		test4: {}
    };
var obj1 = Object.assign({}, obj1)
obj1.age = 24
console.log(obj, obj1)
```

打印obj和obj1的值

```
console.log(obj, obj1)
// ----result
// { name: "wang", test1: null, test2: undefined, test3: function(){alert(1)}, test4: {  } }
// { name: "wang", test1: null, test2: undefined, test3: function(){alert(1)}, test4: {  }, age: 24 }
```

然而。。。。。

🌰：

```
var obj = {
        name: 'wang',
        test1: null,
        test2: undefined,
        test3: function(){alert(1)},
		test4: {}
    };
var obj1 = Object.assign({}, obj1)
obj1.test4.age = 24
```

打印obj和obj1的值

```
console.log(obj, obj1)
// ----result
// { name: "wang", test1: null, test2: undefined, test3: function(){alert(1)}, test4: { age: 24 } }
// { name: "wang", test1: null, test2: undefined, test3: function(){alert(1)}, test4: { age: 24 } }
```

***
#### JSON.parse(JSON.stringfy(obj))

说到深拷贝，你肯定会想到JSON.parse(JSON.stringfy(obj))。

🌰：

```
var obj = {
        name: 'wang',
        test: {
            name: 'test'
        }
    };
var obj1 = JSON.parse(JSON.stringify(obj))
obj1.test.name = 24
```

打印obj和obj1的值

```
console.log(obj, obj1)
// ----result
// { name: "wang", test: { name: "test" } }
// { name: "wang", test: { name: 24 } }
```

然而。。。。

🌰：

```
var obj = {
        name: 'wang',
        test1: null,
        test2: undefined,
        test3: function(){alert(1)},
        test4: {}
    };
var obj1 = JSON.parse(JSON.stringify(obj))
```

打印obj和obj1的值

```
console.log(obj, obj1)
// ---result
// { name: "wang", test1: null, test2: undefined, test3: function(){alert(1)}, test4: {  } }
// { name: "wang", test1: null, test4: {  } }
```

???test2和test3不见了。。。。。

JSON.stringify()方法是将一个JavaScript值(对象或者数组)转换为一个JSON字符串，不能接受函数。

***
#### 手写递归方法

```
function deepClone(obj) {
	let result
    let toString = Object.prototype.toString
    if (toString.call(obj) === '[Object Array]') {
    	result = []
      	for (var i =0; i<obj.length; i++) {
        	result[i] = deepClone(obj[i])
        }
    } else if (toString.call(obj) === '[Object Object]') {
    	result = {}
      	for (var key in obj) {
        	if (obj.hasOwnProperty(key)) {
            	result[key] = deepClone(obj[key])
            }
        }
    } else {
    	return obj
    }
  return result
}
```
🌰：调用函数并打印结果

```
var obj = {
        name: 'wang',
        test1: null,
        test2: undefined,
        test3: function(){alert(1)},
        test4: {}
    };
var obj1 = deepClone(obj)
console.log(obj, obj21)
// { name: "wang", test1: null, test2: undefined, test3: function(){alert(1)}, test4: Object {  } }
// { name: "wang", test1: null, test2: undefined, test3: function(){alert(1)}, test4: Object {  } }
```
***
#### jQuery实现

🌰：

```
var obj1 = {
        name: 'wang',
        test1: null,
        test2: undefined,
        test3: function(){alert(1)},
        test4: {}
    };
var obj2 = $.extend(true, {}, obj1)
```

***
#### lodash实现

🌰：

```
var obj1 = {
        name: 'wclimb',
        test1: null,
        test2: undefined,
        test3: function(){alert(1)},
        test4: {}
    };
var obj2 = _.cloneDeep(obj1)
```
