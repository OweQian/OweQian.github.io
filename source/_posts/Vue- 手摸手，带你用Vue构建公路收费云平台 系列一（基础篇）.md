---
title: 手摸手，带你用Vue构建公路收费云 一（基础篇）
date: 2018-4-12 20:06:52
tags: [Vue]
categories: 前端
---

#### 导语
> 说好的总结终于来了，本篇文章主要来说说在开始写实际业务代码之前的一些准备工作，这里不会教你Webpack的基础配置，热更新原理等，有需求的请自行 Google。

<!--more-->

![](http://ow43yt5wd.bkt.clouddn.com/%E5%85%AC%E8%B7%AF%E6%94%B6%E8%B4%B9%E4%BA%91.png)

* 完整项目地址：[公路收费云平台](http://vue.xjsf.org)
* 系类文章一：手摸手，带你用Vue构建公路收费云平台 系列一（基础篇）
* 系类文章二：手摸手，带你用Vue构建公路收费云平台 系列二（登录权限篇）
* 系类文章三：手摸手，带你用Vue构建公路收费云平台 系列三（实战篇）
* 系类文章四：手摸手，带你用Vue构建公路收费云平台 系列四（挖坑填坑篇）

***
## 前端技术选型
在三大框架中选择了VUE，因为自己对这个框架更为熟悉，同时，VUE在国内的生态环境已经相当成熟，各类组件库，插件库都比较丰富。搭配上vue-router和vuex组成vue全家桶，在UI框架上选择了专门针对pc端的ElementUI，ajax库选择了vue-resource，css预编译使用scss，js语言使用了支持 async function 的ES7，搭配babel编译成生产环境可运行的ES5。打包工具使用webpack。

* 前端框架： vue
* 路由管理： vue-router
* 状态管理： vuex、sessionStorage、localStorage
* UI框架： ElementUI
* Ajax库： vue-resource
* JS语言： ES6、ES7、babel
* CSS预编译语言： scss
* 打包工具： webpack
* 图表：echarts
* ESLint
* CSS 预处理： SASS

***
## 功能模块
![](http://ow43yt5wd.bkt.clouddn.com/%E5%85%AC%E8%B7%AF%E6%94%B6%E8%B4%B9%E4%BA%91%E7%9A%84%E5%89%AF%E6%9C%AC.png)

***
## 目录结构
```   .
├── build           // 构建相关  
├── config           // 配置相关
├── dist            // 编译后静态文件
├── documents         // api ui等相关文档  
├── node_modules        // 依赖相关
├── src            // 源代码
│  ├── api          // 所有请求
│  ├── api-elk        // elk所有请求
│  ├── assets         // 主题 字体等静态资源
│  ├── components       // 全局公用组件
│  ├── directive       // 全局指令
│  ├── filtres        // 全局 filter
│  ├── http          // 全局拦截器
│  ├── mixin         // mixin
│  ├── pages         // pages
│  ├── router         // 路由
│  ├── storage         // localStorage, sessionStorage
│  ├── store         // 全局 store管理
│  ├── styles         // 全局样式
│  ├── svg          // svg
│  ├── config.js       // appConfig
│  ├── main.js        // 入口 加载组件 初始化等
│  ├── resources.js      // api resources
│  ├── utils.js        // 全局公用方法
├── static           // 第三方不打包资源
├── .babelrc          // babel-loader 配置
├── .editorconfig       // 编辑器格式化代码配置
├── .eslintrc.js        // eslint 配置项
├── .gitignore         // git 忽略项
├── index.html         // html模板
└── package.json        // package.json
```
***
简单介绍一下src：
### api 和 pages
随着业务的不断迭代，模块会越来越多，所以我们建议根据业务模块来划分pages和api，使之一一对应，从而方便维护。如下图：

![](http://ow43yt5wd.bkt.clouddn.com/WechatIMG15.jpeg)
![](http://ow43yt5wd.bkt.clouddn.com/WechatIMG14.jpeg)

***
### components
这里的components放置的都是全局公用的一些组件，如自定义卡片组件，日期选择组件等。一些页面级的组件建议还是放在各自的pages文件夹下，方便管理。如图：

![](http://ow43yt5wd.bkt.clouddn.com/WechatIMG18.jpeg)

***
### router
当打包构建应用时，Javascript 包会变得非常大，影响页面加载。如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样写大大减少了初始页面 js 的大小并且能更好的利用游览器的缓存。

我们选择结合 Vue 的异步组件和 Webpack 的代码分割功能，轻松实现路由组件的懒加载。

```
{
  path: '/login',
  name: 'login',
  component: (resolve) => require(['../../pages/Login.vue'], resolve),
  meta: {
    auth: false
  }
}
```

***
### storage
出于某些方面的考虑，我们在项目中用到了Window.sessionStorage和Window.localStorage，在这里关于其原理及相应语法不再进行介绍，有需求的请自行 Google。

***
### styles
Sass(Scss)号称是世界上最成熟、稳定和强大的专业级CSS扩展语言，通过Sass(Scss)来改变前端工程师，提高效率，降低成本。在这里，我们建议将颜色、屏幕分比率常用值等这些反复使用的css属性值定义成变量，与项目其它常规样式分开，便于维护。

![](http://ow43yt5wd.bkt.clouddn.com/WechatIMG19.jpeg)

***
### svg
项目中涉及到一些小图标，我们统一采用svg图形格式。在这里推荐[vue-svg-icon](https://github.com/cenkai88/vue-svg-icon)----一个 vue的可变彩色 svg 图标方案。

```
<icon name="chameleon" :scale="20"></icon>
```

***
### store
个人建议不要为了用 vuex 而用 vuex。就拿收费云来说，它虽然比较庞大，几十个业务模块，几十种权限，但业务之间的耦合度是很低的，所以没有必要使用 vuex 来存储data，每个页面里存放自己的 data 就行。当然有些数据还是需要用 vuex 来统一管理的，如登录token，用户信息等，还是用vuex管理更加的方便，具体当然还是要结合自己的业务场景的。
总之还是那句话，不要为了用vuex而用vuex！

***
## webpack
这里是用vue-cli的webpack-template为基础模板构建的，如果你对这个有什么疑惑请自行Google，相关的配置介绍其他文章已经介绍地很详细了，这里将不再展开。

| 开发环境 | 生产环境 |
| :-- | :-- |
| cors | cors |
| proxy | nginx |

***
### alias
当项目逐渐变大之后，文件与文件之间的直接引用关系会很复杂，这时候就需要使用alias了。

```
resolve: {
  extensions: ['.js', '.vue', '.json'],
  alias: {
    'vue$': 'vue/dist/vue.esm.js',
    '@': resolve('src'),
  }
}
```

```
// 使用
import dataPicker from '@/components/base-date-picker'
```

***
## ESLint
不管是多人合作还是个人项目，代码规范是很重要的。这样做不仅可以很大程度地避免基本语法错误，也保证了代码的可读性，简单介绍一下我们的.eslintrc。

```
module.exports = {
  root: true,
  // https://github.com/feross/standard/blob/master/RULES.md#javascript-standard-style
  parserOptions: {
    parser: 'babel-eslint'
  },
  extends: [
    // https://github.com/vuejs/eslint-plugin-vue#priority-a-essential-error-prevention
    // consider switching to `plugin:vue/strongly-recommended` or `plugin:vue/recommended` for stricter rules.
    'plugin:vue/essential',
    // https://github.com/standard/standard/blob/master/docs/RULES-en.md
    'standard'
  ],
  env: {
    browser: true,
  },
  plugins: [
    'vue'
  ],
  rules: {
    'eol-last': 0,
    'arrow-parens': 0,
    // allow debugger during development
    'no-debugger': process.env.NODE_ENV === 'production' ? 2 : 0,
    "no-new": 0,
    "no-useless-escape": 0,
    "spaced-comment": ["error", "always", { "exceptions": ["-"] }],
    "space-before-function-paren": 0
  }
}
```

***
## 前后端交互
在平时的开发中，交流占据了我们很大一部分时间。但如果前后端有一个好的协作方式的话能节省很多时间。我们的开发流程是前后端一起开会讨论项目，之后后端根据需求，首先定义数据格式和api，然后 mock api 生成好文档，我们前端才开始对接接口。

如果后端不肯来帮你mock数据的话，前端自己来mock也是很简单的。你可以了解下eoLinker，通过eoLinker的Mock API，随机生成虚拟数据接口，让前端在脱离后端的情况下独立进行测试，帮助你实现由文档驱动的敏捷开发。

***
### 跨域问题
前后端交互不可避免的就会遇到跨域问题，在这里推荐两种跨域解决方案。

最推荐的常用方式就是**cors**全称为 Cross Origin Resource Sharing（跨域资源共享）。这玩意对应前端来说和平时发请求写法上没有任何区别，工作量基本都在后端这里。每一次请求浏览器必须先以 OPTIONS 请求方式发送一个预请求，从而获知服务器端对跨源请求所支持 HTTP 方法。在确认服务器允许该跨源请求的情况下，以实际的 HTTP 请求方法发送那个真正的请求。推荐的原因是只要第一次配好了，之后不管有多少接口和项目复用就可以了，一劳永逸的解决了跨域问题，而且不管是开发环境还是测试环境都能方便的使用。

但总有后端觉得麻烦不想这么搞。那前端也是有解决方案的，在开发环境下可使用**webpack 的 proxy，使用也是很方便的，看一下文档就会使用了。但这种方法在生成环境中是不适用的，在生产环境中需要使用Nginx反向代理**。不管是 proxy 和 nginx 的原理都是一样的通过搭建一个中转服务器来转发请求规避跨域的问题。

***
### 封装HTTP请求
在讨论与后端数据交互的选型中，我们选择了vue-resource，它是一个非常轻量的用于处理HTTP请求的插件，提供了两种方式来处理HTTP请求：使用Vue.http或this.$http 使用Vue.resource或this.$resource。

主要特点如下：

* 体积小：压缩以后只有大约12KB，服务端启用gzip压缩后只有4.5KB大小，这远比jQuery的体积要小得多。

* 支持主流的浏览器：除了不支持IE 9以下的浏览器，其他主流的浏览器都支持。
* 支持Promise API和URI Templates**：Promise对象用于异步计算。URI Templates表示URI模板，有些类似于ASP.NET MVC的路由模板。
* 支持拦截器：拦截器是全局的，拦截器可以在请求发送前和发送请求后做一些处理。拦截器在一些场景下会非常有用，比如请求发送前在headers中设置access_token，或者在请求失败时，提供共通的处理方式。

```
import Vue from 'vue'
import { HOST } from '../config'
import store from '../store'
import router from '../router'
import VueResource from 'vue-resource'
import { Message } from 'element-ui'

Vue.use(VueResource)
// 设置全局 api ROOT
Vue.http.options.root = HOST
// request 请求拦截
Vue.http.interceptors.push((request, next) => {
  // if logged in, add the token to the header
  if (store.state.token) {
    // // 越权请求拦截
    // if (store.state.permission) {
    //   let path = `${request.url}`.replace(HOST, '')
    //   let perName = `${request.method},${path}`
    //   console.error('perName', perName)
    //   if (store.state.permission.hasOwnProperty(perName)) {
    //     if (!store.state.permission[perName]) {
    //       this.$message({
    //         message: '无访问权限，请联系企业管理员',
    //         type: 'warning'
    //       })
    //     }
    //   }
    // }
    request.headers.set('Authorization', `JWT ${store.state.token}`)
  }
  next()
})
//  response 拦截
Vue.http.interceptors.push((request, next) => {
  next((response) => {
    // don't handle for login page
    if (store.state.route.path === '/login') {
      return
    }
    if (response.status === 403) {
      store.commit('LOG_OUT')
      router.push({path: '/login', query: {redirect: store.state.route.fullPath}})
    }
    if (response.status === 401
    ) {
      store.commit('LOG_OUT')
      router.push({path: '/login', query: {redirect: store.state.route.fullPath}})
    }
    if (response.status === 500) {
      Message.error({
        dangerouslyUseHTMLString: true,
        message: `服务器错误<div>${response.url}</div>`
      })
    }
  })
})
```

***
## 占坑
* 系类文章一：手摸手，带你用Vue构建公路收费云平台 系列一（基础篇）
* 系类文章二：手摸手，带你用Vue构建公路收费云平台 系列二（登录权限篇）
* 系类文章三：手摸手，带你用Vue构建公路收费云平台 系列三（实战篇）
* 系类文章四：手摸手，带你用Vue构建公路收费云平台 系列四（挖坑填坑篇）


