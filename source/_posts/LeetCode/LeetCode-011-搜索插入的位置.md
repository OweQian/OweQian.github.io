---
title: LeetCode-011-搜索插入的位置
img: https://miro.medium.com/max/1200/1*9EEAMYxLObepKMaOCgYgvA.jpeg
date: 2020-05-13 15:29:52
tags: [LeetCode]
photos: /medias/featureimages/s29484334.jpg
categories: 数据结构与算法
---

#### 导语
> 本系列为 LeetCode 刷题系列，旨在夯实 JavaScript基础，了解常见算法。 

<!--more-->

# 前言

* 难度：简单

* 设计知识：数组、二分查找

* 题目地址：https://leetcode-cn.com/problems/search-insert-position/

* 题目内容：

```
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

示例 1:

输入: [1,3,5,6], 5
输出: 2
示例 2:

输入: [1,3,5,6], 2
输出: 1
示例 3:

输入: [1,3,5,6], 7
输出: 4
示例 4:

输入: [1,3,5,6], 0
输出: 0
```

# 解题

## 解法 - 数组遍历

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var searchInsert = function(nums, target) {
    for (let i = 0; i < nums.length; i++) {
        if (target <= nums[i]) {
            return i
        }
    }
    return nums.length
};
```

* 执行测试

输入：

```
[1,3,5,6]
5
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

遍历数组，target 与当前遍历的元素作比较，如果 target 小于或等于当前遍历的元素，则返回当前遍历的索引，否则，返回数组长度。

## 解法 - 二分法

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var searchInsert = function(nums, target) {
    let left = 0
    let right = nums.length -1
    while(left <= right) {
        let middle = Math.round((left + right) / 2)
        console.warn(middle)
        if (nums[middle] === target) {
            return middle
        } else if (nums[middle] < target) {
            left = middle + 1
        } else if (nums[middle] > target) {
            right = middle - 1
        }
    }
    return left
};
```

* 执行测试

输入：

```
[1,3,5,6]
5
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

首先，我们需要了解的是，一个数 / 2，大概率返回的是小数，而我们的索引需要的是整数，所以我们通过 Math.round() 来四舍五入（向上取整）获取整数。

然后，就是 while 的逻辑判断：

nums：[1, 3, 5, 6]

target：2

比较数组中间的数据与目标数据的大小：

1）如果两数据相等，直接返回中间数据的索引值。

2）如果目标数据比中间数据的值大，则将左遍历索引指向中间数据索引的下一个位置，继续下一轮比较

3）如果目标数据比中间数据的值小，则将右遍历索引指向中间数据索引的上一个位置，继续下一轮比较

直到循环结束(left > right)，返回 left 即可
