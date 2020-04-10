---
title: LeetCode-003-回文数
img: https://miro.medium.com/max/1200/1*9EEAMYxLObepKMaOCgYgvA.jpeg
date: 2020-01-03 18:11:52
tags: [LeetCode]
categories: 数据结构与算法
---

#### 导语
> 本系列为 LeetCode 刷题系列，旨在夯实 JavaScript基础，了解常见算法。 

<!--more-->

# 前言

* 难度：简单

* 设计知识：字符串、数组、数学

* 题目地址：https://leetcode-cn.com/problems/roman-to-integer/

* 题目内容：

```
判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

示例 1:
输入: 121
输出: true

示例 2:
输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。

示例 3:
输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。

进阶:
你能不将整数转为字符串来解决这个问题吗？
```

# 解题

## 解法 - 数组操作

```javascript
/**
 * @param {number} x
 * @return {boolean}
 */
var isPalindrome = function(x) {
    let Arr = String(x).split('')
    for (let i = 0; i < Arr.length / 2; i++) {
        let j = Arr.length - (i+1)
        if (Arr[i] !== Arr[j]) {
            return false
        }
    }
    return true
};
```

* 执行测试

输入：

```
121
```

输出：

```
true
```

预期结果：

```
true
```

* 解题思路：转换为字符串、数组，交叉对比

* split(): 字符串分割为数组
