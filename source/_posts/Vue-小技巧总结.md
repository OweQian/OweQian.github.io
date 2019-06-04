---
title: Vue-小技巧总结[转]
date: 2017-07-03 17:41:00
tags: [Vue]
categories: 前端
---

#### 导语
> 这篇文章主要是为了将开发过程中知道的一些小技巧进行总结，帮助使用Vue开发的小伙伴们愉快地采坑。

<!--more-->

***

### 前端在开发移动端webapp还原设计稿的重要性母庸质疑，目前来说应用最多的应该是rem。然而很多前端开发依然选择要么自己去计算rem值，要么依靠编辑器安装插件转换。

其实，通过一系列的配置后，我们在开发中可以直接使用设计图的尺寸，它为我们自动编译，转换成rem。

#### 技术栈

***

* vue-cli@3.0: 脚手架工具创建项目
* postcss-pxtorem: 转换px为rem的依赖包

只需以下几步，就可以愉快地开发了😊。

***
#### 创建rem.js文件

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
#### main.js中引入rem.js

```
import './utils/rem'
```

引入文件后，可以看到页面的html节点，被自动添加 font-size

***
#### 安装并配置postcss-pxtoren

##### 安装postcss-pxtorem

```
$ npm install postcss-pxtorem -D
```

***
##### 修改跟目录postcss.config.js

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

### 组件实例style的scoped

场景：在组件中用js动态创建的dom，添加样式不生效


```
<template>
    <div class="test"></div>
</template>
<script>
    let a = document.querySelector('test')
    let newDom = document.createElement('div')
    newDom.setAttribute('class', 'testAdd')
    a.appendChild(newDom)
</script>
<style scoped>
    .test{
       background:blue;
       height:100px;
       width:100px;
    }
    .testAdd{
        background:red;
        height:100px;
        width:100px;
    }
</style>
```

结果：

```
// test生效   testAdd 不生效
<div data-v-1b971ada class="test"><div class="testAdd"></div></div>
.test[data-v-1b971ada] { // 注意data-v-1b971ada
    background:blue;
    height:100px;
    width:100px;
}
```

原因:

当 <style> 标签有 scoped 属性时，它的 CSS 只作用于当前组件中的元素。
它会为组件中所有的标签和class样式添加一个scoped标识，就像上面结果中的data-v-1b971ada。
所以原因就很清楚了：因为动态添加的dom没有scoped添加的标识，没有跟testAdd的样式匹配起来，导致样式失效。

解决方式：

* 去掉该组件的scoped

* 动态添加style

```
newDom.style.height='100px';
newDom.style.width='100px';
newDom.style.background='red';
```

***
### Vue 数组/对象更新 视图不更新

```
data() { // data数据
    return {
        arr: [1,2,3],
        obj:{
               a: 1,
                b: 2
             }
           };
        },
       // 数据更新 数组视图不更新
        this.arr[0] = 'OBKoro1';
        this.arr.length = 1;
        console.log(arr);// ['OBKoro1'];
        // 数据更新 对象视图不更新
        this.obj.c = 'OBKoro1';
        delete this.obj.a;
        console.log(obj);  // {b:2,c:'OBKoro1'}
}
```
由于js的限制，Vue 不能检测以上数组的变动，以及对象的添加/删除，很多人会因为像上面这样操作，出现视图没有更新的问题。

解决方式：

* this.$set(array)
