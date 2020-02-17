---
title: 面试题-常见面试题系列之CSS基础
date: 2020-01-02 14:46:52
tags: [面试题]
categories: 前端
---

#### 导语

本系列旨在整理 CSS 考点的非重难点，帮助梳理 HTML 高频面试题。

# CSS选择器的优先级是怎样的？

CSS 选择器的优先级是：内联选择器 > id 选择器 > 类选择器 > 标签选择器

到具体的计算层面，优先级是由 A、B、C、D 的值来决定的，其中它们的值计算规则如下：

* A 的值等于1的前提是存在内联样式，否则 A = 0

* B 的值等于 ID 选择器出现的次数

* C 的值等于类选择器和属性选择器和伪类出现的总次数

* D 的值等于标签选择器和伪元素出现的总次数

例如下面的选择器，它不存在内联样式，所以 A = 0，不存在 id 选择器，B = 0，存在一个类选择器 C = 1，存在三个标签选择器 D = 3，那么最终结果为：{0, 0, 1, 3}

```
ul ol li .red {
	...
}
```

# link 和 @import 的区别？

* link 属于 XHTML 标签，而 @import 是 CSS 提供的。

* 页面被加载时，link 会同时加载。而 @import 会到页面被加载完在加载。

* link 方式的样式权重高于 @import 的权重。

# 有哪些方式（CSS）可以隐藏页面元素？

* opacity: 0：本质是将元素的透明度降为 0，就看起来隐藏了，但是依然占据空间且可以交互。

* visibility: hidden：与上一个方法类似的效果，占据空间，但是不可以交互了。

* overflow: hidden：隐藏元素溢出的部分，占据空间且不可交互。

* display: none：彻底隐藏元素，元素从文档流中消失，既不占据空间，也不交互，也不影响布局。

* z-index: -9999：将层级放到底部。

* transform: scale(0, 0)：平面变换，将元素缩放为 0，但是依然占据空间，不可交互。

# em/px/rem 的区别？

* px：绝对单位，页面按精确像素展示。

* em：相对单位，基准点为父元素字体的大小。

* rem：相对单位，基准点为根节点 html 的字体大小。

# 块级元素水平居中的方法？

* margin: 0 auto;

```css
.center {
	width: 200px;
	height: 200px;
	margin: 0 auto;
}
```

* flex 布局。

```css
.center {
	display: flex;
	justify-content: center;
}
```

* table 方法。

```css
.center {
	display: table;
	margin: 0 auto;
}
```

# CSS 有几种定位方式？

* static：正常文档流定位。块级元素从上到下纵向排列，行级元素从左到右横向排列。

* relative：相对布局。相对于正常文档流的位置。

* absolute：绝对布局。

* fixed：相对于屏幕视口的位置来指定元素位置。

* sticky：粘性定位。近似效果就是 ios 通讯录滚动时候的【顶屁股】。

# 如何理解 z-index？

控制重叠元素的垂直叠加顺序，默认元素的 z-index 为 0，而 z-index 只能影响设置了 position 值的元素。

# 如何理解层叠上下文？

## 是什么？

假定用户正面向网页，而 HTML 元素沿着其相对于用的一条虚构的 Z 轴排开，层叠上下文就是对这些 HTML 元素的一个三维构想。众 HTML 元素基于其元素属性按照优先级顺序占据这个空间。

## 如何产生？

触发以下条件则会产生层叠上下文：

* 根元素

* z-index 值不为 'auto' 的相对/绝对定位

* 一个 z-index 值不为 "auto"的 flex 项目 (flex item)，即：父元素 display: flex|inline-flex

* opacity 属性值小于 1 的元素

* transform 属性值不为 "none"的元素

* mix-blend-mode 属性值不为 "normal"的元素

* filter值不为“none”的元素

* perspective值不为“none”的元素

* isolation 属性被设置为 "isolate"的元素

* position: fixed

* 在 will-change 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值

* -webkit-overflow-scrolling 属性被设置 "touch"的元素

# 清除浮动有哪些方法？

* 空 div 方法：<div style="clear: both;"></div>

* Clearfix 方法： clearfix 类方法

* overflow: hidden; ovrflow: auto; BFC

# 你对 css sprites 的理解，好处是什么？

## 理解

css sprites -- 雪碧图，css 图像合成技术，开发人员往往将小图标合在一起，通过 background-image、background-position、background-size等属性定位到要使用的小图标。

## 优点

* 提前加载资源

* 减少 HTTP 请求书

## 缺点

* 维护成本高

* 加载速度优势在 http2 开启后荡然无存

# 你对媒体查询的理解？

## 是什么

媒体查询，添加自 CSS3，允许内容的呈现针对一个特定范围的输出设备而进行裁剪，而不必改变内容自身，非常适合 web 网页应对不同型号的设备而做出对应的响应适配。

## 如何使用

媒体查询包含一个可选的媒体类型和满足 CSS3 规范的条件下，包含零个或多个表达式，这些表达式描述了媒体类型，最终会被解析为 true 或 false。

```html
<link rel="stylesheet" href="example.css" media="(max-width: 800px)">

<style>
@media (max-width: 600px) {
	.fact_sidebar {
		display: none;
	}
}
</style>
```

# 你对盒模型的理解？

当对一个文档进行布局的时候，浏览器的渲染引擎会根据标准之一的 CSS 基础框盒模型，将所有元素表示为一个个矩形的盒子，CSS 决定这些盒子的大小，位置及属性等。

![](https://xiaomuzhu-image.oss-cn-beijing.aliyuncs.com/14650bf5fbb24066cea1dc1714d52a5b.png)

盒模型由 content、padding、border、margin组成。

## 标准盒模型和怪异盒模型有什么区别？

标准盒模型中，我们定义的元素的 width 为盒模型中 content 的宽度值，定义的 height 为盒模型中 content 的高度值。

因此，标准盒模型下：

元素的宽度 = margin-left + border-left + padding-left + width + padding-right + border-right + margin-right

![](https://xiaomuzhu-image.oss-cn-beijing.aliyuncs.com/232580766e15853d521a4c0bf6a5c794.png)

怪异盒模型中，我们定义的元素的 width 并不是 content 的宽度，而是 border-left + padding-left + width + padding-right + border-right 的总和，height 同理。

![](https://xiaomuzhu-image.oss-cn-beijing.aliyuncs.com/e427c6d19ea6be1359bd0177d7a5b7a3.png)

虽然现代浏览器默认使用W3C的标准盒模型，但是在不少情况下怪异盒模型更好用，于是W3C在css3中加入box-sizing。

# 谈谈对 BFC 的理解？

块级格式化上下文，一块独立的渲染区域，BFC 内部的元素与外部的元素互相隔离。

BFC 触发条件：

* 根元素

* position: fixed/absolute

* overflow 不为 visible

* float 不为 none

* display 的值为 inline-block、table-cell、table-caption

BFC 的作用：

* 防止 margin 发生重叠

* 防止元素塌陷

* 两栏布局，防止文字环绕

# 为什么有时用 translate 来改变位置而不是定位？

translate 改变位置时，元素依然占据其原始空间，不会触发浏览器重新布局或重绘，只会触发复合。

# 伪类和伪元素的区别是什么？

伪类：以 (:) 为前缀，添加到一个选择器末尾的关键字，当你希望样式在特别状态下才被呈现到指定的元素时，你可以在元素的选择器后加上对应的伪类。

伪元素：用于创建一些不在文档树中的元素，并为其添加样式。






