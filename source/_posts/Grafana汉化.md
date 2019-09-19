---
title: Grafana中文化
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


## **翻译工作**

上面简单介绍了一下工具，主要是让我们方便查看监控的数据。这里我还是没有更深入的去研究公式等图形的设置。这里先主要写一下翻译方面的工作。

公司也考虑展示内容为中文化比较好，这里Grafana没有提供语言包的方式来处理多语言问题。在我查看代码过程中，发现工具后台是在GO里面写死的很多导航，返回值等数据。前台是在页面上直接写的很多内容。所以我个人认为无法使用语言包来直接处理多语言问题。那就只好自己来搞定了。


### **翻译的内容**
 
更具代码查看，主要分为两大部分：
* 后端： go文件，主要内容在/pkg 目录下。
* 前端： 1. 系统页面  2. 插件页面  这些在/public 目录下

### **准备工作**

首先git clone Grafana库 
`git clone https://github.com/chenweil/grafana.git`

之后我们根据自己翻译的版本来检出自己的项目。
这里我们使用的v6.3.4 ，官方版本中可以查看到tag v6.3.4,并重命名自己的分支为6.3.4-chs：
`git checkout -b 6.3.4-chs`

通过 `git branch` 命令查看自己处于哪个分支上。
![](https://t1.picb.cc/uploads/2019/09/18/gFRkLM.png)
这里如果你不是很熟悉git命令行，可以使用sourcetree工具操作，相对来说点点鼠标就可以搞定了。

我们在自己创建的分支就可以来处理我们的工作了。

### **前端调试环境**

需要 npm，nodejs，yarn 
开启调试环境时候，是开启前端的热加载来协助我们调试。
这里安装完三个环境可能在执行 yarn start 时报错，这里如果你是在windows上，需要再安装一下sass.(根据报错来看问题，我这里遇到缺少sass问题)

当我们yarn start 执行后，等待一段时间，build at 时间证明准备工作已完成，下面就需要我们在调试模式下测试了。

还需要一个调试的Grafana服务程序，这里是windows环境，所以直接从官方下载了zip包，执行bin下的grafana-server.exe 来启动服务。需要再conf文件夹修改一下public前端资源的配置，如果不修改那么你翻译的信息是看不到的，服务会直接读取的当前的public，我们这需要读取翻译的public文件位置。

配置在windows服务程序的 /conf/defaults.ini
修改内容：
```ini
app_mode = development               # 开发模式
static_root_path = D:\grafana\public #这里配置到git拉取得位置的public
```

按照正常的操作 是需要开启`webpack-dev-server` 访问端口是3333
我这里没有这么设置，直接利用3000端口调试的。（当我们yarn start 后，通过修改页面可以看修改的内容。）

### **翻译前端文件**

前面环境已经搭建好之后，我们通过修改页面文件展示内容来翻译。
例如翻译登陆页面：
`/public/app/partials/login.html`
![](https://t1.picb.cc/uploads/2019/09/18/gFRZft.png)
把对应的英文改为中文，保存后webpack会处理。处理完成刷新页面可以看到结果。

前端翻译文件不止html，还有ts，tsx等文件。这里如果不知道具体文件可以在public文件夹下，通过全局搜索页面的单词等信息定位到文件。
**我没有翻译带有test 的测试文件。**

最后我们把需要的文件都翻译之后，通过`yarn build` 生成文件。这些文件都存在生成的目录`/public/build`中。把这些文件覆盖到自己搭建的项目中完成汉文。
建议把整体public目录替换。 
重启服务既可以看到中文版的页面了。

### **后端环境**

后端是用GO写的。后端我没有调试，不想前端那样可以边调边看。我的办法就是全部改完，build程序，启动查看后端翻译的结果。
所需，本人是在windows10下处理的，需要gcc，go。 

### **翻译后端文件**

文件所在位置：	**/pkg/**
![](https://t1.picb.cc/uploads/2019/09/19/gPIwDR.png)

首页我们的导航，二级菜单这些不是前端控制的，这些是在 **/pkg/api/index.go**

![](https://t1.picb.cc/uploads/2019/09/19/gPIUwX.png)

其余还有很多文件，内容包含：html数据，返回值信息，debug信息等。如果你前端翻译完成，那么后端对你来说也是很轻松的。

*请注意一些参数或者判断不要给翻译了*

当翻译完成后，需要build。
首先到项目根目录，这里可以看到 *build.go* 文件。用这个来生成后端程序。 windows下可以build .exe程序。 时间很短，便于我们调试。

build前，先steup一下，执行 `go run build.go setup`。

```log
$ go run build.go setup
Version: 6.3.4, Linux Version: 6.3.4, Package Iteration: 1568870230
go install -v ./pkg/cmd/grafana-server
github.com/grafana/grafana/pkg/api
github.com/grafana/grafana/pkg/cmd/grafana-server

```

如果没有报错，那么证明是可以执行build了。
这里可能你会遇到一些错误，出现错误先解决错误再重新执行  `go run build.go setup`，直到没有错误。
我遇到一下错误：

* **error loading module requirements**
这个问题一查一大把，原因就是你需要的模块下载不到，地址被墙。
解决方式： 其中一种：go.mod 添加replace() 替换地址。下面并非全部用到，我是偷懒全粘上。

	```go
	replace (
	    golang.org/x/build => github.com/golang/build v0.0.0-20190416225751-b5f252a0a7dd
	    golang.org/x/crypto => github.com/golang/crypto v0.0.0-20190411191339-88737f569e3a
	    golang.org/x/exp => github.com/golang/exp v0.0.0-20190413192849-7f338f571082
	    golang.org/x/image => github.com/golang/image v0.0.0-20190417020941-4e30a6eb7d9a
	    golang.org/x/lint => github.com/golang/lint v0.0.0-20190409202823-959b441ac422
	    golang.org/x/mobile => github.com/golang/mobile v0.0.0-20190415191353-3e0bab5405d6
	    golang.org/x/net => github.com/golang/net v0.0.0-20190415214537-1da14a5a36f2
	    golang.org/x/oauth2 => github.com/golang/oauth2 v0.0.0-20190402181905-9f3314589c9a
	    golang.org/x/perf => github.com/golang/perf v0.0.0-20190312170614-0655857e383f
	    golang.org/x/sync => github.com/golang/sync v0.0.0-20190412183630-56d357773e84
	    golang.org/x/sys => github.com/golang/sys v0.0.0-20190416152802-12500544f89f
	    golang.org/x/text => github.com/golang/text v0.3.0
	    golang.org/x/time => github.com/golang/time v0.0.0-20190308202827-9d24e82272b4
	    golang.org/x/tools => github.com/golang/tools v0.0.0-20190417005754-4ca4b55e2050
	    golang.org/x/xerrors => github.com/golang/xerrors v0.0.0-20190410155217-1f06c39b4373
	    google.golang.org/api => github.com/googleapis/google-api-go-client v0.3.2
	    google.golang.org/appengine => github.com/golang/appengine v1.5.0
	    google.golang.org/genproto => github.com/google/go-genproto v0.0.0-20190415143225-d1146b9035b9
	    google.golang.org/grpc => github.com/grpc/grpc-go v1.20.0
	    gopkg.in/asn1-ber.v1 => github.com/go-asn1-ber/asn1-ber v0.0.0-20181015200546-f715ec2f112d
	    gopkg.in/fsnotify.v1 => github.com/Jwsonic/recinotify v0.0.0-20151201212458-7389700f1b43
	    gopkg.in/gorethink/gorethink.v4 => github.com/rethinkdb/rethinkdb-go v4.0.0+incompatible
	    gopkg.in/ini.v1 => github.com/go-ini/ini v1.42.0
	    gopkg.in/src-d/go-billy.v4 => github.com/src-d/go-billy v4.2.0+incompatible
	    gopkg.in/src-d/go-git-fixtures.v3 => github.com/src-d/go-git-fixtures v3.4.0+incompatible
	    gopkg.in/yaml.v2 => github.com/go-yaml/yaml v2.1.0+incompatible
	    k8s.io/api => github.com/kubernetes/api v0.0.0-20190416052506-9eb4726e83e4
	    k8s.io/apimachinery => github.com/kubernetes/apimachinery v0.0.0-20190416092415-3370b4aef5d6
	    k8s.io/client-go => github.com/kubernetes/client-go v11.0.0+incompatible
	    k8s.io/klog => github.com/simonpasquier/klog-gokit v0.1.0
	    k8s.io/kube-openapi => github.com/kubernetes/kube-openapi v0.0.0-20190401085232-94e1e7b7574c
	    k8s.io/utils => github.com/kubernetes/utils v0.0.0-20190308190857-21c4ce38f2a7
	    sigs.k8s.io/yaml => github.com/kubernetes-sigs/yaml v1.1.0
	    go.uber.org/atomic => github.com/uber-go/atomic v1.3.2
	)
	```   

	还有方法是通过设置Module GOPROXY代理。大概意思就是当构建或运行你的应用时,Go 会通过 GOPROXY 获取依赖。
	这个我没测试，有兴趣自行查阅。


* **exec: "gcc": executable file not found in %PATH%**
	这个问题是我们环境没有gcc，这个玩意儿需要下载一个软件[**MinGW**](http://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/4.8.2/threads-posix/seh/ )。
	此地址提供的压缩包文件。解压可以使用，此网站也提供下载器安装方式。*这网站下载贼慢*
	解压之后设置环境变量，当前解压完路径是：  `C:\MinGW\mingw64` 在环境变量添加此目录。
	cmd 测试 gcc -v 有信息即ok。

没有问题 执行 `go run build.go build`
完成后，就可以得到bin文件，位置在 `/bin/windows-amd64/` ， 里面有grafana-server.exe 程序。

在测试前端时候，用的那个windwos程序可以下岗了，把build之后的bin程序+md5文件一起复制到这目录里。如果你不放心提前先备份一份。

之后按照测试前端那样，打开服务，访问3000，查看自己汉化后端的成果吧。


### 生成docker镜像
docker build -t grafana/grafana:dev .

还没完，我们刚才只是测试一下自己汉化的后端是否可以。如果测试完都可以之后，我们还是要把它build成镜像，利用docker来运行服务。
如果你不想用docker，就考虑在build为linux程序。

生成docker镜像可以分为两种，一种是你所在linux/amd64中生成的镜像，另一种是通用的镜像。
考虑第二种通用镜像：
`make build-docker-full` 或者 `docker build -t grafana/grafana:dev .`

最后我发现这些可以拿到我的测试环境去完成。那么我就打包移到测试环境去生成镜像，镜像做好 加载汉化的public 就可以了。 

到此终于结束啦。