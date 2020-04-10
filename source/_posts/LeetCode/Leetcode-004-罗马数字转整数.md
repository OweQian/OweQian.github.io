---
title: LeetCode-004-罗马数字转整数
img: https://miro.medium.com/max/1200/1*9EEAMYxLObepKMaOCgYgvA.jpeg
date: 2020-04-10 18:11:52
tags: [LeetCode]
categories: 数据结构与算法
---

#### 导语
> 本系列为 LeetCode 刷题系列，旨在夯实 JavaScript基础，了解常见算法。 

<!--more-->

# 前言

* 难度：简单

* 设计知识：数组、数学

* 题目地址：https://leetcode-cn.com/problems/roman-to-integer/

* 题目内容：

```
罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。

字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

示例 1:

输入: "III"
输出: 3
示例 2:

输入: "IV"
输出: 4
示例 3:

输入: "IX"
输出: 9
示例 4:

输入: "LVIII"
输出: 58
解释: L = 50, V= 5, III = 3.
示例 5:

输入: "MCMXCIV"
输出: 1994
解释: M = 1000, CM = 900, XC = 90, IV = 4.
```

# 解题

## 解法 - 转字符串、数组遍历，区分正常情况与特殊情况

```javascript
/**
 * @param {string} s
 * @return {number}
 */
var romanToInt = function(s) {
      /**
       * 特殊情况
       * IV === 4
       * IX === 9
       * XL === 40
       * XC === 90
       * CD === 400
       * CM === 900
       * 正常情况
       * I === 1
       * V === 5
       * X === 10
       * L === 50
       * C === 100
       * D === 500
       * M === 1000
       */
    let arr = s.split('')
    let result = 0
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] === 'I' && arr[i + 1] === 'V') {
            result += 4
            i = i + 1
        } else if (arr[i] === 'I' && arr[i + 1] === 'X') {
            result += 9
            i = i + 1
        } else if (arr[i] === 'X' && arr[i + 1] === 'L') {
            result += 40
            i = i + 1
        } else if (arr[i] === 'X' && arr[i + 1] === 'C') {
            result += 90
            i = i + 1
        } else if (arr[i] === 'C' && arr[i + 1] === 'D') {
            result += 400
            i = i + 1
        } else if (arr[i] === 'C' && arr[i + 1] === 'M') {
            result += 900
            i = i + 1
        } else if (arr[i] === 'I') {
            result += 1
        } else if (arr[i] === 'V') {
            result += 5
        } else if (arr[i] === 'X') {
            result += 10
        } else if (arr[i] === 'L') {
            result += 50
        } else if (arr[i] === 'C') {
            result += 100
        } else if (arr[i] === 'D') {
            result += 500
        } else if (arr[i] === 'M') {
            result += 1000
        }
    }
    return result
};
```

* 执行测试

输入：

```
MCMXCIV
```

输出：

```
1994
```

预期结果：

```
1994
```

* 解题思路：转换为字符串、数组

* split(): 字符串分割为数组
