---
title: Centos7防火墙相关设置
date: 2019-04-18 17:44:22
categories: Linux
tags: Centos7
---

#### Centos7与之前不太一样
以前都是用iptables，公司服务器环境事7，凑巧不熟一台新服务。我为了测试，再本地虚机上装了一台。
这里默认防火墙是 firewall，其实为了省事还是可以安装一个iptables的。这里学习一下firewall一些操作。

#### 查看防火墙服务状态
`systemctl status firewalld`

####查看f防火墙状态
`firewall-cmd --state` 

#### 查看规则
`firewall-cmd --list-all `

####停止&开启防&重启火墙
`systemctl stop firewalld.service`
`systemctl start firewalld.service`
`systemctl restart firewalld.service`

#### 关闭防火墙
`systemctl disable firewalld.service`

#### 重载防火墙
`firewall-cmd —reload`

#### 查询开放端口
`firewall-cmd --list-ports`

#### 开放一个端口 例如tcp 8010
firewall-cmd --zone=public --add-port=80/tcp --permanent

–zone #作用域
–add-port=8010/tcp #添加端口，格式为：端口/通讯协议
–permanent #永久生效，没有此参数重启后失效

#### 查询某端口是否开放(8010)
`firewall-cmd --query-port=8010/tcp`

#### 移除端口规则
`firewall-cmd --permanent --remove-port=8010/tcp`
