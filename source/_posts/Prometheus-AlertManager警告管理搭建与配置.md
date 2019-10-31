---
title: Prometheus-AlertManager警告管理搭建与配置
date: 2019-10-19 09:40:15
categories: Prometheus
tags:
- Prometheus
- AlertManager
---

## AlertManager
>AlertManager处理由客户端应用程序（如Prometheus服务器）发送的警报。
它负责重复数据消除、分组，并将它们路由到正确的接收器集成（如电子邮件、PagerDuty或OpsGenie）。
它还负责消除和抑制警报。

通过翻译官方文档可以了解到,AlertManager是负责为Prometheus(本身不会发送警报)发送警报的工具.
AlertManager不是简单发送警报,可以消除重复警报,分组,抑制警报功能.并支持多接收器.

Prometheus->触发定义的警报规则->AlertManager->发送警报到指定通知渠道.

为了能让Prometheus发送警报,我们需要:
1. 搭建AlertManager服务.
2. 定义AlertManager通知配置.
3. 定义Prometheus警报规则并引入.
4. 测试警报.
5. 定义通知模板.

## 定义AlertManager通知配置
```yaml
  smtp_smarthost: 'smtp.163.com:25'               # 邮箱smtp服务器代理
  smtp_from: 'shitu-0071@163.com'                 # 发送邮箱名称
  resolve_timeout: 5m                             # 处理超时时间，默认为5min
  smtp_auth_username: 'shitu-0071@163.com'        # 邮箱帐户
  smtp_auth_password: '******'                    # 邮箱授权码(注意是授权码,不知道自己查一下)
  wechat_api_url: 'https://qyapi.weixin.qq.com/cgi-bin/'   # 企业微信地址

# 定义模板信心
templates:
  - 'template/*.tmpl'

# 定义路由树信息
route:   
  group_by: ['alertname', 'cluster', 'service']        # 报警分组依据 根据标签分组,
                                                       # 同标签警告会在作为一组警报发送
  group_wait: 10s                                      # 最初即第一次等待多久时间发送一组警报的通知
  group_interval: 10s                                  # 在发送新警报前的等待时间
  repeat_interval: 1m                                  # 发送重复警报的周期
  (对于email配置中，此项不可以设置过低，否则将会由于邮件发送太多频繁，被smtp服务器拒绝)
  receiver: 'email'                                    # 发送警报的接收者的名称，
                                                       # 以下receivers name的名称
  routes:
  - match:                      # 普通匹配
      serverity: critical       # 警告级别critical
    receiver: email             # 通过邮件发送
  - match_re:                   # 正则匹配
      severity: ^(warning)$     # 匹配警告级别为warning的
    receiver: wechat            # 通过微信发送告警
  - receiver: along             # 定义接收者
    match:                      # 匹配
      severity: test            # 等级为test
 

# 定义警报接收者信息
receivers:
  - name: 'email'               # 警报
    email_configs:              # 邮箱配置
    - to: '******@163.com'      # 接收警报的email配置
      html: '{{ template "test.html" . }}'      # 设定邮箱的内容模板
      headers: { Subject: "[WARN] 报警邮件"}     # 接收邮件的标题
    webhook_configs: # webhook配置
    - url: 'http://127.0.0.1:5001'
    send_resolved: true

  - name: 'wechat'
    wechat_configs: # 企业微信报警配置
    - send_resolved: true
      to_party: '1' # 接收组的id
      agent_id: '1000002' # (企业微信-->自定应用-->AgentId)
      corp_id: '******' # 企业信息(我的企业-->CorpId[在底部])
      api_secret: '******' # 企业微信(企业微信-->自定应用-->Secret)
      message: '{{ template "test_wechat.html" . }}' # 发送消息模板的设定

# 一个inhibition规则是在与另一组匹配器匹配的警报存在的条件下，
# 使匹配一组匹配器的警报失效的规则。两个警报必须具有一组相同的标签。 
inhibit_rules: 
  - source_match: 
     severity: 'critical' 
    target_match: 
     severity: 'critical' 
    equal: ['instance']
    # 已经发送的告警通知匹配到target_match和target_match_re规则，
    # 再有新的告警规则如果满足source_match
    # 或者定义的匹配规则，并且已发送的告警与新产生的告警中equal定义的标签完全相同，
    # 则启动抑制机制，新的告警不会发送。
```



