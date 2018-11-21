---
title: Vue-移动端自动转换px为rem(postcss-pxtorem)
date: 2018-10-25 17:41:00
tags: [Vue, 移动端]
categories: 前端
---

#### 导语
> 前端在开发移动端webapp还原设计稿的重要性母庸质疑，目前来说应用最多的应该是rem。然而很多前端开发依然选择要么自己去计算rem值，要么依靠编辑器安装插件转换。

<!--more-->
其实，通过一系列的配置后，我们在开发中可以直接使用设计图的尺寸，它为我们自动编译，转换成rem。

### 技术栈

***

* vue-cli@3.0: 脚手架工具创建项目
* postcss-pxtorem: 转换px为rem的依赖包

只需以下几步，就可以愉快地开发了😊。

***
### 创建rem.js文件

```
// 基准大小
const baseSize = 32
// 设置 rem 函数
function setRem() {
  // 当前页面宽度相对于 750 宽的缩放比例，可根据自己需要修改。
  const scale = document.documentElement.clientWidth / 750
  // 设置页面根节点字体大小
  document.documentElement.style.fontSize = baseSize * Math.min(scale, 2) + 'px'
}
// 初始化
setRem()
// 改变窗口大小时重新设置 rem
window.onresize = function() {
  setRem()
}
```

***
### main.js中引入rem.js

```
import './utils/rem'
```

引入文件后，可以看到页面的html节点，被自动添加 font-size

***
### 安装并配置postcss-pxtoren

#### 安装postcss-pxtorem

```
$ npm install postcss-pxtorem -D
```

***
#### 修改跟目录postcss.config.js

```
module.exports = {
  plugins: {
    autoprefixer: {
      browsers: ['Android >= 4.0', 'iOS >= 7']
    },
    'postcss-pxtorem': {
      rootValue: 32, // The root element font size
      propList: ['*'], // The properties that can change from px to rem.
      minPixelValue: 20, // Set the minimum pixel value to replace.
      selectorBlackList: [
        '.weui-',
        '.ig-',
        '.vux-',
        '.scroller-',
        '.dp-',
        '.mt-',
        '.mint-',
        '.range'
      ] //  The selectors to ignore and leave as px.
    }
  }
}
```

按上述完成配置后，即可在开发中直接使用px单位开发。

举个🌰：

```
body {
 width: 750px;
}
```

转换后：

```
body {
 width: 23.4375rem;
}

```