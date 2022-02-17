---
title: Zabbix-Proxy 部署&运行
date: 2021-06-24 17:20:40
categories: Zabbix
tags: Monitor
---

## 前提

版本： zabbix-server 5.4

任务： 通过SNMP监控网络设备，需要需通过zabbix-proxy 发送到zabbix-server。

## 安装Zabbix-Proxy

1. 安装Zabbix仓库

```bash
    wget https://repo.zabbix.com/zabbix/5.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.4-1+ubuntu20.04_all.deb

    dpkg -i zabbix-release_5.4-1+ubuntu20.04_all.deb

    apt update
```

2. 安装Zabbix-proxy & mysql

_这里我选择的是mysql作为数据库_

`apt install mysql-server`

`apt install zabbix-proxy-mysql`

3. 导入数据

`zcat /usr/share/doc/zabbix-proxy-mysql/schema.sql.gz | mysql -uzabbix -p zabbix`

这里可能跑不通。我装了两次都发现没有 schema.sql.gz 这个文件。
如果你也是，那需要找一下这个sql文件。

下载5.4源码包：
`wget https://cdn.zabbix.com/zabbix/sources/stable/5.4/zabbix-5.4.1.tar.gz`
![](https://img-blog.csdnimg.cn/img_convert/8eadb68dbe4f2051e1c72a2919286f07.png#id=yP1Gu&originHeight=1340&originWidth=652&originalType=binary&ratio=1&status=done&style=none)
解压之后，在 `/zabbix-5.4.1/databases/mysql/` 中

通过 `cat schema.sql | mysql -uzabbix -p` 导入到数据库中。

4.配置zabbix-proxy

`vim /etc/zabbix/zabbix_proxy.conf`

修改Zabbix Server地址,Hostname，在server添加中，此名称要与这里一致。

![](https://img-blog.csdnimg.cn/img_convert/8e3e758e82804c0909ee74bd40d1b988.png#id=HVbW6&originHeight=820&originWidth=1968&originalType=binary&ratio=1&status=done&style=none)

修改为正确的数据库名字、用户名、密码。

![](https://img-blog.csdnimg.cn/img_convert/4f0b8f22d1c7753e24705f37025297a1.png#id=ogGz5&originHeight=994&originWidth=1230&originalType=binary&ratio=1&status=done&style=none)

其他配置可以酌情配置。例如server配置频率，log位置，本地缓存时间、主动被动、监听端口等等。

5. 启动zabbix-proxy
   `systemctl start zabbix-proxy && systemctl enable zabbix-proxy` 
6. 在zabbix-server 中添加proxy，然后在对应的host主机上选择proxy。
   ![](https://img-blog.csdnimg.cn/img_convert/c869c5c2e48a6d94965c4ee9afd1d3a3.png#id=XryPJ&originHeight=1252&originWidth=1686&originalType=binary&ratio=1&status=done&style=none) 

![](https://img-blog.csdnimg.cn/img_convert/4236f0501054b223da6b5dde07a340a0.png#id=UMgsH&originHeight=930&originWidth=3870&originalType=binary&ratio=1&status=done&style=none)

![](https://img-blog.csdnimg.cn/img_convert/f4604e95270f33648790159d8fcde8f6.png#id=UTV3A&originHeight=1158&originWidth=2394&originalType=binary&ratio=1&status=done&style=none)

## zabbix-proxy log

默认配置的位置： `/var/log/zabbix/zabbix_proxy.log`

---

祝好！
本文结束。