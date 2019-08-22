---
title: Centos7时间设置
date: 2019-04-19 11:42:02
categories: Linux
tags: Centos7
---

### Centos7时间相关
#### 查看时间
date 
hwclock 硬件时间
timedatectl  各时间状态

#### 设置&更新服务时间
安装ntpdate
`yum install utp ntpdate`

#### 设置同步
`ntpdate cn.pool.ntp.org`        (time.windows.com) 地址看喜好

#### 设置硬件时间
hwclock --systohc

#### 设置时区
timedatectl set-timezone Asia/Shanghai  （上海）

timedatectl  很多设置，需要请查相关资料。
