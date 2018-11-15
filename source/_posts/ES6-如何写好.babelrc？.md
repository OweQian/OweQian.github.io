---
title: ES6-如何写好.babelrc?
date: 2017-3-14 14:06:52
tags: [ES6]
categories: 前端
---

#### 导语
> Babel-The compiler for writing next generation Javascript. 下一代JavaScript 语法的编译器。

<!--more-->
## 什么是Babel？
作为前端开发，由于浏览器的版本和兼容性等问题，很多JS的新的方法都不能使用，等到可以大胆使用的时候，可能已经过去很久。Babel就因此而生，它可以让你放心使用大部分的JS的新的标准的方法，然后编译成兼容绝大多数的主流浏览器的代码。

在升级到Babel6.X版本之后，所有的插件都是可插拔的，这也意味着你安装了Babel之后，是不能工作的，需要配置对应的.babelrc文件才能发挥完整的作用。
***
## 预设（presets）
使用时需要安装对应的插件，对应babel-preset-xxx。例如下配置，需要npm i babel-preset-es2015。

```dash
{
    "presets": ["es2015"]
}
```
***
### env
```dash
{
    "presets": ["env", options]
}
```
#### 新增选项，有以下options选择。
##### targets: {[string]: number}，默认{}
需要支持的环境，可选例如：chrome，edge，firefox，safari等，甚至可以指定版本，如node：6.5。也使用node：current代表使用当前的版本。
##### browsers: array | string，默认[]
浏览器列表，使用的是browserslist，可选例如：last 2 versions, > 5%。
##### loose: boolean，默认false
是否使用宽松模式，如果设置为true，plugins里的插件如果允许，都会采用宽松模式。
##### debug: boolean，默认false
编译是否会去掉console.log。
##### whitelist: array，默认[]
设置一直引入的plugins列表。
es2015/es2016/es2017/latest

```dash
{
    "presets": ["es2015"]
}
```
***
###### es2015
使用es2015，简单翻译如下，更多细节可以参看文档。

* check-es2015-constants // 检验const常量是否被重新赋值。

* transform-es2015-arrow-functions // 编译箭头函数。

* transform-es2015-block-scoped-functions // 函数声明在作用域内。

* transform-es2015-block-scoping // 编译const和let。

* transform-es2015-classes // 编译class。

* transform-es2015-computed-properties // 编译计算对象属性。

* transform-es2015-destructuring // 编译解构赋值。

* transform-es2015-duplicate-keys // 编译对象中重复的key，其实是转换成计算对象属性。

* transform-es2015-for-of // 编译for…of。

* transform-es2015-function-name // 将function.name语义应用于所有的function。

* transform-es2015-literals // 编译整数(8进制/16进制)和unicode。

* transform-es2015-modules-commonjs // 将modules编译成commonjs。

* transform-es2015-object-super // 编译super。

* transform-es2015-parameters // 编译参数，包括默认参数，不定参数和解构参数。

* transform-es2015-shorthand-properties // 编译属性缩写。

* transform-es2015-spread // 编译展开运算符。

* transform-es2015-sticky-regex // 正则添加sticky属性。

* transform-es2015-template-literals // 编译模版字符串。

* transform-es2015-typeof-symbol // 编译Symbol类型。

* transform-es2015-unicode-regex // 正则添加unicode模式。

* transform-regenerator // 编译generator函数。

***
###### es2016

使用es2016，更多细节可以参看文档。

* transform-exponentiation-operator // 编译幂运算符

***
###### es2017

使用es2017的相关插件，更多细节可以参看文档。

* syntax-trailing-function-commas // function最后一个参数允许使用逗号。

* transform-async-to-generator // 把async函数转化成generator函数。

***
###### latest
latest是一个特殊的presets，包括了es2015，es2016，es2017的插件（目前为止，以后有es2018也会包括进去）。

***
###### react
react是一个比较特别的官方推荐的presets，大概是因为比较火吧。加入了flow，jsx等语法，具体可以看文档。

***
###### stage-x(stage-0/1/2/3/4)
stage-x和上面的es2015等有些类似，但是它是按照JavaScript的提案阶段区分的，一共有5个阶段。而数字越小，阶段越靠后，存在依赖关系。也就是说stage-0是包括stage-1的，以此类推。

