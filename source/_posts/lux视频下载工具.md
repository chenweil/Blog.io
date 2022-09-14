---
title: lux视频下载工具
date: 2022-09-14 14:53:43
categories: soft
tags: soft
---

## 简介
是一个下载视频的软件. 我一般通过此软件下载b站视频.支持专辑下载.
支持的站点: [介绍](https://github.com/iawia002/lux#supported-sites)
## 仓库
[github:lux](https://github.com/iawia002/lux)

## 安装(mac)
`brew install lux`

### 需要的依赖
[ffmpeg](https://www.ffmpeg.org/)


## 使用

命令: `lux [OPTIONS] URL [URL...]`

例子: 
```bash
#我想下载某专辑视频,首先创建一个文件夹
mkdir golang
#然后进入文件夹
cd golang
##执行lux 下载专辑 (-eto bilibili每集文件名不包含播放列表标题)
lux -p -eto https://www.bilibiLi.com/video/BV1SF411z7XW
##选择性下载 例如只下载 10-20集
lux -p -eto -items 10-20 https://www.bilibiLi.com/video/BV1SF411z7XW
## 选择清晰度 需要-i查看一下清晰度.查看 download with值
lux -i https://www.bilibili.com/video/BV1SF411z7XW
## 加入清晰度参数1080(不建议,列表执行清晰度可能不全是1080会导致报错)
lux -p -eto -f 80-7 https://www.bilibili.com/video/BV1SF411z7XW
```

## 图片展示
* 下载:
![下载](https://s2.loli.net/2022/08/24/5MYUxakfXPiBZIq.png)
* 下载完成结果
![41QIMdEcWzi2s5K](https://s2.loli.net/2022/08/24/41QIMdEcWzi2s5K.png)
* 查询清晰度
![mEfOYcG9Uq76L2D](https://s2.loli.net/2022/08/24/mEfOYcG9Uq76L2D.png)