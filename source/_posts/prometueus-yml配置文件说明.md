---
title: prometueus.yml配置文件说明
date: 2019-10-09 15:20:58
categories: Promethes
tags: Prometheus
---

## 整体配置
prometueus.yml 配置文件注解与说明

```yaml
global:                       # 全局配置
  scrape_interval: 15s        # 默认值为 15s，用于设置每次数据收集的间隔
  scrape_timeout: 10s         # 默认10s,收集超时时间
  evaluation_interval: 15s    # 记录规则/告警的执行周期 默认1m
  external_labels:            # 所有时间序列和警告与外部通信时用的外部标签
    monitor: 'ctmonitor'

rule_files:                   # 指定告警规则文件&记录文件
  - "/usr/local/prometheus/rules.yml"

alerting:                     # 告警管理配置
  alert_relable_configs:      # 修改告警内容
  - 
  alertmanagers:              # 告警管理起配置
  - static_configs:           # 静态配置
    - targets:                # 警告器地址
      - 172.16.23.12:9093

# 用于配置 scrape 的 endpoint  配置需要 scrape 的 targets 以及相应的参数
# 抓取(pull)，即监控目标配置。默认只有主机本身的监控配置 


scrape_configs:               # 抓取
- job_name: prometheus        # 默认情况下分配给刮削度量的作业名称。
  scrape_interval: 5s         # 从这项工作中获取目标的频率。
  scrape_timeout: 3s          # 每次获取超时时间
  honor_timestamps: true      # 默认false, 在获取时是否使用当前的时间戳
  metrics_path: /metrics      # 从目标获取度量的http资源路径。
  scheme: http                # 
  static_configs:             # 为此作业标记的静态配置目标的列表。
  - targets:                  # 目监控标
    - 172.16.23.12:9090       # 设备地址+端口

- job_name: 'snmp-10.0.0.1'  
  scrape_interval: 30s
  scrape_timeout: 20s
  static_configs:
    - targets:
      - 10.0.0.1                  # SNMP设备,端口默认5060
  metrics_path: /snmp
  params:
    module: [if_mib]
  relabel_configs:                # 重定义标签
  - source_labels: [__address__]  # 需要修改的标签
    target_label: __param_target  # 改成的标签
  - source_labels: [__param_target]
    target_label: instance
  - target_label: __address__
    replacement: 172.16.23.12:9117
```

## 各部分详解

### scrape_configs
监控配置

<scrape_configs>
配置采集目标
1、根据配置的任务（job）以http/s周期性的收刮（scrape/pull）
2、指定目标（target）上的指标（metric）。目标（target）
3、可以以静态方式或者自动发现方式指定。Prometheus将收刮（scrape）的指标（metric）保存在本地或者远程存储上。

<scrape_config>部分指定一组描述如何刮除它们的目标和参数。 在一般情况下，一个scrape配置指定单个作业。 在高级配置中，这可能会改变。
目标可以通过<static_configs>参数静态配置，也可以使用其中一种支持的服务发现机制动态发现。
此外，<relabel_configs>允许在抓取之前对任何目标及其标签进行高级修改。
其中<job_name>在所有scrape配置中必须是唯一的。

