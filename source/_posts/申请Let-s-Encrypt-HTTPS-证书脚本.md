---
title: 申请Let's Encrypt HTTPS 证书脚本
date: 2020-06-24 13:33:18
categories: HTTPS
tags: 
- HTTPS
- SSL
---

## 最近需要到SSL证书，又想免懒。选择脚本来更新SSL证书文件

> Let’s Encrypt是一个由非营利性组织互联网安全研究小组（ISRG）提供的免费、自动化和开放的证书颁发机构（CA）。
简单的说，借助Let’s Encrypt颁发的证书可以为我们的网站免费启用HTTPS(SSL/TLS) 。

那我们通过一个脚本来申请：

脚本名称： [acme.sh](https://github.com/acmesh-official/acme.sh)

### 安装acme.sh： 
* `curl https://get.acme.sh | sh`

* 创建指令： `alias acme.sh=~/.acme.sh/acme.sh`

* 测试收否安装成功： `acme.sh --Version`
出现版本，安装完成。


### 生成证书
`acme.sh --issue -d demo.com -d www.demo.con -w 
/home/wwwroot/demo.com`

–issue是acme.sh脚本用来颁发证书的指令；
-d是–domain的简称，其后面须填写已备案的域名；
-w是–webroot的简称，其后面须填写网站的根目录。

### 查看证书
`acme.sh --list`

---

### Nginx 配置
项目是Nginx，下面是对Nginx的配置。

acme.sh  --installcert -d demo.com \
         --key-file /etc/nginx/ssl/demo.com.key \
         --fullchain-file /etc/nginx/ssl/fullchain.cer \
         --reloadcmd "service nginx force-reload"

通过 installcert 来完成安装，此处我们需要把*.key,fullchain.cer 文件拷贝到指定位置。最后通过reload命令让Nginx重载（force-reload 我环境中无法使用此参数，这里换成 restart）。

最后我们需要配置Nginx的443 server

```nginx
server {
        listen 443 ssl;
        server_name demo.com;
        
        ssl on;
        ssl_certificate      /etc/nginx/ssl/fullchain.cer;
        ssl_certificate_key  /etc/nginx/ssl/esofar.cn.key;
        
        ...
```

到此基本上配置完成了。
Acme.sh 通过定时任务可以实现定期更新。 查看 `crontab -l`

### acme.sh的更新维护
acme 协议和 letsencrypt CA 都在频繁的更新, 因此 acme.sh 也经常更新以保持同步。

手动更新： `acme.sh --upgrade`

自动更新：`acme.sh --upgrade --auto-upgrade`

取消自动更新： `acme.sh --upgrade --auto-upgrade 0`