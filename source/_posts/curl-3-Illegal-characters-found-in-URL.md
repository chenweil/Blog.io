---
title: 'curl: (3) Illegal characters found in URL'
date: 2019-10-09 14:45:13
categories: note
tags: curl
---


### curl: (3) Illegal characters found in URL

昨天在服务器上执行一个脚本,在linux新建的sh,把本地编辑器的内容粘贴到文件里.
结果执行的时候报错了. 问题就是 curl:(3)Illegal characters found in URL

看着一脸懵逼啊!~

google了一下,看到几个方法.其中一个我感觉还不错:

1. 首先vi 进入sh脚本 `vi XXX.sh`
2. `:set ff?`    # 这里我现实的是 *fileforma=dos* 我这里显示是这个
3. `:set fileformat=unix` # 把fileforma 设置好
4. `:wq`   

通过这个方式,可以解决这个问题,网上也有人提出其他方法把\r\n 手动替换\n的.

参考:
[http://www.itbiancheng.com/linux/4885.html](http://www.itbiancheng.com/linux/4885.html)
