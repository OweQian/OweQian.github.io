---
title: 面试题-常见面试题系列之JS基础
date: 2019-11-25 09:46:52
tags: [面试题]
categories: 前端
---

#### 导语

本系列旨在整理 JS 常见面试题，帮助梳理 JS 基础知识点。

### 🌰 ['1', '2', '3'].map(parseInt) 输出结果

#### parseInt

解析一个字符串参数，返回一个指定基数的整数。

```javascript
const intValue = parseInt(string[, radix]);
```

string 要被解析的值。如果参数不是一个字符串，则将其转换为字符串。字符串开头的空白符会被忽略。

radix 一个介于 2~36之间的整数，表示上述字符串的基数，默认值为 10。

parseInt 返回一个整数或 NaN。

```javascript
parseInt(100); // 100
parseInt(100, 10); // 1*10*10 + 0*10*1 + 0*10*0 = 100
parseInt(100, 2); // 1*2*2 + 0*2*1 + 0*2*0 = 4
```

在 radix 为 undefined ，或 radix 为 0 或没有指定的情况下，JS 作如下处理：

* string 以 '0x' 或 '0X' 开头，则 radix 为 16。

* string 以 '0' 开头，radix 为 8 或者 10，具体哪个基数由实现环境决定。

* string 以其它任何值开头，则 radix 为 10。

#### map

创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

```javascript
var new_array = arr.map(function callback(currentValue[,index[, array]]) {
 // Return element for new_array
 }[, thisArg])
```

callback 回调函数需要三个参数，分别为 currentValue(数组中当前正在处理的元素的值)、index(数组中当前正在处理的元素的索引)、array(map 方法中被调用的数组)。

```javascript
const arr = [1, 2, 3]
arr.map(num => num * 2) // [2, 4, 6]
```

#### ['1', '2', '3'].map(parseInt) 输出

对于每个迭代 map，parseInt() 传递两个参数：字符串和基数。所以实际执行的代码为：

```javascript
['1', '2', '3'].map(parseInt((item, index) => parseInt(item, index))
```

执行步骤为：

```javascript
parseInt('1', 0); // 1 (10 进制)
parseInt('2', 1); // NaN radix(2-36)
parseInt('3', 2); // NaN 不符合2进制位数取值(0-1)
```

所以：

```javascript
['1', '2', '3'].map(parseInt) // [1, NaN, NaN]
```