***
###### stage-4
已完成的提案，与年度发布的release有关，包含2015年到明年正式发布的内容。例如，现在是2016年，stage-4应该是包括es2015，es2016，es2017。经过测试，babel-preset-stage-4这个npm包是不存在的，如果你单纯的需要stage-4的相关方法，需要引入es2015~es2017的presets。

***
###### stage-3
除了stage-4的内容，还包括以下插件，更多细节请看文档。

* transform-object-rest-spread // 编译对象的解构赋值和不定参数。

* transform-async-generator-functions // 将async generator function和for await编译为es2015的generator。

***
###### stage-2
除了stage-3的内容，还包括以下插件，更多细节请看文档。

* transform-class-properties // 编译静态属性(es2015)和属性初始化语法声明的属性(es2016)。

***
###### stage-1
除了stage-2的内容，还包括以下插件，更多细节请看文档。

* transform-class-constructor-call // 编译class中的constructor，在Babel7中会被移除。

* transform-export-extensions // 编译额外的export语法，如export * as ns from “mod”。


***
###### stage-0
除了stage-1的内容，还包括以下插件，更多细节请看文档。

* transform-do-expressions // 编译do表达式。

* transform-function-bind // 编译bind运算符，也就是::。

***
## 插件(plugins)
其实看了上面的应该也明白了，presets，也就是一堆plugins的预设，起到方便的作用。如果你不采用presets，完全可以单独引入某个功能，比如以下的设置就会引入编译箭头函数的功能。

```dash
{
    "plugins": ["transform-es2015-arrow-functions"]
}
```
***
还有一些方法是presets中不提供的，这时候就需要单独引入了，介绍几个常见的插件。
### transform-runtime

```dash
{
    "plugins": ["transform-es2015-arrow-functions"]
}
```
主要有以下options选择。

* helpers: boolean // 默认为true，使用babel的helper函数。

* polyfill: boolean // 默认为true，使用babel的polyfill，但不能完全取代bebel-polyfill。

* regenerator: boolean // 默认为true，使用babel的regenerator。

* noduleName: string // 默认babel-runtime，使用对应module处理。

options一般不用自己设置，使用默认的即可。这个插件的作用主要是：

* 解决编译中产生的重复的工具函数，减小代码体积

* 非实例方法的poly-fill，如Object.assign，但是实例方法不支持，如”foobar”.includes(“foo”)，这时候需要单独引入babel-polyfill。

更多细节参见文档。

***
### transform-remove-console

```dash
{
    "plugins": ["transform-remove-console"]
}
```
使用这个插件，编译后的代码都会移除console.*，妈妈再也不用担心线上代码有多余的console.log了。当然很多时候，我们如果使用webpack，会在webpack中配置。

这也告诉我们，Babel不仅仅是编译代码的工具，还能对代码进行压缩，也许有一天，你不再需要代码压缩的插件了，因为你有了Babel！

***
## 自定义预设或插件
Babel支持自定义的预设(presets)或插件(plugins)。如果你的插件在npm上，可以直接采用这种方式”plugins”: [“babel-plugin-myPlugin”]，当然，你也可以缩写，它和”plugins”: [“myPlugin”]是等价的。此外，你还可以采用本地的相对路径引入插件，比如”plugins”: [“./node_modules/asdf/plugin”]。

presets同理。

***
## plugins/presets排序
也许你会问，plugins和presets编译，也许会有相同的功能，或者有联系的功能，按照怎么的顺序进行编译？答案是会按照一定的顺序。

* 具体而言，plugins优先于presets进行编译。

* plugins按照数组的index增序(从数组第一个到最后一个)进行编译。

* presets按照数组的index倒序(从数组最后一个到第一个)进行编译。因为作者认为大部分会把presets写成[“es2015”, “stage-0”]。具体细节可以看这个。

***
## 总结

```bash
{
  "presets": [
    ["env", {
      "modules": false,
      "targets": {
        "browsers": ["> 1%", "last 2 versions", "not ie <= 8"]
      }
    }],
    "stage-2"
  ],
  "plugins": ["transform-runtime", "transform-vue-jsx"],
  "comments": false
}

```
强烈推荐使用transform-runtime。

