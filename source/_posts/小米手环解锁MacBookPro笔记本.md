---
title: 小米手环解锁MacOS系统笔记本MacBookPro
date: 2021-05-21 16:51:23
categories: 小米手环
tags:
---

## 通过小米手环解锁笔记本
官方windows是提供了方法的。
我目前用的MacBookPro，所以说下苹果笔记本的解锁方式。

## 安装软件`BLEUnlock`

[库](https://github.com/ts1/BLEUnlock)

### 安装方式：

brew 安装 `brew install bleunlock`

或下载程序 [下载发布的程序](https://github.com/ts1/BLEUnlock/releases)

### 安装好打开软件：

![](https://i.loli.net/2021/05/21/BAINRZxyTp3zV5Y.png)

设备列表选择手环，如果发现不到就在`小米运动app`中打开`实验室`选项里`小米笔记本解锁`开关。

![实验室功能](https://i.loli.net/2021/05/21/vtHLPFJ2jIfoAWR.jpg)

设备列表选择你的小米手环。

![选择设备](https://i.loli.net/2021/05/21/JjhRDlLHA4idP3O.png)

解锁RSSI与锁定RSSI 是根据你dBM值来判断是否锁定/解锁笔记本。是一个阈值。

![锁定RSSI](https://i.loli.net/2021/05/21/wJcOye9SYs7QLXM.png)

延迟锁定，无信号超时是时间阈值。功能顾名思义。

我这里选择开启了屏保来锁定、以及开机启动。

通过以上配置之后，我们就可以通过小米手环来解锁MacOS笔记本了。

## 请注意一点
如果你是每天背着本上下班的话，那我建议上下班前后别开启此功能。
为什么呢，因为你设定的RSSI值肯定是离近笔记本的。这时候你带着手环和笔记本的时候。他很容易就吧本解锁了。然后你发现从书包拿出来本巨热无比。

为啥呢，他唤醒了设备啊 还解锁了～

这点我不知道怎么搞定呢，好 结束～

祝好

拜拜～