---
title: Prometheus-snmp_export部署
date: 2019-08-29 10:06:01
categories: Prometheus
tags: SNMP
---

## SNMP

SNMP(simple network management protocol)是因特网架构委员会IAB定义的一个应用层协议。SNMP广泛用于管理和监控网络设备，大多数专业的网络设备都有SNMP agent代理，这些代理被激活和配置后用于和SNMP管理 NMS(network management system)网络管理系统通信。

## 目的

通过snmp_export,获取设备信息.

## 准备

系统: centos7,docker19.
之前已经安装好 Prometheus

此处目标设备是华为交换机 s2700

## 部署snmp_expoer

snmp.yml 配置文件不是自己定义的,是通过注册生成或下载的.这里我通过github下载配置文件.

[snmp.yml](https://github.com/prometheus/snmp_exporter/blob/master/snmp.yml)

* 配置snmp_export 配置文件 snmp.yml

```yml
version: 2
auth:
  community: **交换机设置的团体名**
```

查找到if_mib 再此段结尾中加入 上面的配置(大概行数6199).

![demo](https://t1.picb.cc/uploads/2019/08/29/gjuSAc.png)

### 部署snmp_expor

```bash
docker run -d --restart always \
-v /home/along/snmp.yml:/etc/snmp_exporter/snmp.yml \
-p 9116:9116 --name snmp-exporter prom/snmp-exporter \ --config.file="/etc/snmp_exporter/snmp.yml"
```

### 配置华为s2700交换机

自行查阅文档.懒得写了.

### 验证服务

访问 http://IP:9116/metrics 能返回数据,snmp_export服务正常.

测试是否能获取到目标设备的数据:
访问 http://IP:9116/snmp?target=DEVIP
能获取到数据,配置成功.

**注意防火墙 把需要的端口加入规则中,不然访问不到排查绕弯路**

### 配置promthues

修改 promthues.yml文件. 添加一个新的job.

```yml
- job_name: snmp
  honor_timestamps: true
  params:
    module:
    - if_mib
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /snmp
  scheme: http
  static_configs:
  - targets:
    - 172.16.23.253
    labels:
      tag: huawei-switch-s2700
  relabel_configs:
  - source_labels: [__address__]
    separator: ;
    regex: (.*)
    target_label: __param_target
    replacement: $1
    action: replace
  - source_labels: [__param_target]
    separator: ;
    regex: (.*)
    target_label: instance
    replacement: $1
    action: replace
  - separator: ;
    regex: (.*)
    target_label: __address__
    replacement: 172.16.23.12:9116
    action: replace
```

之前部署prometheus 有一个参数是为了热加载配置的.
这里需要reload一下配置,`curl -X POST http://IP:9090/-/reload`,如果你没有就重启服务吧.

### 验证 Prometheus配置

访问 http://IP:9090/
点击 Status->Target 可以看到监控的节点,之前我们是有一个,现在是两个节点了.

![](https://t1.picb.cc/uploads/2019/08/29/gjCvPF.png)

有数据之后,就可以在grafana中展示设备的数据了.

## 参考

https://github.com/prometheus/snmp_exporter

https://prometheus.io/docs/instrumenting/exporters/

http://owelinux.github.io/owelinux.github.io/2018/07/25/article8-linux-prometheus/


