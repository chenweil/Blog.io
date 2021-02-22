---
title: Grafana插件Plugin中文汉化
date: 2020-11-30 13:46:47
categories: Grafana
tags:
---

## 汉化三方插件
前面说过汉化Grafana的工作。目前在7.2.1上面，大部分已经完成。细节继续完善。
今天考虑在第三方插件上做一些汉化。点到插件一看全是英文感觉很突出。领导看到了也不爽啊-.-！。

## 找个软的捏
饼图在展示方面比较直观。Grafana上有一个插件[Pie Chart](https://grafana.com/grafana/plugins/grafana-piechart-panel)
。这个现象比较少，同时在一些模版上使用中。就拿这个热热身。

## 具体步骤
1. 下载项目

   项目地址：[piechart-panel](https://github.com/grafana/piechart-panel)
   文件结构：
   
   ![](https://t1.picb.cc/uploads/2020/11/30/ZVbHPT.jpg)
   
   ```bash
   git clone git@github.com:grafana/piechart-panel.git
   cd piechart-panel # 进入到目录
   yarn install  
   ```
   
   **我直接把项目clone到grafana存放插件的位置，我的grafana是为了测试run的一个docker镜像。把插件目录挂载到本机，代码clone到目录中。**
   
2. 汉化工作

   根据上面目录看，主要修改文件都在src里面。
   IDE打开此项目，在src中修改需要编辑的文件。
   
   ![](https://t1.picb.cc/uploads/2020/11/30/ZVb8Ht.jpg)
   
   图片举例，选项第一项选择图形类型。选项内容`pie` / `donut`。通过翻译我修改成了 派/甜甜圈。根据修改内容其他地方设计修改的都需要修改。我通过查询替换方式，在其他文件中修改了代码中的判断。例如上图右侧展示的文件类似。
   
3. build插件
 
   修改完需要的内容之后，grafana是能识别到有一个插件，但没有build时候他会提示你没有build插件。就是他不认识你的项目代码。
   
   这个怎么处理呢？看[**官方的文档**](https://grafana.com/tutorials/build-a-panel-plugin/#3)
   
   执行 `yarn dev` 
   
   ```bash
   # 执行结束提示，美滋滋～
   ✔ Bundling plugin in dev mode
   ✨  Done in 4.91s.
   ```
   
   执行完毕我们重启grafana就可以看到成果了。
   
   对比下原来的版本和汉化后的版本：
   
   before：
   
   ![](https://t1.picb.cc/uploads/2020/11/30/ZVb5gM.jpg)
   
   After：
   
   ![](https://t1.picb.cc/uploads/2020/11/30/ZVbGO6.png)

   
4. 测试&调试
    
   以上2，3步骤基本就是一个测试、调试的过程。
   
   * 我开始先把所有配置项汉化。然后再处理选项参数。
   * 接着build，重启grafana查看。如此往复达到预期目标。

**我本机调试用docker启动grafana，测完删了容器就好了。**
   
## 持续改进   
考虑持续处理某个插件，可以考虑fork原插件项目，remote add XXX源。
然后新建分之来做自己的处理。master fetch XXX源 以跟踪上游的更新。
这样自己项目安装插件时候拉自己的就好啦，美滋滋。
   