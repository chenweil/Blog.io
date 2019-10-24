---
title: windows10家庭版启用组策略gpedit.msc
date: 2019-10-15 11:35:11
categories: windows
tags: windows
---


## 启用组策略gpedit.msc
家庭版很多功能不能使用,凑巧用的就是家庭版. 还想使用gpedit.msc来关闭windows10的更新.
找到一个可行的方法.


1. 需要创建一个脚本. 如果你没有编辑器,那么可以创建一个文本文档.
2. 复制下面一段到本文中.
```bash
@echo off
pushd "%~dp0"
dir /b C:\Windows\servicing\Packages\Microsoft-Windows-GroupPolicy-ClientExtensions-Package~3*.mum >List.txt
dir /b C:\Windows\servicing\Packages\Microsoft-Windows-GroupPolicy-ClientTools-Package~3*.mum >>List.txt
for /f %%i in ('findstr /i . List.txt 2^>nul') do dism /online /norestart /add-package:"C:\Windows\servicing\Packages\%%i"
pause
```
3. 如果编辑器直接复制一个文档另存到XX.cmd 即可. 如果是文本文档那么就是把后缀的.txt改成.cmd.
4. 管理员身份运行这个脚本.等他走完会退出,win+r 即可使用gpedit.msc了. 美滋滋!~

## 禁用windows10更新 
1. win+r 输入gpedit.msc.
2. “本地计算机策略”-“计算机配置”-“管理模板”-“Windows组件”-“Windows 更新”-“配置自动更新”. 点击配置自动更新设置为*禁用*