```yaml
# 默认分配给已抓取指标的job名称。
job_name: <job_name>

# 从job中抓取目标的频率.
[ scrape_interval: <duration> | default = <global_config.scrape_interval> ]

# 抓取此job时，每次抓取超时时间.
[ scrape_timeout: <duration> | default = <global_config.scrape_timeout> ]

# 从目标获取指标的HTTP资源路径.
[ metrics_path: <path> | default = /metrics ]

# honor_labels控制Prometheus如何处理已经存在于已抓取数据中的标签与Prometheus将附加服务器端的标签之间的冲突（"job"和"instance"标签，手动配置的目标标签以及服务发现实现生成的标签）。
# 
# 如果honor_labels设置为"true"，则通过保留已抓取数据的标签值并忽略冲突的服务器端标签来解决标签冲突。
#
# 如果honor_labels设置为"false"，则通过将已抓取数据中的冲突标签重命名为"exported_ <original-label>"（例如"exported_instance"，"exported_job"）然后附加服务器端标签来解决标签冲突。 这对于联合等用例很有用，其中应保留目标中指定的所有标签。
# 
# 请注意，任何全局配置的"external_labels"都不受此设置的影响。 在与外部系统通信时，它们始终仅在时间序列尚未具有给定标签时应用，否则将被忽略。
# 
[ honor_labels: <boolean> | default = false ]

# 配置用于请求的协议方案.
[ scheme: <scheme> | default = http ]

# 可选的HTTP URL参数.
params:
  [ <string>: [<string>, ...] ]

# 使用配置的用户名和密码在每个scrape请求上设置`Authorization`标头。 password和password_file是互斥的。
basic_auth:
  [ username: <string> ]
  [ password: <secret> ]
  [ password_file: <string> ]

# 使用配置的承载令牌在每个scrape请求上设置`Authorization`标头。 它`bearer_token_file`和是互斥的。
[ bearer_token: <secret> ]

# 使用配置的承载令牌在每个scrape请求上设置`Authorization`标头。 它`bearer_token`和是互斥的。
[ bearer_token_file: /path/to/bearer/token/file ]

# 配置scrape请求的TLS设置.
tls_config:
  [ <tls_config> ]

# 可选的代理URL.
[ proxy_url: <string> ]

# Azure服务发现配置列表.
azure_sd_configs:
  [ - <azure_sd_config> ... ]

# Consul服务发现配置列表.
consul_sd_configs:
  [ - <consul_sd_config> ... ]

# DNS服务发现配置列表。
dns_sd_configs:
  [ - <dns_sd_config> ... ]

# EC2服务发现配置列表。
ec2_sd_configs:
  [ - <ec2_sd_config> ... ]

# OpenStack服务发现配置列表。
openstack_sd_configs:
  [ - <openstack_sd_config> ... ]

# 文件服务发现配置列表。
file_sd_configs:
  [ - <file_sd_config> ... ]

# GCE服务发现配置列表。
gce_sd_configs:
  [ - <gce_sd_config> ... ]

# Kubernetes服务发现配置列表。
kubernetes_sd_configs:
  [ - <kubernetes_sd_config> ... ]

# Marathon服务发现配置列表。
marathon_sd_configs:
  [ - <marathon_sd_config> ... ]

# AirBnB的神经服务发现配置列表。
nerve_sd_configs:
  [ - <nerve_sd_config> ... ]

# Zookeeper Serverset服务发现配置列表。
serverset_sd_configs:
  [ - <serverset_sd_config> ... ]

# Triton服务发现配置列表。
triton_sd_configs:
  [ - <triton_sd_config> ... ]

# 此job的标记静态配置目标列表。
static_configs:
  [ - <static_config> ... ]

# 目标重新标记配置列表。
relabel_configs:
  [ - <relabel_config> ... ]

# 度量标准重新配置列表。
metric_relabel_configs:
  [ - <relabel_config> ... ]

# 对每个将被接受的样本数量的每次抓取限制。
# 如果在度量重新标记后存在超过此数量的样本，则整个抓取将被视为失败。 0表示没有限制。
[ sample_limit: <int> | default = 0 ]
```


### rule_files
记录规则,编写的记录规则是定义一些常用计算规则.这些规则会存储到数据中.

```yaml
rule_files:
  [ - <filepath_glob> ... ]
```

### alerting

Alertmanager相关配置

```yaml
alerting:
  alert_relabel_configs:
    [ - <relabel_config> ... ]
  alertmanagers:
    [ - <alertmanager_config> ... ]
```

