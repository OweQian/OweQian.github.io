---
title: Webpack-Webpack系列之预处理器
date: 2020-01-14 15:21:00
tags: [Webpack]
categories: 前端
---

#### 导语
> webpack是一个现代JavaScript应用程序的静态模块打包器，在webpack处理应用程序时，它会在内部创建一个依赖图，用于映射到项目需要的每个模块，然后将所有这些依赖生成到一个或多个bundle。

<!--more-->

# 样式处理

## 分离样式文件

Webpack社区有专门的插件：extract-text-webpack-plugin（适用于Webpack 4之前版本）和mini-css-extract-plugin（适用于Webpack 4及以上版本），它们就是专门用于提取样式到CSS文件的。

### extract-text-webpack-plugin

```javascript
npm i extract-text-webpack-plugin
```

在 webpack.config.js 中引入：

```javascript
const ExtractTextPlugin = require('extract-text-webpack-plugin')

module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
            fallback: 'style-loader',
            use: 'css-loader'
        })
      }
    ]},
  plugins: [
    new ExtractTextPlugin('bundle.css')
  ]
}
```

内部的fallback属性用于指定当插件无法提取样式时所采用的loader。

use（extract方法里面的）用于指定在提取样式之前采用哪些loader来预先进行处理。

除此之外，还要在Webpack的plugins配置中添加该插件，并传入提取后的资源文件名。

### 多样式文件处理

上面我们将bundle.css作为文件名传给了extract-text-webpack-plugin，但当工程有多个入口时就会发生重名问题。就像在前面的章节中我们配置动态的output.filename一样，这里我们也要对插件提取的CSS文件使用类似模板的命名方式。

```javascript
const ExtractTextPlugin = require('extract-text-webpack-plugin')

module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
            fallback: 'style-loader',
            use: 'css-loader'
        })
      }
    ]},
  plugins: [
    new ExtractTextPlugin('[name].css')
  ]
}
```

### mini-css-extract-plugin

说到mini-css-extract-plugin的特性，最重要的就是它支持按需加载CSS，以前在使用extract-text-webpack-plugin的时候我们是做不到这一点的。

举个例子，a.js通过import()函数异步加载了b.js，b.js里面加载了style.css，那么style.css最终只能被同步加载（通过HTML的link标签）。但是现在mini-css-extract-plugin会单独打包出一个0.css（假设使用默认配置），这个CSS文件将由a.js通过动态插入link标签的方式加载。

在配置上mini-css-extract-plugin与extract-text-webpack-plugin有以下几点不同：

* loader规则设置的形式不同，并且mini-css-extract-plugin支持配置publicPath，用来指定异步CSS的加载路径。

* 不需要设置fallback。

