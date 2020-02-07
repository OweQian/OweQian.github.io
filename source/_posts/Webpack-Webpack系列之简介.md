---
title: Webpack-Webpack系列之简介
date: 2020-01-14 15:21:00
tags: [Webpack]
categories: 前端
---

#### 导语
> webpack是一个现代JavaScript应用程序的静态模块打包器，在webpack处理应用程序时，它会在内部创建一个依赖图，用于映射到项目需要的每个模块，然后将所有这些依赖生成到一个或多个bundle。

<!--more-->

# Webpack 简介

## Webpack是什么

Webpack是一个开源的JavaScript模块打包工具，其最核心的功能是解决模块之间的依赖，把各个模块按照特定的规则和顺序组织在一起，最终合并为一个JS文件（有时会有多个，这里讨论的只是最基本的情况）。

## 模块打包工具

模块打包工具（module bundler）的任务就是解决模块间的依赖，使其打包后的结果能运行在浏览器上。它的工作方式主要分为两种

* 将存在依赖关系的模块按照特定规则合并为单个JS文件，一次全部加载进页面中。

* 在页面初始时加载一个入口模块，其他模块异步地进行加载。

## Webpack 优势

* 默认支持多种模块标准，包括AMD、CommonJS，以及最新的ES6模块。

* 完备的代码分割（code splitting）解决方案，首屏只加载重要的部分。

* 可以处理各种类型的资源。除了JavaScript以外，Webpack还可以处理样式、模板，甚至图片等。

* 拥有庞大的社区支持。

## 安装

使用npm或者yarn来安装webpack

***
### 全局安装

```
$ npm i webpack webpack-cli -g

or

$ yarn -g add webpack webpack-cli
```

等待安装完毕，就可以全局执行webpack命令了。注：webpack-cli在4.x版本之后不再作为webpack的依赖了，我们使用时需要单独安装。

***
### 局部安装

在项目中，我们更推荐将webpack作为项目的开发依赖来安装使用，这样可以指定项目中使用的webpack版本。

```
$ npm i webpack webpack-cli -D

or

$ yarn add webpack webpack-cli -D
```

等待安装完毕，webpack会出现在项目的package.json文件中，我们添加一个npm scripts:

```
  "scripts": {
    "build": "webpack --mode production"
  },
  "devDependencies": {
    "webpack": "^4.1.1",
    "webpack-cli": "^2.0.12",
  }
```

## webpack-dev-server

webpack-dev-server可以看作一个服务者，它的主要工作就是接收浏览器的请求，然后将资源返回。当服务启动时，会先让Webpack进行模块打包并将资源准备好（在示例中就是bundle.js）。当webpack-dev-server接收到浏览器的资源请求时，它会首先进行URL地址校验。如果该地址是资源服务地址（上面配置的publicPath），就会从Webpack的打包结果中寻找该资源并返回给浏览器。反之，如果请求地址不属于资源服务地址，则直接读取硬盘中的源文件并将其返回。

```javascript
$ npm i webpack-dev-server -D
```

为了便捷地启动webpack-dev-server，我们在package.json中添加一个dev指令：

```json
"scripts": {
  "build": "webpack",
  "dev": "webpack-dev-server"
}
```

最后，我们还需要对webpack-dev-server进行配置。编辑webpack.config.js如下：

```javascript
module.exports = {
    entry: "./src/index.js",
    output: {
        filename: "./bundle.js"
    },
    mode: "development",
    devServer: {
        publicPath: "/dist"
    }   
}
```

这里有一点需要注意。直接用Webpack开发和使用webpack-dev-server有一个很大的区别，前者每次都会生成budnle.js，而webpack-dev-server只是将打包结果放在内存中，并不会写入实际的bundle.js，在每次webpack-dev-server接收到请求时都只是将内存中的打包结果返回给浏览器。

webpack-dev-server还有一项很便捷的特性就是live-reloading（自动刷新）。

注：关于内容自动更新，webpack-dev-server 与 hot-module-replacement（模块热替换）的区别在于：前者会刷新浏览器，后者不需要刷新浏览器就能获得更新之后的内容。