<alertmanager_config>
alertmanager_config部分指定Prometheus服务器向其发送警报的Alertmanager实例。 它还提供参数以配置如何与这些Alertmanagers进行通信。
Alertmanagers可以通过static_configs参数静态配置，也可以使用其中一种支持的服务发现机制动态发现。
此外，relabel_configs允许从发现的实体中选择Alertmanagers，并对使用的API路径提供高级修改，该路径通过__alerts_path__标签公开。

```yaml
# 推送警报时按目标Alertmanager超时。
[ timeout: <duration> | default = 10s ]

# 将推送HTTP路径警报的前缀。
[ path_prefix: <path> | default = / ]

# 配置用于请求的协议方案。
[ scheme: <scheme> | default = http ]

# 使用配置的用户名和密码在每个请求上设置`Authorization`标头。 password和password_file是互斥的。
basic_auth:
  [ username: <string> ]
  [ password: <string> ]
  [ password_file: <string> ]

# 使用配置的承载令牌在每个请求上设置“Authorization”标头。 它与`bearer_token_file`互斥。
[ bearer_token: <string> ]

# 使用配置的承载令牌在每个请求上设置“Authorization”标头。 它与`bearer_token`互斥。
[ bearer_token_file: /path/to/bearer/token/file ]

# 配置scrape请求的TLS设置。
tls_config:
  [ <tls_config> ]

# 可选的代理URL。
[ proxy_url: <string> ]

# Azure服务发现配置列表。
azure_sd_configs:
  [ - <azure_sd_config> ... ]

# Consul服务发现配置列表。
consul_sd_configs:
  [ - <consul_sd_config> ... ]

# DNS服务发现配置列表。
dns_sd_configs:
  [ - <dns_sd_config> ... ]

# ECS服务发现配置列表。
ec2_sd_configs:
  [ - <ec2_sd_config> ... ]

# 文件服务发现配置列表。
file_sd_configs:
  [ - <file_sd_config> ... ]

# GCE服务发现配置列表。
gce_sd_configs:
  [ - <gce_sd_config> ... ]

# K8S服务发现配置列表。
kubernetes_sd_configs:
  [ - <kubernetes_sd_config> ... ]

# Marathon服务发现配置列表。
marathon_sd_configs:
  [ - <marathon_sd_config> ... ]

# AirBnB's Nerve 服务发现配置列表。
nerve_sd_configs:
  [ - <nerve_sd_config> ... ]

# Zookepper服务发现配置列表。
serverset_sd_configs:
  [ - <serverset_sd_config> ... ]

# Triton服务发现配置列表。
triton_sd_configs:
  [ - <triton_sd_config> ... ]

# 标记为静态配置的Alertmanagers列表。
static_configs:
  [ - <static_config> ... ]

# Alertmanager重新配置列表。
relabel_configs:
  [ - <relabel_config> ... ]
```

### remote_write
云端写入数据
<remote_write>
write_relabel_configs是在将样本发送到远程端点之前应用于样本的重新标记。 在外部标签之后应用写入重新标记。 这可用于限制发送的样本。

