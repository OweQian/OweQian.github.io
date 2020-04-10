---
title: Webpack-代码分片
img: https://media.trytn.us/library/2019/02/webpack.jpg
date: 2020-01-14 15:21:00
tags: [Webpack]
categories: 前端
---

#### 导语
> webpack是一个现代JavaScript应用程序的静态模块打包器，在webpack处理应用程序时，它会在内部创建一个依赖图，用于映射到项目需要的每个模块，然后将所有这些依赖生成到一个或多个bundle。

<!--more-->

# 代码分片

代码分片（code splitting）是Webpack作为打包工具所特有的一项技术，通过这项技术我们可以把代码按照特定的形式进行拆分，使用户不必一次全部加载，而是按需加载。

代码分片可以有效降低首屏加载资源的大小，但同时也会带来新的问题，比如我们应该对哪些模块进行分片、分片后的资源如何管理等，这些也是需要关注的。

## 通过入口划分代码

在Webpack中每个入口（entry）都将生成一个对应的资源文件，通过入口的配置我们可以进行一些简单有效的代码拆分。

对于Web应用来说通常会有一些库和工具是不常变动的，可以把它们放在一个单独的入口中，由该入口产生的资源不会经常更新，因此可以有效地利用客户端缓存，让用户不必在每次请求页面时都重新加载。如：

```
// webpack.config.js

entry: {
   app: './app.js',
   lib: ['lib-a', 'lib-b', 'lib-c']
}

// index.html
<script src="dist/lib.js"></script>
<script src="dist/app.js"></script>
```

这种拆分方法主要适合于那些将接口绑定在全局对象上的库，因为业务代码中的模块无法直接引用库中的模块，二者属于不同的依赖树。

## CommonsChunkPlugin

CommonsChunkPlugin是Webpack 4之前内部自带的插件（Webpack 4之后替换为了SplitChunks）。它可以将多个Chunk中公共的部分提取出来。公共模块的提取可以为项目带来几个收益：

* 开发过程中减少了重复模块打包，可以提升开发速度；

* 减小整体资源体积；

* 合理分片后的代码可以更有效地利用客户端缓存。

更改webpack.config.js，添加CommonsChunkPlugin。

```javascript
const webpack = require('webpack')

module.exports = {
    entry: {
        foo: './foo.js',
        bar: './bar.js'
    },
    output: {
        filename: '[name].js'
    },
    plugins: [
        new webpack.optimize.CommonsChunkOlugin({
        	name: 'commons',
        	filename: 'commons.js'
        })
    ]
}
```

* name：用于指定公共chunk的名字。

* filename：提取后的资源文件名。

最后，记得在页面中添加一个script标签来引入commons.js，并且注意，该JS一定要在其他JS之前引入。

### 提取 vendor

虽然CommonsChunkPlugin主要用于提取多入口之间的公共模块，但这不代表对于单入口的应用就无法使用。我们仍然可以用它来提取第三方类库及业务中不常更新的模块，只需要单独为它们创建一个入口即可。

```
const webpack = require('webpack')

module.exports = {
    entry: {
        app: './app.js',
        vendor: ['react']
    },
    output: {
        filename: '[name].js'
    },
    plugins: [
        new webpack.optimize.CommonsChunkOlugin({
        	name: 'vendor',
        	filename: 'vendor.js'
        })
    ]
}

// app.js
import React from 'react'
document.write('app.js', React.version)
```

为了将react从app.js提取出来，我们在配置中加入了一个入口vendor，并使其只包含react，这样就把react变为了app和vendor这两个chunk所共有的模块


### 设置提取范围

通过CommonsChunkPlugin中的chunks配置项可以规定从哪些入口中提取公共模块。

```
const webpack = require('webpack')

module.exports = {
    entry: {
        a: './a.js',
        b: './b.js',
        c: './c.js',
    },
    output: {
        filename: '[name].js'
    },
    plugins: [
        new webpack.optimize.CommonsChunkOlugin({
        	name: 'common',
        	filename: 'common.js',
        	chunks: ['a', 'b']
        })
    ]
}
```

