---
title: 关闭Mac的Microsoft AutoUpdate
date: 2020-10-13 13:14:34
categories: 
tags: office
---



最近使用Office 发现AutoUpdate一直会启动。我也不需要里面的更新。每次还要把它推出。

网上看到有两种方法，一种是暴力删除，另一种是通过权限限制。

暴力可不是我喜欢的方式，所以选择后者。



方法：

打开终端

```bash
cd /Library/Application\ Support/Microsoft/MAU2.0
sudo chmod 000 Microsoft\ AutoUpdate.app  
```

两行命令后，输入密码就可以了。



