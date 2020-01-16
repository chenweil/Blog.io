---
title: Golang 环境准备
date: 2020-01-16 18:51:34
categories: Golang
tags:
- Golang
---


## 安装GOlang

环境:macOS
 
shell: zsh

* 安装步骤：

```bash
brew update && brew upgrade # 更新
brew install go             # 安装 go
```

* 配置环境变量
我的本shell 是zsh 下面是按照zsh配置：
如果需要修改默认的环境变量配置修改 `vim ~/.bash_profile` 或 `vim ~/.zshrc`

```bash
# GOROOT安装的路径
export GOROOT=/usr/local/Cellar/go/1.9/libexec
#GOPATH root bin
export GOBIN=$GOROOT/bin
export PATH=$PATH:$GOBIN
#GOPATH
export GOPATH=$HOME/go
#GOPATH bin
export PATH=$PATH:$GOPATH/bin
```

* 退出保存后，使文件生效 `source ~/.zshrc=`

