---
title: Ubutun下安装Docker
date: 2019-04-04 14:57:16
categories: Linux
tags: [Docker,Ubutun]
---

### Docker简介
> 一个能够把开发的应用程序自动部署到容器的开源引擎 
> 三大概念：镜像（Image）容器（Container）仓库（Repository）

具体信息请参考官方。[官方概述](https://docs.docker.com/engine/docker-overview/)（养成看文档习惯）

### 安装环境
Ubuntu 16.04 LTS

### Docker安装
根据官方doc安装。[官方doc](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
1.如果你之前装过，命令卸载。
`sudo apt-get remove docker docker-engine docker.io containerd runc`

2.更新包索引
`apt-get update`

3.安装包以允许apt通过HTTPS使用存储库:
`sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common`
（斜线换行，一条命令）

4.添加Docker的官方GPG密钥:
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
`sudo apt-key fingerprint 0EBFCD88`

5.使用以下命令设置稳定存储库。要添加 夜间或测试存储库，请在下面的命令中的单词后添加单词nightly或test（或两者）stable。
`$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"`
 (lsb_release -cs子命令返回Ubuntu发行版的名称)
 
 6.安装最新版本的Docker CE和containerd，或者转到下一步安装特定版本：
 `sudo apt-get install docker-ce docker-ce-cli containerd.io`
 
 7.运行hello-world 映像验证是否正确安装了Docker CE:
 `sudo docker run hello-world`
 (执行之后，返回docker的信息)
 
 至此，安装过程是结束了。
