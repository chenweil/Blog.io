---
title: Prometheus标签处理&服务发现
date: 2019-12-03 15:14:16
categories: Prometheus
tags: Prometheus
---

### 标签处理的重要性

之前的[配置]([https://blog.51ai.vip/2019/10/09/prometueus-yml%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E/](https://blog.51ai.vip/2019/10/09/prometueus-yml%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E/)中提到了标签的处理,不过由于写的是静态的配置,标签可以自己设置或者不设置都可以.

当使用服务发现之后发现标签处理的重要性提升了更高的级别.

### 标签处理

```yaml
 - job_name: 'node'
    static_configs:
    - targets: ['172.16.23.110:9100','172.16.23.111:9100']

    metric_relable_configs:  #通过正则重命名标签
    - action: replace  #replace替换是默认动作。keep（只参加匹配标签的实例）、drop（不采集匹配正则的实例）、labelkeep\labeldrop(对标签进行过滤处理而非实例)等动作

      source_labels: ['job']  #原标签，job是默认就会产生的标签，这里job标签的值是node
      regex: (.*)  #正则匹配，这里匹配job标签内的内容，也就是node
      replacement: beijing  #替换成什么内容，如果写$1就是将正则里读取的值

      target_label: idc  #把替换内容赋值给idc标签

    - action: labeldrop  #删除标签
      regex: job  #把原job标签删除

- job_name: 'prometheus'
   static_configs:
    - targets: ['localhost:9090']
      labels:
        location: bj3

    relabel_configs:
    - action: replace
      source_labels: ['job']
      regex: (.*)
      replacement: $1
      target_label: server
```

以上两个例子都是替换标签,job:node中,删除了前job标签,下面的job新增了'server'标签内容取的job内容,但没删除job标签.

通过标签我们组成多维模型.可以对标签重命名,删除,过滤信息等.

### 服务发现

之前配置中的静态配置需要一个一些写配置,设备或者服务多的时候容易头大.

这里可以通过服务发现简化手动配置工作.

Prometheus支持多种服务发现机制,例如: consul、dns、openstack、file、kubernetes等.

这里举例file.比较简单的方式.

file机制中:需要提供文件来获取内容.文件格式为YAML 或 JSON格式.

prometheus配置:

```
scrape_configs:
  - job_name: 'prometheus'
    file_sd_configs: 
      - files: ['/usr/local/prometheus/files_sd_configs/*.yaml']  
        ##指定服务发现文件位置
        refresh_interval: 5s
        ##刷新间隔改为5秒
```

Prometheus 每5秒扫描一次指定位置的配置文件.

服务发现文件格式:

```yaml
- targets: ['localhost:9090']    # 监控目标
  labels:                        # 配置标签
    server: local           
```


