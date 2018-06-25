---
title: Vue-Chrome插件开发实录[转]
date: 2018-5-20 21:06:52
tags: [Vue]
categories: 前端
---

#### 导语
> 开发一个Chrome插件就跟我们平时做web开发流程没多大的区别，就是先搭好基本的页面，然后使用js来写交互逻辑等功能。

<!--more-->
## Chrome插件开发基本知识
在应用商店中下载下来的插件基本上都是以.crx为文件后缀，该文件其实就是一个压缩包，包括插件所需要的html、css、javascript、图片资源等等文件。

开发一个插件就跟我们平时做web开发流程没多大的区别，就是先搭好基本的页面，然后使用js来写交互逻辑等功能。

比如我这个插件的目录文件如下：

![](http://ow43yt5wd.bkt.clouddn.com/%E5%89%8D%E7%AB%AF%E5%A4%A7%E5%85%A8.png)

***
## manifest.json文件
文件中需要注意一下的mainfest.json这个文件，这个json文件的作用是提供插件的各种信息，例如插件能够做的事情，以及插件的文件配置等等信息。下面是一个清单文件的示例：

```
{
  "manifest_version": 2,
  "name": "One-click Kittens",
  "description": "This extension demonstrates a browser action with kittens.",
  "version": "1.0",
  "permissions": [
    "https://secure.flickr.com/"
  ],
  "browser_action": {
    "default_icon": "icon.png",
    "default_popup": "popup.html"
  }
}
```

第一行声明我们使用清单文件格式的版本 2，必须包含（版本 1 是旧的，已弃用，不建议使用）。

接下来的部分定义扩展程序的名称、描述与版本。这些都会在 Chrome 浏览器中使用，向用户显示已安装的扩展程序，同时在 Chrome 网上应用店中向潜在的新用户显示您的扩展程序。名称应该简练，描述不要比一句话左右还长（后面将会有更多的空间用于更详细的描述）。

最后一部分首先请求权限，用于访问 https://secure.flickr.com/ 上的数据，并声明该扩展程序实现了一个浏览器按钮，同时在这一过程中为它指定一个默认图标与弹出窗口。

定义浏览器按钮时指向了两个资源文件：icon.png 与 popup.html。这两个资源都必须在扩展程序包中存在，图片是扩展的显示，html是扩展具体运行的基础文件。

具体详细的开发教程可以看看官方的这个文档，非常简明的入门教程。

***

## 功能实现-Vuejs实践
整个插件的核心交互功能非常简单，如文章开头的动图所示，用户选择对齐方式，代码区域显示对应的代码。这种简单数据交互使用vuejs再适合不过了，vuejs基础知识这里就不再细说了。

这里需要注意的一点是，chrome 扩展的运行环境有一些特殊要求，称为 Content Security Policy (CSP)，使得通常的 vue 不能被正常使用。如果用的是 vue 1.x，那么可以下载 csp 版本，在 这里。如果是 2.x 版本，请参考官网文档的这一段。

核心代码如下所示。

HTML：

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <!-- 引入样式 -->
  <link rel="stylesheet" href="main.css">
    <link rel="stylesheet" href="mystique.css">
</head>
<body>
    <div id="app">
      <h1 class="title-box">flexbox对齐就是这么简单</h1>
    </div>
    <div id="type-select">
      <select v-model="selected">
        <option v-for="option in options" v-bind:value="option.value">
          {{ option.text }}
        </option>
      </select>
      <!-- <span>Selected: {{ selected }}</span> -->
      <div class="resule-preview">
        <div v-bind:class="selected" class="cols">
          <div class="col col-3">
              <p>对齐</p>
          </div>
          <div class="col col-3">
            <p>对齐</p>
          </div>
          <div class="col col-3">
            <p>对齐</p>
          </div>
        </div>
      </div>
      <div class="resule-code">
        <pre class="code-display">
            <code class="code-lang">
               {{ cssMsg }}
            </code>
        </pre>
      </div>
    </div>
    <!-- 先引入 Vue -->
    <script src="vue.js"></script>
    <!-- 引入组件库 -->
    <script src="main.js"></script>
</body>
</html><!-- 引入样式 -->
```

CSS就不列出来了，可以在源代码中查看。

下面来使用vuejs来实现插件的功能。

***
## 功能实现

使用 v-for 指令根据一组数组的选项列表进行渲染。 v-for 指令需要以 item in items 形式的特殊语法， items 是源数据数组并且 item 是数组元素迭代的别名。

而下拉框（select）列表的渲染，就可以使用vue中的v-for方法来渲染下拉列表选项，下拉选项数据写在js中的data对象中的options中。用v-bind方法来绑定option的value值，代码如下所示：

XHTML

```
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
```

在vuejs中可以用 v-model 指令在表单控件元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。这里在select中使用v-model方法来监听选中的值。

为了能预览不同对齐的效果，先在CSS中写好和下拉框中值相同的对应的类名样式，这样当用户选中不同的值的时候能显示不同的效果。这里我们使用v-bind方法来实现这个功能，它主要用于属性绑定，我们可以给v-bind:class一个对象，以动态地切换class。

```
<div v-bind:class="selected" class="cols">
  <div class="col col-3">
    <p>对齐</p>
  </div>
  <div class="col col-3">
    <p>对齐</p>
  </div>
  <div class="col col-3">
    <p>对齐</p>
  </div>
</div>
```

下拉框这块功能就这样，简简单单几行代码就实现了。想想要是用jquery或者是原生的js来实现同样的功能，不仅代码量要大而且写起来也没有vuejs这么舒服。

接下来是代码同步功能，即在代码区域显示对应flex对齐的CSS代码。

开始之前先讲讲vuejs中的computed属性方法，可计算属性 (computed properties) 就是不存在于原始数据中，而是在运行时实时计算出来的属性。

对应到我们这个实例，就是当用户选择flexbox不同的对齐方式的时候，及时同步对应的CSS代码到代码预览区域。简单起见，直接把几个不同的代码写到js中：

```
data: {
    selected: 'cols-center',
      cssText: {
      'cols-center' : 'nr-webkit-justify-content: center;nr-ms-flex-pack: center;nrjustify-content: center;',
      'cols-space-between' : 'nr-webkit-justify-content: space-between;nr-ms-flex-pack: justify;nrjustify-content: space-between;',
      'cols-space-around' : 'nr-webkit-justify-content: space-around;nr-ms-flex-pack: distribute;nrjustify-content: space-around;'
    },
    options: [
      { text: '居中对齐', value: 'cols-center'},
      { text: '两端对齐', value: 'cols-space-between'},
      { text: '间隔相等', value: 'cols-space-around'}
    ]
  }
```

根据不同的名字对应不同的CSS代码。然后使用computed方法来根据用户选取的值实时取出对应的CSS代码：

```
computed:{
   cssMsg:function(){
   console.log(this)
   return this.cssText[this.selected];
   }
}
```

完整代码如下：

```
var typeSelect = new Vue({
  	el: 'body',
  	data: {
    selected: 'cols-center',
    cssText:{
    	'cols-center' : 'nr-webkit-justify-content: center;nr-ms-flex-pack: center;nrjustify-content: center;',
    	'cols-space-between' : 'nr-webkit-justify-content: space-between;nr-ms-flex-pack: justify;nrjustify-content: space-between;',
    	'cols-space-around' : 'nr-webkit-justify-content: space-around;nr-ms-flex-pack: distribute;nrjustify-content: space-around;'
    },
    options: [
      { text: '居中对齐', value: 'cols-center'},
      { text: '两端对齐', value: 'cols-space-between'},
      { text: '间隔相等', value: 'cols-space-around'}
     ]
    },
    computed:{
    	cssMsg:function(){
    	console.log(this)
    	return this.cssText[this.selected];
    	}
    }
})
```


最后在html中绑定通过computed方法得到数据也就是CSS：

```
<div class="resule-code">
 <pre class="code-display">
   <code class="code-lang">
     {{ cssMsg }}
   </code>
 </pre>
</div>
```


插件代码下载（https://share.weiyun.com/d51b9e473e12f8c43e7eaafc9e2f11a7）

开发好之后，可以直接在chrome中运行来调试。打开扩展面板，勾选开发者模式，然后加载刚开发扩展所在的目录就可以直接运行了。

![](http://ow43yt5wd.bkt.clouddn.com/670.jpeg)

一个简单的插件就完成了，通过这一个简单的chrome插件就可以体验到vuejs在web开发中简单、优雅的魅力，还有什么理由不用起来呢。