***
以下是官方文档配置翻译的文档.供参考具体详细的配置介绍.不细看先略过到下个步骤.
***
<route>

路由块定义路由树中的节点及其子节点。如果未设置，则其可选配置参数将从其父节点继承。
每个警报都在配置的顶级路由处进入路由树，该路由树必须与所有警报匹配（即没有任何配置的匹配器）。
然后它遍历子节点。如果continue设置为false，则在第一个匹配的子级之后停止。
如果匹配节点上的continue为true，则警报将继续与后续同级节点匹配。
如果警报与节点的任何子节点不匹配（没有匹配的子节点，或者不存在），则基于当前节点的配置参数来处理警报。

```yaml
[ receiver: <string> ]
# 用于将传入警报分组在一起的标签。 
# 例如，针对cluster = A和alertname = LatencyHigh的多个警报将被分为一个组。
# 要按所有可能的标签进行汇总，请使用特殊值'...'作为唯一的标签名称，例如：group_by：['...']
# 这样可以有效地完全禁用聚合，并按原样传递所有警报。 
# 除非您的警报量非常低或上游通知系统执行自己的分组，否则这不太可能是您想要的。
[ group_by: '[' <labelname>, ... ']' ]

# 警报是否应继续匹配后续的同级节点
[ continue: <boolean> | default = false ]

# 警报必须满足的一组相等匹配器才能匹配节点。
match:
  [ <labelname>: <labelvalue>, ... ]

# 警报必须满足以匹配节点的一组正则表达式匹配器。
match_re:
  [ <labelname>: <regex>, ... ]

# 最初等待为一组警报发送通知的时间。 
# 允许等待禁止警报到达或为同一组收集更多初始警报。 （通常〜0秒到几分钟。）
[ group_wait: <duration> | default = 30s ]

# 发送有关新警报的通知之前要等待的时间，
# 该通知将添加到已为其发送了初始通知的一组警报中。 （通常〜5m或更多。）
[ group_interval: <duration> | default = 5m ]

# 如果已成功发送警报，则等待多长时间才能再次发送通知。 （通常〜3h或更长时间）。
[ repeat_interval: <duration> | default = 4h ]

# 零个或多个子路由。
routes:
  [ - <route> ... ]
```

<inhibit_rule>
当与另一组匹配器匹配的警报（源）存在时，禁止规则静默匹配匹配器集合的警报（目标）。
对于相等列表中的标签名称，目标和源警报必须具有相同的标签值。
从语义上讲，缺失的标签和空值的标签是一样的。
因此，如果源警报和目标警报中均缺少equal中列出的所有标签名称，则将应用抑制规则。
为了防止警报抑制自身，与规则的目标端和源端都匹配的警报不能被相同的警报（包括自身）抑制。
但是，我们建议选择目标和源匹配器时，警报不会同时匹配双方。这很容易推理，也不会引发这种特殊情况。

```yaml
# 必须在警报中完成的匹配项才能被禁用
target_match:
  [ <labelname>: <labelvalue>, ... ]
target_match_re:
  [ <labelname>: <regex>, ... ]

# 匹配器必须具有一个或多个警报才能使抑制生效
source_match:
  [ <labelname>: <labelvalue>, ... ]
source_match_re:
  [ <labelname>: <regex>, ... ]

# 必须在源警报和目标警报中具有相等值的标签才能使抑制生效
[ equal: '[' <labelname>, ... ']' ]
```

<receiver>

```yaml
# 接收者的唯一名称
name: <string>

# 多个通知集成的配置(这里只列出三个其他请看官网)
email_configs:
  [ - <email_config>, ... ]
webhook_configs:
  [ - <webhook_config>, ... ]
wechat_configs:
  [ - <wechat_config>, ... ]
```

