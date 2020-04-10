---
title: LeetCode-002-整数反转
img: https://miro.medium.com/max/1200/1*9EEAMYxLObepKMaOCgYgvA.jpeg
date: 2020-01-02 18:11:52
tags: [LeetCode]
categories: 数据结构与算法
---

#### 导语
> 本系列为 LeetCode 刷题系列，旨在夯实 JavaScript基础，了解常见算法。 

<!--more-->

# 前言

* 难度：简单

* 设计知识：数组

* 题目地址：https://leetcode-cn.com/problems/reverse-integer/

* 题目内容：

```
给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

示例 1:

输入: 123
输出: 321
 示例 2:

输入: -123
输出: -321
示例 3:

输入: 120
输出: 21
注意:

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231,  231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。
```

# 解题

## 解法 - 转字符串、数组反转

```javascript
/**
 * @param {number} x
 * @return {number}
 */
var reverse = function(x) {
    // 转换为整数
    let num = Math.abs(x)
    // 转换为字符串
    let string = String(num)
    // 字符串反转
    let result = Number(string.split('').reverse().join(''))
    if (result < Math.pow(2, -31) || result > Math.pow(2, 31) -1) {
        return 0
    }
    if (x < 0) {
        return -result
    }
    return result    
};
```

* 执行测试

输入：

```
123
```

输出：

```
321
```

预期结果：

```
321
```

* 解题思路：转换为字符串、数组

* String: 将其他值转换为字符串

* Number: 将其他值转换为数字值

* Math: JS 内置对象

* split(): 字符串分割为数组

* reverse(): 数组翻转

* join(): 数组转换为字符串

