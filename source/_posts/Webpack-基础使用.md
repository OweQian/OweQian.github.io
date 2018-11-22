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

***
#### 全局安装

```
$ npm i webpack webpack-cli -g

or

$ yarn -g add webpack webpack-cli
```

等待安装完毕，就可以全局执行webpack命令了。注：webpack-cli在4.x版本之后不再作为webpack的依赖了，我们使用时需要单独安装。

***
#### 局部安装

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

项目根目录新建./src/index.js，任意写JS代码，保存，执行npm run build或者yarn build命令，我们会发现项目根目录下新增了一个dist目录，里面存放了webpack构建好的main.js文件。

webpack4.x可以零配置就开始构建，但有时我们的项目需要更多的功能。

***
### 基本概念

先放一张经典图：

![](https://user-gold-cdn.xitu.io/2018/3/19/1623bfac4a1e0945?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

***
#### 入口

如上图左上角的.js，这个.js就是多个代码模块的入口，也是webpack构建的入口。webpack会读取这个文件，并从它开始解析依赖，然后进行打包。

在我们的项目中，如果是单页应用，那么可能入口只有一个。如果是多页应用，那么经常是一个页面对应一个构建入口。

用法：entry

***
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

***
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

***
#### loader

loader，我们可以理解为一个转换器，负责把某种文件格式的内容转换成webpack可以支持打包的模块。

默认情况下，webpack会默认把所有依赖打包成JS文件，如果入口文件依赖一个.pug模块文件或者.scss样式文件，那么我们就需要相应的loader来把不同格式的文件转换为JavaScript，以便打包后在浏览器运行。

用法：module.rules

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.jsx?/, // 条件
        include: [
          path.resolve(__dirname, 'src')
        ], // 条件
        use: 'babel-loader' // 规则应用结果
      } // 一个Object即一条规则
    ]
  }
}
```

loader匹配规则中最重要的两个因素：匹配条件和匹配规则后的应用。

***
##### 规则条件配置

匹配条件通常使用请求资源文件的绝对路径来进行匹配，官方文档成为resource。

上述代码中的test和include都用于匹配resource路径，是resource.test和resource.include的简写。你也可以这样写：

```
module.exports = {
  // ...
  rules: [
      {
        resource: { // resource 的匹配条件
          test: /\.jsx?/,
          include: [
            path.resolve(__dirname, 'src'),
          ]
        },
        use: 'babel-loader'
      },
      // ...
    ],
}
```

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

***
##### 常用rule配置项

###### CSS

```
const ExtractTextPlugin = require('extract-text-webpack-plugin')

module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.css$/,
        // 因为这个插件需要干涉模块转换的内容，所以需要使用它对应的 loader
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: 'css-loader',
        })
      }
    ]
  },
  plugins: [
    // 引入插件，配置文件名，这里同样可以使用 [hash]
    new ExtractTextPlugin('[name].css'),
  ],
}
```

* css-loader：负责解析CSS代码，主要处理CSS中的依赖，🌰：@import、url()等。
* style-loader：将css-loader解析的结果转变为JS代码，运行时动态插入style标签来让CSS生效。

***
###### 预处理器

```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.less/,
        // 因为这个插件需要干涉模块转换的内容，所以需要使用它对应的 loader
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: ['css-loader', 'less-loader'],
        })
      }
    ]
  },
  plugins: [
    // 引入插件，配置文件名，这里同样可以使用 [hash]
    new ExtractTextPlugin('[name].css')
  ]
}
```

***
###### 图片

webpack无法处理图片格式，我们选择file-loader，它不仅可以处理图片格式，还可用于处理其它很多类型的文件。直接输出文件，将构建后的文件路径返回。

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

***
###### Babel

Babel是一个让我们能够使用ES6、ES7等新标准来编写JS的编译工具。

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
##### 常见webpack plugin使用

###### HtmlWebpackPlugin

webpack默认从作为入口的JS文件进行构建，但通常一个前端项目都是从一个页面(HTML)出发的。通常我们会创建一个HTML文件，使用script标签直接引入构建好的js文件。

```
<script src="./dist/bundle.js"></script>
```

然而，问题来了，如果我们的文件名或者路径变化，每次构建完成后，我们要手动去修改HTML文件中的js路径?

如果能将HTML引用路径和我们的webpack构建结果关联起来就好了。

html-webpack-plugin就可以帮我们解决这个问题。

```
npm i html-webpack-plugin -D

