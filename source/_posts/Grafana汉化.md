---
title: Grafana汉化
date: 2019-09-16 15:37:46
categories: 可视化图表
tags: Grafana
---

## 可视化图表

Grafana是一个通用的可视化工具。通过Grafana可以管理用户权限，数据分析，查看，导出，设置告警等。

### 仪表盘Dashboard

通过数据源定义好可视化的数据来源之后，对于用户而言最重要的事情就是实现数据的可视化。

### 面板 Panel

Panel是Grafana中最基本的可视化单元。每一种类型的面板都提供了相应的查询编辑器(Query Editor)，让用户可以从不同的数据源（如Prometheus）中查询出相应的监控数据，并且以可视化的方式展现。
Grafana中所有的面板均以插件的形式进行使用，当前内置了5种类型的面板，分别是：Graph，Singlestat，Heatmap, Dashlist，Table以及Text。


## 汉化工作

上面简单介绍了一下工具，主要是让我们方便查看监控的数据。这里我还是没有更深入的去研究公式等图形的设置。这里先主要写一下汉化方面的工作。

公司也考虑展示内容为中文化比较好，这里Grafana没有提供语言包的方式来处理多语言问题。在我查看代码过程中，发现工具后台是在GO里面写死的很多导航，返回值等数据。前台是在页面上直接写的很多内容。所以我个人认为无法使用语言包来直接处理多语言问题。那就只好自己来搞定了。


### 汉化的内容
 
更具代码查看，主要分为两大部分：
* 后端： go文件，主要内容在/pkg 目录下。
* 前端： 1. 系统页面  2. 插件页面  这些在/public 目录下

### 准备工作

首先git clone Grafana库 
`git clone https://github.com/chenweil/grafana.git`

之后我们根据自己汉化的版本来检出自己的项目。
这里我们使用的v6.3.4 ，官方版本中可以查看到tag v6.3.4,并重命名自己的分支为6.3.4-chs：
`git checkout -b 6.3.4-chs`

通过 `git branch` 命令查看自己处于哪个分支上。
![](https://t1.picb.cc/uploads/2019/09/18/gFRkLM.png)
这里如果你不是很熟悉git命令行，可以使用sourcetree工具操作，相对来说点点鼠标就可以搞定了。

我们在自己创建的分支就可以来处理我们的工作了。

### 前端调试环境

需要 npm，nodejs，yarn 
开启调试环境时候，是开启前端的热加载来协助我们调试。
这里安装完三个环境可能在执行 yarn start 时报错，这里如果你是在windows上，需要再安装一下sass.(根据报错来看问题，我这里遇到缺少sass问题)

当我们yarn start 执行后，等待一段时间，build at 时间证明准备工作已完成，下面就需要我们在调试模式下测试了。

还需要一个调试的Grafana服务程序，这里是windows环境，所以直接从官方下载了zip包，执行bin下的grafana-server.exe 来启动服务。需要再conf文件夹修改一下public前端资源的配置，如果不修改那么你汉化的信息是看不到的，服务会直接读取的当前的public，我们这需要读取汉化的public文件位置。

配置在windows服务程序的 /conf/defaults.ini
修改内容：
```ini
app_mode = development               # 开发模式
static_root_path = D:\grafana\public #这里配置到git拉取得位置的public
```

按照正常的操作 是需要开启`webpack-dev-server` 访问端口是3333
我这里没有这么设置，直接利用3000端口调试的。（当我们yarn start 后，通过修改页面可以看修改的内容。）

### 汉化前端文件

前面环境已经搭建好之后，我们通过修改页面文件展示内容来汉化。
例如汉化登陆页面：
`/public/app/partials/login.html`
![](https://t1.picb.cc/uploads/2019/09/18/gFRZft.png)
把对应的英文改为中文，保存后webpack会处理。处理完成刷新页面可以看到结果。

前端汉化文件不止html，还有ts，tsx等文件。这里如果不知道具体文件可以在public文件夹下，通过全局搜索页面的单词等信息定位到文件。
**我没有汉化带有test 的测试文件。**

最后我们把需要的文件都翻译之后，通过`yarn build` 生成文件。这些文件都存在生成的目录`/public/build`中。把这些文件覆盖到自己搭建的项目中完成汉文。
建议把整体public目录替换。 
重启服务既可以看到中文版的页面了。

### 汉化后端文件
编写中。。。