---
title: yaml规则
date: 2019-09-24 10:20:31
categories: yaml
tags: yaml
---

## yml 语法

最近经常配置一些服务，发现大部分都是yml类型文件。小记一下。

### 规则
1. 大小写敏感
2. 缩进表示层级
3. 注释 # 

### 结构：

对象： ：符号

``` yml
name: admin
```



数组： - 符号      

``` yml
user:
  - name: admin
  - height: 178
  - age: 30
```

字符串： 默认无引号，内部含有空格或特殊符号需要加''

``` yml
name: admin
desc: 'he was cool'
```

null： ~ 

``` yml
value: ~
```

层级依靠缩进：

``` yml
job:
  - jobconfig:
    name: 'snmp-sw01'
    - target:
      - 192.168.1.88
```