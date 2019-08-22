---
title: Centos7启动等级设置
date: 2019-04-15 16:55:19
categories: 
- Linux
tags: Centos7
---

#### Centos7启动级别
启动级别分为7个：
0 - 系统停机状态
1 - 单用户工作状态
2 - 多用户状态（没有NFS）
3 - 多用户状态（有NFS）
4 - 系统未使用，留给用户
5 - 图形界面
6 - 系统正常关闭并重新启动

#### 切换启动级别
之前一直都是在种端中输入指令 init3 切换启动级别。
设置永久启动3级别， `vi /etc/inittab`  把init3设置默认即可。

**centos7 设置出现不同**
runlevels被targets所取代，即CentOS7采用加载target的方式来替代之前的启动级别。
multi-user.target  = init3
graphical.target    = init5
我们日常实用图形窗口init5，我们不需要图形，可以切换到init3等启动级别上。
`systemctl set-default multi-user.target`  设置为init3 
`systemctl set-default graphical.target` 设置为init5
