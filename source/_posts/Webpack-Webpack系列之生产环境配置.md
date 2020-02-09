---
title: Webpack-Webpack系列之生产环境配置
date: 2020-01-14 15:21:00
tags: [Webpack]
categories: 前端
---

#### 导语
> webpack是一个现代JavaScript应用程序的静态模块打包器，在webpack处理应用程序时，它会在内部创建一个依赖图，用于映射到项目需要的每个模块，然后将所有这些依赖生成到一个或多个bundle。

<!--more-->

# 生产环境配置

在生产环境中我们关注的是如何让用户更快地加载资源，涉及如何压缩资源、如何添加环境变量优化打包、如何最大限度地利用缓存等。

## 环境配置的封装

生产环境的配置与开发环境有所不同，如何让Webpack可以按照不同环境采用不同的配置呢？一般来说有以下两种方式。

* 使用相同的配置文件

比如令Webpack不管在什么环境下打包都使用webpack.config.js，只是在构建开始前将当前所属环境作为一个变量传进去，然后在webpack.config.js中通过各种判断条件来决定具体使用哪个配置。

```
// package.json
{
	...
	"scripts": {
		"dev": "ENV=development webpack-dev-server",
		"build": "ENV=production webpack"
	}
}

// webpack.config.js
const ENV = process.env.ENV
const isProd = ENV === 'production'
module.exports = {
	output: {
		filename: isProd ? 'bundle@[chunkhash].js' : 'bundle.js'
	},
	mode: ENV
}
```

* 为不同环境创建各自的配置文件

比如，我们可以单独创建一个webpack.production.config.js，开发环境的则可以叫webpack.development.config.js。

```
// package.json
{
	...
	"scripts": {
		"dev": "webpack-dev-server --config=webpack.development.config.js",
		"build": "webpack --config=webpack.production.config.js"
	}
}
```

但这种方法存在一个问题，即webpack.development.config.js和webpack.production.config.js肯定会有重复的部分，一改都要改，不利于维护。在这种情况下，可以将公共的配置提取出来，比如我们单独创建一个webpack.common.config.js。

```javascript
module.exports = {
    entry: './src/index.js',
    // development 和 production 共有配置
}
```

然后让另外两个JS分别引用该文件，并添加上自身环境的配置即可。

## 开启 production 模式

Webpack 4中直接加了一个mode配置项，让开发者可以通过它来直接切换打包模式。如：

```javascript
module.exports = {
    mode: 'production'
}
```

这意味着当前处于生产环境模式，Webpack会自动添加许多适用于生产环境的配置项，减少了人为手动的工作。

大部分时候仅仅设置mode是不够的，下面我们继续介绍其他与生产环境相关的自定义配置。

## 环境变量

通常我们需要为生产环境和本地环境添加不同的环境变量，在Webpack中可以使用DefinePlugin进行设置。

```javascript
const webpack = require('webpack')
module.exports = {
    entry: './app.js',
    output: {
        filename: 'bundle.js'
    },
    mode: 'production',
    plugins: [
        new webpack.DefinePlugin({
        		process.env.NODE_ENV: JSON.stringify('production')
        })
    ]
}
```

注：我们在一些值的外面加上了JSON.stringify，这是因为DefinePlugin在替换环境变量时对于字符串类型的值进行的是完全替换。假如不添加JSON.stringify的话，在替换后就会成为变量名，而非字符串值。因此对于字符串环境变量及包含字符串的对象都要加上JSON.stringify才行。

## source map

source map指的是将编译、打包、压缩后的代码映射回源代码的过程。

### 工作原理

Webpack对于工程源代码的每一步处理都有可能会改变代码的位置、结构，甚至是所处文件，因此每一步都需要生成对应的source map。若我们启用了devtool配置项，source map就会跟随源代码一步步被传递，直到生成最后的map文件。这个文件默认就是打包后的文件名加上.map，如bundle.js.map。

当我们打开了浏览器的开发者工具时，map文件会同时被加载，这时浏览器会使用它来对打包后的bundle文件进行解析，分析出源代码的目录结构和内容。

### 配置

JavaScript的source map的配置很简单，只要在webpack.config.js中添加devtool即可。

```javascript
module.exports = {
    // ...
    devtool: 'source-map'
}
```

对于CSS、SCSS、Less来说，则需要添加额外的source map配置项。

```javascript
const path = require('path')
module.exports = {
    // ...
    devtool: 'source-map',
    module: {
        rules: [
            {
                test: /\.scss$/,
                use: [
                    'style-loader',
                    {
                        loader: 'css-loader',
                        options: {
                            sourceMap: true
                        }
                    }, {
                    		loader: 'sass-loader',
                        options: {
                            sourceMap: true
                        }
                    }
                ]
            }]
    }
}
```

Webpack支持多种source map的形式。除了配置为devtool：'source-map'以外，还可以根据不同的需求选择cheap-source-map、eval-source-map等。通常它们都是source map的一些简略版本，因为生成完整的source map会延长整体构建时间，如果对打包速度需求比较高的话，建议选择一个简化版的sourcemap。比如，在开发环境中，cheap-module-eval-source-map通常是一个不错的选择，属于打包速度和源码信息还原程度的一个良好折中。

