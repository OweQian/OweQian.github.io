---
title: LeetCode-007-最长公共前缀
img: https://miro.medium.com/max/1200/1*9EEAMYxLObepKMaOCgYgvA.jpeg
date: 2020-05-12 20:01:52
tags: [LeetCode]
photos: /medias/featureimages/s29484334.jpg
categories: 数据结构与算法
---

#### 导语
> 本系列为 LeetCode 刷题系列，旨在夯实 JavaScript基础，了解常见算法。 

<!--more-->

# 前言

* 难度：简单

* 设计知识：水平扫描

* 题目地址：https://leetcode-cn.com/problems/longest-common-prefix/

* 题目内容：

```
编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

示例 1:

输入: ["flower","flow","flight"]
输出: "fl"
示例 2:

输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
说明:

所有输入只包含小写字母 a-z 。
```

# 解题

## 解法 - 水平扫描

```javascript
/**
 * @param {string[]} strs
 * @return {string}
 */
var longestCommonPrefix = function(strs) {
    if(strs.length < 2) {
        return strs.length ? strs[0] : ''
    }
    let result = strs[0]
    for (let i = 0; i < result.length; i++) {
        for (let j = 1; j < strs.length; j++) {
            if (result[i] !== strs[j][i]) {
                return result.substring(0, i)
            }
        }
    }
    return result
};
```

* 执行测试

输入：

```
["flower","flow","flight"]
```

输出：

```
"fl"
```

预期结果：

```
"fl"
```

* 解题思路：

首先判断数组为空或者数组长度为1，若数组为空，直接返回空字符串。若数组长度为1，直接返回数组的第一个元素。

定义变量 result，取值为数组的第一个元素，遍历 result 中的每个字符，嵌套循环遍历输入数组的其它元素，如果对比元素不相等，则截取 result 并返回子字符串。

负责，默认情况下返回 result。