我们在chunks中配置了a和b，这意味着只会从a.js和b.js中提取公共模块。

### 设置提取规则

CommonsChunkPlugin的默认规则是只要一个模块被两个入口chunk所使用就会被提取出来，比如只要a和b用了react，react就会被提取出来。

然而现实情况是，有些时候我们不希望所有的公共模块都被提取出来，此时我们可以通过CommonsChunkPlugin的minChunks配置项来设置提取的规则，该配置项非常灵活，支持多种输入形式。

* 数字

minChunks可以接受一个数字，当设置minChunks为n时，只有该模块被n个入口同时引用才会进行提取。另外，这个阈值不会影响通过数组形式入口传入模块的提取。

```
const webpack = require('webpack')

module.exports = {
    entry: {
        foo: './foo.js',
        bar: './bar.js',
        vendor: ['react'],
    },
    output: {
        filename: '[name].js'
    },
    plugins: [
        new webpack.optimize.CommonsChunkOlugin({
        	name: 'vendor',
        	filename: 'vendor.js',
        	minChunks: 3
        })
    ]
}
```

我们令foo.js和bar.js共同引用一个util.js。

```javascript
// foo.js
import React from 'react'
import './util'
document.write('foo.js', React.version)

// foo.js
import React from 'react'
import './util'
document.write('bar.js', React.version)

// util.js
console.log('util')
```

如果实际打包应该可以发现，由于我们设置minChunks为3，util.js并不会被提取到vendor.js中，然而react并不受这个的影响，仍然会出现在vendor.js中。

* Infinity

设置为无穷代表提取的阈值无限高，也就是说所有模块都不会被提取。

* 函数

minChunks支持传入一个函数，它可以让我们更细粒度地控制公共模块。Webpack打包过程中的每个模块都会经过这个函数的处理，当函数的返回值是true时进行提取。

```
module.exports = {
    plugins: [
        new webpack.optimize.CommonsChunkOlugin({
        	name: 'vendor',
        	filename: 'vendor.js',
        	minChunks: function(module, count) {
        	  // module.context 模块目录路径
        	  if (module.context && module.context.include('node_modules')) {
        	    return true
        	  }
        	  // module.resource 包含模块名的完整路径
        	  if (module.resource && module.resource.endsWith('util.js')) {
        	    return true
        	  }
        	  // count 为模块被引用的次数
        	  if (count > 5) {
        	    return true
        	  }
        	}
        })
    ]
}
```

借助上面的配置，我们可以分别提取node_modules目录下的模块、名称为util.js的模块，以及被引用5次（不包含5次）以上的模块。

### hash 与 长效缓存

当我们使用该插件提取公共模块时，提取后的资源内部不仅仅是模块的代码，往往还包含Webpack的运行时（runtime）。Webpack的运行时指的是初始化环境的代码，如创建模块缓存对象、声明模块加载函数等。

将运行时的代码单独提取出来。请看下面这个例子：

```
const webpack = require('webpack')

module.exports = {
    entry: {
        app: './app.js',
        vendor: ['react'],
    },
    output: {
        filename: '[name].js'
    },
    plugins: [
        new webpack.optimize.CommonsChunkOlugin({
        	name: 'vendor'
        }),
        new webpack.optimize.CommonsChunkOlugin({
          name: 'manifest'
        }),
    ]
}
```

上面的配置中，通过添加了一个name为manifest的CommonsChunkPlugin来提取Webpack的运行时。

注：manifest的CommonsChunkPlugin必须出现在最后，否则Webpack将无法正常提取模块。

### CommonsChunkPlugin 的不足

在提取公共模块方面，CommonsChunkPlugin可以满足很多场景的需求，但是它也有一些欠缺的地方。

* 一个CommonsChunkPlugin只能提取一个vendor，假如我们想提取多个vendor则需要配置多个插件，这会增加很多重复的配置代码。

