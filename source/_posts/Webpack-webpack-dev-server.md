---
title: Webpack-webpack-dev-server
date: 2018-09-26 15:15:00
tags: [Webpack]
categories: 前端
---

#### 导语
> webpack是一个现代JavaScript应用程序的静态模块打包器，在webpack处理应用程序时，它会在内部创建一个依赖图，用于映射到项目需要的每个模块，然后将所有这些依赖生成到一个或多个bundle。

<!--more-->

### webpack-dev-server

webpack-dev-server为webpack提供的一个静态服务的工具，并能够实时重新加载。

***
#### 基础使用

安装：

```
npm i webpack-dev-server -D
```

package.json添加scripts配置：

```
{
  // ...
  "scripts": {
    "dev": "webpack-dev-server --mode development" // 指定mode为development
  }
}
```

```
npm run dev
```

如果项目构建了HTML文件，直接访问http://localhost:8080/就可以看到index.html页面了。否则，就会看到一个静态资源列表列表的页面。

***
### 基础配置

在webpack配置中，可以通过devServer字段来配置webpack-dev-server。

```
module.exports = {
  devServer: {
    // ...
  }
}
```

* host：指定使用一个host，默认是localhost。
* port：指定端口号，默认是8080。
* publicPath: 构建好的静态文件在浏览器中以什么路径去访问，默认为'/'。假设服务器运行在http://localhost:8080，并且output.filename被设置为bundle.js。完整的访问路径是 http://localhost:8080/bundle.js，如果你配置了 publicPath: 'assets/'，那么上述 bundle.js 的完整访问路径就是 http://localhost:8080/assets/bundle.js。

建议devServer.publicPath和output.publicPath一样。

* proxy：配置代理

```
proxy: {
  '/api': {
    target: 'http://localhost:3000' // 将url中带有/api的请求代理到本地3000端口的服务器上
    pathRewrite: {'^/api': ''} // 把URL中path部分的api去掉
  }
}
```
* color：启用/jiny9ong控制台的彩色输出。
* contentBase：提供未经webpack构建的额外静态文件内容的目录。

```
// 使用当前目录下的 public
contentBase: path.join(__dirname, "public")

// 也可以使用数组提供多个路径
contentBase: [path.join(__dirname, "public"), path.join(__dirname, "assets")]
```
publicePath的优先级高于constBase。

* before：在服务内部的所有其他中间件之前， 提供执行自定义中间件的功能。

* after：在服务内部的所有其他中间件之后， 提供执行自定义中间件的功能。