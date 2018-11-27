---
title: Linux-常用命令
date: 2017-10-03 16:41:00
tags: [Linux]
categories: 后端
---

#### 导语
> 这篇文章主要是为了使用Linux系统服务器做开发，总结的一些常用命令。

<!--more-->

### Linux常用操作指令列表
| 命令 | 说明 |
| --- | --- |
| ssh username@ip | 连接服务器 |
| cd path | 跳转到路径对应的目录下 |
| rm filename | 删除文件 |
| rm -rf path | 删除目录及目录下的所有文件 |
| ls [-a] | 查看当前目录下的文件和目录(-a：查看所有文件，包括隐藏文件) |
| ll | 查看当前目录下的文件详情(文件权限) |
| pwd | 查看当前路径的完整路径 |
| which name | 默认位置查找该名字 |
| mkdir dirname | 创建空目录 |
| cat path | 查看文件内容 |
| cp origin target | 复制一个文件到另一个地方 |
| mv origin target | 移动一个文件到另一个地方 |
| ps -aux | 显示当前进程 |
| kill -9 processid | 结束进程 |
| tar -zxvf filename | 解压文件 |
| tar -zcvf filename | 压缩文件 |
| vi path | 进入文件编辑模式 |
| vi编辑文件，按下i进入编辑模式 |
| vi退出文件，esc+:wq或esc+:x保存退出 |
| vi退出文件，esc+:q或esc+:q!强制退出 |