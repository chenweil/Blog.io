---
title: 解决MAC软件提示已损坏或移到废纸篓
date: 2020-02-07 20:24:02
categories: note
tags: MAC
---



### 系统版本

macOS Catalina 10.15.1



* 方法一

  允许任何来源的应用。

  系统偏好设置 -> 安全性和隐私： 在 **允许从以下位置下载的应用程序** 选项中选择 **任何来源**

  在 Sierra 10.12 中可能看不到这个选项，开启此功能需要执行命令`sudo spctl --master-disable`,

  输入密码就可以看到此选项。

* 方法二

  如果上面发法还是打不开，可能需要此方法。

  `sudo xattr -r -d com.apple.quarantine /Applications/xxxx.app/` 

  *这里的x x.app如果你不知道名字，可以通过应用中查看应用简介中的名称与扩展名,*

  *或者在Applications目录下输入名称补全*

  ![](https://s2.ax1x.com/2020/02/07/12ElE6.jpg)

  