在生产环境中由于我们会对代码进行压缩，而最常见的压缩插件UglifyjsWebpack-Plugin目前只支持完全的source-map，因此没有那么多选择，我们只能使用source-map、hidden-source-map、nosources-source-map这3者之一。下面介绍一下这3种sourcemap在安全性方面的不同。

### 安全

有了source map也就意味着任何人通过浏览器的开发者工具都可以看到工程源码，对于安全性来说也是极大的隐患。那么如何才能在保持其功能的同时，防止暴露源码给用户呢？Webpack提供了hidden-source-map及nosources-source-map两种策略来提升source map的安全性。

hidden-source-map意味着Webpack仍然会产出完整的map文件，只不过不会在bundle文件中添加对于map文件的引用。这样一来，当打开浏览器的开发者工具时，我们是看不到map文件的，浏览器自然也无法对bundle进行解析。如果我们想要追溯源码，则要利用一些第三方服务，将map文件上传到那上面。目前最流行的解决方案是Sentry。

Sentry是一个错误跟踪平台，开发者接入后可以进行错误的收集和聚类，以便于更好地发现和解决线上问题。Sentry支持JavaScript的source map，我们可以通过它所提供的命令行工具或者Webpack插件来自动上传map文件。同时我们还要在工程代码中添加Sentry对应的工具包，每当JavaScript执行出错时就会上报给Sentry。Sentry在接收到错误后，就会去找对应的map文件进行源码解析，并给出源码中的错误栈。

另一种配置是nosources-source-map，它对于安全性的保护则没那么强，但是使用方式相对简单。打包部署之后，我们可以在浏览器开发者工具的Sources选项卡中看到源码的目录结构，但是文件的具体内容会被隐藏起来。对于错误来说，我们仍然可以在Console控制台中查看源代码的错误栈，或者console日志的准确行数。它对于追溯错误来说基本足够，并且其安全性相对于可以看到整个源码的source-map配置来说要略高一些。

## 资源压缩

### 压缩 javascript

压缩JavaScript大多数时候使用的工具有两个，一个是UglifyJS（Webpack 3已集成），另一个是terser（Webpack 4已集成）。

在Webpack 3中的话，开启压缩需调用webpack.optimize.UglifyJsPlugin。

```javascript
// webpack version < 4
const webpack = require('webpack')
module.exports = {
    entry: './app.js',
    output: {
        filename: 'bundle.js'
    },
    plugins: [
        new webpack.optimize.UglifyJsPlugin()
    ]
}
```

从Webpack 4之后，这项配置被移到了config.optimization.minimize。

```javascript
// webpack version > 4
module.exports = {
    entry: './app.js',
    output: {
        filename: 'bundle.js'
    },
    optimization: {
        minimize: true
    }
}
```

### 压缩CSS

压缩CSS文件的前提是使用extract-text-webpack-plugin或mini-css-extract-plugin将样式提取出来，接着使用optimize-css-assets-webpack-plugin来进行压缩，这个插件本质上使用的是压缩器cssnano，当然我们也可以通过其配置进行切换。

```javascript
const ExtractTextPlugin = require('extract-text-webpack-plugin')
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin')
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
        ]
    },
    plugins: [new ExtractTextPlugin('style.css')],
    optimization: {
        minimizer: [new OptimizeCssAssetsPlugin({
        		assetNameRegExp: /\.optimize\.css$/g,
        		cssProcessor: require('cssnano'),
        		cssProcessorOptions: {
        		    discardComments: {removeAll: true},
        		    canPrint: true
        		}
        })]
    }
}
```

## 缓存

缓存是指重复利用浏览器已经获取过的资源。合理地使用缓存是提升客户端性能的一个关键因素。

### 资源 hash

一个常用的方法是在每次打包的过程中对资源的内容计算一次hash，并作为版本号存放在文件名中，如bundle@2e0a691e769edb228e2.js。

```javascript
module.exports = {
    entry: './app.js',
    output: {
        filename: 'bundle@[chunkhash].js',
    },
    mode: 'production'
}
```

### 输出动态 HTML

接下来我们面临的问题是，资源名的改变也就意味着HTML中的引用路径的改变。每次更改后都要手动地去维护它是很困难的，理想的情况是在打包结束后自动把最新的资源名同步过去。使用html-webpack-plugin可以帮我们做到这一点。

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    // ...
    plugins: [
        new HtmlWebpackPlugin()
    ]
}
```

html-webpack-plugin会自动地将我们打包出来的资源名放入生成的index.html中，这样我们就不必手动地更新资源URL了。

### bundle 体积监控和分析

一个很有用的工具是webpack-bundle-analyzer，它能够帮助我们分析一个bundle的构成。使用方法也很简单，只要将其添加进plugins配置即可。

```javascript
const Analyzer = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
module.exports = {
    // ...
    plugins: [
        new Analyzer()
    ]
}
```
