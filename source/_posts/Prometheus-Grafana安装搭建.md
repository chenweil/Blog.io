---
title: Prometheus+Grafana安装搭建
date: 2019-08-28 16:23:05
categories: Prometheus
tags: [Prometheus,Grafana]
---

## 介绍

>   Prometheus是由SoundCloud开发的开源监控报警系统和时序列数据库(TSDB)。Prometheus使用Go语言开发，是Google BorgMon监控系统的开源版本。
>   2016年由Google发起Linux基金会旗下的原生云基金会(Cloud Native Computing Foundation), 将Prometheus纳入其下第二大开源项目。Prometheus目前在开源社区相当活跃。
>   Prometheus和Heapster(Heapster是K8S的一个子项目，用于获取集群的性能数据。)相比功能更完善、更全面。Prometheus性能也足够支撑上万台规模的集群。

> Prometheus的特点:
> 
> 1. 多维度数据模型。
> 2. 灵活的查询语言。
> 3. 不依赖分布式存储，单个服务器节点是自主的。
> 4. 通过基于HTTP的pull方式采集时序数据。
> 5. 可以通过中间网关进行时序列数据推送。
> 6. 通过服务发现或者静态配置来发现目标服务对象。
> 7. 支持多种多样的图表和界面展示，比如Grafana等。

## 架构图

