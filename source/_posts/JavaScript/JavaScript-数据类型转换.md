---
title: JavaScript-数据类型转换
img: https://miro.medium.com/max/2560/1*UvWOnSlWP7AbbOtSVUeHMg.png
date: 2020-01-05 19:30:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 类型转换分为显式类型转换和隐式类型转换，每种转换又分为原始类型转换和对象类型转换。

<!--more-->             
***

# 显式类型转换

显式类型转换就是我们所说的强制类型转换

## 其它数据类型转字符串(对象除外)

| 数据类型 | 字符串类型 |
| --- | --- |
| 数字 | 转化为数字对应的字符串 |
| true | 'true' |
| false | 'false' |
| null| 'null' |
| undefined | 'undefined' |
| [] | '' |
| ['Q', 'X'] | 'Q,X' |
| function(){} | 'function(){}' |

```javascript
String(123);      // "123"
String(true);     // "true"
String(null);     // "null"
String(undefined);// "undefined"
String([1,2])   // "1,2"
String({});		  // "[object Object]"
```

## 其它类型转换为数字(对象除外)

| 数据类型 |数字类型 |
| --- | --- |
| 字符串 | 1)数字转换为对应的数字 2）其它转换为 NaN |
| '' | 0 |
| true | 1 |
| false | 0 |
| null | 0 |
| undefined | NaN |
| [] | 0 |
| [9] | 9 存在一个元素且为数字转数字； |
| ['a', 'b'] | NaN |
| function(){} | NaN |


```javascript
Number(20);        // 10 
Number('20');      // 10 
Number(null);      // 0  
Number('');        // 0  
Number(true);      // 1  
Number(false);     // 0  
Number([]);        // 0 
Number([1,2,3]);     // NaN
Number('20a');     // NaN
Number(undefined); // NaN
```

## 其它数据类型转换为布尔类型(包括对象)

除几个特殊值：0、''、false、null、undefined、NaN转换为 Boolean 为 false，其它类型值均为 true（包括对象）。

## 对象转原始类型(字符串和数字)

对象到字符串和字符串到对象的转换是通过调用待转对象的一个方法来完成的，一个麻烦的事实是，JavaScript 对象有两个不同的方法来进行转换。

所有对象继承了两个转换方法：

1）toString()，它的作用是返回一个反映这个对象的字符串。

对象类的 toString() 返回一个字符串：'[object, Object]'

数组类的 toString() 将每个数组元素转换为字符串，并在元素之间添加逗号后合并成字符串

函数类的 toString() 返回这个函数的实现定义的表示方式

日期类的 toString() 返回一个可读的日期和时间字符串

正则表达式的 toString() 返回这个正则表达式直接量的字符串

```javascript
({x: 1, y: 2}).toString() // '[object, Object]'
[1, 2].toString() // '1,2'
(function() {}).toString() // 'function() {}'
(/\d+/).toString() // '/\\d/'
new Date(2010,0,1).toString() // 'Fri Jan 012010 00:00:00 GMT-0800(PST)'
```

2) valueOf()，如果存在原始值，它就默认将对象转换为表示它的原始值。对象是复合值，而且大多数对象无法真正表示为一个原始值，因此默认的 valueOf() 方法简单的返回对象本身（日期对象除外）。

```javascript
({x: 1, y: 2}).valueOf() // {x: 1, y: 2}
[1, 2].valueOf() // [1, 2]
(function() {}).valueOf() // function() {}
(/\d+/).valueOf() // /\\d/
new Date(2010,0,1).valueOf() // 1262275200000
```

### 对象转换为字符串

对象转换为字符串经历了以下步骤：

* 如果对象具有 toString() 方法，则调用这个方法。如果它返回一个原始值，将这个值转换为字符串并返回。

* 如果对象没有 toString() 方法，或者这个方法不返回一个原始值，则调用 valueOf() 方法，如果它返回一个原始值，将这个值转换为字符串并返回。

* 如果两者都不成功，抛出类型错误。

### 对象转换为数字

