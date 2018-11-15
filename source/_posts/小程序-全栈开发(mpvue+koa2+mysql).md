---
title: 小程序-全栈开发(mpvue+koa2+mysql)
date: 2017-3-02 16:06:52
tags: [小程序]
categories: 前端
---

#### 导语
> 接触小程序并做了一些项目之后，又开始了Vue的旅程，受其核心思想的影响，对数据/状态管理、组件化、跨平台等都有较高的追求，mpvue 是一个使用 Vue.js开发小程序的前端框架，由此开始了mpvue踩坑之旅，想在提高代码可读性的同时，也增加一点Vue.js的开发体验。

<!--more-->
### 技术栈

***
前端： 微信小程序、mpvue、 eslint、sass、ES6、ES7
后端：koa2、Koa-Router、Knex、腾讯云Wafer2
数据库：Mysql
数据库可视化工具：Navicate Premium

***
## 小程序介绍
一个功能简单的小程序，包含了个人中心、图书列表、个人评论列表三个tab页，后端的数据定义、分类、和存取等，各有其色。

***
### 成果分享

个人中心：

![](http://ow43yt5wd.bkt.clouddn.com/user1.gif)

个人中心：

![](http://ow43yt5wd.bkt.clouddn.com/user2.gif)

图书列表：

![](http://ow43yt5wd.bkt.clouddn.com/home1.gif)

图书详情：

![](http://ow43yt5wd.bkt.clouddn.com/home1.gif)

评论列表：

![](http://ow43yt5wd.bkt.clouddn.com/list-01.gif)

***
### 脑图

![](http://ow43yt5wd.bkt.clouddn.com/naotu.jpg)

***
### 初始化项目

*全局安装vue-cli

$npm install --global vue-cli

*创建基于mpvue-quickstart模板的新项目，一路回车。

$vue init mpvue/mpvue-quickstart koa-wx

*安装依赖

$cd koa-wx

$npm install

$npm run dev

***
### 项目目录

```   .
├── build           // 构建相关  
├── config           // 配置相关
├── dist            // 编译后静态文件 
├── node_modules        // 依赖相关
├── server            // 服务端源代码
│  ├── controllers     
│  ├── middlewares    
│  ├── node_modules    
│  ├── routes    
│  ├── tools     
│  ├── .eslintrc.js 
│  ├── .eslintrc.json  
│  ├── app.js     
│  ├── config.js   
│  ├── nodemon.json   
│  ├── package.json    
│  ├── process.prod.json   
│  ├── qcloud.js   
│  ├── README.md    
│  ├── tools.md  
├── src            // 前端源代码
│  ├── components     
│  ├── config    
│  ├── mock     
│  ├── pages     
│  ├── styles     
│  ├── utils       
│  ├── App.vue        
│  ├── main.js       
│  ├── store.js      
│  ├── weui.css    
├── static          
├── .babelrc         
├── .editorconfig      
├── .eslintignore    
├── .eslintrc.js       
├── .postcssrc.js      
├── index.html        
└── package.json      
└── package-lock.json 
└── project.config.json 
└── README.md 
```
***

### 搭建后端本地开发环境

打开 server/config.js添加以下配置：

```
const CONF = {
      // 其他配置 ...
    serverHost: 'localhost',
    tunnelServerUrl: '',
    tunnelSignatureKey: '27fb7d1c161b7ca52d73cce0f1d833f9f5b5ec89',
      // 腾讯云相关配置可以查看云 API 秘钥控制台：https://console.cloud.tencent.com/capi
    qcloudAppId: '您的腾讯云 AppID',
    qcloudSecretId: '您的腾讯云 SecretId',
    qcloudSecretKey: '您的腾讯云 SecretKey',
    wxMessageToken: 'weixinmsgtoken',
    networkTimeout: 30000
}
```

并修改 MySQL 相关的配置为本地的 MySQL 数据库。

```
mysql: {
    host: 'localhost',
    port: 3306,
    user: 'root',
    db: 'cAuth',
    pass: '******',
    char: 'utf8mb4'
  },
```

*切换到服务端代码目录
$cd server

*安装依赖
$npm install

*安装全局依赖
$npm install -g nodemon

$npm run dev

初始化数据库 - 打开 terminal 输入如下命令：

$node tools/initdb.js

进入Mysql选中刚才创建的数据库，输入 show tables; 可以看到初始化时创建了一个名叫 cSessionInfo 的表。说明本地环境已经搭建成功。

***
### 部署到腾讯云环境

当本地本地开发出一版时，可以部署到腾讯云的开发环境，这样就可以在线上访问该项目。

首先修改项目根目录的 project.config.json 文件，添加：

```
"qcloudRoot": "./server/",
```

然后将本地开发时添加到 server/config.js 的配置删除，在 mysql.pass 那里填上自己的 APPID。

接着进入小程序开发工具，点击右上角的 腾讯云，上传测试代码，弹出如图选项。第一次部署如下选择，之后再次上传选择 智能上传 即可。部署成功会有提示。

![](http://static.imkk.xin/blog/photo/uploadToTecentCloud.png)

***
###上传前端代码


在小程序开发工具右上角找到 上传 选项，填写版本信息，上传后进入开发者控制台，开发管理页面，将该小程序设置为体验版本。

然后在用户身份 ==> 成员管理中，添加项目成员，配置权限，这样好友也可以体验该小程序了。

项目地址：https://github.com/wangqian2017/mpvue-koa2