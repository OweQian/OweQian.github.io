---
title: Vue-cli配置环境变量[转]
date: 2018-7-09 22:19:00
tags: [Vue]
categories: 前端
---

#### 导语
> vue-cli环境下，默认已经配置了一个NODE_ENV的环境变量，有时我们可能觉得光是只有一个开发环境和生产环境还不够，比如我们还想来一个测试坏境，这时候就需要自己定义了。

<!--more-->
在实际项目开发中，经常会碰到需要环境变量的情景，在vue-cli的环境下，默认已经配置了一个NODE_ENV的环境变量，当npm run dev的时候这个是development， 当npm run build 的时候这个是production.用过vue-cli开发的通常对这个都不陌生，用这个最多的基本就是我们配置ajax请求的域名头了。

```
if (process.env.NODE_ENV === 'production') {
  axios.defaults.baseURL = 'http://customer.medsci.cn'
}
```

***
通常会有测试服务器和正式服务器环境之分，我们请求的域名也得区分，所以通过proces.env.NODE_ENV来区分环境变量，通过这个能够全局访问的参数，我们可以处理很多需要环境处理不同的操作。

上面的process.env.NODE_ENV是vue-cli默认配置的，有时候我们可能觉得光是只有一个开发环境和生产环境还不够，比如我们还想来一个测试坏境，这时候就需要自己定义了。以下以windows坏境为例，mac环境类似。

首先可以安装cross-env

```
npm install cross-env --save-dev
```

vue-cli的配置本质就是webpack的配置，当我们运行npm run dev 的时候，其实执行的是package.json 中的scripts 的dev中 ，即build/dev-server.js 这里面一系列的配置启动整个服务。

我们只想加一个环境变量，可以复制一份，在dev下加一个devtest命令:

```
"devtest": "cross-env TESTING=true  node build/dev-server.js"
```

这样我们就可以通过npm run devtest也能启动整个服务了，注意上面的命令，我们加了一个TESTING=true ，这个就是加入的环境变量，TESTING:true， 通过上面的devtest的配置，我们可以运行npm run devtest像nppm run dev那样运行起来整个程序，不过我们发现，此时在项目中我们获取不到process.env.TESTING这个变量，这是为什么呢，如果是直接用webpack是可以的，经过查看，是在vue-cli的一系列配置文件中，已经写死了这些，不过我们可以改动加上
在根目录 config下，有dev.env.js和prod.env.js，这两个文件就是关键了，我们在NODE_ENV下面加一行TESTING:process.env.TESTING。

```
module.exports = {


  NODE_ENV: '"production"'，
  TESTING: process.env.TESTING
}
```

这时我们就能全局访问process.env.TESTING变量了，当我们npm run dev的时候，这个是undefined，当npm run devtest的时候是true， 同理我们可以添加一个npm run test命令， 与npm run build命令对应，加入打包测试的环境变量。
vue-cli的配置本质还是webpack的配置， 普通的webpack配置也可以采用此方法。



