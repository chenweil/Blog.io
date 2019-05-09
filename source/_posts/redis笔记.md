---
title: redis笔记
date: 2019-04-25 17:12:52
categories: redis
tags: redis,db
---

## redis笔记 
单进程,默认16库,

select  N  切换库

flushdb 清空库
 

### 类型
 
* string 字符串
* list 列表 
* set 集合  
* sorted set有序集合 
* hash哈希

一个字符串支持512M
 
有序集合 每个元素会关联一个double类型分数。成员唯一，分数可以重复。

### 常用命令
##### key：

    keys *
      
    exists key
    
    move key db  移除key 从库中
    
    expire key 为key 设置过期时间
    
    ttl key 查看多少秒过期，-1 永不过期， -2已过期
    
    type key 查看类型
    
    del key 删除

### string：
getrange key 0-N  setrange key 0-N XXX   获取字符串范围内容， 设置范围内为XXX
 
setex 设置生命值多少秒  setnx key 设置一个不存在的key

mset mget msetnx 

### list：

    lpush rpush lrange
     
    lpop rpop
     
    lindex
     
    llen
    
    lrem key 2 value 删除2个value
     
    ltrim key 0-N  截取并复制给key （其他的删除了）
    
    rpoplpush  弹出前面key的值 加入后面的key中
    
    lset key index value 设置key中 index下标的值
    
    linsert key before/afrer value1 value2 key中1值得前面后后面加入2值 

### set：
    sadd  key value 添加到key集合
    
    smembers key 查询集合  
    
    sismember key m  查询m是否在key集合中
    
    scard key 集合ket的基数
    
    spop key 随机移除一个元素并返回元素的值
    
    srem key m 移除m从key的集合中
    
    smove K1 K2 m  将k1的m一刀k2里
    
    sinter key1 key2 交集
    
    sunion key1 key2 并集
    
    sdiff key1 key2 差集 

### hash：
    hset user name ali 
    
    hset user age 33     设置user数据
    
    hget user name  获取user.name
    
    hmset  human name tom age 44  设置多数据
    
    hmget human name age  获取多数据
    
    hgetall human  
    
    hdel human  name 删除name
    
    hlen human 长度
    
    hexists human age 是否存在
    
    hkeys human 获取所有key 
    
    hvals  human 获取所有value 
    
    hincrby  hincrbyfloat   
    
    hsetnx 不存在添加


### zset：
    zadd key value：score 设置值的分数
    
    zrange key 
    
    zrangebyscore key  min max      （不包含   limit   升序
    
    zrevrangebyscore 降序
    
    zrem key value 
    
    zcount key min max  范围内多少个
    
    zscore key m 返回key 中m的分数值
    
    zrevrange key start stop 降序展示


### 持久化：
#### rdb aof

 * rdb 
  
        快照方式定期生成临时文件，从临时文件替换上次持久化的文件。数据不是非常敏感。 dump.rdb
        
        dump.rdb 关机会清空文件，备份需要导出到另一台机器。
        
        设备启动会去读取domp.rdb来恢复数据（文件名可以设置）。
        
        关闭rdb  设置save为空
        
        save命令 bgsave后台异步快照  备份到dump.rdb 
    
        优势： 适合大规模莫恢复，完整性和一致性要求不高。
            
        劣势： 意外down  最后一次备份不到。内存被克隆一份，2倍的性能膨胀。

* aof

        日行形式记录每个写操作，将所有写指令记录。
         
        appendonly  yes  开启
        
        注意flushdb all  这些东西也会记录操作。
        
        同时存在两种备份，优先恢复aof文件，如果aof失败， 导入rbd备份数据。
        
        aof文件损毁或异常时， 通过redis-check-aof程序修复后再恢复。

#### 配置： 
appendfsync    always/everysec/no  同步设置

rewrite：

    aof 采用文件追加方式，记录文件会越来越大，重写机智，aof

    文件大小超过阈值时，会启动aof文件的内容压缩，只保留可恢复的最小指令。默认配置64M

事务：

    mulit 开启  语句  exec执行， discard 取消

    语法错误时，全部没执行，如果设置错误，其他执行，错误的不执行。

监控 锁 乐观锁 悲观锁:

    悲观锁，锁表。

    乐观锁，行信息版本更新。 谁先提交谁成功。

    wacth 监控字段，执行事务，如果监控字段未出现变化，事务执行成功。



复制机制：

       master 写  slave读
    
       配置slave为主     slaveof 主库id 端口
    
       info replication  查看信息
    
       从机不能写数据

* 方式 1主机 多从机

        主机down，从机待命。主机启动，从机继续同步主机。
    
        从机端开，会变成master，除非配置文件规定。否则需要 slaveof 重新顶可以。
    
        主机down后，如果从机某台执行 slaveof no one ，使当前从库变主库。

* 方式2 主机->从机->从机  相连

* 方式3 哨兵

      监控主机是否down，down后根据投票选出从机转换主库。
    
      配置中添加 sentinel.conf  ，编写配置：
    
      sentinel monitor  主机配置  地址 端口  1  1标识投票
    
      启动哨兵：redis-sentinel 哨兵配置 