* 在plugins设置中，除了指定同步加载的CSS资源名（filename），还要指定异步加载的CSS资源名（chunkFilename）

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
            {
                loader: MiniCssExtractPlugin.loader,
                options: {
                    publicPath: '../'
                }
            },
            'css-loader'
        ]
      }
    ]},
  plugins: [
    new MiniCssExtractPlugin({
    	filename: '[name].css',
    	chunkFilename: '[id].css'
    })
  ]
}
```

## 样式预处理

样式预处理指的是在开发中我们经常会使用一些样式预编译语言，如SCSS、Less等，在项目打包过程中再将这些预编译语言转换为CSS。

### Sass 与 Scss

sass-loader就是将SCSS语法编译为CSS，因此在使用时通常还要搭配css-loader和style-loader。类似于我们装babel-loader时还要安装babel-core，loader本身只是编译核心库与Webpack的连接器，因此这里我们除了sass-loader以外还要安装node-sass，node-sass是真正用来编译SCSS的，而sass-loader只是起到黏合的作用。

```javascript
npm i sass-loader node-sass
```

```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader']
      }
    ]
  }
}
```

### Less

```javascript
npm i less-loader less
```

```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.less/,
        use: ['style-loader', 'css-loader', 'less-loader']
      }
    ]
  }
}
```

## PostCss

PostCSS并不能算是一个CSS的预编译器，它只是一个编译插件的容器。它的工作模式是接收样式源代码并交由编译插件处理，最后输出CSS。

### PostCss 与 Webpack

使用postcss-loader可以轻松地将PostCSS与Webpack连接起来。使用npm进行安装。

```
npm i postcss-loader
```


```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.css/,
        use: ['style-loader', 'css-loader', 'postcss-loader']
      }
    ]
  }
}
```

postcss-loader可以结合css-loader使用，也可以单独使用，也就是说不配置css-loader也可以达到相同的效果。唯一不同的是，单独使用postcss-loader时不建议使用CSS中的@import语句，否则会产生冗余代码。

除此之外，PostCSS要求必须有一个单独的配置文件。我们需要在项目的根目录下创建一个postcss.config.js。

```
module.exports = {
  // ...
  module: {}
}
```

### 自动前缀

Autoprefixer是一个样式工具，可以根据caniuse.com上的数据，自动决定是否要为某一特性添加厂商前缀，并且可以由开发者为其指定支持浏览器的范围。

```
npm i autoprefixer
```

在postcss.config.js中添加autoprefixer。

```
const autoprefixer = require('autoprefixer')

module.exports = {
  // ...
  plugins: [
    autoprefixer({
      grid: true,
      browsers: [
        '> 1%',
        'last 3 versions',
        'android 4.2',
        'ie 8'
      ]
    })
  ]
}
```

### stylelint

stylelint是一个CSS的质量检测工具，就像eslint一样，我们可以为其添加各种规则，来统一项目的代码风格，确保代码质量。

```
npm i stylelint
```

在postcss.config.js中添加相应配置。

```
const autoprefixer = require('autoprefixer')

module.exports = {
  // ...
  plugins: [
    stylelint({
      config: {
        rules: {
          'declaration-no-important': true
        }
      }
    })
  ]
}
```

这里我们添加了declaration-no-important这样一条规则，当我们的代码中出现了“！important”时就会给出警告。

使用stylelint可以检测出代码中的样式问题（语法错误、重复的属性等），帮助我们写出更加安全并且风格更加一致的代码。

### CssNext

PostCSS可以与CSSNext结合使用，让我们在应用中使用最新的CSS语法特性。

```
npm i postcss-cssnext
```

在postcss.config.js中添加相应配置。

```
const postcssCssnext = require('postcss-cssnext')

module.exports = {
  // ...
  plugins: [
    postcssCssnext({
      browsers: [
        '> 1%',
        'last 2 versions',
      ]
    })
  ]
}
```

指定好需要支持的浏览器之后，我们就可以顺畅地使用CSSNext的特性了。

## Css Modules

CSS Modules是近年来比较流行的一种开发模式，其理念就是把CSS模块化，让CSS也拥有模块的特点:

* 每个CSS文件中的样式都拥有单独的作用域，不会和外界发生命名冲突。

* 对CSS进行依赖管理，可以通过相对路径引入CSS文件。

* 可以通过composes轻松复用其他CSS模块

使用CSS Modules不需要额外安装模块，只要开启css-loader中的modules配置项即可。

```javascript

module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
        'style-loader',
        {
           loader: 'css-loader',
           options: {
                 modules: true,
                 localIdentName: '[name]__[local]__[hash:base64:5]'
              }
           }
        ]
      }
    ]},
}
```

这里比较值得一提的是localIdentName配置项，它用于指明CSS代码中的类名会如何来编译。

例：

```css
/* style.css */

.title {
	color: #fff;
}
```

经过编译后可能将成为.style__title__1CFy6。

* [name]指代的是模块名，这里被替换为style。

* [local]指代的是原本的选择器标识符，这里被替换为title。

* [hash：base64：5]指代的是一个5位的hash值，这个hash值是根据模块名和标识符计算的，因此不同模块中相同的标识符也不会造成样式冲突。
