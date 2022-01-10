---
title: armbian配置（N1盒子）
date: 2021-11-20 14:04:47
categories:
  - NetWork
tags: NetWork
---

armbian配置（N1盒子）

## 设置时区：
`timedatectl set-timezone Asia/Shanghai`

## 安装docker
1. `apt update`

2. `apt install ca-certificates  curl  gnupg  lsb-release`

3. `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`

4. `echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null`

5. `apt update`

6. `apt install docker-ce docker-ce-cli containerd.io`

## 安装docker-compose
*wget地址是加速器转换地址，版本和地址请根据版本号编辑地址* 

`wget https://download.fastgit.org/docker/compose/releases/download/v2.1.1/docker-compose-linux-aarch64 && mv docker-compose-linux-aarch64 /usr/local/bin/docker-compose && chmod +x /usr/local/bin/docker-compose`

## WIFI
* 查看附近无线网络信号：nmcli dev wifi list

* 无密码的 WIFI：nmcli device wifi connect <SSID|BSSID>

* 加密WIFI： nmcli device wifi connect <SSID|BSSID> password <password>

## 网卡配置

* 设置多IP 

```ini
auto eth0:1
allow-hotplug eth0:1
iface eth0:1 inet static
address 1.1.1.1/30
```