* 前面我们提到的manifest实际上会使浏览器多加载一个资源，这对于页面渲染速度是不友好的。

* 由于内部设计上的一些缺陷，CommonsChunkPlugin在提取公共模块的时候会破坏掉原有Chunk中模块的依赖关系，导致难以进行更多的优化。

## optimization.SplitChunks

optimization.SplitChunks（简称SplitChunks）是Webpack 4为了改进CommonsChunk-Plugin而重新设计和实现的代码分片特性。

比如我们前面异步加载的例子，在换成Webpack 4的SplitChunks之后，就可以自动提取出react了。

```
module.exports = {
    entry: './foo.js',
    output: {
        filename: 'foo.js',
        publicPath: '/dist/'
    },
    mode: 'development',
    optimization: {
      splitChunks: {
        chunks: 'all'
      }
    }
}

// foo.js
import React from 'react'
import('./bar.js')
document.write('app.js', React.version)

// bar.js
import React from 'react'
document.write('bar.js', React.version)
```

此处Webpack 4的配置与之前相比有两点不同：

* 使用optimization.splitChunks替代了CommonsChunkPlugin，并指定了chunks的值为all，这个配置项的含义是，SplitChunks将会对所有的chunks生效（默认情况下，SplitChunks只对异步chunks生效，并且不需要配置）。

以下是SplitChunks默认情形下的提取条件：

* 提取后的chunk可被共享或者来自node_modules目录。

* 提取后的Javascript chunk体积大于30kB（压缩和gzip之前），CSS chunk体积大于50kB。

* 在按需加载过程中，并行请求的资源最大值小于等于5。按需加载指的是，通过动态插入script标签的方式加载脚本。

* 在首次加载时，并行请求的资源数最大值小于等于3。

### 默认的异步提取

前面我们对SplitChunks添加了一个chunks：all的配置，这是为了提取foo.js和bar.js的公共模块。实际上SplitChunks不需要配置也能生效，但仅仅针对异步资源。


```
module.exports = {
    entry: './foo.js',
    output: {
        filename: 'foo.js',
        publicPath: '/dist/'
    },
    mode: 'development'
}

// foo.js
import('./bar.js')
document.write('app.js', React.version)

// bar.js
import React from 'react'
document.write('bar.js', React.version)
```

### 配置

为了更好地了解SplitChunks是怎样工作的，我们来看一下它的默认配置。

```
		optimization: {
       splitChunks: {
          chunks: 'async',
          minSize: {
              javascript: 30000,
              style: 50000
          },
          maxSize: 0,
          minChunks: 1,
          maxAsyncRequest3: 3,
          maxInitialRequests: 2,
          automaticNameDelimiter: '~',
          name: true,
          cacheGroups: {
            vendors: {
              test: /[\\/]node_modules[\\/]/
            },
            default: {
              minChunks: 2,
              priority: -20,
              reuseExistingChunk: true
            }
          }
       }
    }
```

* 匹配模式

通过chunks我们可以配置SplitChunks的工作模式。它有3个可选值，分别为async（默认）、initial和all。async即只提取异步chunk，initial则只对入口chunk生效（如果配置了initial则上面异步的例子将失效），all则是两种模式同时开启。

* 匹配条件

minSize、minChunks、maxAsyncRequests、maxInitialRequests都属于匹配条件。

* 命名

配置项name默认为true，它意味着SplitChunks可以根据cacheGroups和作用范围自动为新生成的chunk命名，并以automaticNameDelimiter分隔。

* cacheGroups

可以理解成分离chunks时的规则。默认情况下有两种规则——vendors和default。vendors用于提取所有node_modules中符合条件的模块，default则作用于被多次引用的模块。我们可以对这些规则进行增加或者修改，如果想要禁用某种规则，也可以直接将其置为false。当一个模块同时符合多个cacheGroups时，则根据其中的priority配置项确定优先级。


