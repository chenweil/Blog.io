---
title: S5720 SNMP v3配置
date: 2021-11-16 16:38:35
categories:
  - NetWork
  - SNMP
tags: NetWork
---


##  S5720 SNMP v3配置
### 系统视图
`system-view`
### SNMP服务
`snmp-agent`
### 管理端口(默认161)
`snmp-agent udp-port *port-num*`
### 配置版本(默认v3)
`snmp-agent sys-info version *v3*`
### 配置用户组
`snmp-agent group v3 *group-name* {authentication | privacy | noauthentication}`

三种认证加密方式

### 配置v3用户
`snmp-agent usm-user v3 *user-name* [ group *group-name*] `

### 配置用户认证密码
`snmp-agent usm-user v3 *user-name* authentication-mode { md5 | sha } [ cipher *password* ]`

### 配置加密密码
`snmp-agent usm-user v3 *user-name* privacy-mode { des56 | aes128 |aes192 | aes256 | 3des } [ cipher *password* ]`
