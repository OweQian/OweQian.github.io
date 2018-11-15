---
title: Vue-小技巧总结[转]
date: 2017-3-03 17:41:00
tags: [Vue]
categories: 前端
---

#### 导语
> 这篇文章主要是为了将开发过程中知道的一些小技巧进行总结，帮助使用Vue开发的小伙伴们愉快地采坑。

<!--more-->

***
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