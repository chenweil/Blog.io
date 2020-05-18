---
title: Linux重启后Docker设置自动启动&容器自动启动设置
date: 2020-05-18 11:03:48
categories: 
    - Linux
    - Docker
tags: 
    - Linux
    - Docker
---

## Linux系统重启后Docker自动启动
  系统重启后，如果docker没有启动，那么docker下所有的服务就都挂了。
  配置过一次总是忘记命令，这里特意记录一下：
  `systemctl enable docker.service`

## 容器自动启动配置

容器配置好自动启动后，当docker运行后，容器也自动启动。这样能保证服务的稳定性。不用再登录到系统来操作各种容易启动问题。
涉及到的参数为：
`--restart=always`

参数有：

  * no，默认策略，在容器退出时不重启容器

  * on-failure，在容器非正常退出时（退出状态非0），才会重启容器。例如：on-failure:3，在容器非正常退出时重启容器，最多重启3次

  * always，在容器退出时总是重启容器

  * unless-stopped，在容器退出时总是重启容器，但是不考虑在Docker守护进程启动时就已经停止了的容器

### 容器已存在更新重启配置
可能容器我们已经生成了，后面想实现always这个参数，可以用到下面的命令：
`docker container update --restart=always 容器名XXX`
这样就达到了想要的目的。
