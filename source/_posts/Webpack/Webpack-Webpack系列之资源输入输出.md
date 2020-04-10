---
title: Webpack-资源输入输出
img: https://media.trytn.us/library/2019/02/webpack.jpg
date: 2020-01-14 15:21:00
tags: [Webpack]
categories: 前端
---

#### 导语
> webpack是一个现代JavaScript应用程序的静态模块打包器，在webpack处理应用程序时，它会在内部创建一个依赖图，用于映射到项目需要的每个模块，然后将所有这些依赖生成到一个或多个bundle。

<!--more-->

# 资源输入输出

## 资源处理流程

在一切流程的最开始，我们需要指定一个或多个入口（entry），也就是告诉Webpack具体从源码目录下的哪个文件开始打包。如果把工程中各个模块的依赖关系当作一棵树，那么入口就是这棵依赖树的根，这些存在依赖关系的模块会在打包时被封装为一个chunk。

chunk字面的意思是代码块，在Webpack中可以理解成被抽象和包装过后的一些模块。它就像一个装着很多文件的文件袋，里面的文件就是各个模块，Webpack在外面加了一层包裹，从而形成了chunk。根据具体配置不同，一个工程打包时可能会产生一个或多个chunk。

Webpack会从入口文件开始检索，并将具有依赖关系的模块生成一棵依赖树，最终得到一个chunk。由这个chunk得到的打包产物我们一般称之为bundle。

## 配置资源入口

通过context和entry这两个配置项来共同决定入口文件的路径。在配置入口时，实际上做了两件事：

* 确定入口模块位置，告诉Webpack从哪里开始进行打包。

* 定义chunk name。如果工程只有一个入口，那么默认其chunk name为“main”；如果工程有多个入口，我们需要为每个入口定义chunk name，来作为该chunk的唯一标识。

### context

context可以理解为资源入口的路径前缀，在配置时要求必须使用绝对路径的形式。

```javascript
module.exports = {
    context: path.join(__dirname, './src/script'),
    entry: './index.js'
}
```

配置context的主要目的是让entry的编写更加简洁，尤其是在多入口的情况下。context可以省略，默认值为当前工程的根目录。

### entry

entry的配置可以有多种形式：字符串、数组、对象、函数。可以根据不同的需求场景来选择。

* 字符串类型入口

直接传入文件路径：

```javascript
module.exports = {
  entry: './src/index'
}
```

* 数组类型入口

传入一个数组的作用是将多个资源预先合并，在打包时Webpack会将数组中的最后一个元素作为实际的入口路径。如：

```javascript
module.exports = {
  entry: ['babel-polyfill', './src/index']
}
```

* 对象类型入口

如果想要定义多入口，则必须使用对象的形式。

```javascript
module.exports = {
  entry: {
    foo: './src/foo',
    bar: './src/bar'
  }
}
```

* 函数类型入口

用函数定义入口时，只要返回上面介绍的任何配置形式即可。

```javascript
module.exports = {
  entry: () => ({
                    foo: './src/foo',
                    bar: './src/bar'
                  })
}
```

### output

所有与出口相关的配置都集中在output对象里。

#### filename

filename的作用是控制输出资源的文件名。

* 字符串形式

```javascript
module.exports = {
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
}
```

* 在多入口的场景中，我们需要为对应产生的每个bundle指定不同的名字，Webpack支持使用一种类似模板语言的形式动态地生成文件名。

```javascript
// 多个入口生成不同的文件
module.exports = {
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  }
}
```

* 路径中使用hash，每次构建都有一个不同的hash值，避免发布新版本使用浏览器缓存。

```javascript
module.exports = {
  output: {
    path: path.resolve(__dirname, 'dist/[hash]'),
    filename: '[name].js'
  }
}
```

#### path

path可以指定资源输出的位置，要求值必须为绝对路径。如：

```javascript
module.exports = {
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
}
```

#### publicPath

publicPath是一个非常重要的配置项，并且容易与path相混淆。从功能上来说，path用来指定资源的输出位置，而publicPath则用来指定资源的请求位置。让我们详细解释这两个定义。

* 输出位置：打包完成后资源产生的目录，一般将其指定为工程中的dist目录。

* 请求位置：由JS或CSS所请求的间接资源路径。页面中的资源分为两种，一种是由HTML页面直接请求的，比如通过script标签加载的JS；另一种是由JS或CSS请求的，如异步加载的JS、从CSS请求的图片字体等。publicPath的作用就是指定这部分间接资源的请求位置。
