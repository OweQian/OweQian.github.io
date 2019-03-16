---
title: JS-数据结构与算法之栈
date: 2019-3-15 20:57:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 栈是和列表类似的一种数据结构，它可以用来解决计算机世界里的很多问题。

<!--more-->
***
### 对栈的操作

栈是一种特殊的列表，栈内的元素只能通过列表的一端访问，这一端称为栈顶。栈被称为一种后入先出 (LIFO，last-in-first-out) 的数据结构。

由于栈具有后入先出的特点，所以任何不在栈顶的元素都无法访问。为了得到栈底的元素，必须先拿掉上面的元素。

对栈的两种主要操作是将一个元素压入栈和将一个元素弹出栈。入栈使用 push() 方法，出栈使用 pop() 方法。

另一个常用的操作就是预览栈顶的元素。 pop() 方法虽然可以访问到栈顶的元素，但是该方法也将栈顶元素永久性地删除了。peek() 方法则只返回栈顶元素，而不删除它。

![](https://www.oreilly.com/library/view/data-structures-and/9781449373931/images/dsaj_0401.png)

为了记录栈顶的位置，同时也为了哪里可以加入新元素，我们使用变量 top 。当向栈内压入元素时，该变量增大。当从栈内弹出元素时，该变量减小。

push()、pop()、peek() 是栈的3个主要方法，但是栈还有其它方法和属性。clear() 清除栈内所有元素，length 属性记录栈内元素的个数，empty 属性，用以表示栈内是否有元素。

***
### 栈的实现

实现一个栈，首先是要决定存储数据的底层数据结构。这里，我们选用数组。

我们从实现以定义 Stack 类的构造函数开始：

```javascript
function Stack () {
  this.dataStore = []
  this.top = 0
  this.push = push
  this.pop = pop
  this.peek = peek
  this.clear = clear
  this.length = length
}
```

我们用数组 dataStore 来保存栈内的元素，构造函数将其初始化为一个空数组。变量 top 记录栈顶位置，被构造函数初始化为 0 ，表示栈顶对应数组的起始位置 0 。如果有元素被压入栈内，该变量的值将随之变化。

push() 方法 -- 当向栈内压入一个新元素时，需要将其保存在数组中变量 top 所对应的位置，然后将 top 值加 1，让其指向数组中下一个空位值。

```javascript
function push (element) {
  this.dataStore[this.top++] = element
}
```

这里需要注意 ++ 操作符的位置，它放在 this.top 的后面，这样新入栈的元素就会被放在 top 的当前值指向的位置，然后再将 top 的值加 1 ，指向下一个位置。

pop() 方法 -- 返回栈顶元素，同时将变量 top 的值减 1 。

```javascript
function pop () {
  return this.dataStore[--this.top]
}
```

peek() 方法 -- 返回栈顶元素，即将变量 top 的值减 1 。

```javascript
function peek () {
  return this.dataStore[this.top - 1]
}
```

如果对一个空栈调用 peek() 方法，结果为 undefined 。

length() 方法 -- 通过返回变量 top 值得方式来获取栈内存储了多少元素。

clear() -- 将变量 top 的值设为 0 ，轻松清空一个栈。

```javascript
function clear () {
  this.top = 0
}
```

***
### 使用 Stack 类

在开发过程中，有一些问题特别适合用栈来解决：

#### 数制间的相互转换

可以利用栈实现一个数字从一种数制转换为另一种数制。假设想将数字 n 转换为以 b 为基数的数字，实现转换的算法如下(注：此算法只针对基数为2—9的情况)：

(1) 最高位为 n % b，将此位压入栈。
(2) 使用 n / b 代替 n 。
(3) 重复步骤 1 和 2 ，直到 n 等于 0 ，且没有余数。
(4) 持续将栈内元素弹出，直到栈为空，依次将这些元素排列，就得到转换后数字的字符串形式。

```javascript
function mulBase (num, base) {
  var s = new Stack()
  do {
    s.push(num % base)
    num = Math.floor(num /= base)
  } while (num > 0)
  var converted = ''
  while (s.length() > 0) {
    converted += s.pop()
  }
  return converted
}
```

#### 回文

回文是指这样一种现象：一个单词、短语或数字，从前往后写和从后往前写都是一样的。如：单词 'dad' , 'racecar' 就是回文。

使用栈，可以轻松判断一个字符串是否是回文。我们将拿到的字符串的每个字符从左到右的顺序压入栈。当字符串中的字符都入栈后，栈内就保存了一个反转后的字符串，最后的字符在栈顶，第一个字符在栈底。

![](https://www.oreilly.com/library/view/data-structures-and/9781449373931/images/dsaj_0402.png)

字符串完整压入栈内后，通过持续弹出栈中的每个字母就可以得到一个新字符串，该字符串刚好与原来的字符串顺序相反。我们只需比较这两个字符串即可，如果它们相等，就是一个回文。

```javascript
function isPalindrome (word) {
  var s = new Stack()
  for (let i of word) {
    s.push(i)
  }
  var rWord = ''
  while (s.length() > 0) {
    rWord += s.pop()
  }
  return rWord == word
}
```
#### 递归演示

为了演示如何用栈实现递归，考虑一下求阶乘函数的递归定义，首先看看 5 的阶乘是怎么定义的：

```javascript
function factorial (n) {
  if (n === 0) {
    return 1
  }
  return n * factorial(n - 1)
}
```

使用栈模拟递归的过程

```javascript
function fact (n) {
  var s = new Stack()
  while (n > 1) {
    s.push(n--)
  }
  var product = 1
  console.log(s.length())
  while (s.length() > 0) {
    product *= s.pop()
  }
  return product
}
```