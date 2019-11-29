---
title: M3DB笔记
date: 2019-11-29 17:41:43
categories: m3db
tags: m3db
---



## M3DB笔记

前阵子研究prometheus,初期没有考虑存储问题.本地默认存储30天数据.

监控已经折腾完毕,现在需要来处理存储的方案.

通过互联网的科普,发现目前有两个方案可以解决这个问题.

1. thanos

2. M3DB 
   
   thanos是需要存储云端数据(本地存储官方不推荐).不符合我们的考虑范围内.那就来学习M3DB了.

### 简介

> M3可以在较长的保留时间内可靠地存储大规模指标。为了向更广泛的社区中的其他人提供这些好处，我们决定开放M3平台作为Prometheus的远程存储后端，Prometheus是一种流行的监控和警报解决方案。正如其文档所述，Prometheus的可扩展性和耐用性受到单个节点的限制。 M3平台旨在为Prometheus指标提供安全，可扩展且可配置的多租户的存储。
> 
> M3于2015年发布，目前拥有超过66亿个时间序列。 M3每秒聚合5亿个指标，并在全球范围内（使用M3DB）每秒持续存储2000万个度量指标，批量写入将每个指标持久保存到区域中的三个副本。它还允许工程师编写度量策略，告诉M3以更短或更长的保留时间（两天，一个月，六个月，一年，三年，五年等）以特定的粒度（一秒，十秒，一分钟，十分钟等）。这允许工程师和数据科学家使用与定义的存储策略匹配的度量标签（标签），在精细和粗粒度范围内智能地存储不同保留的时间序列。例如，工程师可以选择存储“应用程序”标记为“mobile_api”且“端点”标记为“注册”的所有度量标准，这些标记在10秒粒度下为30天，在一小时粒度下为5年。



### 相关组件

> #### M3 Coordinator
> 
> M3 Coordinator是一种服务，用于协调上游系统（如Prometheus和M3DB）之间的读写操作。它还提供了管理API，用于设置和配置M3的不同部分。它是用户可以部署以访问M3DB的优势的桥梁，例如长期存储和与其他监控系统（如Prometheus）的多DC设置。
> 
> #### M3DB
> 
> M3DB是一个分布式时间序列数据库，提供可扩展存储和时间序列的反向索引。它经过优化，具有成本效益和可靠的实时和长期保留指标存储和索引
> 
> #### M3 Query
> 
> M3 Query是一种服务，它包含一个分布式查询引擎，用于查询实时和历史指标，支持多种不同的查询语言。它旨在支持低延迟实时查询和可能需要更长时间执行的查询，聚合更大的数据集，用于分析用例
> 
> #### M3 Aggregator
> 
> M3 Aggregator是一种作为专用度量聚合器运行的服务，它基于存储在etcd中的动态规则提供基于流的下采样。它使用领导者选举和聚合窗口跟踪，利用etcd来管理此状态，从而可靠地为低采样度量标准发送至少一次聚合到长期存储。这提供了成本有效且可靠的下采样和汇总指标。这些功能也存在于M3协调器中，但专用聚合器是分片和复制的，而M3协调器则不需要并且需要谨慎部署和以高可用性方式运行。还有一些工作要使用户更容易访问聚合器，而无需他们编写自己的兼容生产者和消费者。



### 入门

![](https://t1.picb.cc/uploads/2019/11/29/kIj7yu.png)

上面的组件通俗讲:

*prometheus* 需要通过*M3 Coordinator*来协调存储与查询到M3DB,*prometheus*本地存储数据时间与这个没关系.

上面没有提到一个名字etcd服务.此服务推断拓扑\配置功能. 如果db嵌入此服务就称为种子节点SeedNode.

----

官方提供了一个镜象,里面包含 M3 Coordinator + SeedNode.

拉取镜象:`docker pull quay.io/m3db/m3dbnode:latest`

启动名为m3db容器:`docker run -d -p 7201:7201 -p 7203:7203 -p 9003:9003 --name m3db -v $(pwd)/m3db_data:/var/lib/m3db quay.io/m3db/m3dbnode:latest`

该容器的端口为7201（用于管理集群拓扑），端口为7203，Prometheus用来刮除M3DB和M3Coordinator生成的度量，端口9003（用于读取和写入度量）已公开。



这时候m3db已经启动,需要创建一个初始命名空间.官方镜象默认是type:local,namespace:default. 这个可以进入容器查看到配置位置`/etc/d3dbnode/m3dbnode.yml`.如果我们创建其他命名空间需要在m3coordinator中配置好.

`curl -X POST http://localhost:7201/api/v1/database/create -d '{
  "type": "local",
  "namespaceName": "default",
  "retentionTime": "24h"
}'`

这里配置的参数除了type与namespaceName,还有一个保留时间.超过时间就被清除了.我们测试设置24小时可以了,如果我是监测自己的设备,不是生产的情况其实偷懒可以用这个单机存储设置一个较长时间.例如:1y(看你了兄嘚).

设置完稍等几分钟就可以使用了.

通过接口可以查看: `curl http://localhost:7201/api/v1/placement`

看到很多`AVAILABLE` 就是ok了.

访问`localhost:7201/api/v1/openapi` 可以看到官方提供的接口文档.



### 配置远程读写

上面的库已经ok了,现在配置一下Prometheus.配置很简单只有两条.一条写,一条读.

```yaml
remote_read:
  - url: "http://localhost:7201/api/v1/prom/remote/read"
    read_recent: true

remote_write:
  - url: "http://localhost:7201/api/v1/prom/remote/write"
```

### 监控指标

我们这个用的是官方提供的镜象,嵌入式M3Coordinator的M3DB.所以按照下面这种方式配置.

```yaml
- job_name: 'm3'
  static_configs:
    - targets: ['<HOST_NAME>:7203']
```