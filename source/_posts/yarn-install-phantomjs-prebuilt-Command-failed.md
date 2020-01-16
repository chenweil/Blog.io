---
title: 'yarn install phantomjs-prebuilt: Command failed.'
date: 2020-01-16 14:21:26
categories: note
tags:
- yarn
- js
---

### 项目yarn install 出现phantomjs-prebuilt： Command failed.

自己在项目中发现执行 `yarn install`时候，一直卡住没走完。

最后报错， *error phantomjs-prebuilt： Command failed.*

可以看到错误中，他是从 github.com/Medium/...   感觉就是没下载成功吧。

最开始以为网络问题，翻墙等方式都试过后发现还是没完成。

没办法，借助网络得知。可以轻松搞定：



> npm config set phantomjs_cdnurl=[http://cdn.npm.taobao.org/dis...](http://cdn.npm.taobao.org/dist/phantomjs_cdnurl)
> yarn config set "phantomjs_cdnurl" "https://npm.taobao.org/mirrors/phantomjs"



看你是npm 还是node。按照上面方式设置一下。

`rm -rf path/node_moudels`

`yarn install`

解决问题，美滋滋。



[引用地址](https://segmentfault.com/q/1010000010278132)