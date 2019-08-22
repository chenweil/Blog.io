---
title: Laradock安装与使用
date: 2019-08-15 10:01:03
categories: Laravel5
tags: [Laravel, docker]
---

### Laradock 安装与使用

[官网](https://laradock.io) 

GitHub: [https://github.com/laradock/laradock](https://github.com/laradock/laradock) 

### 要求

> * Git
> * Docker >= 17.12

### 项目的位置
1. 已有项目情况: `git submodule add https://github.com/Laradock/laradock.git` 克隆到项目根目录.

结构 :
> + project-a
  + laradock-a
+ project-b
  + laradock-b

2. 没有项目情况: `git clone https://github.com/laradock/laradock.git` 克隆后,在同级部署项目.

> 
* laradock
* project-x
* project-y

### 启动环境
clone下来还没有生成.

进入laradock目录,编辑Web服务器站点配置. `cp env-exalpme .env`
环境是laradock环境,里面可以对相应的版本,配置进行修改.
例如指定mysql版本为5.7 ,`vim .env` ,搜索到mysql部分, 修改**MYSQL_VERSION=5.7.26** 保存退出.(这里还没生成容器前可以统一配置好需要的环境,再生成容器.) 

例如我们需要启动环境需要 mysql,redis,nginx.

执行 docker-compose up -d nginx mysql redis 

经过漫长等待后,可以得到我们想要的环境,通过 docker ps 或者 docker-compose ps 查看容器. 

如果先生成容器,在之后编辑环境时,需要停掉容器,修改完 build之后 再start 容器.
例如修改nginx:
1. `docker-compose stop nginx`
2. 修改.env 或者 nginx conf
3. `docker-compose build nginx` (完全重建,加参数: --no-cache)
4. `docker-compose start nginx` 

### Nginx 配置项目

我们再laradock中,进入nginx/sites/ 下.复制laravel.conf.example 命名为我们的项目.

`cp laravel.conf.example Myblog.conf`

我们在Myblog.conf 配置nginx 具体信息.这里与配置Vhost一样的操作.

**修改完成后,需要build nginx(重建nginx容器).**

### 项目配置数据库
laravel项目, .env 中 此项需要改为**DB_HOST=mysql**,其他参数按照容器mysql配置账号密码等.
Redis 修改类似.

### 项目执行php artisan 方式

在laravel 需要执行 php artisan命令时,我们进入到workspace容器,

`docker-compose exec workspace bash`

进入workkspace后,就可以像以前一样进入项目目录中可以执行.

也可以通过开启ssh.连接workspace 执行.

### 关闭所有正在运行的容器
`docker-compose stop`

### 删除所有现有容器
`docker-compose down`

 *其他内容请详见手册吧*
