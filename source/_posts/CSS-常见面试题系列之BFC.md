---
title: CSS-常见面试题系列之BFC
date: 2019-08-26 16:24:52
tags: [CSS]
categories: 前端
---

#### 导语
> BFC (Block Formatting Context) 块级格式化上下文，页面的可视化 CSS 渲染的一部分，是一个独立的渲染区域，块内的子元素布局与块外的元素互不干扰。

### BFC 解决的问题

* 浮动定位
* 消除外边距折叠
* 清除浮动
* 自适应多栏布局
...

***
### BFC 的创建

* body根元素
* 浮动: float 除 none 以外的值
* 绝对定位: position (absolute, fixed)
* overflow 值不为 visible
* 弹性布局或网格布局

***
### BFC 的约束规则
* 属于同一个 BFC 中的两个相邻的 Box 的 margin 会发生折叠。
* 每个元素的左外边距与包含块的左边界相接触，即使浮动元素也是如此。
* BFC 的区域不会与 float 的元素区域重叠
* 计算 BFC 的高度时，浮动子元素也参与计算

***
### BFC 的应用

#### 防止 margin 重叠

```html
<body>
  <p>top</p>
  <p>bottom</p>
</body>
<style>
  p {
    width: 100px;
    height: 100px;
    background: yellow;
    line-height: 100px;
    margin: 10px;
    text-align: center
  }
</style>
```

展示效果：

![]('http://pwvl8v6t5.bkt.clouddn.com/css-bfc.png')

两个 box 中间的间距为 10px，而不是 20px，因为它们处于同一个 BFC 中(body)。

解决方案为给第二个 box 包一层 div，设置其 overflow 属性，使它们处于不同的 BFC。

```html
<body>
   <p>top</p>
   <div>
      <p>bottom</p>  
  </div>
</body>
<style>
  p {
    width: 100px;
    height: 100px;
    background: yellow;
    line-height: 100px;
    margin: 10px;
    text-align: center
  }
  div {
    overflow: hidden;
  }
</style>
```

展示效果：

![]('http://pwvl8v6t5.bkt.clouddn.com/css-bfc-margin.png')

#### 让浮动内容与周围内容等高

```html
<div class="box">
    <div class="float">浮动元素</div>
    <p>未浮动元素</p>
</div>
<style>
.box {
    background-color: rgb(224, 206, 247);
    border: 5px solid rebeccapurple;
}
.float {
    float: left;
    width: 200px;
    height: 150px;
    background-color: white;
    border:1px solid black;
    padding: 10px;
}      
</style>
```

展示效果：

![]('http://pwvl8v6t5.bkt.clouddn.com/float-bfc-margin.png')

由于浮动，使得浮动元素的高度高于旁边的元素，解决方式为使父 box 生成一个 BFC，如下：

```html
<div class="box">
    <div class="float">浮动元素</div>
    <p>未浮动元素</p>
</div>
<style>
.box {
    background-color: rgb(224, 206, 247);
    border: 5px solid rebeccapurple;
    overflow: auto;
}

.float {
    float: left;
    width: 200px;
    height: 150px;
    background-color: white;
    border:1px solid black;
    padding: 10px;
}      
</style>
```

展示效果：

![]('http://pwvl8v6t5.bkt.clouddn.com/float-bfc.png')

#### 阻止元素被浮动元素覆盖

```html
<div class="float">我是一个左浮动的元素</div>
<div class="box">我是一个没有设置浮动, 也没有触发 BFC 元素</div>
<style>
.float {
  width: 100px;
  height: 100px;
  background: #eee;
  float: left;
}

.box {
  width: 200px;
  height: 200px;
  background: yellow;
}
</style>
```

展示效果：

![]('http://pwvl8v6t5.bkt.clouddn.com/column-css.png')

第二个元素有部分被浮动元素覆盖，此时可触发第二个元素的 BFC 特性，在第二个元素中加入 overflow: hidden。

```html
<div class="float">我是一个左浮动的元素</div>
<div class="box">我是一个没有设置浮动, 也没有触发 BFC 元素</div>
<style>
    .float {
      width: 100px;
      height: 100px;
      background: #eee;
      float: left;
    }
    
    .box {
      overflow: hidden;
      width: 200px;
      height: 200px;
      background: yellow;
    }
</style>
```

展示效果：

![]('http://pwvl8v6t5.bkt.clouddn.com/columb-css-bfc.png')

此方法可用来实现两列自适应布局

### 参考

* [BFC原理解析](https://github.com/louzhedong/blog/issues/145)
* [10 分钟理解 BFC 原理](https://zhuanlan.zhihu.com/p/25321647)