<email_config>
```yaml
# 是否通知已解决的警报
[ send_resolved: <boolean> | default = false ]

# 要向其发送通知的电子邮件地址
to: <tmpl_string>

# 发件人地址
[ from: <tmpl_string> | default = global.smtp_from ]

# 发送电子邮件的SMTP主机
[ smarthost: <string> | default = global.smtp_smarthost ]

# 要标识到SMTP服务器的主机名
[ hello: <string> | default = global.smtp_hello ]

# SMTP身份验证信息.
[ auth_username: <string> | default = global.smtp_auth_username ]
[ auth_password: <secret> | default = global.smtp_auth_password ]
[ auth_secret: <secret> | default = global.smtp_auth_secret ]
[ auth_identity: <string> | default = global.smtp_auth_identity ]

# SMTP TLS要求
[ require_tls: <bool> | default = global.smtp_require_tls ]

# TLS配置
tls_config:
  [ <tls_config> ]

# 电子邮件通知的HTML正文
[ html: <tmpl_string> | default = '{{ template "email.default.html" . }}' ]
# 电子邮件通知的正文
[ text: <tmpl_string> ]

# 更多标题电子邮件标题键/值对,重写通知实现以前设置的任何头
# 先前由通知实现设置的。
[ headers: { <string>: <tmpl_string>, ... } ]
```

<webhook_config>
```yaml
# 是否通知已解决的警报
[ send_resolved: <boolean> | default = true ]

# 要向其发送HTTP POST请求的终结点
url: <string>

# http客户端的配置
[ http_config: <http_config> | default = global.http_config ]
```
微信json 格式

```json
{
  "version": "4",
  "groupKey": <string>,    // 识别警报组的密钥（例如重复数据消除）
  "status": "<resolved|firing>",
  "receiver": <string>,
  "groupLabels": <object>,
  "commonLabels": <object>,
  "commonAnnotations": <object>,
  "externalURL": <string>,  // 指向AlertManager的反向链接
  "alerts": [
    {
      "status": "<resolved|firing>",
      "labels": <object>,
      "annotations": <object>,
      "startsAt": "<rfc3339>",
      "endsAt": "<rfc3339>",
      "generatorURL": <string> // 标识导致警报的实体
    },
    ...
  ]
}
```


<wechat_config>

```yaml
# 是否通知已解决的警报
[ send_resolved: <boolean> | default = false ]

# 与微信API通信时要使用的API密钥
[ api_secret: <secret> | default = global.wechat_api_secret ]

# 微信api网址
[ api_url: <string> | default = global.wechat_api_url ]

# 用于身份验证的公司ID
[ corp_id: <string> | default = global.wechat_api_corp_id ]

# 微信API定义的API请求数据
[ message: <tmpl_string> | default = '{{ template "wechat.default.message" . }}' ]
[ agent_id: <string> | default = '{{ template "wechat.default.agent_id" . }}' ]
[ to_user: <string> | default = '{{ template "wechat.default.to_user" . }}' ]
[ to_party: <string> | default = '{{ template "wechat.default.to_party" . }}' ]
[ to_tag: <string> | default = '{{ template "wechat.default.to_tag" . }}' ]
```


## 搭建AlertManager服务

