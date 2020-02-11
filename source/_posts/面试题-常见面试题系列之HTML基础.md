---
title: 面试题-常见面试题系列之JS基础
date: 2019-11-25 09:46:52
tags: [面试题]
categories: 前端
---

#### 导语

本系列旨在整理 HTML 考点的非重难点，帮助梳理 HTML 高频面试题。

# doctype 的作用是什么？

doctype 是 html5 标准的网页声明，必须声明在 html 文档的第一行。规定了浏览器的解析器以何种渲染模式来解析文档，不同的渲染模式会影响到浏览器对 css 甚至 js 脚本的解析。

解析模式有：

* BackCompat: 怪异模式，浏览器使用自己的怪异模式解析文档（默认值）。

* CSS1Compat: 标准模式，浏览器使用 W3C 的标准解析文档。

* 近乎标准模式：IE8 有一种介乎于上述两者之间的近乎标准的模式。

## 这三者的区别是什么？

* 标准模式：页面会按照 HTML 与 CSS 的定义渲染。

* 怪异模式：页面会模拟更旧的浏览器的行为。

* 近乎标准模式： 会实施一种表单元格尺寸的怪异行为。

# HTML、XHTML、XML 有什么区别？

* HTML (超文本标记语言)：在 html4 之前，HTML 先有实现再有标准，导致 html 非常混乱。

* XML (可扩展标记语言)：主要用于存储数据和结构，可扩展。

* XHTML (可扩展超文本标记语言)：基于上面两者而来，W3C 为了解决 html 混乱问题而生，并基于此诞生了 HTML5。

# 什么是 data- 属性？

html 的数据属性，用于将数据存储在标准的 html 元素中作为额为信息，可以用 js 访问并操作它。

```html
<article
	id="electriccars"
	data-columns="3"
	data-index-number="12321"
	data-parent="cars">
</article>
```

```
前端框架出现后，这种方法已经不流行了
```

# 你对 HTML 语义化的理解？

语义化指运用恰当语义的 html 标签，使页面具有良好的结构与定义。

语义化的好处：

* 开发者友好：增强了可读性，开发者可以清晰地看出网页的结构。

* 机器友好：便于搜索引擎的爬虫爬取有效的信息。

# HTML4 与 HTML5 的不同之处？

* 文档类型声明 (<!DOCTYPE>) 仅有一型：<!DOCTYPE HTML>。

* 新的解析顺序：不再基于 SGML。

* 新的元素：section、video、progress、nav、meter、time、footer、header、summary、source等。

* input 元素的新类型：date、email、url等。

* 全域属性：id、tabindex、repeat 等。

* 新的全域属性：contenteditable, contextmenu, draggable, dropzone, hidden, spellcheck。

* 移除元素：acronym, applet, basefont, big, center, dir, font, frame, frameset, isindex, noframes, strike。

# 有哪些常用的 meta 标签？

meta 标签用于描述一个 html 文档的元信息，如：作者、日期和时间等。

* charset：用于描述 html 文档的编码形式。

```html
<meta charset="UTF-8">
```

* viewport：控制视口的大小和比例。

```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
```

* http-equiv：相当于 http 的文件头作用。

```html
<meta http-equiv="expires" content="Web, 20 Jun 2020 22:28:00 GMT">
```

* app-mobile-web-app-status-bar-style：自定义苹果工具栏的颜色。

```html
<meta name="app-mobile-web-app-status-bar-style" content="black-translucent">
```

# href 和 src 的区别？

* src：指向外部资源，指向的内容会下载并嵌入到文档中当前标签所在的位置。当浏览器解析到该元素时，会暂停其它资源的下载和处理，直到当前资源加载、编译、执行完毕。

* href：指向网络资源所在位置，用来建立和当前元素或文档之间的连接，当浏览器识别到它所指向的文件时，则会并行下载资源。

## img 的 srcset 作用是什么？

使用 img 的 两个新属性 srcset 和 sizes 来提供更多额外的资源图像和提示，帮助浏览器选择一个正确的资源。

srcset 定义了我们允许浏览器选择的图像集以及每个图像的大小。

sizes 定义了一组媒体条件，并指明当某些媒体条件为真时，什么样的图片尺寸是最佳选择。

有了这些属性，浏览器会：

* 查看设备宽度

* 检查 sizes 哪个媒体条件为真

* 查看给予该媒体查询的槽大小

* 加载 srcset 中引用的最接近所选的槽的大小的图像

```html
<img src="clock-demo-thumb-200.png"
     alt="Clock"
     srcset="clock-demo-thumb-200.png 200w,
             clock-demo-thumb-400.png 400w"
     sizes="(min-width: 600px) 200px, 50vw">
```

## 还有哪一个标签能起到和 srcset 类似作用？

<picture> 元素通过包含零个或多个 <source> 元素和一个 <img> 元素来为不同的显示、设备场景提供图像版本。浏览器会选择最匹配的子 <source> 元素，如果没有匹配的，就会选择 <img> 元素的 src 属性中的 url。

```html
<picture>
    <source srcset="/media/examples/surfer-240-200.jpg"
            media="(min-width: 800px)">
    <img src="/media/examples/painted-hand-298-332.jpg" />
</picture>
```

# script 标签中 defer 和 async 的区别？

* defer：异步加载脚本，等待文档被解析完毕后执行。

* async：异步加载脚本，加载完毕后立即执行。

# 有几种前端存储方式？

cookie、localStorage、sessionStorage、indexedDB

## 这些方式的区别？

* cookie：优点是兼容性好，请求头自带 cookie 方便。缺点是大小只有4k，自动请求头加入 cookie 浪费流量，每个 domain 限制20 个 cookie。

* localStorage：优点是操作方便，永久性存储，大小为 5M，缺点是只能手动删除。

* sessionStorage：与 localStorage 基本类似。区别是当页面关闭后会清理。不能在所有同源窗口中被共享。

* indexedDB：标准的数据库存储方案，是 NoSQL 数据库，用键值对进行存储，可快速读取。

