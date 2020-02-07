---
title: Webpack-Webpack系列之预处理器
date: 2020-01-14 15:21:00
tags: [Webpack]
categories: 前端
---

#### 导语
> webpack是一个现代JavaScript应用程序的静态模块打包器，在webpack处理应用程序时，它会在内部创建一个依赖图，用于映射到项目需要的每个模块，然后将所有这些依赖生成到一个或多个bundle。

<!--more-->

# 预处理器（loader）

每个loader本质上都是一个函数。在Webpack 4之前，函数的输入和输出都必须为字符串；在Webpack 4之后，loader也同时支持抽象语法树（AST）的传递，通过这种方法来减少重复的代码解析。用公式表达loader的本质则为以下形式：

output=loader(input)

这里的input可能是工程源文件的字符串，也可能是上一个loader转化后的结果，包括转化后的结果（也是字符串类型）、source map，以及AST对象；output同样包含这几种信息，转化后的文件字符串、source map，以及AST。如果这是最后一个loader，结果将直接被送到Webpack进行后续处理，否则将作为下一个loader的输入向后传递。

## loader 的配置

Webpack本身只认识JavaScript，对于其他类型的资源必须预先定义一个或多个loader对其进行转译，输出为Webpack能够接收的形式再继续进行，因此loader做的实际上是一个预处理的工作。

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.jsx?/, // 条件
        use: 'babel-loader' // 规则应用结果
      } // 一个Object即一条规则
    ]
  }
}
```

与loader相关的配置都在module对象中，其中module.rules代表了模块的处理规则。每条规则内部可以包含很多配置项，这里我们只使用了最重要的两项—test和use。

* test可接收一个正则表达式或者一个元素为正则表达式的数组，只有正则匹配上的模块才会使用这条规则。

* use可接收一个数组，数组包含该规则所使用的loader。

### 链式 loader

很多时候，在处理某一类资源时我们都需要使用多个loader。

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.css?/, // 条件
        use: ['style-loader', 'css-loader'] // 规则应用结果
      } // 一个Object即一条规则
    ]
  }
}
```

把style-loader加到了css-loader前面，这是因为在Webpack打包时是按照数组从后往前的顺序将资源交给loader处理的，因此要把最后生效的放在前面。

### loader options

webpack为loader提供了多种匹配条件的配置方式：

* test: ... 匹配特定条件
* include: ... 匹配特定路径
* exclude: ... 排除特定路径
* and: [] 必须匹配数组中的所有条件
* or: [] 匹配数组中的任意一个条件
* not: [] 排除匹配数组中的所有条件

条件值类型：

* 字符串：必须以提供的字符串开始（绝对路径）
* 正则表达式
* 数组：至少包含一个条件的数组
* 对象：匹配所有属性值的条件
* 函数：返回true表示匹配


***
##### 规则应用配置

匹配规则后的应用，我们可以使用use字段：

```
rules: [
  {
    test: /\.less/,
    use: [
      'style-loader',
      {
        loader: 'css-loader',
        options: {
          // ...
        }
      },
      {
        loader: 'less-loader',
        options: {
          // ...
        }
      }
    ]
  }
]
```

使用options可以给对应的loader传递一些配置项。

对于上述的less规则应用配置，一个less模块文件可以经过多个loader的转换处理，执行顺序为从右到左。

如果多个rule匹配了同一个模块文件，loader的应用顺序应该如何处理？

```
rules: [
  {
    test: /\.js$/,
    loader: "eslint-loader",
  },
  {
    test: /\.js$/,
    loader: "babel-loader",
  }
]
```

eslint-loader用于检查人工编写的代码，babel-loader用于转换代码。所以，eslint-loader应该在babel-loader之前执行，那么该如何保证执行顺序？

webpack为每一个匹配规则提供了enforce字段来配置当前rule得的loader类型。pre(前置) > 行内 > 普通 > 后置(post)。 我们要确保eslint-loader在babel-loader之前执行，只需给rule规则项添加enforce字段，并设置其值为pre。

```
rules: [
  {
    enforce: 'pre',
    test: /\.js$/,
    loader: "eslint-loader",
  },
  {
    test: /\.js$/,
    loader: "babel-loader",
  }
]
```

## 常用 loader 介绍

### babel-loader

 babel-loader用来处理ES6+并将其编译为ES5。
 
 ```javascript
npm i babel-loader @babel/core @babel/preset-env
```

各个模块的作用如下:

* babel-loader：它是使Babel与Webpack协同工作的模块。

* @babel/core：顾名思义，它是Babel编译器的核心模块。

* @babel/preset-env：它是Babel官方推荐的预置器，可根据用户设置的目标环境自动添加所需的插件和补丁来编译ES6+代码。

```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.jsx?/,
        loader: 'babel-loader'
      }
    ]
  }
}
```

***

### ts-loader

ts-loader与babel-loader的性质类似，它是用于连接Webpack与Typescript的模块。

```javascript
npm i ts-loader typescript
```

```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.ts?/,
        loader: 'ts-loader'
      }
    ]
  }
}
```

***

### html-loader

html-loader用于将HTML文件转化为字符串并进行格式化，这使得我们可以把一个HTML片段通过JS加载进来。

```javascript
npm i html-loader
```

```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.html?/,
        loader: 'html-loader'
      }
    ]
  }
}
```

### handlebars-loader

handlebars-loader用于处理handlebars模板，在安装时要额外安装handlebars。

```javascript
npm i handlebars-loader handlebars
```

```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.handlebars?/,
        loader: 'handlebars-loader'
      }
    ]
  }
}
```

handlebars文件加载后得到的是一个函数，可以接收一个变量对象并返回最终的字符串。

### file-loader

file-loader用于打包文件类型的资源，并返回其publicPath。

```javascript
npm i file-loader
```

```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: 'file-loader'
      }
    ]
  }
}
```

### url-loader

url-loader与file-loader作用类似，唯一的不同在于用户可以设置一个文件大小的阈值，当大于该阈值时与file-loader一样返回publicPath，而小于该阈值时则返回文件base64形式编码。

```javascript
npm i url-loader
```

```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: {
            loader: 'file-loader',
            options: {
                limit: 10240,
                name: '[name].[ext]',
                publicPath: './assets-path/'
            }
        }
      }
    ]
  }
}
```