对象转换为数字经历了以下步骤：

* 如果对象具有 valueOf() 方法，则调用这个方法。如果它返回一个原始值，将这个值转换为数字并返回。

* 否则，如果对象具有 toString() 方法，则调用 toString() 方法，如果它返回一个原始值，将这个值转换为数字并返回。

* 如果两者都不成功，抛出类型错误。

# 四则运算

隐式类型转化是不需要认为的强制类型转化，javaScript 自动将类型转化为需要的类型，所以称之为隐式类型转换。

## 加法运算

加法运算符是在运行时决定，到底是执行相加，还是执行连接。

如果双方都不是字符串：

* Boolean + Boolean 会转换为数字相加

* Boolean + Number 布尔类型会转换为数字相加

* Object + Number Object型调用 valueOf，如果不是 String、Boolean或者 Number类型，则继续调用 toString()转化为字符串。

```javascript
true + true  // 2
1 + true     // 2
[1] + 3      // '13'
```

字符串和字符串以及字符串和非字符串相加都会进行拼接。

```javascript
1 + 'b'     // ‘1b’
false + 'b' // ‘falseb’
```

## 其它运算

其它算数运算符(减乘除)，所有运算数一律转换为数值，再进行相应的运算。

```javascript
1 * '2'  // 2
1 * []   // 0
```

# 逻辑运算符

逻辑运算符包括两种情况：条件判断和赋值操作。

## 条件判断

* &&：所有条件为真，整体才为真

* ||：其中任一条件为真，整体就为真

```javascript
true && true   // true
true && false  // false
true || true   // true
true || false  // true
```

## 赋值操作

* A && B

首先看 A 的真假，A 为假，返回 A 的值，A 为真则返回 B 的值（不管B是什么）

```javascript
console.log(0 && 1) // 0
console.log(1 && 2) // 2
```

* A || B

首先看 A 的真假，A 为真，返回 A 的值，A 为假则返回 B 的值（不管B是什么）

```javascript
console.log(0 || 1) // 1
console.log(1 || 2) // 1
```

# 比较远算符

比较运算符在逻辑语句中使用，以判定变量或值是否相等。

```
== 和 === 的区别？
```

对于 === 来说，是严格意义上的相等，会比较两个操作符的类型和值。

* 如果 X 和 Y 类型不同，则返回 false

* 如果 X 和 Y 类型相同，则根据下方表格进一步判断

| 条件 | 例子 | 返回值 |
| --- | --- | --- |
| undefined === undefined | undefined === undefined | true |
| null === null | null === null | true |
| String === String | 'a' === 'a'<br>'a' === 'aa' | true<br>false |
| Boolean === Boolean | true === true<br>false === false | true<br>false |
| Symbol === Symbol | 相同的 Symbol 返回 true<br>不相同的 Symbol 返回 true |
| Number === Number<br>其中一个为 NaN，返回false<br>X 和 Y 值相等，返回 true<br> 0 和 -0，返回 true | NaN === NaN<br>NaN === 1<br>3===3<br>0 === -0 | false<br>false<br>true<br>true |

对于 ==来说，是非严格意义上的相等，先判断两个操作符的类型是否相等，如果类型不同，则先进行类型转换，然后再判断值是否相等。

* 如果 X 和 Y 的类型相同，返回 X == Y 的比较结果；

* 如果 X 和 Y 的类型不同，根据下方表格进一步判断;

| 条件 | 例子 | 返回值 |
| --- | --- | --- |
| null == undefined | null == undefined | true |
| String == Number，String 转 Number | '2' == 2 | true |
| Boolean == Number，Boolean 转 Number |  true == 1 | true |
| Object == String,Number,Symbol，将 Object 转化为原始类型再比较值大小n | [1] == 1<br>[1] == '1' | true<br>true |
| 其他返回 false |  | false |

# 参考文档：

[ 动画：《大前端吊打面试官系列》 之原生 JavaScript 精华篇]:https://juejin.im/post/5e34d19de51d4558864b1d1f
