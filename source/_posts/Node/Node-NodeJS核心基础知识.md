---
title: Node-NodeJS核心基础知识
img: https://miro.medium.com/max/723/1*GGeDSLuC8vPZimiZ-t88mQ.jpeg
date: 2019-6-26 23:11:52
tags: [Node]
categories: 前端
---

#### 导语
> 截止今天，像谷歌、亚马逊等全球 Top 10 互联网大公司，都早已入坑 Node...

<!--more-->

### Nodejs 概述

Nodejs 是基于 Chrome V8 引擎构建的，由事件循环分布 I/O 任务，最终工作线程会将任务放到线程池中执行，而事件循环等待执行结果就可以了。

* Nodejs 是 Javascript 的运行时环境。

* Nodejs 构建在 Chrome V8 这个著名的 Javascript 引擎上。

* Nodejs 每个函数都是同步的，而 I/O 操作是异步的。

* Nodejs 使用 npm 作为包管理器。

### Nodejs 安装

Nodejs 支持 macOS、Linux 以及 Windows 等多个主流操作系统。

#### 3m 安装法

* nvm 开源的 Nodejs 版本管理器

* npm Nodejs 包管理端

* nrm 解决 npm 镜像访问慢的问题。

##### nvm

###### 安装 nvm

首先，在终端执行如下命令：

```javascript
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash
```

意思是通过 curl 命令下载 install.sh 脚本并执行。执行完成后，重新打开终端窗口，执行

```javascript
vi ~/.zshrc
```

把 nvm 命令的执行路径放到 ~/.zshrc 文件下：


```javascript
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
```

执行 source 命令，使系统环境变量生效：

```javascript
source ~/.zshrc
```

输入：

```javascript
nvm --version

0.33.1
```

###### 查看可安装的 Nodejs 版本

安装 Node 之前，需要了解通过 nvm 可以安装哪些版本的 Node。

```javascript
nvm ls-remote
```

* LTS 版本是指长期支持版本，推荐给大部分用户使用，一般在生产环境中使用。

* Current 版本是指当前正在开发的尝鲜版本，不完全版本，需要经过一段时间的测试、开发和修复 bug 等才能变为 LTS 版本，一般供开发者学习。


###### 安装 Node

```javascript
nvm install 10.15.3
```

通过 nvm 安装的 Node 位于用户目录下，而非系统目录下。

```javascript
which node
/Users/wangqian/.nvm/versions/node/v10.15.3/bin/node
```

默认使用某个 Node 版本来编译代码，需要手动指定一个 default 别名。

```javascript
nvm alias default node
default -> node (-> v10.15.3)
```

此时，输入 node -v，以后在终端的任何地方使用的默认版本都会是你设置的版本。

```javascript
node -v
v10.15.3
```


如何使用 nvm 切换 Node 版本？首先安装 Node 8.x

```javascript
nvm install 8
```

切换到 8.x 版本：

```javascript
nvm use 8
Now using node v8.16.0 (npm v6.4.1)
node -v
v8.16.0
```

如何知道本机通过 nvm 安装了哪些 Node 版本呢？

```javascript
nvm ls
->      v8.16.0
       v10.15.3
default -> node (-> v10.15.3)
node -> stable (-> v10.15.3) (default)
stable -> 10.15 (-> v10.15.3) (default)
iojs -> N/A (default)
lts/* -> lts/dubnium (-> N/A)
lts/argon -> v4.9.1 (-> N/A)
lts/boron -> v6.17.1 (-> N/A)
lts/carbon -> v8.16.0
lts/dubnium -> v10.16.0 (-> N/A)
```

若要经常切换 Node 版本，最痛苦的莫过于全局模块需要重新安装。针对这种情况，nvm 提供了一个很贴心的一键安装全局模块的 nvm reinstall-packages命令。

```javascript
nvm reinstall-packages 6
```

nvm 默认远端下载地址是 https://nodejs.org/dist，如果想安装自定义的 Node 版本，可以指定 nvm 的远端下载地址。

```javascript
NVM_NODEJS_ORG_MIRROR=https://nodejs.org/download/chakracore-nightly
```

对于一些正在测试的尝鲜版本，可以在下面的地址下载。

```javascript
NVM_NODEJS_ORG_MIRROR=https://nodejs.org/download/test
```
