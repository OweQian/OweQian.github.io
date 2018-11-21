---
title: Webpack-基础使用
date: 2018-9-25 15:15:00
tags: [Webpack]
categories: 前端
---

#### 导语
> webpack是一个现代JavaScript应用程序的静态模块打包器，在webpack处理应用程序时，它会在内部创建一个依赖图，用于映射到项目需要的每个模块，然后将所有这些依赖生成到一个或多个bundle。

<!--more-->

### 安装

使用npm或者yarn来安装webpack

#### 全局安装

```
$ npm install webpack webpack-cli -g

or

$ yarn -g add webpack webpack-cli
```

等待安装完毕，就可以全局执行webpack命令了。注：webpack-cli在4.x版本之后不再作为webpack的依赖了，我们使用时需要单独安装。

#### 局部安装

在项目中，我们更推荐将webpack作为项目的开发依赖来安装使用，这样可以指定项目中使用的webpack版本。

```
$ npm install webpack webpack-cli -D

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

项目根目录新建./src/index.js，任意写js代码，保存，执行npm run build或者yarn build命令，我们会发现项目根目录下新增了一个dist目录，里面存放了webpack构建好的main.js文件。

webpack4.x可以零配置就开始构建，但有时我们的项目需要更多的功能。

***
### 基本概念

先放一张经典图：

![](https://user-gold-cdn.xitu.io/2018/3/19/1623bfac4a1e0945?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)


#### 入口

如上图左上角的.js，这个.js就是多个代码模块的入口，也是webpack构建的入口。webpack会读取这个文件，并从它开始解析依赖，然后进行打包。

在我们的项目中，如果是单页应用，那么可能入口只有一个。如果是多页应用，那么经常是一个页面对应一个构建入口。

用法：entry

##### 单个入口语法

```
// 简写
module.exports = {
  entry: './src/index'
}

module.exports = {
  entry: {
    main: './src/index'
  }
}

```

##### 多个入口语法

```
module.exports = {
  entry: {
    foo: './src/foo',
    bar: './src/bar'
  }
}

```

***
#### 输出

输出即指webpack最终构建出来的静态文件，构建结果的文件名，路径等都是可以配置的。

用法：output

```
module.exports = {
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
}

// 多个入口生成不同的文件
module.exports = {
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  }
}

// 路径中使用hash，每次构建都有一个不同的hash值，避免发布新版本使用浏览器缓存
module.exports = {
  output: {
    path: path.resolve(__dirname, 'dist/[hash]'),
    filename: '[name].js'
  }
}
```
#### loader

loader，我们可以理解为一个转换器，负责把某种文件格式的内容转换成webpack可以支持打包的模块。

默认情况下，webpack会默认把所有依赖打包成js文件，如果入口文件依赖一个.pug模块文件或者.scss样式文件，那么我们就需要相应的loader来把不同格式的文件转换为JavaScript，以便打包后在浏览器运行。

用法：module.rules

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        include: [
          path.resolve(__dirname, 'src')
        ],
        use: 'css-loader'
      }
    ]
  }
}
```

***
#### plugin

插件是webpack的支柱功能，在于解决loader无法实现的其他事情。

用法：plugin

```
const UglifyPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
  plugins: [new UglifyPlugin()]
}
```

***
#### 🌰

```
const path = require('path')
const UglifyPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
  mode: 'production',
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        include: [
        path.resolve(__dirname, 'src')
        ],
        use: 'css-loader'
      }
    ],
  },

  // 代码模块路径解析的配置
  resolve: {
    modules: [
      "node_modules",
      path.resolve(__dirname, 'src')
    ],
    extensions: [".wasm", ".mjs", ".js", ".json", ".jsx"],
  },

  plugins: [
    new UglifyPlugin(),
  ]
}
```

***
### 配置loader
loader相当于webpack的转换器，在日常项目开发中，可能会需要一些比较复杂的配置细节。

### 匹配规则

举个🌰：

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/, // 条件
        include: [
          path.resolve(__dirname, 'src')
        ], // 条件
        use: 'css-loader' // 应用
      }
    ]
  }
}
```
loader在匹配规则中有两个最关键的因素：匹配条件和匹配规则后的应用

