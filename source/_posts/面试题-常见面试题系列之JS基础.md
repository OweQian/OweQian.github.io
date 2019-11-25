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

### 🌰 什么是节流和防抖？区别？如何实现？

#### 防抖

动作绑定事件，动作发生后一定时间内触发时间，在这段时间内如果该动作又发生，则重新等待一定时间再触发事件。

```javascript
/**
 * 防抖 debounce 定时器
 * @param fn {function} 回调函数
 * @param wait {number} 时间间隔
 * @author wangxiaobai
 */
export function debounce (fn, wait) {
  let timer = null;
  return () => {
    // 清除上次执行得定时器
    if (timer) {
      clearTimeout(timer);
    }
    // 设立新定时器
    timer = setTimeout(() => {
      fn.apply(this, arguments);
    }, wait);
  }
}
```

#### 节流

动作绑定事件，动作发生后一定时间内触发事件，在这段时间内如果该动作又发生，则无视该动作，直到事件执行完后，才能重新触发。

```javascript
/**
 * 节流 throttle 定时器
 * @param fn {function} 回调函数
 * @param wait {number} 时间间隔
 * @author wangxiaobai
 */
export function throttle (fn, wait) {
  // 上一次函数的执行时间  
  let activeTime = 0;
  return () => {
      let currentTime = +new Date();
      if (currentTime - activeTime > wait) {
          fn.apply(this, arguments);
          activeTime = currentTime;
      }
  }
}
```

### 🌰 Set、Map、WeakSet、WeakMap

#### Set

一种叫做集合的数据结构，类似于数组，成员唯一且无序。

```text
new Set([iterable])
```

```javascript
const s = new Set();
[1, 2, 3, 4, 3, 2, 1].forEach(item => s.add(item));

for (let item of s) {
    console.log(item) // 1 2 3 4
}

let arr = [1, 2, 3, 2, 1, 1, 1];
[... new Set(arr)]; // [1, 2, 3]
```

Set 允许你存储任何类型的唯一值，无论是原始值还是对象引用。

向 Set 加入值的时候，不会发生类型转换，所以 5 和 '5' 是两个不同的值。

在 Set 中，NaN 等于自身。

```javascript
let set = new Set();
set.add(NaN);
set.add(NaN);
set; // {NaN} 

let set1 = new Set();
set1.add(5);
set1.add('5');
set; // {5, '5'}
```

* Set 实例属性

constructor: 构造函数

size: 元素数量

```javascript
let set = new Set([1, 2, 3, 2, 1]);
console.log(set.size); // 3
```

* Set 实例方法

操作方法：
 
add(value) 新增
 
delete(value) 存在即删除集合中的 value
 
has(value) 判断集合中是否存在 value
 
clear() 清空集合
 
```javascript
let set = new Set();
set.add(1).add(2).add(1);

set.size; // 2
set.has(1); // true
set.has(3); // false
set.delete(1);
set.has(1); // false
set.clear();
set.size; // 0
```

Array.from 可以将 Set 结构转化为数组	

```javascript
const items = new Set([1, 2, 3, 1]);
const array = Array.from(items);
console.log(array); // [1, 2, 3]

// 或

const arr = [...items]
console.log(arr); // [1, 2, 3]
```

遍历方法：

keys() 返回一个包含集合中所有键的迭代器

values() 返回一个包含集合中所有值的迭代器

entries() 返回一个包含集合中所有键值对的迭代器

forEach(callbackFn, thisArg)

map(callbackFn, thisArg)

filter(callbackFn, thisArg)

```javascript
let set = new Set([1, 2, 3])
console.log(set.keys()); // SetIterator {1, 2, 3}
console.log(set.values()); // SetIterator {1, 2, 3}
console.log(set.entries()); // SetIterator {1 => 1, 2 => 2, 3 => 3}
set.forEach((value, key) => {
    console.log(key + ' : ' + value);
});	// 1:1 2:2 3:3
set = new Set([...set].map(item => item * 2));
console.log(set);	// {2, 4, 6}

set = new Set([...set].filter(item => (item >= 4)));
console.log(set);	// {4, 6}
```

因此，Set 很容易实现两个数组的交集、并集、差集

```javascript
let set1 = new Set([1, 2, 3]);
let set2 = new Set([4, 3, 2]);

let intersect = new Set([...set1].filter(value => set2.has(value)));
let union = new Set([...set1, ...set2]);
let difference = new Set([...set1].filter(value => !set2.has(value)));

console.log(intersect); // {2, 3}
console.log(union); // {1, 2, 3, 4}
console.log(difference); // {1}
```

#### WeakSet

允许你将弱引用对象储存在一个集合中。

WeakSet 与 Set 的区别：

* WeakSet 只能储存对象引用，不能存放值，而 Set 对象都可以

* WeakSet 中储存的对象值都是被弱引用的，即垃圾回收机制不考虑 WeakSet 对该对象的引用，如果没有其它的变量或属性引用这个对象值，则这个对象或被垃圾回收掉

属性：

constructor: 构造函数

方法：

add(value) 添加元素 value

has(value) 判断是否包含 value

delete(value) 删除 value

