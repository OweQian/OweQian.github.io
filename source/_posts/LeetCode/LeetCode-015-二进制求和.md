---
title: LeetCode-015-二进制求和
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

* 设计知识：字符串

* 题目地址：https://leetcode-cn.com/problems/add-binary/

* 题目内容：

```
给你两个二进制字符串，返回它们的和（用二进制表示）。

输入为 非空 字符串且只包含数字 1 和 0。

 

示例 1:

输入: a = "11", b = "1"
输出: "100"
示例 2:

输入: a = "1010", b = "1011"
输出: "10101"
 

提示：

每个字符串仅由字符 '0' 或 '1' 组成。
1 <= a.length, b.length <= 10^4
字符串如果不是 "0" ，就都不含前导零。
```

# 解题



## 解法 - 字符串遍历

```javascript
/**
 * @param {string} a
 * @param {string} b
 * @return {string}
 */
var addBinary = function(a, b) {
    let result = []
    let carry = 0
    let aIndex = a.length - 1
    let bIndex = b.length - 1
    while(aIndex >=0 || bIndex >=0) {
        let sum = (Number(a[aIndex]) || 0) + (Number(b[bIndex]) || 0) + carry
        carry = sum > 1 ? 1 : 0
        result.push(sum % 2)
        aIndex--
        bIndex--
    }
    if (carry) {
        result.push(1)
    }
    return result.reverse().join('')
};
```

* 执行测试

输入：

```
"11"
"1"
```

输出：

```
"100"
```

预期结果：

```
"100"
```

* 解题思路：

首先，通过进位标志 carry 和结果值 sum，它将每一步的值都存储起来。

接着，当循环遍历完毕，但是 carry 还有值（不为 0）的时候，就给数组首位添加 1。

最后，因为数组遍历是顺序，所以需要通过 reverse() 将数组反转，再通过 split() 转成字符串即可。

