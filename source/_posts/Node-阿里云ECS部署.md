---
title: Node-阿里云ECS部署
date: 2018-11-27 12:19:00
tags: [Node]
categories: 后端
---

#### 导语
> 双11抢购了一台阿里云服务器，趁工作不忙，搞了一个简单的node项目。那么问题来了，如何将node项目部署到阿里云ECS？

<!--more-->

此教程仅供新手借鉴，多少有些缺陷，有问题还望各位大佬指正。

### 准备工作

* 一个域名
* 阿里云ECS(实例镜像：CentOS7.4)
* 如果使用Windows，推荐xshell。如果是Mac，推荐iTerm2

(注：因本人使用Mac，以下均已Mac为主)

***
### 终端连接服务器

* 打开iTerm2，输入连接命令ssh username@ip公网地址，回车，输入你在购买服务器时设置的密码。

![](http://images.gitbook.cn/fb945240-f68e-11e7-b8d1-49d5de1a2fe1)

* 密码输入正确，进入服务器。

![](http://images.gitbook.cn/8a21b2f0-f68f-11e7-b8d1-49d5de1a2fe1)

cd / 进入根目录，创建一个空目录 mkdir soft，用来放我们的安装包。

***
### 安装Node

下载node包。

```
wget https://nodejs.org/dist/v8.9.4/node-v8.9.4.tar.gz
```

下载完成后，进行压缩包解压。

```
tar -zxvf v8.9.4.tar.gz
```

进入解压好的目录并执行config配置命令。

```
cd node-v8.9.4 && ./configure
```

执行编译及安装命令。

```
make && make install
```

经过漫长的等待之后，一个node环境就安装好了。可以通过node -v 或者 npm -v来检查当前node/npm是否安装成功。

![](http://images.gitbook.cn/aa87c390-f6e4-11e7-b0db-b9b8a7444865)

如果你需要升级node和npm版本，执行以下命令即可。

```
npm i -g n
n stable
```

***
### 安装nrm和pm2

nrm是一个管理npm源的插件，方便切换国内外以及私人npm库。

pm2是node服务器的守护进程，方便管理当前服务器上的所有服务。

这里说一下cnpm，如果安装依赖包很慢或者根本没进度，输入命令

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

然后使用cnpm代替npm安装。

回到我们熟悉的soft目录。

***
#### nrm

安装nrm。

```
npm install -g nrm
```

常用命令：

* nrm add name address 添加新的源
* nrm use name 切换到源
* nrm ls 查看所有可用的源

![](http://images.gitbook.cn/ef923690-f6e5-11e7-b28d-519a9998aa5b)

***
#### pm2

安装pm2。

```
npm install -g pm2
```

常用命令：

* pm2 list 查看pm2守护服务
* pm2 start index.js 启动一个pm2守护进程命令
* pm2 restart id/name 重启一个pm2守护进程命令
* pm2 delete id/name 删除一个正在使用的进程
* pm2 delete all 删除所有正在使用的进程
* pm2 monit 查看pm2的消耗

![](http://images.gitbook.cn/24e04d00-f6e6-11e7-b0db-b9b8a7444865)

***
### 安装mysql

卸载已有的mysql。

```
rpm -qa|grep -i mysql
yum remove 'mysql'
```

下载mysql源。

```
// 5.7.24
wget http://repo.mysql.com//mysql57-community-release-el7-7.noarch.rpm
```

安装下载的源。

```
rpm -ivh mysql57-community-release-el7-7.noarch.rpm
```

下载安装mysql。

```
yum install mysql-server
yum install mysql-devel
yum install mysql
```

启动mysql。

```
service mysqld start
```

常用命令：

* service mysqld status 查看mysql当前的状态
* service mysqld stop 停止mysql
* service mysqld restart 重启mysql
* service mysqld start 启动mysql

如果遇到错误：ERROR 2002 (HY000): Can't connect to local MySQL server through socket /var/lib/mysql/mysql.sock'，请在tmp下创建一个软连接：

```
ln -s /tmp/mysql.sock /var/lib/mysql
```

打开my.cnf并修改。

```
vi /etc/my.cnf
```

![](http://images.gitbook.cn/4d980cc0-f6ea-11e7-9a49-93c72a04fa82)

重启数据库。

```
service mysqld restart
```

登录(默认root是没有密码的)。

```
mysql -u root

```

这时出现ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)，登录root帐号需要密码，现在我们没有设置密码，哪来的密码。

于是我们开始忘记密码的操作

第一步，在/etc/my.cnf文件中添加skip-grant-tables
第二步，重启mysql，service mysqld restart
第三步，登录mysql，mysql -u root
第四步，修改密码：

```
mysql>use mysql;
mysql>update mysql.user set authentication_string=password('your password') where user='root';
mysql>flush privileges;
mysql>exit;
```

第五步，恢复/etc/my.cnf，将skip-grant-tables删除或者注释掉
第六步，重启mysql，service mysqld restart

分配用户

host指定该用户在哪个主机上可以登陆，此处的”localhost”，是指该用户只能在本地登录，不能在另外一台机器上远程登录，如果想远程登录的话，将”localhost”改为”%”，表示在任何一台电脑上都可以登录;也可以指定某台机器可以远程登录。

```
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
CREATE USER 'test'@'%' IDENTIFIED BY 'password';
```

给创建的用户权限

* privileges 用户的操作权限,如SELECT，INSERT，UPDATE等.如果要授予所的权限则使用ALL
* databasename 数据库名
* tablename 表名,如果要授予该用户对所有数据库和表的相应操作权限则可用表示, 如.*.

```
GRANT privileges ON databasename.tablename TO 'username'@'host'
```

***
### 安装Redis

Redis是一个非常好用的数据仓库，既可以做nosql数据库，也可以做缓存数据库。

下载安装包。

```
wget http://download.redis.io/releases/redis-4.0.6.tar.gz
```

解压并进入解压之后的文件夹。

```
tar -zxvf redis-4.0.6.tar.gz &&cd redis-4.0.6
```

编译并安装。

```
make && make install
```

启动redis服务。

```
redis-server redis.conf
```

![](http://images.gitbook.cn/9ab114d0-f82e-11e7-b753-f548c55dc4dd)

此时我们发现，不能再输入任何命令，因为redis-server已经占用了这个命令行客户端。我们需要再打开一个命令行工具并连接服务器。

使用redis客户端连接Redis服务，执行Redis文件夹下的redis-cli文件redis-cli，输入几个命令测试一下简单的set/get：

![](http://images.gitbook.cn/c4d479e0-f82f-11e7-bec2-d1185e7bb6e2)

关掉客户端，redis服务也会停止。这时，我们就需要修改redis.conf文件，将daemonizede的值改为yes。

![](http://images.gitbook.cn/afb6f050-f830-11e7-b753-f548c55dc4dd)

再次运行Redis的启动命令redis-server redis.conf，这次就不会占用命令行了，可以继续操作其他的，Redis服务也不会停止。

***
### nginx

nginx在转发请求，负载均衡等方面非常有用。它可以将几个不同的服务集中在一个80端口下，根据域名或者请求路径来区分。

在安装nginx之前，先要安装几个依赖插件。

```
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
```

安装openssl。

```
yum install -y openssl openssl-devel
```

下载nginx包。

```
wget https://nginx.org/download/nginx-1.15.6.tar.gz
```

解压并进入解压好的目录。

```
tar -zxvf nginx-1.15.6.tar.gz && cd nginx-1.15.6
```

执行配置命令。

```
./configure
```

编译并安装。

```
make && make install
```

查看安装结果。

```
whereis nginx
```

![](http://images.gitbook.cn/d892e470-f835-11e7-b753-f548c55dc4dd)

跳转到安装好的nginx目录下，进入sbin目录。

```
cd /usr/local/nginx/sbin
```

启动nginx。

```
./nginx
```

常用命令：

* ./nginx 启动nginx
* ./nginx -s stop 停止nginx
* ./nginx -s quit 退出nginx
* ./nginx -s reload 重启nginx

***
### 安装Git

centOS上没有安装git，有需要的话，我们可以自己手动安装一下。

```
yum install git
git --version
```

***
### 使用pm2启动项目

在本地搭建并测试好node项目，上传到GitHub。然后git clone一下。

配置ECS安全组规则，暴露3000接口。

如果项目中使用数据库，运行之前要先建立好数据库，然后再运行项目，登录数据库执行create database dabasename;

```
git clone https://github.com/***
cd ***
cnpm i
pm2 start index.js
```

注：项目监听3000端口，如果顺利的话，项目已经运行起来了。
   可以使用pm2 list查看进程列表，使用pm2 log打印日志。

***
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