有一个如何使用此功能的小型演示。
```yaml
# 要发送样本的端点的URL.
url: <string>

# 对远程写端点的请求超时。
[ remote_timeout: <duration> | default = 30s ]

# 远程写入重新标记配置列表。
write_relabel_configs:
  [ - <relabel_config> ... ]

# 使用配置的用户名和密码在每个远程写请求上设置`Authorization`标头.password和password_file是互斥的。
basic_auth:
  [ username: <string> ]
  [ password: <string> ]
  [ password_file: <string> ]

# 使用配置的承载令牌在每个远程写请求上设置`Authorization`头。 它与`bearer_token_file`互斥。
[ bearer_token: <string> ]

# 使用配置的承载令牌在每个远程写请求上设置`Authorization`头。 它与`bearer_token`互斥。
[ bearer_token_file: /path/to/bearer/token/file ]

# 配置远程写入请求的TLS设置。
tls_config:
  [ <tls_config> ]

# 可选的代理URL。
[ proxy_url: <string> ]

# 配置用于写入远程存储的队列。
queue_config:
  # 在我们开始删除之前每个分片缓冲的样本数。
  [ capacity: <int> | default = 10000 ]
  # 最大分片数，即并发数。
  [ max_shards: <int> | default = 1000 ]
  # 最小分片数，即并发数。
  [ min_shards: <int> | default = 1 ]
  # 每次发送的最大样本数。
  [ max_samples_per_send: <int> | default = 100]
  # 样本在缓冲区中等待的最长时间。
  [ batch_send_deadline: <duration> | default = 5s ]
  # 在可恢复错误上重试批处理的最大次数。
  [ max_retries: <int> | default = 3 ]
  # 初始重试延迟。 每次重试都会加倍。
  [ min_backoff: <duration> | default = 30ms ]
  # 最大重试延迟。
  [ max_backoff: <duration> | default = 100ms ]
```


### remote_read
云端读取数据
<remote_read>
```yaml
# 要发送样本的端点的URL.
url: <string>

# 可选的匹配器列表，必须存在于选择器中以查询远程读取端点。
required_matchers:
  [ <labelname>: <labelvalue> ... ]

# 对远程读取端点的请求超时。
[ remote_timeout: <duration> | default = 1m ]

# 本地存储应该有完整的数据。
[ read_recent: <boolean> | default = false ]

# 使用配置的用户名和密码在每个远程写请求上设置`Authorization`标头.password和password_file是互斥的。
basic_auth:
  [ username: <string> ]
  [ password: <string> ]
  [ password_file: <string> ]

# 使用配置的承载令牌在每个远程写请求上设置`Authorization`头。 它与`bearer_toke_filen`互斥。
[ bearer_token: <string> ]

# 使用配置的承载令牌在每个远程写请求上设置`Authorization`头。 它与`bearer_token`互斥。
[ bearer_token_file: /path/to/bearer/token/file ]

# 配置远程写入请求的TLS设置。
tls_config:
  [ <tls_config> ]

# 可选的代理URL。
[ proxy_url: <string> ]
```

### relabel_configs
用来重新打标记,修改标签.

<relabel_configs>
Prometheus 重新标签
允许在采集之前对任何目标及其标签进行修改

　　• 重命名标签名
　　• 删除标签
　　• 过滤目标

action：重新标签动作

* replace：默认，通过regex匹配source_label的值，使用replacement来引用表达式匹配的分组
* keep：删除regex与连接不匹配的目标 source_labels
* drop：删除regex与连接匹配的目标 source_labels
* labeldrop：删除regex匹配的标签
* labelkeep：删除regex不匹配的标签
* hashmod：设置target_label为modulus连接的哈希值source_labels
* labelmap：匹配regex所有标签名称。然后复制匹配标签的值进行分组，replacement分组引用（${1},${2},…）替代

```yaml
relable_configs:
# 源标签从现有标签中选择值。 它们的内容使用已配置的分隔符进行连接，并与已配置的正则表达式进行匹配，以进行替换，保留和删除操作。
[ source_labels: '[' <labelname> [, ...] ']' ]

# 分隔符放置在连接的源标签值之间。
[ separator: <string> | default = ; ]

# 在替换操作中将结果值写入的标签。
# 替换操作是强制性的。 正则表达式捕获组可用。
[ target_label: <labelname> ]

# 与提取的值匹配的正则表达式。
[ regex: <regex> | default = (.*) ]

# 采用源标签值的散列的模数。
[ modulus: <uint64> ]

# 如果正则表达式匹配，则执行正则表达式替换的替换值。 正则表达式捕获组可用。
[ replacement: <string> | default = $1 ]

# 基于正则表达式匹配执行的操作。
[ action: <relabel_action> | default = replace ]
```


