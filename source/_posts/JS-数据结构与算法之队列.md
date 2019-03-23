---
title: JS-数据结构与算法之队列
date: 2019-3-16 20:57:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 队列是一种列表，不同的是，队列只能在队尾插入元素，在队首删除元素。队列用于存储按顺序排列的数据，先进先出。

<!--more-->               
***
### 对队列的操作

队列的两种主要操作是：向队列中插入新元素和删除队列中的元素。插入操作也叫做入队，删除操作也叫做出队。入队操作在队尾插入新元素，出队操作删除队头的元素。

队列的另一项重要操作是读取队头的元素，这个操作叫 peek() 。该操作返回队头元素，但不把它从队列中删除。除了读取队头元素，我们还可以用 length 属性来知道队列中存储了多少元素，用 clear() 方法来清空队列中的元素。

![](https://www.oreilly.com/library/view/data-structures-and/9781449373931/images/dsaj_0501.png)

***
### 用数组实现的队列

使用数组来实现队列看起来顺理成章。数组的 push() 方法可以在数组末尾加入元素，shift() 方法则可以删除数组的第一个元素。

push() 方法将它的参数插入数组中第一个开放的位置，该位置总在数组的末尾，即使是个空数组也是如此。

准备开始实现 Queue 类，先从构造函数开始：

```javascript
function Queue () {
  this.dataStore = []
  this.enqueue = enqueue
  this.dequeue = dequeue
  this.front = front
  this.back = back
  this.toString = toString
  this.empty = empty
}
```

enqueue() 方法向队尾添加一个元素

```javascript
function enqueue (element) {
  this.dataStore.push(element)
}
```

dequeue() 方法删除队首的元素

```javascript
function dequeue () {
  return this.dataStore.shift()
}
```

使用如下的方法读取队首和队尾的元素

```javascript
function front () {
  return this.dataStore[0]
}

function back () {
  return this.dataStore[this.dataStore.length -1]
}
```

toString() 方法显示队列内的所有元素

```javascript
function toString () {
  var retStr = ''
  for (var i = 0; i < this.dataStore.length; i++) {
    retStr += this.dataStore[i] + ' '
  }
  return retStr
}
```

empty() 方法判断队列是否为空

```javascript
function empty () {
  return this.dataStore.length === 0
}
```

***
### 使用队列：方块舞的舞伴分配问题

男男女女来到舞池，他们按照自己的性别排成两队。当舞池中有地方空出来时，选两个队列中第一个人组成舞伴。他们身后的人各自向前移动一个位置，变成新的队首。当一对舞伴迈入舞池时，主持人会大声喊出他们的名字。当一队舞伴走出舞池，且两排队伍中有任意一队没人时，主持人也会把这个情况告诉大家。

为了模拟这种情况，我们把跳方块舞的男男女女的姓名储存在 dancers 变量中：

```javascript
var dancers = 'F Allison \n M Frank \n M Mason \n M Clayton \n F Cheryl \n M Raymond \n F Jennifer \n M Bryan \n M David \n M Danny \n F Aurora'
```

每个舞者的信息都被存储在一个 Dancer 对象中：

```javascript
function Dancer (name, sex) {
  this.name = name
  this.sex = sex
}
```

下面我们需要一个函数，将舞者信息读到程序中：

```javascript
function getDancers (maleDancers, femaleDancers) {
  var names = dancers.split('\n')
  for (var i = 0; i < names.length; i++) {
    names[i] = names[i].trim()
  }
  for (var i = 0; i < names.length; i++) {
    var dancer = names[i].split(' ')
    var sex = dancer[0]
    var name = dancer[1]
    if (sex === 'F') {
      femaleDancers.enqueue(new Dancer(name, sex))
    } else {
      maleDancers.enqueue(new Dancer(name, sex))
    }
  }
}
```

舞者的姓名被从变量 dancers 中读入数组。然后 trim() 函数除去了每行字符串的空格。第二个循环将每行字符串按姓名和性别分成两部分存入一个数组。然后根据性别，将舞者加入不同的队列。

下一个函数将男性和女性组成舞伴，并且宣布配对结果。

```javascript
function dance (females, males) {
  console.log('the dance partners are: ')
  let person
  while (!females.empty() && !males.empty()) {
    person = females.dequeue()
    console.log('female dancer is ', person)
    person = males.dequeue()
    console.log('and male dancer is ', person)
  }
}
```

***
### 使用队列对数据进行排序

队列不仅用于执行现实生活中与排队有关的操作，还可以用于对数据进行排序。对于0 ~99 的数字，我们可以基于基数排序对数据扫描两次。第一次按个位上的数字进行排序，第二次按十位上的数字进行排序。每个数字根据对应位上的数值被分在不同的盒子上。假设有以下数字：

```text
91, 46, 85, 15, 92, 35, 31, 22
```

经过基数排序第一次扫描之后，数字被分配到如下的盒子里：

```text
Bin 0:
Bin 1: 91, 31
Bin 2: 92, 22
Bin 3:
Bin 4:
Bin 5: 85, 15, 35
Bin 6: 46
Bin 7:
Bin 8:
Bin 9:
```

根据盒子的顺序，第一次排序的结果如下：

```text
91, 31, 92, 22, 85, 15, 35, 46
```

然后根据十位上的数值再次将上述排序的结果分配到不同的盒子里：

```text
Bin 0:
Bin 1: 15
Bin 2: 22
Bin 3: 31, 35
Bin 4: 46
Bin 5: 
Bin 6: 
Bin 7: 
Bin 8: 85
Bin 9: 91, 92
```

最后，将盒子中的数字取出，组成一个新的列表，该列表即为排好序的数字：

```text
15， 22， 31， 35， 46， 85， 91， 92
```

使用队列代表盒子，可以实现这个算法。我们需要九个队列，每个对应一个数字，将所有队列保存在一个数组中，使用取余和除法操作决定个位和十位。算法的剩余部分将数字加入相应的队列，根据个位数值对其重新排序，然后再根据十位上的数值进行排序，结果即为排好序的数字。

```javascript
function Queue () {
  this.dataStore = []
  this.enqueue = enqueue
  this.dequeue = dequeue
  this.front = front
  this.back = back
  this.toString = toString
  this.count = count
  this.empty = empty
}

function enqueue (element) {
  this.dataStore.push(element)
}

function dequeue () {
  return this.dataStore.shift()
}

function front () {
  return this.dataStore[0]
}

function back () {
  return this.dataStore[this.dataStore.length -1]
}

function toString () {
  var retStr = ''
  for (var i = 0; i < this.dataStore.length; i++) {
    retStr += this.dataStore[i] + ' '
  }
  return retStr
}

function count () {
  return this.dataStore.length
}

function empty () {
  return this.dataStore.length === 0
}

//基数排序

var queues = []   //定义队列数组
var nums = []     //定义数字数组

//选十个0~99的随机数进行排序
for (var i = 0; i < 10; i ++){
    queues[i] = new Queue()
    nums[i] = Math.floor( Math.random() * 101 )
}

//排序之前
console.log( 'before radix sort: ' + nums )

//基数排序
distribution(nums, queues , 10 , 1)
collect(queues, nums)
distribution(nums, queues , 10 , 10)
collect(queues, nums)

//排序之后
console.info('after radix sort: ' + nums)

//根据相应的（个位和十位）数值，将数字分配到相应队列
function distribution (nums, queues, n, digit) {  //digit表示个位或者十位的值
    for(var i = 0; i < n; i++){
        if(digit == 1){
            queues[nums[i] % 10].enqueue(nums[i])
        }else{
            queues[Math.floor(nums[i] / 10)].enqueue(nums[i])
        }
    }
  console.log(queues.toString())
}

//从队列中收集数字

function collect (queues, nums) {
    var i = 0
    for (var digit = 0; digit < 10 ; digit++ ){
        while (!queues[digit].empty()){
            nums[i++] = queues[digit].dequeue()
        }
    }
}
```

***
### 优先队列

一般情况下，从队列中删除元素，一定是最新入队的元素，但是也有一些使用队列的应用，在删除元素时不必遵循先进先出的约定。这种应用，需要使用一个叫做优先队列的数据结构来进行模拟。

从优先队列中删除元素时，需要考虑优先权的限制。

先来定义存储队列元素的对象，然后在构建我们的优先队列系统：

```javascript
function Patient (name, code) {
  this.name = name
  this.code = code
}
```

变量 code 是一个整数，表示优先级。

现在需要重新定义 dequeue() 方法，使其删除队列中拥有最高优先级的元素。我们规定，优先码的值最小，代表优先级最高。新的 dequeue() 方法遍历队列的底层存储数组，从中找出优先码值最小的元素，然后使用数组的 splice() 方法删除优先级最高的元素。新的 dequeue() 方法定义如下所示：

```javascript
function dequeue () {
  var entry = 0
  for (var i = 1; i < this.dataStore.length; ++i) {
    if (this.dataStore[i].code < this.dataStore[entry].code) {
      entry = i
    }
  }
  return this.dataStore.splice(entry, 1)
}
```

优先队列的实现：

```javascript
function Queue () {
  this.dataStore = []
  this.enqueue = enqueue
  this.dequeue = dequeue
  this.front = front
  this.back = back
  this.toString = toString
  this.count = count
  this.empty = empty
}

function enqueue (element) {
  this.dataStore.push(element)
}

function dequeue () {
  var entry = 0
  for (var i = 1; i < this.dataStore.length; ++i) {
    if (this.dataStore[i].code < this.dataStore[entry].code) {
      entry = i
    }
  }
  return this.dataStore.splice(entry, 1)
}

function front () {
  return this.dataStore[0]
}

function back () {
  return this.dataStore[this.dataStore.length -1]
}

function toString () {
  var retStr = ''
  for (var i = 0; i < this.dataStore.length; i++) {
    retStr += this.dataStore[i].name + ' code:' + this.dataStore[i].code + ' '
  }
  return retStr
}

function count () {
  return this.dataStore.length
}

function empty () {
  return this.dataStore.length === 0
}

function Patient (name, code) {
  this.name = name
  this.code = code
}

var p = new Patient('smith', 5)
var ed = new Queue()
ed.enqueue(p)
p = new Patient('Jones', 4)
ed.enqueue(p)
p = new Patient('Wang', 6)
ed.enqueue(p)
p = new Patient('Qian', 1)
ed.enqueue(p)
p = new Patient('Brown', 1)
ed.enqueue(p)
var seen = ed.dequeue()
console.log('Patient being treated: ' + seen[0].name)
console.log('Patients watting to be seen:')
console.log(ed.toString())

seen = ed.dequeue()
console.log('Patient being treated: ' + seen[0].name)
console.log('Patients watting to be seen:')
console.log(ed.toString())

seen = ed.dequeue()
console.log('Patient being treated: ' + seen[0].name)
console.log('Patients watting to be seen:')
console.log(ed.toString())
```
