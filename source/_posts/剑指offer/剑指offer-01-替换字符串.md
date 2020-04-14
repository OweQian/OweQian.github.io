---
title: 剑指offer-01-替换字符串
img: https://www.linuxidc.com/upload/2017_02/170201195910721.jpg
date: 2020-04-10 18:44:52
tags: [剑指offer]
photos: /medias/featureimages/s29484334.jpg
categories: 数据结构与算法
---

#### 导语
> 本系列为 剑指offer 刷题系列，旨在夯实 JavaScript基础，了解常见算法。 

<!--more-->

# 前言

* 难度：简单

* 设计知识：正则表达式、数组遍历

* 题目内容：

```
请实现一个函数，把字符串中的每个空格替换成"%20"。

例如输入“We are happy.”，则输出“We%20are%20happy.”。
```

# 解题

## 解法 - 正则表达式

```javascript
/**
 * @param {String} str
 */
function replaceEmpty(str) {
  return str.replace(/ /g, '%20');
}
```

* 执行测试

输入：

```
replaceEmpty('we are family')
```

输出：

```
"we%20are%20family"
```

预期结果：

```
"we%20are%20family"
```

* 解题思路：

直接使用正则表达式全局替换

String.prototype.replace: 返回一个由替换值替换一些或所有匹配的模式后的新字符串。模式可以是一个字符串或者是一个正则表达式，替换值可以是一个字符串或者是一个每次匹配都要调用的回调函数。

```
str.replace(regexp|substr, newSubStr|function)
```

* regexp (pattern)

一个RegExp 对象或者其字面量。该正则所匹配的内容会被第二个参数的返回值替换掉。

* substr (pattern)

一个将被 newSubStr 替换的 字符串。其被视为一整个字符串，而不是一个正则表达式。仅第一个匹配项会被替换。

* newSubStr (replacement)

用于替换掉第一个参数在原字符串中的匹配部分的字符串。

* function (replacement)

一个用来创建新子字符串的函数，该函数的返回值将替换掉第一个参数匹配到的结果。

返回值：

一个部分或全部匹配由替代模式所取代的新的字符串。

描述：

该方法并不改变调用它的字符串本身，而只是返回一个新的替换后的字符串。

在进行全局的搜索替换时，正则表达式需包含 g 标志。
