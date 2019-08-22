---
title: Composer笔记
date: 2019-07-15 17:24:45
categories: Composer
tags: Composer
---

### composer - laravel5

#### 创建laravel项目：

`conposer create-project laravel/laravel=5.8.* --prefer-dist  ./XXX`

laravel=5.8.* 这里代表要部署5.8中最高版本   --prefer-dist  参数代表优先下载zip

#### 安装vendor:

`composer install`

`composer install --prefer-dist`

#### 更新：

`composer update`

#### composer版本更新：

`composer self-update`

#### 利用composer 创建laravel控制器：

`php artisan make:controller HomeController`
  会在http下 创建一Home的控制器

如果存在分目录情况，需要指定目录：
`php artisan make:controller Home/HomeController`  

#### Laravel config:

编写一些类的别名，controller中 use 简短的别名为目的。

位置：config/app    存在一数组 aliases  在里面添加

#### 创建模型：

创建一个user 的model

`php artisan make:model User`

指定目录加入目录即可

#### 获取项目路由：

`php artisan route:list`

#### composer在项目中安装三方库时候出现报错：

执行命令： `composer Install`

返回错误： Your requirements could not be resolved to an installable set of packages.

解决：

使用 `composer install --ignore-platform-reqs` 命令设置忽略版本匹配然后再进行安装你所需要的composer包。
