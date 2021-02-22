---
title: '解决/usr/local/go/pkg/darwin_amd64/runtime/cgo.a: permission denied问题'
date: 2021-02-22 09:06:08
categories: Golang
tags:
---

### 最近Goland在run的时候发现一个问题
`open /usr/local/go/pkg/darwin_amd64/runtime/cgo.a: permission denied`

情况具体是当我run的时候有问题。debug可以。根据错误提示看到是权限的事。

### 解决方式
执行 `sudo chown -R xxx:yyy /usr/local/go`

*xxx 用户名， yyy 组名*

命令的目的：更改go目录的所有者用户和组。

### 查看用户名&用户组
当前用户名 常用命令 `who am i`
查看当前用户名和组 `ls -la` 

### 参考
[https://github.com/golang/go/issues/37962](https://github.com/golang/go/issues/37962)