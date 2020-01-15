---
title: MySQL5.7修改root密码
date: 2020-01-15 15:23:54
categories: MySQL
tags:
- MySQL
---

最近维护一个MySQL数据库，由于各种原因，密码已经不知道了。现在让我在这台服务器上使用里面的MySQL数据库。

怎么办？ 

首先问了一圈没有人知道。那么只能靠自己了。

查看软件版本:  `mysql --version`

之后通过神奇的Google科普了一下。 
知道了具体的方法：

* 关闭mysql服务。

* 修改my.conf
  在里面[mysqld] 下面最后加入一行
  
  ```ini
  [mysqld]
  ...
  skip-grant-tables
  ```
  
  修改完保存退出。

* 重启mysql服务。

* `mysql` 进入mysql 不需要密码了。 
  
    `show databases;`  查看数据库
  
    `use mysql;` 选择 mysql 数据库
    在此数据库执行更新语句（修改root用户的密码为root）：
    `update user set authentication_string=password('root') where user='root';
    `
    `flush privileges;` 更新权限
  
    退出mysql

* 把最开始my.conf加入的语句删除。

* 重启mysql服务

最后可以通过设置的密码登陆数据库了。
