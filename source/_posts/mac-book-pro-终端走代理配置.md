---
title: mac book pro 终端走代理配置
date: 2020-01-20 08:13:10
categories: note
tags:
- macbook
- terminal
---



### mac 终端走ssr代理

**前提是你已经知道怎么使用shadowsocks软件，并且可以出去之后。**

看下自己ssr 代理端口号是多少 
高级设置看下 本地Socks5监听端口
记住这个端口号。
后面：
mac 现在默认终端是用的zsh 
编辑 `vi ～/.zshrc`
在最后加入
//alias 后面的名字自己可以按照习惯定义， 比如我定义proxy 是 ss。 每当我需要时候 ss 一下就可以了。 不需要的时候执行第二句。

```ini
//端口按照你的来配置alias unproxy='unset all_proxy'
alias proxy='export all_proxy=socks5://127.0.0.1:1086'
```

测试：
首先在没有走ss 的时候， `curl cip.cc` 得到一个国内的信息。
当使用ss之后， 再通过这个查询就可以看到变化了。

为了便捷，我觉得这个命令也可以设置成别名方式。

设置一个where 每次当我迷茫不知道自己出没出去的时候，就可以 输入where 看下。 
`alias where='curl cip.cc'`
