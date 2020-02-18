---
title: JavaScript-基础之数据类型存储形式
date: 2020-01-05 11:37:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> JavaScript 数据类型分为原始类型和引用类型（对象类型），两种类型在内存中有不同的存储形式。

<!--more-->               
***

栈（stack）和堆（Heap），是两种基本的数据结构。

栈在内存中是自动分配内存空间，堆在内存中是动态分配内存空间，不一定会自动释放。

一般我们在项目中将对象类型手动置为 null，减少无用内存消耗。

原始类型和对象类型分别在内存中的存在形式如下图：

![](https://ss.csdn.net/p?https://mmbiz.qpic.cn/mmbiz_png/aVp1YC8UV0fXooDPZouVS6juuGEP1GxibzBmJd1UmVEjEbyNxr7MlAk5jIQkmEjEMDclZgPogN8n940Lb3jwZfA/640?wx_fmt=png)

原始类型是按值形式存放在栈中的数据段，内存空间可以自由分配，同时按值直接访问。

引用类型在栈中保存的是这个对象在堆内存中的引用地址，值保存在堆内存中。
