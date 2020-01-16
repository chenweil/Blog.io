---
title: 'golang.org/x/xerrors：undefined: errors.Frame'
date: 2020-01-16 19:05:39
categories: Golang
tags: Golang
---


### 项目初始化遇到问题
错误为：

```bahs
../go/pkg/mod/golang.org/x/xerrors@v0.0.0-20190410155217-1f06c39b4373/adaptor_go1_13.go:16:14: undefined: errors.Frame
../go/pkg/mod/golang.org/x/xerrors@v0.0.0-20190410155217-1f06c39b4373/format_go1_13.go:12:18: undefined: errors.Formatter
exit status 2
exit status 1
```

通过科普得到一个方法：
`go get -u golang.org/x/xerrors`

问题解决了。