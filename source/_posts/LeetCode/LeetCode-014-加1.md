---
title: LeetCode-014-加1
img: https://miro.medium.com/max/1200/1*9EEAMYxLObepKMaOCgYgvA.jpeg
date: 2020-05-13 18:16:52
tags: [LeetCode]
photos: /medias/featureimages/s29484334.jpg
categories: 数据结构与算法
---

#### 导语
> 本系列为 LeetCode 刷题系列，旨在夯实 JavaScript基础，了解常见算法。 

<!--more-->

# 前言

* 难度：简单

* 设计知识：数组

* 题目地址：https://leetcode-cn.com/problems/plus-one/

* 题目内容：

```
给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

示例 1:

输入: [1,2,3]
输出: [1,2,4]
解释: 输入数组表示数字 123。
示例 2:

输入: [4,3,2,1]
输出: [4,3,2,2]
解释: 输入数组表示数字 4321。
```

# 解题



## 解法 - 正则表达式、数组

```javascript
/**
 * @param {number[]} digits
 * @return {number[]}
 */
var plusOne = function(digits) {
    for (let i = (digits.length - 1); i >= 0; i--) {
        digits[i]++
        digits[i] = digits[i] % 10
        if (digits[i] > 0) {
            return digits
        }
    }
    return [1, ...digits]
};
```

* 执行测试

输入：

```
[1,2,3]
```

输出：

```
[1,2,4]
```

预期结果：

```
[1,2,4]
```

* 解题思路：

此处有一个小坑，对于 [9, 9, 9] 这样的数组，最后一个元素 + 1后，正确返回的数组应为 [1, 0, 0, 0]

从最后一项开始遍历数组，最后一项元素加 1 后，判断值是否为 10，1-9 与 10 取余，结果仍为 1 - 9，10 与 10 取余，结果为 0.

如果取余结果大于 0，则直接返回，终止循环

如果取余结果为 0，则进入下一轮循环，当碰到 999 这类情况时，循环会执行到最后一位，因为它是极致情况，最后遍历出来的是 [0, 0, 0]，这时候，我们只需要往数组首位添加一个 1 即可，所以我们使用 ES6 的 [...] 扩展运算符来快速达到我们的目的。