![结构图](https://t1.picb.cc/uploads/2019/08/29/gjevPW.png)

## Prometheus服务大致过程：

* Prometheus 定时去目标上抓取metrics(指标)数据，每个抓取目标需要暴露一个http服务的接口给它定时抓取。Prometheus支持通过配置文件、文本文件、Zookeeper、Consul、DNS SRV Lookup等方式指定抓取目标。Prometheus采用PULL的方式进行监控，即服务器可以直接通过目标PULL数据或者间接地通过中间网关来Push数据。

* Prometheus在本地存储抓取的所有数据，并通过一定规则进行清理和整理数据，并把得到的结果存储到新的时间序列中。

* Prometheus通过PromQL和其他API可视化地展示收集的数据。Prometheus支持很多方式的图表可视化，例如Grafana、自带的Promdash以及自身提供的模版引擎等等。Prometheus还提供HTTP API的查询方式，自定义所需要的输出。

* PushGateway支持Client主动推送metrics到PushGateway，而Prometheus只是定时去Gateway上抓取数据。

* Alertmanager是独立于Prometheus的一个组件，可以支持Prometheus的查询语句，提供十分灵活的报警方式。

* Prometheus 支持通过SNMP协议获取mertics数据.通过配置job,利用snmp_export读取设备监控信息.

## 指标(Metric)类型

* **Counter**   计数器,从数据0开始累计计算. 理想状态会永远增长. 累计计算请求次数等
* **Gauges**    瞬时状态的值. 可以任意变化的数值，适用 CPU 使用率 温度等
* **Histogram** 对一段时间范围内数据进行采样，并对所有数值求和与统计数量、柱状图. 某个时间对某个度量值，分组，一段时间http相应大小，请求耗时的时间。
* **Summary**  同样产生多个指标，分别带有后缀_bucket(仅histogram)、_sum、_count

> Histogram和Summary都可以获取分位数。
> 通过Histogram获得分位数，要将直方图指标数据收集prometheus中， 然后用prometheus的查询函数[histogram_quantile()](https://prometheus.io/docs/prometheus/latest/querying/functions/#histogram_quantile)计算出来。 Summary则是在应用程序中直接计算出了分位数。
> [Histograms and summaries](https://prometheus.io/docs/practices/histograms/)中阐述了两者的区别，特别是Summary的的分位数不能被聚合。
> 注意，这个不能聚合不是说功能上不支持，而是说对分位数做聚合操作通常是没有意义的。
> [LatencyTipOfTheDay: You can’t average percentiles. Period](https://latencytipoftheday.blogspot.com/2014/06/latencytipoftheday-you-cant-average.html)中对“分位数”不能被相加平均的做了很详细的说明：分位数本身是用来切分数据的，它们的平均数没有同样的分位效果。

主要我们监控用到最上面两种,下面两种类型目前我没有接触,上面这段文字与介绍引用自[lijiaocn](https://www.lijiaocn.com/%E9%A1%B9%E7%9B%AE/2018/08/03/prometheus-usage.html#metric%E7%B1%BB%E5%9E%8B)

## 安装Prometheus

**本次搭建利用docker方式.整体搭建完成需要两个容器.暂不配置告警相关,只做监控数据**

### 前提

* 搭建位置: */home/aLong/prometheus/*

* 环境:docker19.03.1 需要指定版本请查阅官方文档.

* 系统:centos7 

### 准备工作

Prometheus的配置文件: prometheus.yml
我们建立在搭建位置的根下: `touch prometheus.yml` 
在配置文件中加入测试演示配置

```yml
global:
  scrape_interval: 15s
  scrape_timeout: 10s
  evaluation_interval: 15s
scrape_configs:
- job_name: prometheus
  honor_timestamps: true
  scrape_interval: 5s
  scrape_timeout: 3s
  metrics_path: /metrics
  scheme: http
  static_configs:
  - targets:
    - localhost:9090
```

**注意配置文件的格式为yaml,语法问题请参考[这里](https://blog.51ai.vip/2019/09/24/yaml%E8%A7%84%E5%88%99/).**

### 安装与运行

* 通过docker 启动 prometheus.
  
  ```bash
  docker run -d -p 9090:9090 \
           -v /home/along/prometheus.yml:/etc/prometheus/prometheus.yml \
            --name prometheus \
            prom/prometheus \
            --config.file=/etc/prometheus/prometheus.yml \
            --web.enable-lifecycle
  ```

**--web.enable-lifecycle 启用远程热加载配置文件**
`curl -X POST http://IP:9090/-/reload`

---

**注意这里docker热加载存在一个问题,上面挂在文件为/home/along/prometheus.yml 如果直接编辑此文件会改变文件的inode, 热加载不会成功.**

**解决办法: 我们不在挂在配置文件上做修改,复制一份,通过冲顶下方式到prometheus.yml上面 **

例如:

1. `cp /home/along/prometheus.yml /home/along/prom-edit.yml` 

2. `vi /home/along/prom-edit.ym`

3. `cat /home/along/prom-edit.ym > /home/along/prometheus.yml`

4. 此时 `curl -X POST http://IP:9090/-/reload` 会成功加载.

---

### 验证服务

访问 http://IP:9090  会进入简单webUI界面中.这是prometheus的web界面.

里面看到一些信息和监控数据.可以展示图表.

点击 Status->Target 可以看到监控的设备信息.

访问http://IP:9090/metrics 可以看到监控数据.

到这里,Prometheus已经安装成功,并监测到本机数据.

## Grafana安装

> Grafana是用于可视化大型测量数据的开源程序，它提供了强大和优雅的方式去创建、共享、浏览数据。
> Dashboard中显示了你不同metric数据源中的数据。
> Grafana最常用于因特网基础设施和应用分析，但在其他领域也有用到，比如：工业传感器、家庭自动化、过程控制等等。
> Grafana支持热插拔控制面板和可扩展的数据源，目前已经支持Graphite、InfluxDB、OpenTSDB、Elasticsearch、Prometheus等

官方：`docker run -d -p 3000:3000 --name grafana grafana/grafana`

汉化：`docker run -d -p 3000:3000 --name grafana chenwl2016/grafana-chs:0.1.5`

执行后,通过http:IP:3000 访问grafana.
缺省账号密码admin

进入后会有首页的一个引导.
添加数据源,选择prometheus.
之后可以看到默认的模板上会有数据.

通过官网查询模板插件.导入到系统中.
自定义模板选择需要的数据来展示,这里我还没玩6,暂不多说了.

## 参考

[https://grafana.com/docs/](https://grafana.com/docs/)
[https://prometheus.io/docs/introduction/overview/](https://prometheus.io/docs/introduction/overview/)
[https://www.hi-linux.com/posts/25047.html#%E5%AE%89%E8%A3%85prometheus](https://www.hi-linux.com/posts/25047.html#%E5%AE%89%E8%A3%85prometheus)
[https://www.lijiaocn.com/%E9%A1%B9%E7%9B%AE/2018/08/03/prometheus-usage.html#metric%E7%B1%BB%E5%9E%8B](https://www.lijiaocn.com/%E9%A1%B9%E7%9B%AE/2018/08/03/prometheus-usage.html#metric%E7%B1%BB%E5%9E%8B)
