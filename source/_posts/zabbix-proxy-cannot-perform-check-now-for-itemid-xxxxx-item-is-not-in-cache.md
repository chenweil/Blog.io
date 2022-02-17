---
title: 'zabbix proxy cannot perform check now for itemid [xxxxx]: item is not in cache'
date: 2021-10-27 15:06:44
categories: Zabbix
Tags: zabbix
---

# zabbix proxy cannot perform check now for itemid [xxxxx]: item is not in cache

## 情况

接上次做完容器部署proxy后，为其添加host进行添加任务。

发现一直没有数据，就到item里面执行 `execute now`。

然后过了几分钟回来一看，还是没有。 

Emmm，看下log吧。

Server没一场，那问题就在proxy了吧。

连上proxy去看看：
![uG8E7k](https://gitee.com/chenweil/aLong_note/raw/master/upimg/uG8E7k.png)

提示好像是去检查对应的itemid，然后说item不在还cache中。
赶紧上网科普！

## 原因

![I5WWwc](https://gitee.com/chenweil/aLong_note/raw/master/upimg/I5WWwc.png)

因为是主动的proxy，那他会定期去server要数据。

这个3600就是配置的更新周期了。1个小时才去要一次，所以肯定是没监控了。

为了验证，就等了1小时看看：
![EWJu2V](https://gitee.com/chenweil/aLong_note/raw/master/upimg/EWJu2V.png)
实锤了，1小时。后面也就有了数据。
![C0pDSl](https://gitee.com/chenweil/aLong_note/raw/master/upimg/C0pDSl.png)
Host是1小时之后开始有数据的，也就是他同步后就开始执行监控项了。 

    查询到的内容：
    [地址](https://subscription.packtpub.com/book/networking_and_servers/9781784399764/1/ch01lvl1sec10/understanding-the-zabbix-proxies-data-flow)

## 解决

Ok，那么在重新部署的容器加上此参数(ZBX_CONFIGFREQUENCY)。

```bash
docker run --name zbxproxy -d \
-e ZBX_SERVER_HOST=192.168.10.66 \
-e ZBX_HOSTNAME="testproxy" \
-e ZBX_TIMEOUT="10" \
-e ZBX_TLSACCEPT=psk \
-e ZBX_TLSCONNECT=psk \
-e ZBX_TLSPSKIDENTITY=helloworld \
-e ZBX_TLSPSKFILE=zbx_proxy.psk \
-e ZBX_CONFIGFREQUENCY=600 \
-v /etc/localtime:/etc/localtime:ro \
-v /zbx_proxy.psk:/var/lib/zabbix/enc/zbx_proxy.psk \
--restart=always \
zabbix/zabbix-proxy-sqlite3:alpine-trunk
```

--祝好

本文结束