```javascript
var ws = new WeakSet();
var obj = {}
var foo = {}

ws.add(obj);
ws.add(foo);

ws.has(obj); // true
ws.has(foo); // false

ws.delete(obj);
ws.has(obj); // false
```

#### Map

一种叫做字典的数据结构，以 [key, value] 的形式存储

```javascript
const m = new Map();
const o = {
    p: 'haha'
};
m.set(o, 'content');
m.get(o); // 'content'

m.has(o); // true
m.delete(o);
m.has(o); // false
```

任何具有 Iterator 接口，每个成员都是一个双元素的数组的数据结构都可以当做 Map 构造函数的参数。

```javascript
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

map.size; // 2
map.has('name'); // true
map.get('name'); // "张三"
map.has('title'); // true
map.get('title'); // "Author"

const set = new Set([
  ['foo', 1],
  ['bar', 2]
]);
const m1 = new Map(set);
m1.get('foo'); // 1

const m2 = new Map([['baz', 3]]);
const m3 = new Map(m2);
m3.get('baz'); // 3
```

如果读取一个未知的键，则返回undefined。

```javascript
new Map().get('asfddfsasadf'); // undefined
```

只有对同一个对象的引用，Map 结构才将其视为同一个键。这一点要非常小心。

```javascript
const map = new Map();

map.set(['a'], 555);
map.get(['a']); // undefined
```

Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map 将其视为一个键，比如0和-0就是一个键，布尔值true和字符串true则是两个不同的键。另外，undefined和null也是两个不同的键。虽然NaN不严格相等于自身，但 Map 将其视为同一个键。

```javascript
let map = new Map();

map.set(-0, 123);
map.get(+0); // 123

map.set(true, 1);
map.set('true', 2);
map.get(true); // 1

map.set(undefined, 3);
map.set(null, 4);
map.get(undefined); // 3

map.set(NaN, 123);
map.get(NaN); // 123
```

属性：

constructor：构造函数

size: 字典中所包含的元素个数

```javascript
const map = new Map([
  ['name', 'An'],
  ['des', 'JS']
]);

map.size; // 2
```

操作方法：

set(key, value) 添加新元素

get(key) 通过键查找特定的数值并返回

has(key) 判断字典中是否存在 key

delete(key) 通过键 key 从字典中移除对应的数据

clear() 字典中所有元素删除

遍历方法：

keys() 将字典中包含的所有键名以迭代器形式返回
 
values() 将字典中包含的所有数值以迭代器形式返回

entries() 返回所有成员的迭代器

forEach() 遍历字典的所有成员

```javascript
const map = new Map([
            ['name', 'An'],
            ['des', 'JS']
        ]);
console.log(map.entries());	// MapIterator {"name" => "An", "des" => "JS"}
console.log(map.keys()); // MapIterator {"name", "des"}
```

与其它数据结构相互转换

1. Map 转 Array

```javascript
const map = new Map([[1, 1], [2, 2], [3, 3]])
console.log([...map]); // [[1, 1], [2, 2], [3, 3]]
```

2. Array 转 map

```javascript
const map = new Map([[1, 1], [2, 2], [3, 3]])
console.log(map);	// Map {1 => 1, 2 => 2, 3 => 3}
```

3. Map 转 Object

```javascript
function mapToObj(map) {
    let obj = Object.create(null);
    for (let [key, value] of map) {
        obj[key] = value;
    }
    return obj
}
const map = new Map().set('name', 'An').set('des', 'JS');
mapToObj(map);  // {name: "An", des: "JS"}
```

4. Object 转 Map

```javascript
function objToMap(obj) {
    let map = new Map();
    for (let key of obj.keys()) {
        map.set(key, obj[key])
    }
    return map
}
objToMap({'name': 'An', 'des': 'JS'}) // Map {"name" => "An", "des" => "JS"}
```

5. Map 转 JSON

```javascript
function mapToJson(map) {
    return JSON.stringify([...map]);
}

let map = new Map().set('name', 'An').set('des', 'JS');
mapToJson(map);	// [["name","An"],["des","JS"]]
```

6. JSON 转 Map

```javascript
function jsonToStrMap(jsonStr) {
  return objToMap(JSON.parse(jsonStr));
}

jsonToStrMap('{"name": "An", "des": "JS"}'); // Map {"name" => "An", "des" => "JS"}
```

#### WeakMap

WeakMap 对象是一组键值对的集合，其中键是弱引用对象，而值可以是任意。

每个键对自己所引用对象的引用都是弱引用，在没有其他引用和该键引用同一对象，这个对象将会被垃圾回收。

属性：

constructor: 构造函数

方法：

has(key)：判断是否有 key 关联对象

get(key)：返回key关联对象（没有则则返回 undefined）

set(key)：设置一组key关联对象

delete(key)：移除 key 的关联对象

#### 总结

* Set

成员唯一、无序

可以遍历

* WeakSet

成员都是对象

成员都是弱引用，可以被垃圾回收机制回收

不能遍历

* Map

本质是键值对的集合

可以遍历

* WeakMap

只接受对象作为键名

键名是弱引用，键值可以是任意的，键名所指向的对象可以被垃圾回收机制回收

不能遍历
