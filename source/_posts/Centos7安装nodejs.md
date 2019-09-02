---
title: Centos7安装nodejs
date: 2019-09-02 10:15:01
categories: Centos
tags: nodejs
---

## 安装nodejs

### 下载官方node的tar包:
*https://nodejs.org/en/download/*

`wget https://nodejs.org/dist/v10.16.3/node-v10.16.3-linux-x64.tar.xz`

### 解压下载文件 
`tar -xvf node-v10.16.3-linux-x64.tar.xz`

### 部署bin
*这里下载位置为家里面 ~/*

`ln -s ~/node-v10.16.3-linux-x64/bin/node /usr/bin/node`

`ln -s ~/node-v10.16.3-linux-x64/bin/npm /usr/bin/npm`

一个是node 另一个是npm

### 验证
node -v
npm -v 

