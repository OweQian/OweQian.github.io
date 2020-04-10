---
title: JavaScript-数组去重
img: https://miro.medium.com/max/2560/1*UvWOnSlWP7AbbOtSVUeHMg.png
date: 2020-01-01 20:57:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 数组去重方法老生常谈，本文主要总结常见的数组去重方法。

<!--more-->               
***

🌰 array = [1, '1', 1, '1']

# 双层循环

```javascript
function unique(array) {
	let result = []
	for (var i = 0, arrayLen = array.length; i < arrayLen; i++) {
  	for (var j = 0, resultLen = result.length; j < resultLen; j++) {
    	if (array[i] === result[j]) {
      	break
      }
    }
    if (j === resultLen) {
      result.push(array[i])
    }
  }
  return result
}

console.log(unique([1, 1, '1', '1']))
```

使用循环嵌套，最外层循环 array，里面循环 result，如果 array[i] 的值跟 result[j] 的值相等，就跳出循环，如果都不等于，说明元素是唯一的，这时候 j 的值就会等于 result 的长度，根据这个特点进行判断，将值添加进 result。

## indexOf

可以使用 indexOf 来简化内层循环：

```javascript
function unique(array) {
	let result = []
	for (var i = 0, arrayLen = array.length; i < arrayLen; i++) {
  	let current = array[i]
    if (result.indexOf(current) === -1) {
      result.push(current)
    }
  }
  return result
}

console.log(unique([1, 1, '1', '1']))
```

## filter

可以使用 filter 的方法：

```javascript
function unique(array) {
    var res = array.filter(function(item, index, array){
        return array.indexOf(item) === index;
    })
    return res;
}
console.log(unique([1, 2, 1, 1, '1']));
```

## Object 键值对

这种方法是利用一个空的 Object 对象，我们把数组的值存成 Object 的 key 值，比如 Object[value1] = true，在判断另一个值的时候，如果 Object[value2]存在的话，就说明该值是重复的。

```javascript
function unique(array) {
    var obj = {};
    return array.filter(function(item, index, array){
        return obj.hasOwnProperty(item) ? false : (obj[item] = true)
    })
}

console.log(unique([1, 2, 1, 1, '1'])); // [1, 2]
```

我们可以发现，是有问题的，因为 1 和 '1' 是不同的，但是这种方法会判断为同一个值，这是因为对象的键值只能是字符串，所以我们可以使用 typeof item + item 拼成字符串作为 key 值来避免这个问题：

```javascript
function unique(array) {
    var obj = {};
    return array.filter(function(item, index, array){
        return obj.hasOwnProperty(typeof item + item) ? false : (obj[typeof item + item] = true)
    })
}

console.log(unique([1, 2, 1, 1, '1'])); // [1, 2, "1"]
```

## ES6

### Set

```javascript
function unique(array) {
  return Array.from(new Set(array))
}

console.log(unique([1, 2, 1, 1, '1'])); // [1, 2, "1"]
```

```javascript
function unique(array) {
  return [...new Set(array)]
}

console.log(unique([1, 2, 1, 1, '1']));
```


