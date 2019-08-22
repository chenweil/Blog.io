---
title: Ubutun16.04安装Python
date: 2019-05-24 11:17:59
categories: Python
tags: [Python,Ubutun]
---

### 目的 
安装python3.7.3 
安装pip

### 准备工作
* 系统内置python2.X,去除默认python的软链, `sudo rm /usr/bin/python`
* 安装一些软件包&软件包保持最新状态. 

`sudo apt-get update` 

`sudo apt-get install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev wget`

### 安装Python 
通过编译安装python

默认我下载在home里, `cd`

下载新python文件, `wget https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tgz
`

解压文件, `tar zxf Python-3.7.3.tgz`

把这个文件拷贝到放置的位置. 这里我放到/usr/local/python ` mkdir -p /usr/local/python
`

进入这个目录, 执行 `./configure --enable-optimizations`
之后执行 `sudo make -j 8`  这里8根据设备cpu核心数来的,不知道你可以写1(手动滑稽)

make之后 该 ~~make install~~ 嘛? NO! 是 `sudo make altinstall`

装完之后, 可以尝试 python --version 看看有没有, 如果没有或者版本不对.可能是准备里你没有删除 /usr/bin/python 或者这个不存在.
需要手动添加一下,我这个是没有给我创建成功. 

`sudo ln -s /usr/local/Python-3.7.3/python /usr/bin/python`

这里版本是3.7.3版本的python已经好了,但我发现没有pip.那我只好装一下pip.

### 安装pip

同样在home目录下载:
`curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`

下载完成后, 执行 `python get-pip.py` 
我遇到一个问题是: Command 'lsb_release -a' returned non-zero exit status 1
查了一下,大概意思是lsb_release上的问题,这里python2.X用到的(Ubutun自带2.X),那我解决办法是干掉他 `sudo rm -f /usr/bin/lsb_release`

重新执行上面的命令,ok 已经安装上pip. 到此结束.
