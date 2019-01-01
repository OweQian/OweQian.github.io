---
title: HTML5-必知必会特性

date: 2017-3-17 17:41:00
=======
date: 2017-4-17 17:41:00
>>>>>>> aff06a6f51e3129ed0ace59ece2572a51f4c43df
tags: [HTML5]
categories: 前端
---

#### 导语
> HTML5-拥有更加丰富的标签，对微数据、微结构有着非常友好的支持，赋予网页更好的意义和结构等等...

<!--more-->

### HTML5新的语义
HTML5出现之前，通常使用DIV+CSS来表述元素语义，但语义本身的意义在于让机器能够读懂页面的含义。HTML5的出现，赋予网页更好的意义和结构。

***
#### 新元素的到来

部分新元素一览表：

| 标签 | 说明 |
| --- | --- |
| header | 可表示介绍内容的容器或一组导航链接 |
| nav | 导航 |
| article | 标识页面中的主体内容 |
| section | 标记页面上重要的内容 |
| aside | 和页面内容相关，但不是页面的一部分 |
| footer | 文章或章节的页脚 |
| address | 地址 |
| time | 日期 |
...

***
##### 表单的增强作用

* input元素的Type属性扩展

| 属性值 | 说明 |
| --- | --- |
| search | 搜索框 |
| tel | 电话号码，pattern，maxlength |
| url | 输入url地址 |
| email | 输入电子邮件地址 |
| date | 输入日期 |
| color | 输入颜色 |
| number | 输入数字 |
| range | 滑块，min，max，step |
...

* input通过属性进行表单验证

| 属性 | 说明 |
| --- | --- |
| required | 必填 |
| pattern | 正则表达式验证表单输入 |
| title | 校验失败的提示信息 |
...

* input元素的其它属性

| 属性 | 说明 |
| --- | --- |
| autofocus | 自动获取焦点 |
| form | 绑定form表单 |
| placeholder | 占位符 |
...

* 表单新元素

| 标签 | 说明 |
| --- | --- |
| progress | 进度条，max，min |
| meter | 标尺，max，min，value |
...

***
##### 音频视频

* 音频-Audio

```
<audio controls>
    <source src="vincent.ogg"/>
    <source src="vincent.mp3"/>
    你的浏览器不支持Audio标记
</audio>
```

audio属性一览表：

| 标签 | 说明 |
| --- | --- |
| controls | 是否显示标准的音频空间 |
| autoplay | 是否自动播放 |
| loop | 是否循环播放 |
| preload | 预加载方式，none（不预加载）、metadata（只加载元数据）、auto（预加载整个音频） |
| volum | 值0-1之间 |
...

常用api：audio.play()，audio.pause()

* 视频-Video

```
<video width="400" height="500" controls>
    <source src="dizzy.mp4" type="video/mp4"/>
    <source src="dizzy.webm" type="video/webm"/>
    <source src="dizzy.ogg" type="video/ogg"/>
    你的浏览器不支持Video标记
</video>
```

常用属性：currentTime（播放时间位置）

***
##### 访问你的设备

* 定位当前物理位置

HTML5提供了Geolocation API来构建基于地理位置的应用。

各浏览器对Geolocation的支持情况一览表：

| 浏览器 | 版本 | 只支持HTTPS版本 |
| --- | --- | --- |
| IE | 9+ | - |
| Edge | 12+ | - |
| Firefox | 3.5+ | - |
| Chrome | 5+ | 50+ |
| Safari | 5+ | 39+ |
| ios Safari | 3.2+ | 10.2+ |
| Android Browser | 2.1+ | 56+ |
| Chrome for Android | 57+ | 57+ |
| UC Browser fro Android | 11.4+ | - |

（* 出于安全考虑，部分最新的浏览器只允许通过HTTPS协议使用Geolocation API。）
（* 在开发阶段，127.0.0.1等本地域在两种协议下均可使用。）