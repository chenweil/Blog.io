---
title: hexo博客安装与配置
date: 2019-04-01 17:59:31
categories: Hexo
tags: Hexo
---

### wordpress 之后

wordpress 使用很方便，但是折腾几次之后。由于一次意外，导致管理者把我的vps被停掉。虽然有些文章还是保留了。但是这次之后感觉自己还是找一个稳妥的家。连接hexo搭建的博客之后，打算自己来折腾一下。


### 记录笔记环境
在windows上写笔记，环境目前是windows下操作。linux，mac系统中需要注意一些细节吧。存在提不到情况，先做好出现问题考虑自行排查。



### 准备

看下hexo的安装提示。
![安装提示](http://t1.aixinxi.net/o_1d7c68cm911cu1p74qfa133ikh2a.jpg-w.jpg "前提")

hexo 需要Node.js 和 Git 。
* 安装 Node.js
 官网:  [官网](https://nodejs.org/en/download/)
 widows，mac，linux 都有对应的安装方法。根据自己的环境来安装。
* 安装 Git
  官网:  [官网](https://git-scm.com/downloads/)
  根据自己环境安装。
* 安装Hexo
  通过npm来安装 Hexo。
  命令:  `npm install -g hexo-cli`
  什么鬼，通过这个命令发现没有实现正常安装。理由，我们在天朝。
  解决方式： 替换国内npm源。
 命令:   `npm install -g cnpm --registry=https://registry.npm.taobao.org`
 请注意不同系统在操作此命令时，需要一些设置。linux 如果使用下面命令需要自建软链。  `cnpm   ln -s /yourdir/bin/cnpm  /usr/local/npm`
 下一步用cnpm 来安装 Hexo： `cnpm install hexo-cli -g`
 验证hexo 是否安装： `hexo v`  会列出版本信息。



### 下面使用Hexo来创建blog：
* 创建项目文件夹。这里开始通过git bash来使用命令行操作。
* 进入项目文件夹，初始化。 `hexo init` （这里也可以，通过 `hexo init 你的项目文件夹名` 结果一样）
这里会看到目录有相关文件了。具体这些文件，看下 [手册](https://hexo.io/zh-cn/docs/setup) 是什么意思。
这时候其实已经是一个博客站点了。
* 命令 `hexo g`  , `hexo s`  得到信息：Hexo is running at http：//lcoalhost:4000` 注意4000端口需要未被占用。 访问地址就可以看到初始化的站点了。 *（不喜欢默认主题可以修改主题）*


### 写文章
写文章需要先创建文档，这个文档默认生成在_post 文件夹下。
* 命令 `hexo new testdoc`  得到信息：  Created:···path/testdoc.md   文档的位置，需要编辑这个文档来写文章（Markdown文档）。
文档写好保存之后。
命令 `hexo g`  , ` hexo s`  之后我们访问之 localhost:4000 就能看到自己的新文章了。


### 推送到Git仓库，在线访问。
首先需要一个 [GitHub](https://github.com/) ，注册账号。
创建一个与账户名一样的库， 用户名.github.io , 之后在项目文件夹中，编辑 _config.yml 配置文件。

```
deploy:
    type: git
    repo: https://github.com/用户名/用户名.github.io.git  (我相信你能知道这个地址在哪里)
    branch: master
```
之前写过testdoc.md  这个文件。提交到git库上，命令： `hexo d` , 提交是，会弹出账号密码让你输入。接着得到提示： Deploy done: git。 这里我们就提交到库上了。   *（账号密码提交比较麻烦，可以通过配置公钥来解决）*
这里如果出现错误 Deployer not found：git  ，需要安装一下。 命令  `npm install hexo-deployer-git --save`
这时候我们可以通过 用户名.github.io.git 这个地址访问到博客了。*（不喜欢这个地址，可以通过域名来绑定）*



### 主题更换
默认的主题让我们觉得太不个性化了。还没有能力自己操刀编辑，怎么办？

建议先看下[文档](https://hexo.io/zh-cn/docs/themes).了解一下,培养看文档习惯.

可以使用别人的主题，[官网提供](https://hexo.io/themes/) 提供一些，也可以通过网上其他人的推荐来使用自己喜欢的主题。
例如在官网地址上看到有一个名字是 Next(很多人有这个,或基于此主题修改). 点击可以语言并且访问.
[地址](https://github.com/theme-next/hexo-theme-next) , 我们需要克隆到自己项目下 themes 文件下. 你可以下载zip到自己项目下解压,不过有些麻烦也不够B格.我们使用git操作.

  打开gitbash, 进入到自己项目下/themes
  命令 `git clone https://github.com/theme-next/hexo-theme-next.git` . 执行完后,themes 下会有这个文件. 
  使用next 主题, 需要在 _config.yml 上面设置主题.
  theme: next     配置主题为next
  之后通过命令 `hexo g`, `hexo s`. 两个命令后, 登陆localshot:4000 查看一下. 嗯,已经替换成功了.

目前只是替换了主题,主题也是需要配置的,我们需要在  _config.yml 配置菜单等一些参数. 主题相关修改参数配置是需要在 项目/themes/languages/{language}.yml
具体配置希望你看下每个主题的README,或者文档来学习着自己修改.

还要涉及到页面的问题,我们之前 hexo new xx文章 ,都是默认_post文件夹下, 如果我们要定义归档,友链,等页面.也是通过new 命令来实现的.  例如: 建立 tags页面      `hexo new page tags` 
之后你会看到 项目下/source/ 会出现tags 文件夹,进入里面会有一个index.md .这个文件就是你需要的tags 页面.


### 域名
我们觉得github 这个url不太喜欢,并且也很长.可以配置自己的域名.
首先我们需要一个自己的域名,通过万网什么的来购买一个.我是通过阿里云上万网购买的一个域名.
这里不详细来说明域名的购买.

1. 万网控制台里面,有域名的功能(例如阿里云,登陆后,控制台->域名->点击域名->域名解析).
![domain](http://t1.aixinxi.net/o_1d7gmnnf81q5sl9n159g1osikh2a.png-w.jpg "域名解析")

2. 在github，xxx.github.io  点击 settings
![settings](http://t1.aixinxi.net/o_1d7gn0s4a4bb1lau1qal8731m3ka.jpg-w.jpg "settings")
![set pages](http://t1.aixinxi.net/o_1d7gmqciljvsnfo10eb8hpinla.png-w.jpg "custom domain")
**Custom domain** 这里填写你的域名，save。

**此处注意**：你配置完后，会看到库中存在一个文件，CNANE文件。如果你再次提交新文档，会发现你的配置域名无法访问了！
原因：你本地文件没有这个CNAME，通过hexo d 方式 更新库文件后，CNAME没了。
**通过配置域名后，把这个文件下载到本地项目中，位置请注意：项目/source 下。不然hexo d 不会提交到库中。
**
如果不搞定这个，那你每次hexo d 之后，都要改一次custom domain。（我想谁也不会每次都这么操作）
到这里基本上你这台电脑上，发布你的博客。更新，推送到github都没有问题。还有一个问题就是那么如果我换一台电脑怎么办？



### 通过分支来完善博客
工作中使用版本控制器，很方便管理项目代码和文件。那么我们这个hexo 博客也需要这种方式来吧本地的hexo 博客项目推送到线上。如果过本地电脑出故障，或者更换电脑等情况下。我们依然可以通过clone到本地，进行发布博客。

1. 克隆博客
   gitbash clone一份自己的博客(省略命令), 删除克隆后出.git 这个文件外其他文件。
   把之前本地初始化项目中的文件都复制到此项目中。
2. 新建分支   `git branch hexo`, `git checkout hexo`. 两条命令,新建分支,切换至hexo 分支.
3. 在新分支中，提交我们刚才复制过来的文件。  ` git add --all `
4. 提交文件并push到远程。 `git commit -m "mybolg files" `,  `git push origin hexo` 推送带云端。

这下完成了，不用担心换电脑。换电脑后，clone一下，继续可以发布。ps：git你需要自己装。
之后我们一直再这个 hexo 分支就好啦。每次hexo d 之后记得把新文件提交到hexo分支。 add commit push 三步骤不能忘。

### 配置公钥
之前没提到这个，是因为我怕忘记密码，每次都手输入密码。也不是每个人都喜欢我这么操作。那可以选择配置公钥来解决提交时的认证。
1. gitbash  `ssh-keygen` 生成密钥，注意看信息密钥提示位置。
2. 打开生成目录下的 id_res.pub  这个是公钥。打开复制里面的数据，复制。
3. 需要粘贴到github settings->SSH and GPG keys 里面。
![add keys](http://t1.aixinxi.net/o_1d7gsgtpitdq195h1ettdim3i0a.png-w.jpg 'settings')
![ssh keys](http://t1.aixinxi.net/o_1d7gscv061tfgbla1ahn86kti5a.jpg-w.jpg "add keys done")

测试一下配置 gitbash  `ssh -T git@github.com`  得到信息 You've successfully authenticated, but GitHub does not provide shell access.  配置正常。

改完这里还不可以，需要配置项目hexo配置文件了，还记得是哪个文件嘛？（ _config.yml）
之前我们用的是https，现在我们需要用ssh地址提交了。
![use ssh](http://t1.aixinxi.net/o_1d7gsuim4ffkp72alt1qnlreea.png-w.jpg "use ssh")
4.修改 _config.yml 这段配置的 repo地址，看下之前参数和现在参数。

```
deploy:
    type: git
    #repo: https://github.com/chenweil/chenweil.github.io.git   # https 
    repo: git@github.com:chenweil/chenweil.github.io.git       # ssh 
    branch: master
  ```
到这里我们已经配置好，我们下次写完文章是 hexo d 不会让你输入密码了。
真的不用输入密码了吗? 你可能会遇到问题,怎么还需要认证?(因为我这环境出现了问题)
看下错误 :
`git push origin hexo`
fatal: HttpRequestException encountered.
如果你现者句话,那么需要你更新一下Windows的git凭证管理器.[管理器地址](https://github.com/Microsoft/Git-Credential-Manager-for-Windows/releases/)

--------

到此基本的配置已经完成，文中只是简单的描述了基础工作。再哪方面出现问题需要通过文档和网络来查询问题。第一次接触此框架，还是好好看文档。你通过网络查询的很多结果存在一些问题。例如版本不同，环境不同等。最好的方法还是自己来分析，处理。养成好习惯，戒心浮气躁，坚持自己来解决问题。
