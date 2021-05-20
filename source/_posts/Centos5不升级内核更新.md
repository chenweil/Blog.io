---
title: Centos5不升级内核更新
date: 2019-08-23 17:37:03
---

### 前提
公司需要环境Centos5, 又不能升级内核.


查了一下 大概是 需要 `yum –exclude=kernel* update`   或者修改 yum.conf

### 测试一下 出现一个问题: 

```bash
Loaded plugins: fastestmirror, security
Loading mirror speeds from cached hostfile
YumRepo Error: All mirror URLs are not using ftp, http[s] or file.
 Eg. Invalid release/
removing mirrorlist with no valid mirrors: /var/cache/yum/base/mirrorlist.txt
Error: Cannot find a valid baseurl for repo: base
```

根据查询到的信息是,没有正常的源.

### 解决问题 根据网上的源地址 修改一下:
位置: */etc/yum.repos.d/CentOS-Base.repo*

### 修改内容:
``` conf
[base]
name=CentOS-5.11 - Base
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=os
baseurl=http://vault.centos.org/5.11/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#released updates
[updates]
name=CentOS-5.11 - Updates
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=updates
baseurl=http://vault.centos.org/5.11/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#packages used/produced in the build but not released
[addons]
name=CentOS-5.11 - Addons
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=addons
baseurl=http://vault.centos.org/5.11/addons/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#additional packages that may be useful
[extras]
name=CentOS-5.11 - Extras
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=extras
baseurl=http://vault.centos.org/5.11/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-5.11 - Plus
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=centosplus
baseurl=http://vault.centos.org/5.11/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#contrib - packages by Centos Users
[contrib]
name=CentOS-5.11 - Contrib
#mirrorlist=http://mirrorlist.centos.org/?release=5.11&arch=$basearch&repo=contrib
baseurl=http://vault.centos.org/5.11/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

```

之后 再使用上面的yum 去更新,发现yum 语句是有问题的. 

正确的命令是: `yum --exclude=kernel\* update`   或者  `yum -x 'kernel*' update  `

### 确认是否生效
首先 `yum update` 内容可以看到 安装内容中 包含 'kernel' 信息.

之后 执行上句命令后,可以看到 更新信息里没有 'kernel' .

最后可以确定,这个方案是有效的.