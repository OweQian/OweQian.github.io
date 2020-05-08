---
title: LeetCode-005-有效的括号
img: https://miro.medium.com/max/1200/1*9EEAMYxLObepKMaOCgYgvA.jpeg
date: 2020-05-08 20:01:52
tags: [LeetCode]
photos: /medias/featureimages/s29484334.jpg
categories: 数据结构与算法
---

#### 导语
> 本系列为 LeetCode 刷题系列，旨在夯实 JavaScript基础，了解常见算法。 

<!--more-->

# 前言

* 难度：简单

* 设计知识：数组、栈

* 题目地址：https://leetcode-cn.com/problems/valid-parentheses/

* 题目内容：

```
给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。

示例 1:

输入: "()"
输出: true
示例 2:

输入: "()[]{}"
输出: true
示例 3:

输入: "(]"
输出: false
示例 4:

输入: "([)]"
输出: false
示例 5:

输入: "{[]}"
输出: true
```

# 解题

## 解法 - 栈

```javascript
/**
 * @param {string} s
 * @return {boolean}
 */
var isValid = function(s) {
    const stack = []
    const mapper = {
        '(': ')',
        '{': '}',
        '[': ']'
    }
    for (let item of s) {
        if (['(', '{', '['].indexOf(item) > -1) {
            stack.push(item)
        } else {
            let peak = stack.pop();
            if (item !== mapper[peak]) {
                return false
            }
        }
    }
    if (stack.length > 0) return false
    return true
};
```

* 执行测试

输入：

```
"()[]{}"
```

输出：

```
true
```

预期结果：

```
true
```

* 解题思路：

使用栈，遍历输入字符串

如果当前字符为左半边括号时，则将其压入栈中

如果遇到右半边括号时，分类讨论：

1）如栈不为空且为对应的左半边括号，则取出栈顶元素，继续循环

2）若此时栈为空，则直接返回 false

3）若不为对应的左半边括号，反之返回 false

![](https://github.com/azl397985856/leetcode/raw/master/assets/20.validParentheses.gif)
