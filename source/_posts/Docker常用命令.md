---
title: Docker常用命令
date: 2019-04-11 15:09:19
categories: Docker
tags: Docker
---


## Docker常用命令
说常用不如说自己用到的命令。

### 容器相关
学习了一下docker，基础常用命令记录下。
####docker run/新建并启动容器
这个run其实包含两个不走，先执行新建容器(docker create),接着启动容器(docker start)。敲两个是不是有点麻烦吧。

docker run  xx [COMMAND] 

例子 `docker run -it ubuntu:14.04 /bin/bash`

这里希望启动一个基于 ubuntu 14.04镜像 来创建一个容器，-t选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上，-i则让容器的标准输入保持打开。更多的命令选项可以通过man docker-run命令来查看。之后命令还有一项，启动一个bash终端。 这条命令涉及到很多知识了。
-参数 常用 -i -d -t -p，   -d 是否在后台运行，-p 映射到本地主机端口。剩下的看手册来补下。  

#### docker create && docker start && docker stop 
创建，启动，停止。
有一个容易停止了，可以用 docker start  XX容器 启动。 XX 可以是容器的ID，也可以是name。

#### docker rm
删除一个容器（最好先把这个容器停止了再删除）。
-f 可以强制删除。-v 删除与容器关联的卷（如果刚学习还真不知道什么是卷）。

#### docker attach 
进入容器，如果开启了一个 -d 后台启动容器。 我们怎么进去看看？  `docker attach XX容器`
这个命令我学习时候用过，感觉有时候不太好使。命令执行完卡那不动。

#### docker exec 
可以在容器内直接执行任意命令。
`docker exec -it xx /bin/bash`    这可以进入xx镜像，并打开bash。 相比这个比上面的attach 好多了。

#### docker ps
列出启动中的容器， `docker ps -a` 列出所有镜像。

###仓库相关
#### docker images
列出本地镜像文件

#### docker rmi
删除本地镜像文件

#### docker seach xxx 
在docker hub查询xxx 镜像

#### docker pull 
像不像git？ docker pull xx 可以下载xx镜像到本地。 （还能联想到 push 吧？ commit ？ 这些吧）

#### docker login 
登陆docker hub

#### docker push 
推送本地镜像到docker hub上。

### 数据相关
#### -v 
在容器内创建数据卷    `docker run -d  -v /test ubuntu`  在此镜像下创建一个test数据卷
也可以挂在主机目录为数据卷  `docker run -d  -v /usr/local/src:/opt/test ubuntu`   将本地的/usr/lcoal/src  挂载到此镜象的 /opt/test  作为数据卷。 在本机修改，容器内可以看到。
这里可以增加参数来控制读写，默认读写。

#### volumes-from
数据卷容器
容器与容器间的数据挂在参数。
例如有个容器为 files    ，通过另一个 test来挂在files。 `docker run -it --volumes-from files --name test ubuntu`
`--name` 是为后者容器起名。这个名字叫test 挂在了files 容器的数据。

### 端口映射，容器互联
#### -p && -P
`docker run -itd -p 8080:80 --name web nginx:1.15`   本机8080端口映射到容器80.
-p 需要自己分配端口   -P Docker会随机映射一个49000~49900的端口
#### docker port
查看映射端口配置

#### link
`--link`参数可以让容器之间安全地进行交互

基本上了解到的命令吧，后续根据搭建环境以及使用中来丰富其他的命令和参数。
