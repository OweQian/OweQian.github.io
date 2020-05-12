---
title: LeetCode-010-实现 strStr()
img: https://miro.medium.com/max/1200/1*9EEAMYxLObepKMaOCgYgvA.jpeg
date: 2020-05-09 20:01:52
tags: [LeetCode]
photos: /medias/featureimages/s29484334.jpg
categories: 数据结构与算法
---

#### 导语
> 本系列为 LeetCode 刷题系列，旨在夯实 JavaScript基础，了解常见算法。 

<!--more-->

# 前言

* 难度：简单

* 设计知识：字符串

* 题目地址：https://leetcode-cn.com/problems/implement-strstr/

* 题目内容：

```
实现 strStr() 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

示例 1:

输入: haystack = "hello", needle = "ll"
输出: 2
示例 2:

输入: haystack = "aaaaa", needle = "bba"
输出: -1
说明:

当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。
```

# 解题

## 解法 - indexOf

```javascript
/**
 * @param {string} haystack
 * @param {string} needle
 * @return {number}
 */
var strStr = function(haystack, needle) {
    return haystack.indexOf(needle);
};
```

* 执行测试

输入：

```
"hello"
"ll"
```

输出：

```
2
```

预期结果：

```
2
```

* 解题思路：

JS 内置方法 indexOf，判断数组中是否存在判断条件中的值。如果存在，则返回第一次出现的索引；如果不存在，则返回 -1。

## 解法 - 遍历，subString

```javascript
/**
 * @param {string} haystack
 * @param {string} needle
 * @return {number}
 */
var strStr = function(haystack, needle) {
    if (!needle) {
        return 0
    }
    for (let i = 0; i < haystack.length; i++) {
        if (haystack.substring(i, i+needle.length) === needle) {
            return i
        }
    }
    return -1;
};
```

* 执行测试

输入：

```
"hello"
"ll"
```

输出：

```
2
```

预期结果：

```
2
```

* 解题思路：

判断 needle 是否为空，为空返回 0。

遍历 haystack，从 i 开始，判断 haystack 中的 i + needle.length 位是否等于 needle，是则返回对应的坐标 i。
