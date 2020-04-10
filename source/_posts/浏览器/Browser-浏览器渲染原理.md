---
title: JavaScript-基础之this
img: https://img4.orsoon.com/ico/201907/16153649_b8cd5d3a43.png
date: 2020-03-17 11:10:52
tags: [JavaScript]
categories: 前端
---

#### 导语
> 本系列旨在总结浏览器相关知识，本篇围绕浏览器渲染原理展开。

<!--more-->           
***

# 浏览器的主要组成部分

讲解浏览器渲染之前，先主要了解浏览器有哪些组成部分。

* 用户界面：包括地址栏、前进/后退按钮，书签菜单等。

* 浏览器引擎：在用户界面和渲染引擎之间传送指令。

* 渲染引擎：负责显示请求的内容。如：解析 HTML 和 CSS，并将解析后的内容显示在屏幕上。

* 网络：用于网络调用，比如 HTTP 请求。

* 用户界面后端：用于绘制基本的窗口小部件，如组合框和窗口等。

* JS 解释器：用于解析和执行 JS 代码。

* 数据存储：这是持久层，用于浏览器在硬盘上保存各种数据。

![](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=531622336,2916455443&fm=26&gp=0.jpg)

# 常见的浏览器内核

| 浏览器 | 内核（渲染引擎） | JS 引擎 |
| --- | --- | --- |
| Chrome | Blink（28~）<br/> Webkit | V8 |
| Safari | Webkit | JavaScriptCore |
| FireFox | Gecko | SpiderMonkey |
| Edge | EdgeHTML | Chakra |
| IE | Trident | Chakra |

# 什么是 DOM

DOM（Document Object Model 文档对象模型），是对 HTML 文档结构化的表述，允许程序和脚本动态的访问和更新文档的内容、结构和样式。服务器返回给浏览器渲染引擎的HTML的文件字节流是无法直接被浏览器渲染引擎理解的，要转化为浏览器渲染引擎可以理解的内部结构，这个结构就是 DOM。

# 浏览器接受 HTML 文件并转换为 DOM 树

从服务器返回给浏览器渲染引擎的 HTML 文件字节流，第一步要经过渲染引擎中的 HTML 解析器。它实现了将 HTML 字节流转换为 DOM 树结构。HTML 文件字节流返回的过程中，HTML 解析器就一直在解析（边加载边解析）。

阶段一：在网络中传输的内容都是 0 和 1 这些字节数据，当浏览器接收到这些字节数据后，会将这些字节数据转换为字符串，就是我们平时写的代码。

![](https://user-gold-cdn.xitu.io/2018/11/27/16754281e59587f3?imageslim)

阶段二：当数据转换为字符串后，浏览器会先将这些字符串通过分词器转化为 Token(标记)，Token 分为 Tag Token 和文本 TOken。

![](https://user-gold-cdn.xitu.io/2018/11/27/16754288f37a5347?imageslim)

标记还是字符串，是构成代码的最小单位。这一过程会将代码分拆成一块块，并给这些内容打上标记，便于理解这些最小单位的代码是什么意思。

![](https://user-gold-cdn.xitu.io/2018/11/27/167540a7b5cef612?imageslim)

阶段三：将 Token 解析为 DOM 节点，并将 DOM 节点添加到 DOM 树中。

HTML 解析器维护了一个 Token 栈，这个栈的目的就是用来计算节点间的父子关系，再上一个阶段生成的 Token 会被顺序压到这个栈中。

* HTML 解析器开始工作时，会默认创建一个根为 document 的空 DOM 结构，同时将一个 StartTag document 的 Token 压入栈底。

* 如果压入到栈中的是 StartTagToken，HTML 解析器会为该 Token 创建一个 DOM 节点，然后将这个 DOM 节点加入到 DOM 树中，它的父节点就是栈中相邻的那个元素生成的 DOM 节点。

* 如果是文本 Token，那么会生成一个文本节点，然后把这个文本 Dom 节点加入到 DOM 树中（注:文本Token不需入栈），它的 父节点就是当前栈顶 Token 所对应的 DOM 节点。

* 如果分词器解析出来的是 EndTag 标签，比如例子中的 EndTag div，HTML 解析器会查看 Token栈顶的元素是否是 StartTag div，如果是，就将 StartTag div从栈中弹出，边上该 div 元素解析完成。

* 最后按照上面的规则，分词器一路解析下来，就形成了这个简单的 DOM 树。

![](https://xiaomuzhu-image.oss-cn-beijing.aliyuncs.com/6ccc9594a0dc0a616804780992d4298d.png)

# 浏览器接受 CSS 文件并转换为 CSSOM 树

转换 CSS 到 CSSOM 树的过程和上一小节的过程极其相似。

![](https://user-gold-cdn.xitu.io/2018/11/27/167542a9af5f193f?imageslim)

## 浏览器如何解析 CSS 选择器

浏览器会【从右向左】解析 CSS 选择器。

以下段 CSS 为例：

```
.mode-nav h3 span {font-size: 16px;}
```

我们对应的DOM Tree如下：

![](https://xiaomuzhu-image.oss-cn-beijing.aliyuncs.com/4fe91032bd748f2509e0f0da3e56dcc1.png)

若从左向右匹配：过程是：

1. 从 .mod-nav 开始，遍历子节点 header 和子节点 div

2. 然后各自向子节点遍历。在右侧 div 的分支中

3. 最后遍历到叶子节点 a ，发现不符合规则，需要回溯到 ul 节点，再遍历下一个 li-a，一颗DOM树的节点动不动上千，这种效率很低

若从右向左匹配：

1. 先找到所有的最右节点 span，对于每一个 span，向上寻找节点 h3

2. 由 h3再向上寻找 class=mod-nav 的节点

3. 最后找到根元素 html 则结束这个分支的遍历

后者匹配性能更好，是因为从右向左的匹配在第一步就筛选掉了大量的不符合条件的最右节点（叶子节点）；而从左向右的匹配规则的性能都浪费在了失败的查找上面。


