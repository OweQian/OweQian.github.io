---
title: LeetCode-009-移除元素
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

* 设计知识：数组

* 题目地址：https://leetcode-cn.com/problems/remove-element/

* 题目内容：

```
给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

 

示例 1:

给定 nums = [3,2,2,3], val = 3,

函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。

你不需要考虑数组中超出新长度后面的元素。
示例 2:

给定 nums = [0,1,2,2,3,0,4,2], val = 2,

函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。

注意这五个元素可为任意顺序。

你不需要考虑数组中超出新长度后面的元素。
 

说明:

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以「引用」方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

// nums 是以“引用”方式传递的。也就是说，不对实参作任何拷贝
int len = removeElement(nums, val);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中 该长度范围内 的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

# 解题

## 解法 - 遍历、splice

```javascript
/**
 * @param {number[]} nums
 * @param {number} val
 * @return {number}
 */
var removeElement = function(nums, val) {
    for (let i = 0; i < nums.length; i++) {
        if (nums[i] === val) {
            nums.splice(i, 1)
            --i
        }
    }
    return nums.length
};
```

* 执行测试

输入：

```
[3,2,2,3]
3
```

输出：

```
[2,2]
```

预期结果：

```
[2,2]
```

* 解题思路：

遍历数组，比较当前元素和 val，如果相等，则移除元素，并处理当前索引(因为元素移除，当前索引值需要减一个值)，继续遍历，直到结束，返回数组长度。

## 解法 - 递归

```javascript
/**
 * @param {number[]} nums
 * @param {number} val
 * @return {number}
 */
var removeElement = function(nums, val) {
   if (nums.indexOf(val) !== -1) {
       nums.splice(nums.indexOf(val), 1)
       return removeElement(nums, val)
   }
   return nums.length
};
```

* 执行测试

输入：

```
[3,2,2,3]
3
```

输出：

```
[2,2]
```

预期结果：

```
[2,2]
```

* 解题思路：

Array.prototype.indexOf 查找数组中第一个复合条件的元素，如果存在，就移除元素，并递归调用函数进行下一轮查找。
