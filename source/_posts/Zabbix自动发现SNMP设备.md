---
title: Zabbix自动发现SNMP设备
date: 2022-09-21 13:33:12
categories: zabbix 
tags: Zabbix
---

> Zabbix提供高效灵活的网络自动发现功能。
>    网络发现的优势：
>    * 加快部署速度
>    * 简化管理
>    * 在快速变化的环境中避免过度管理
[manual-zh](https://www.zabbix.com/documentation/6.0/zh/manual/discovery/network_discovery)


## 发现SNMP例子
*网段内存在几天设备，其中一台是SNMPv2，一台SNMPv3，通过发现将他们加入并监控*

大概流程：
1. 发现规则，通过发现规则去发现设备。
2. 发现后的动作，发现设备后做什么。
3. 验证发现结果。

### 创建发现规则
![QkadRCY9h4MlpH1](https://s2.loli.net/2022/09/21/QkadRCY9h4MlpH1.png)
`Configuration -> Discovery -> Create discovery rule `

![lUKJ59gjF6wtWzv](https://s2.loli.net/2022/09/21/lUKJ59gjF6wtWzv.png)

通过SNMPv2协议，根据oid检查。 这个oid是设备名。 community是根据设备来的。通过SNMP取设备名，host名称是IP地址，可见名称是SNMP取的设备名称。

**重点配置内容**：
* IP range  发现IP范围
* Update interval 发现周期
* Checks  使用此检查列表来执行网络发现
* Device uniqueness criteria 唯一标识
* Host name  主机名称
* Visible name 可见名称


### 创建发现动作
![aE74jtgmZpHCJkG](https://s2.loli.net/2022/09/21/aE74jtgmZpHCJkG.png)

`Configuration -> Actions -> Discovery actions`


![k2zJWCjy13bVt7Z](https://s2.loli.net/2022/09/21/k2zJWCjy13bVt7Z.png)

Action 选项中：
主要配置Conditions内容：
我写了两个条件，因为我发现写了v2，v3版本的协议。所以有两个条件。如果都是v2就写一个就ok。简单来讲满足我demo。

Operations 选项：
![rEc2Y5apTeO6JK3](https://s2.loli.net/2022/09/21/rEc2Y5apTeO6JK3.png)

1. 发现的设备添加， Add host 。
2. 加入设备组， Add to host groups：组名。
3. 关联模版，Link to templates：模版名。
4. 开启Host。

通过这个配置后，发现的主机会根据这个配置进行操作。

### 最终结果
![iPfZF8gN7K4yOjp](https://s2.loli.net/2022/09/21/iPfZF8gN7K4yOjp.png)
通过Monitoring -> Discovery 能看到发现信息。

![FV39yemtZ45uYgb](https://s2.loli.net/2022/09/21/FV39yemtZ45uYgb.png)

并且监控的主机配置参数都正常，可以取到模版配置的数据。

简单的demo完成。
完结
祝好 ～