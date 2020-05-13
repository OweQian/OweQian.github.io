---
title: LeetCode-013-最后一个单词的长度
img: https://miro.medium.com/max/1200/1*9EEAMYxLObepKMaOCgYgvA.jpeg
date: 2020-05-13 17:16:52
tags: [LeetCode]
photos: /medias/featureimages/s29484334.jpg
categories: 数据结构与算法
---

#### 导语
> 本系列为 LeetCode 刷题系列，旨在夯实 JavaScript基础，了解常见算法。 

<!--more-->

# 前言

* 难度：简单

* 设计知识：字符串、数组、正则表达式

* 题目地址：https://leetcode-cn.com/problems/length-of-last-word/

* 题目内容：

```
给定一个仅包含大小写字母和空格 ' ' 的字符串 s，返回其最后一个单词的长度。如果字符串从左向右滚动显示，那么最后一个单词就是最后出现的单词。

如果不存在最后一个单词，请返回 0 。

说明：一个单词是指仅由字母组成、不包含任何空格字符的 最大子字符串。

 

示例:

输入: "Hello World"
输出: 5
```

# 解题

## 解法 - 正则表达式、数组

```javascript
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLastWord = function(s) {
  s = s.replace(/\s*$/g, "");
  let arr = s.split(' ');
  return arr[arr.length - 1].length;
};
```

* 执行测试

输入：

```
"Hello World"
```

输出：

```
5
```

预期结果：

```
5
```

* 解题思路：

此处有一个小坑，如果直接用 String.prototype.split()，对于末尾有空格的字符串来说，如：'a '，会返回['a', '']，所以我们首先用正则表达式匹配替换掉以空格结尾的字符串的空格，然后再调用 String.prototype.split() 将字符串转化为数组，最后返回数组最后一个元素的长度即为最后一个单词的长度。