部署AlertManager可以通过官网[https://prometheus.io/download/](https://prometheus.io/download/)下载二进制文件.
这里演示docker部署AlertManager服务.其他方式请参考官网.

docker部署前,需要先完成配置文件的工作.

我在/home/along/下创建了一个配置文件 `touch alertmanager.yml`
之后编辑 `vi alertmanager.yml`,具体看上文的配置介绍.

启动容器:
```bash
docker run -d -p 9093:9093 --name alertmanagter
-v /home/along/alertmanager.yml:/etc/alertmanager/alertmanager.yml
quay.io/prometheus/alertmanager ```

如果加载模板的话需要挂在一下模板目录(模板在下面有介绍):
```bash
docker run -d -p 9093:9093 --name alert9093
-v /home/along/alertmanager/alertmanager.yml:/etc/alertmanager/alertmanager.yml
-v /home/along/alertmanager/template:/alertmanager/template
quay.io/prometheus/alertmanager```

服务端口9093,挂在刚才设定的配置文件.

访问IP:9093 可以查看AlertManager的web界面(类似prometheus的web界面).


## 定义Prometheus警报规则并引入配置

### 配置警报规则文件

警报规则文件顾名思义,你想监控的指标何时需要警报. 
例如设备温度超过多少要警告.
创建alert.yml,`touch alert.yml` 
```yaml
groups:
- name: metricsUp                           # 定义这组告警的组名
rules:
  - alert: 监测对象挂了                       # 警报名 可理解为警告的标题 
    expr: up{instance="192.168.23.11:9090"} == 0  # 判断某值的规则
    for: 5m                                 # 上面规则持续5分钟为0进行告警,5分钟内触发是pending状态
    labels:                                 # 定义警报标签
      severity: waring                      # 定义警报等级为 waring
    annotations:                            # 备注描述
      summary: "设备: {{ $labels.instance }} 已断开5分钟"   # 警告中呈现的具体信息可以写在这里
```

### Prometheus引入配置

警报规则是Prometheus引入的文件.
Prometheus引入文件的方式:
```yaml
rule_files:
  - "/usr/local/prometheus/alert.yml" # 引入定义的警报规则

```

## 测试警报

我们上面配置好之后,需要各服务已经读取到相关配置文件了之后开始测试.
上面的规则是监测某个监控节点断开,手动断开一个节点.然后5分钟之后观察是否得到警报.

当由警报时收到的邮件为:

![](https://s2.ax1x.com/2019/10/29/KRVQMT.png)

访问AlertManager页面也可以看到告警信息:

![](https://s2.ax1x.com/2019/10/29/KRV1LF.png)


这里图例有些是演示用,与其他可能不存在关系.(不是同时截图的业务,图片仅供参考)


### 静默操作演示

如果有些警报是我们调试的,例如我这里设置值偏低来演示ping值警报,如果我们测时候不想看到警报,可以通过静默来不让他总是发送警报.

![](https://s2.ax1x.com/2019/10/29/KRVlsU.md.png)

![](https://s2.ax1x.com/2019/10/29/KRV8Z4.md.png)

之后点击create 就可以创建此警报的静默操作.

**也可以通过正则,警报组名,实例等来静默各种警报.**

## 定义通知模板
默认模板我们看到了,他是默认的一个告警模板,在我们测试时候可以使用,如果面向用户使用者似乎这个模板不太友好.
而且在面对多数据展示时,此模板也显得不是很清晰.

通过定义了模板,在触发不同警报可以通过AlertManager中,receivers选项来选择模板.
```yaml
templates:
  - 'template/*.tmpl'           # 定义模板中心
receivers:
  - name: 'email'               # 警报
    email_configs:              # 邮箱配置
    - to: '******@163.com'      # 接收警报的email配置
      html: '{{ template "test.html" . }}'      # 设定邮箱的内容模板
      headers: { Subject: "[WARN] 报警邮件"}     # 接收邮件的标题
```

这段配置中,可以看到警报通过test.html作为模板的.他的位置在上面的定义中可以看到是 template/test.html (**如果模板定制有错误,警报可能为空板,不能正常显示内容**)
现在来配置这个test.html 

在 /template/ 下创建 `touch test.html`
模板基于Go的模板系统,[详情点击这里](http://golang.org/pkg/text/template)
如果不想深入连接可以结合默认模板模仿一下语法,[默认模板点击这里](https://github.com/prometheus/alertmanager/blob/master/template/default.tmpl)

```tmpl
{{ define "email.demo.html" }}
<pre>
<table border="1" cellspacing="0">
    <tr>
        <td>报警名</td>
        <td>设备</td>
        <td>发现时间</td>
        <td>详情</td>
    </tr>
    {{ range $i, $alert := .Alerts }}
    <tr>
        <td>{{ index $alert.Labels "alertname" }}</td>
        <td>{{ $alert.Labels.instance }}</td>
        <td>{{ $alert.StartsAt.Format "2006-01-02 15:04:05" }}</td>
        <td>{{ $alert.Annotations.summary }}</td>
    </tr>
    {{ end }}
</table>
</pre>
{{ end }}
```

模板保存后,测试邮件接收情况:
![](https://s2.ax1x.com/2019/10/29/KR89WF.png)

#### 模板时区问题
Prometheus中所有时间都是UTC时间,为了便于我们展示友好时间(东八区),我们需要计算一下时间.
修改模板时间:
```yaml
<td>{{ ($alert.StartsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}</td>
```

*参考*
[ishenping](http://www.ishenping.com/ArtInfo/1265281.html#Comment)
[songjiayang](https://songjiayang.gitbooks.io/prometheus/content/alertmanager/email.html)
[prometheus.io](https://prometheus.io/docs/alerting/alertmanager/)
