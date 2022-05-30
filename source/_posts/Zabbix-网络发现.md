---
title: Zabbix6 网络发现
date: 2022-01-14 10:15:53
categories: Zabbix
tags: zabbix
---

Zabbix6 网络发现

### 功能

- 快速发现并添加主机

- 简单的管理

- 随着环境的改变而快速搭建系统

### 发现配置依据

- IP地址段

- 基于服务(FTP、SSH、Web、POP3、IMAP、TCP...)的

- 从Zabbix-Agent接收到的信息

- SNMP agent接收的信息

### 添加方式

1. 创建 Discovery rule
   
   ![3qoFJE.png](https://s2.loli.net/2022/05/30/8kqFzeiKIAr6fDN.png)
   
   - Name：规则名称（唯一）
   
   - Discovery by proxy： 是否由代理执行
   
   - IP range： IP地址范围

> 单个IP: 192.168.1.33
> IP段: 192.168.1-10.1-255. 范围受限于覆盖地址的总数（小于64K）。
> 子网掩码: : 192.168.4.0/24
> 支持的子网掩码:
> /16 - /30 for IPv4 addresses
> /112 - /128 for IPv6 addresses\\IP列表: 192.168.1.1-255, 192.168.2.1-100, 192.168.2.200, 192.168.4.0/24
> Zabbix 3.0.0起，此字段支持空格，表格和多行。

- Update interval: Zabbix执行规则的频率

- Checks: 发现的方式
  
  ![FTCW1e.png](https://s2.loli.net/2022/05/30/Y8R6VhGNgmFq7KS.png)

- Device uniqueness criteria：设备唯一标识

- Host name ： 主机名

- Visible name：描述名
  
  ![ohbmIZ.png](https://s2.loli.net/2022/05/30/8azsd4mVbDPU39L.png)

   这三个选项是根据，checks里面的相关类型出来的数据。基本上都有IP地址，当checks Type 选择了 SNMP或者zabbix agent时，下面选项可以提供这两种对应的数据作为选项。

  唯一标识可以通过IP(默认)，或者SNMP的取值，或者zabbix agent取值。重复的名称不会处理。

  Host name 和 Visible name选项类似。Host name肯定是要唯一的。

2. 创建 Discovery actions
   
   action配置：
   ![UnF5zk.png](https://s2.loli.net/2022/05/30/NWKO1iMeCRHqAgp.png)
   
   Name ： action 名称
   
   Conditions：action的条件
   
   Enabled: action的开关
   
   条件详情：
   
   ![mbUrYY.png](https://s2.loli.net/2022/05/30/W3ELpOmN2x5tU8e.png)
   
   Type多个方式：
   
   ![T7qbkT.png](https://s2.loli.net/2022/05/30/mVhnrkE9f2tlBUp.png)
   
   可以通过多种类型适配条件。
   
   根据选择的Type类型，下面的Operator是根据Type来配置的选项。
   
   Operator： 基本就是 等于或不等于。
   
   第三个选项是动态的，当选择 Type 是Discovery check，下面是配置 Discovery check的名称。
   
   如果Type 是Host IP，那么第三个选项就变成了Value。
   
   配置Operations：
   
   配置这个Operations 的意思是，当通过前面action条件匹配的到数据后续的操作内容。例如加入/移除设备组、关联/取消模版、发送通知等操作。
   
   ![yUO3dE.png](https://s2.loli.net/2022/05/30/DOEN57RnsWM8cmA.png)
   
   ### 验证结果
   
   我上面截图的例子中：
   
   actions 配置的内容为，通过icmp ping 发现负责此条件的设备，把这些设备分配到xxx网络摄像头的设备组里；并把主机关联的模版是icmp ping。
   
   发现之后的结果就是：
   
   在内网找到很多摄像头
   
   ![aj6697.png](https://s2.loli.net/2022/05/30/sExWG2UNDCO3ld1.png)