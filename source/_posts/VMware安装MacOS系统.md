---
title: VMware安装MacOS系统
date: 2019-08-12 11:29:38
categories: note
tags: vm
---

## 虚拟机安装 macOS
#### 准备工作:
* VM关闭进程,利用macOS Unlocker修改VM使其能安装macOS系统, 执行程序 win-install.cmd 使用管理员权限运行脚本.
* 准备好macOS镜象.
* 利用VM创建虚机,系统类型选择macOS,版本号选择与下载的镜象版本相同.

### 装系统:
* 启动虚拟机,并通过cdrom加载镜象.
* 首次安装需要先利用系统内硬盘工具格式化硬盘,之后利用安装工具进行系统安装.


### 异常问题:
* 开启虚拟机弹出错误:vcpu-0 错误.

修改虚机镜象文件.vmx   在smc.present = “TRUE”下面插入一行代码:    smc.version = 0

* 不能正常登陆APPID

需要修改虚拟机,利用Chameleon Wizard 伪造设备信息. 保存 生成的信息 去修改镜象所在文件下的.vmx  

修改 board-id.reflectHost = "TRUE" 为FALSE,并在下面插入需要的伪造设备信息例子:

board-id = "Mac-94245B3640C91C81"
hw.model.reflectHost = "FALSE"
hw.model = "MacBook Pro"
serialNumber.reflectHost = "FALSE"
serialNumber = "C02JJ8B3DH2G"
smbios.reflectHost = "FALSE"

这段信息中的board-id 与  serialNumber 不要与例子的相同.其他可以参考.最后保存,重启.

重启之后尝试是否可以登陆市场.