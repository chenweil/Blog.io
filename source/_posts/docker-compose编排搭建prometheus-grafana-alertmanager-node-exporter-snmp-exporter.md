---
title: docker-compose编排搭建prometheus+grafana+alertmanager+node-exporter+snmp-exporter
date: 2020-11-17 13:02:50
categories:
  - Docker
tags:
  - Prometheus
  - Grafana
  - AlertManager
  - Node-Exporter
  - Snmp-Exporter
  - Black-Exporter
---

## Docker-compose

目前集成很多Exporter，加上grafana的image-renderer，后面又加上ping-exporter，很多东西加起来发现操作一次docker 很烦啊。

科普之后感觉自己对k8s还有有些发怵的。从简单的一个入手吧，选择了docker-compose。

>>>Docker-Compose项目是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。

### 安装

安装方式看了一下，我选择直接下载bin文件方式：



 ```bash
curl -L https://get.daocloud.io/docker/compose/releases/download/1.12.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose``` 
 ```



通过 `docker-compose version`  看到版本信息算是安装完成。



### 编写docker-compose.yml



1. 目录结构：

```
├── docker-compose.yaml
├── prometheus
│   ├── rules 
│   │   └── *(rules).yaml/json
│   ├── nodes
│   │   └── *(nodes).yaml
│   ├── data
│   │   └── ... # 挂载prom的data数据
│   └── prometheus.yaml
├── alertmanager
│   ├── templates
│   │   └── *.tmpl
│   └── alertmanager.yaml
├── grafana
│   ├── data
│   │   ├── plugins #插件目录
│   │   ├── png     
│   │   └── grafana.db
│   └── grafana.ini
├── snmp(snmp_exporter)
│   └── snmp.yml
└── blackbox(balck box-exporter)
    └── blackbox.yml
```



2. 根据结构编写

   

   ```yaml
   version: "3.8"
   
   networks:
       monitor:
           driver: bridge
   
   services:
     snmp-exporter:
       image: prom/snmp-exporter:v0.19.0
       container_name: snmp
       restart: always
       expose:
         - 9116
       volumes:
         - "./snmp/snmp.yml:/etc/snmp_exporter/snmp.yml"
       networks:
         - monitor
   
     node-exporter:
       image: prom/node-exporter:v1.0.1
       container_name: node-exporter
       volumes:
         - /proc:/host/proc:ro
         - /sys:/host/sys:ro
         - /:/rootfs:ro
       command:
         - '--path.procfs=/host/proc'
         - '--path.rootfs=/rootfs'
         - '--path.sysfs=/host/sys'
         - '--collector.filesystem.ignored-mount-points=^/(sys|proc|dev|host|etc)($$|/)'
       restart: unless-stopped
       expose:
         - 9100
       networks:
         - monitor
   
     blackbox-exporter:
       image: prom/blackbox-exporter:v0.18.0
       expose:
         - 9115
       container_name: blackbox
       restart: unless-stopped
       volumes:
         - "./blackbox/:/config"
       command:
         - "--config.file=/config/blackbox.yml"
       networks:
         - monitor
   
     cadvisor:
       image: google/cadvisor:v0.33.0
       container_name: cadvisor
       volumes:
         - /:/rootfs:ro
         - /var/run:/var/run:rw
         - /sys:/sys:ro
         - /var/lib/docker:/var/lib/docker:ro
       restart: unless-stopped
       expose:
         - 8080
       networks:
         - monitor
       depends_on:
         - prometheus
   
     prometheus:
       image: prom/prometheus:v2.22.1
       container_name: prom
       restart: always
       user: "0"
       ports:
         - "9090:9090"
       volumes:
         - "./prometheus/:/prometheus"
       command: 
         - "--storage.tsdb.retention.time=60d"
         - "--config.file=/prometheus/prometheus.yml"
         - "--web.enable-lifecycle"
       networks:
         - monitor
       depends_on:
         - blackbox-exporter
         - node-exporter
         - snmp-exporter
   
     alertmanager:
       image: prom/alertmanager:v0.21.0
       restart: always
       container_name: alert
       volumes:
         - ./alertmanager/:/alert      
       command:
         - '--config.file=/alert/alertmanager.yml'
         - '--storage.path=/alert'
       ports:
         - "9093:9093"
       networks:
         - monitor
       depends_on:
         - prometheus
   
     grafana:
       image: chenwl2016/grafana-chs:0.1.4
       restart: always
       container_name: grafana
       user: "0"
       ports:
         - "3000:3000"
       environment:
         - "GF_SECURITY_ADMIN_PASSWORD=helloGf"
         - "GF_RENDERING_SERVER_URL=http://renderer:8081/render"
         - "GF_RENDERING_CALLBACK_URL=http://grafana:3000/"
         - "GF_LOG_FILTERS=rendering:debug"
       volumes:  
         - ./grafana/:/grafana
         - ./grafana/data/:/var/lib/grafana
       networks:
         - monitor
       depends_on:
         - prometheus
         - renderer
   
     renderer:
       image: grafana/grafana-image-renderer:latest
       container_name: renderer
       ports:
         - "8081:8081"
       environment:
         - "ENABLE_METRICS=true"
         - "RENDERING_MODE=clustered"
         - "RENDERING_CLUSTERING_MODE=context"
         - "RENDERING_CLUSTERING_MAX_CONCURRENCY=5"
       networks:
         - monitor
   
   ```



​      **当前的配置，主要是文件存储到宿主机上。通过容器挂载卷到容器内部。存储到宿主机目录时prometheus与grafana 会有权限问题。 配置中我是在user: "0"账户下，挂载目录 prometheus/grafana 都是 root:root。如果你不是root 需要根据uid:gid进行对应的配置。**



prometheus 目录下的nodes是file_sd的配置文件，结合实际考虑docker-compose 的具体细节。