or

yarn add html-webpack-plugin -D
```

然后在webpack配置中，将html-webpack-plugin添加到plugins列表中：

```
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  // ...
  plugins: [
    new HtmlWebpackPlugin(),
  ],
}
```

配置好之后，构建时html-webpack-plugin会为我们创建一个HTML文件，其中会引用构建出来的JS文件。

我们还可以在html-webpack-plugin中传递我们自己写好的HTML模板：

```
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  // ...
  plugins: [
    new HtmlWebpackPlugin({
      filename: "index.html",
      template: "assets/index.html"
    }),
  ],
}
```

***
###### DefinePlugin

DefinePlugin是webpack的内置插件，可以通过webpack.DefinePlugin直接引用。

主要用于创建一些在编译时可以配置的全局变量。

```
module.exports = {
  // ...
  plugins: [
    new webpack.DefinePlugin({
      VERSION: JSON.stringfy('v1.1.0'), // const VERSION = 'v1.1.0'
      TWO: '1 + 1', // const TWO = 2
      BROWSER_SUPPORT: true, // const BROWSER_SUPPORT = 'true'
      CONSTANTS: {
        APP_VERSION: JSON.stringfy('1.1.1')
      } // const CONSTANTS = { APP_VERSION: '1.1.1'}
    })
  ]
}
```

配置好之后，我们就可以在应用代码中，访问配置好的变量了。

```
console.log(VERSION)
```

有关配置的值，简述一下配置规则：

* 如果配置的值为字符串，那么整个字符串会被当做代码片段来执行，其结果将作为最终配置的值。

* 如果配置的值不是字符串，也不是对象字面量，那么该值会被转换为一个字符串。

* 如果配置的值是一个对象字面量，那么该对象的所有key会以同样的方式去定义。

***
###### CopyWebpackPlugin

通常我们会把源码和资源文件放在项目的src目录下，构建完成后会有一个dist目录，我们会直接拿dist目录中的所有文件来发布。有些文件没有经过webpack处理，但是我们希望它们也能出现在dist目录下，这时，我们就可以使用CopyWebpackPlugin来处理。

```
const CopyWebpackPlugin = require('copy-webpack-plugin')

module.exports = {
  // ...
  plugins: [
    new CopyWebpackPlugin([
       { from: 'src/*.txt', to: 'dist/*.txt' } // from: 配置来源；to: 配置目标
    ])
  ]
}
```

***
###### ExtractTextWebpackPlugin

通常CSS经过相应的loader处理后，会转换为JS，和index.js一起打包。如果需要单独把CSS文件分离出来，就需要使用extract-text-webpack-plugin插件。

```
const ExtractTextWebpackPlugin = require('extract-text-webpack-plugin')

module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextWebpackPlugin.extract({
          fallback: 'style-loader',
          use: 'css-loader'
        })
      }
    ]
  },
  plugins: [
    //  style.css 作为单独分离出来的文件名
    new ExtractTextWebpackPlugin('style.css')
  ]
}
```

它还可以为每一个入口创建单独分离的文件，所以，最好这样配置：

```
plugins: [
  new ExtractTextPlugin('[name].css'),
]
```

***
###### ProvidePlugin

ProvidePlugin是webpack的内置插件，可以通过webpack.ProvidePlugin直接引用。

主要用于自动加载模块，不必到处import或require

```
new webpack.ProvidePlugin({
  identifier: 'module',
  // ...
})

or

new webpack.ProvidePlugin({
  identifier: ['module', 'property'], // 即引用 module 下的 property，类似 import { property } from 'module'
  // ...
})
```

任何时候，当identifier被当作未赋值的变量时，module就会自动被加载，并且identifier会被这个module输出的内容所赋值。

注：如果是ES的default export，那么你需要指定模块的default属性：identifier: ['module', 'default']。

***
###### IgnorePlugin

IgnorePlugin是webpack的内置插件，可以通过webpack.IgnorePlugin直接引用。

主要用于忽略某些特定的模块，让webpack不把这些指定的模块打包进去。

配置参数：匹配引入模块路径的正则表达式，匹配模块的上下文。

```
module.exports = {
  // ...
  plugins: [
    new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)
  ]
}
```

***