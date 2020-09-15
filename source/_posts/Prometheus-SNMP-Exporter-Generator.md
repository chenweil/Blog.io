---
title: Prometheus-SNMP_Exporter Generator
date: 2019-12-24 15:26:33
categories: SNMP
tags:
- SNMP
- SNMP_Exporter
- Prometheus
---

## Prometheus-SNMP Exporter

>  生成器从generator.yml读取并写入snmp.yml。

之前在说[Prometheus-snmp_export部署](https://blog.51ai.vip/2019/08/29/Prometheus-snmp-export%E9%83%A8%E7%BD%B2/)时,没有具体提到snmp.yml的生成器是怎么生成的.几乎用的都是github上的[snmp.yml](https://github.com/prometheus/snmp_exporter/blob/master/snmp.yml)文件(只在demo中添加了auth配置).

因为刚好所有通用的指标都取得通用的mib树. 在后期我搜集设备的信息需要一些私有mib的数据,这时候需要自己通过生成器来生成snmp.yml.

## Generator 的操作步骤



### 下载需要的程序

```
# Debian-based distributions.
sudo apt-get install unzip build-essential libsnmp-dev # Debian-based distros
# Redhat-based distributions.
sudo yum install gcc gcc-g++ make net-snmp net-snmp-utils net-snmp-libs net-snmp-devel # RHEL-based distros

go get github.com/prometheus/snmp_exporter/generator
cd ${GOPATH-$HOME/go}/src/github.com/prometheus/snmp_exporter/generator
go build
make mibs(不建议直接make)
```

这里直接make mibs 可能会失败,他在make文件里设置的源有些已经不能访问了.

我建议先下载好mibs ,我已经上传[github](https://github.com/chenweil/mibs).

***建议自行搜集mib 不执行make mibs会方便一些***

把所有的mib放入mibs 目录下.

需要准备好所有需要涉及到的mib文件. 除了公有的mib,我们还需要监控目标设备的私有mib.思科/华为之类的会提供这些mib,像锐捷这种需要和商务部联系.

---

*一些mib:*

  [https://github.com/netdisco/netdisco-mibs](https://github.com/netdisco/netdisco-mibs)

  [https://github.com/pgmillon/observium/tree/master/mibs](https://github.com/pgmillon/observium/tree/master/mibs)

  [https://github.com/librenms/librenms/tree/master/mibs](https://github.com/librenms/librenms/tree/master/mibs)

---

### 当我们准备好所有的mib后,需要编写一个generator.yml.

下面是一个翻译的官方文档(翻译比较烂,自行查阅[原文]([https://github.com/prometheus/snmp_exporter/tree/master/generator](https://github.com/prometheus/snmp_exporter/tree/master/generator))):

```yaml
modules:
  module_name:  # 模块名称。您可以根据需要拥有任意数量的模块。
    walk:       # 要walk的OID列表。 也可以是SNMP对象名称或特定实例。
      - 1.3.6.1.2.1.2              # 与“接口”相同。
      - sysUpTime                  # 与“ 1.3.6.1.2.1.1.3”相同。
      - 1.3.6.1.2.1.31.1.1.1.6.40  # 索引为“ 40”的“ ifHCInOctets”的实例。

    version: 2  # 要使用的SNMP版本。 默认2。
                # 1将使用GETNEXT，2和3将使用GETBULK。
    max_repetitions: 25  # 使用GET / GETBULK请求多少个对象，默认为25。
                         # 对于有故障的设备，可能需要减少。
    retries: 3   # 重试失败请求的次数，默认为3。
    timeout: 10s # 每次步行的超时时间，默认为10秒。

    auth:
      # 社区字符串与SNMP v1和v2一起使用。 默认为“ public”。
      community: public

      # v3具有不同且更复杂的设置。
      # 需要哪些取决于security_level。
      # 还列出了NetSNMP命令上的等效选项，例如snmpbulkwalk和snmpget。
      # 请参见snmpcmd（1）。

      username: user  # 必需，无默认值。 NetSNMP的-u选项。
      security_level: noAuthNoPriv  # 默认为noAuthNoPriv。 NetSNMP的-l选项。
                                    # 可以是noAuthNoPriv，authNoPriv或authPriv。
      password: pass  # 没有默认值。 也称为authKey，NetSNMP的-A选项。
                      # 如果security_level是authNoPriv或authPriv，则为必需。
      auth_protocol: MD5  # MD5或SHA，默认为MD5。 -NetSNMP的选项。
                          # 如果security_level为authNoPriv或authPriv，则使用此属性。
      priv_protocol: DES  # DES或AES，默认为DES。 NetSNMP的-x选项。
                          # 如果security_level为authPriv，则使用。
      priv_password: otherPass # 没有默认值。 也称为privKey，NetSNMP的-X选项。
                               # 如果security_level是authPriv，则为必需。
      context_name: context # 没有默认值。 NetSNMP的-n选项。
                            # 如果在设备上配置了上下文，则为必需。

    lookups:  # 要执行的查找的可选列表。
              # “keep_source_indexes”的默认值为false。 
              # 索引必须唯一，才能使用此选项。


      # 如果表的索引是bsnDot11EssIndex，则通常是该表结果度量的标签。
      # 相反,使用索引查找bsnDot11EssSsid表条目，并使用该值创建一个bsnDot11EssSsid标签。
      - source_indexes: [bsnDot11EssIndex]
        lookup: bsnDot11EssSsid
        drop_source_indexes: false  # 如果为true，则删除此查找的源索引标签。
                                    # 当新索引唯一时，这可以避免标签混乱。

     overrides: # 允许每个模块覆盖MIB的位
       metricName:
         ignore: true # 从输出中删除度量。
         regex_extracts:
           Temp: # 将创建一个新的度量，并将其附加到metricName上，成为metricNameTemp。
             - regex: '(.*)' # 正则表达式从返回的SNMP walk的值中提取一个值。
               value: '$1' # 结果将解析为float64，默认为$1。
           Status:
             - regex: '.*Example'
               value: '1'
             - regex: '.*'
               value: '0'
         type: DisplayString # 覆盖度量标准类型，可能的类型有：
                             #   gauge:   带gauge的整数。
                             #   counter: 带类型计数器的整数。
                             #   OctetString: 一个位字符串，呈现为0xff34。
                             #   DateAndTime: RFC 2579日期和时间字节序列。如果设备没有时区数据，则使用UTC。
                             #   DisplayString: ASCII或UTF-8字符串。
                             #   PhysAddress48: 一个48位的MAC地址，呈现为00:01:02:03:04:ff。
                             #   Float: 一个32位浮点值，带有类型gauge。
                             #   Double: 一个64位浮点值，带有类型gauge。
                             #   InetAddressIPv4: IPv4地址，呈现为1.2.3.4。
                             #   InetAddressIPv6: IPv6地址，呈现为0102:0304:0506:0708:090A:0B0C:0D0E:0F10。
                             #   InetAddress: 每个RFC 4001有一个InetAddress。必须以InetAddressType开头。
                             #   InetAddressMissingSize: 因索引中没有大小而违反RFC 4001第4.1节的InetAddress。
                             #                           必须以InetAddressType开头。
                             #   EnumAsInfo: 为其创建单个时间序列的枚举。适用于恒定值。
                             #   EnumAsStateSet: 每个状态具有时间序列的枚举。适用于可变低基数枚举。
                             #   Bits: 一种RFC2578位结构，它产生一个具有每位时间序列的状态集。
```

下面提供一个自己编写的generator.yml

```yaml
modules:
  ruijie_mib:
    walk: 
      - interfaces
      - ifXTable
      - sysUpTime
      - sysName
      - myMemoryPoolCurrentUtilization
      - myCPUUtilization5Sec
    timeout: 12s
    version: 2
    auth:
      community: Monitor@#@Jkj
    lookups:
      - source_indexes: [ifIndex]
        lookup: ifAlias
      - source_indexes: [ifIndex]
        lookup: ifDescr
      - source_indexes: [ifIndex]
        lookup: 1.3.6.1.2.1.31.1.1.1.1 
    overrides:
      ifAlias:
        ignore: true
      ifDescr:
        ignore: true
      ifName:
        ignore: true
      ifType:
        type: EnumAsInfo
      sysDescr:
        type: DisplayString
      sysLocation:
        type: DisplayString
      ifPhysAddress:
        type: PhysAddress48
      sysName:
        type: DisplayString
```

解释一下此配置的目的:

* 模块名称 ruijie_mib  通过名字可以知道是作用锐捷设备

* walk 中, 是需要获取的指标. 其中前四个是共有mib获取到的,后面是私有mib获取到的.

* timeout 超时定义12秒

* version snmp协议是v2 

* auth 定义的团体名 

* looksups 通过索引查找列表

* overrides  前三个指标删除,后面几项是定义了他们的数据类型.

### 万事具备,只差执行.

准备工作完成之后,就可以执行程序了. 

```bash
export MIBDIRS=mibs
./generator generate
```

执行后,可以看到snmp.yml.
