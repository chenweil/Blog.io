---
title: linux环境redis开机启动
date: 2022-01-11 10:16:39
categories: Linux
tags: Redis
---

## 前提
系统部署在ubuntu20.04中，用到redis数据库。
但是测试时候，设备重启发现redis服务没有启动。 
由于是变异安装的，系统找不到redis.service。

## 解决方案
系统添加服务文件，并执行。
### 编写文件
文件路径`/usr/lib/systemd/system` 

编写文件 `vi /usr/lib/systemd/system/redis.service`

```ini
[Unit]
#服务描述
Description=Redis persistent key-value database
#服务依赖
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
#启动 命令
ExecStart=/home/monitor/redis-6.0.8/src/redis-server /home/monitor/redis-6.0.8/redis.conf --protected-mode no
#停止命令
ExecStop=/home/monitor/redis-6.0.8/src/redis-cli shutdown
#
Restart=always
#服务类型
Type=forking
#User=redis
#Group=redis
RuntimeDirectory=redis
RuntimeDirectoryMode=0755

[Install]
#服务安装设置
WantedBy=multi-user.target
```	

服务配置文件分为[Unit]、[Service]和[Install]三部分。
具体详细的解释需要结合linux知识补充。

### 服务生效
系统重新读取所有服务文件： `systemctl daemon-reload`
启用/禁用开机自启动: `systemctl enable/disable redis`
启动/重启redis： `systemctl start/restart redis`