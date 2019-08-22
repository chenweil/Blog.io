---
title: Python建立SocketSSL连接
date: 2019-06-04 10:23:11
categories: Python
tags: [Python,Socket]
---

### Python Socket连接

5月中旬遇到一个功能,需要利用Python建立Socket tcp连接,于设备通讯发送相关数据.

这块没接触,Python也是hello world水平.

赶紧恶补一下:

Socket是网络编程的一个抽象概念。

通常我们用一个Socket表示“打开了一个网络链接”，打开一个Socket需要知道目标计算机的IP地址和端口号，再指定协议类型。

服务端我也不知道什么样,这里只记录客户端的相关. 

这里我们得到一个文档说,需要建立socket SSL 连接,通过XML格式发送数据.

#### 非ssl的socket:

``` python
import socket

# 创建一个socket:
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 建立连接:
s.connect(('192.168.1.230', 80))
```

#### SSL socket:
端口是3344,ssl跳过验证,如果验证参数需要修改.
``` python
import socket

 s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        
 c = ssl.wrap_socket(s, cert_reqs=ssl.CERT_NONE)
 try:
    c.connect(('192.168.1.230', '3344'))
except:
    return 2
```

这下与那台设备可以正常通讯了,后面实现具体功能就ok了.
