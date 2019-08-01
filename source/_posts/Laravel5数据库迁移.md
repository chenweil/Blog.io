---
layout: laravel5
title: 数据库迁移
date: 2019-07-26 14:30:25
tags: Laravel
---

## Laravel5 数据库迁移笔记

* ### 创建迁移文件
     命令: `make:migration`
     
     举例: `php artisan make:migration create_users_table --create=users`

     生成位置: 项目/database/migrations/下 文件名已时间开头,后面是自己创建迁移文件名字.

    --creat 指定数据库中表的名字

* ### 编辑迁移文件
    打开迁移文件:
    
    ```php <?php
       
       use Illuminate\Support\Facades\Schema;
       use Illuminate\Database\Schema\Blueprint;
       use Illuminate\Database\Migrations\Migration;
       
       class CreateUsersTable extends Migration
       {
           /**
            * Run the migrations.
            *
            * @return void
            */
           public function up()
           {
               Schema::create('users', function (Blueprint $table) {
                   $table->bigIncrements('id');
                   $table->string('name');
                   $table->string('email')->unique();
                   $table->timestamp('email_verified_at')->nullable();
                   $table->string('password');
                   $table->rememberToken();
                   $table->timestamps();
               });
           }
       
           /**
            * Reverse the migrations.
            *
            * @return void
            */
           public function down()
           {
               Schema::dropIfExists('users');
           }
       }
```
文件中有两个方法,up/down . 可以理解为创建执行时up方法,rollback时候是执行down方法.

up方法中:
    创建了7个字段,表明为 users.
    
    创建字段操作 `$table->` 后面加上字段类型 `string` ,指定字段名称`('name')` 字段被指 `->comment('名子')`; 
    
    具体每个字段类型,以及其他属性定义,请查询手册.
    


* ### 执行迁移文件

  这个迁移文件是创建表,所以down是删除表.也就是当我们执行 `php artisan migrate:rollback` 回滚到上一步就是把这表删除(前提创建之后).

  执行命令: `php artisan migrate` 执行迁移文件创建表单.
  
  如果我们后悔创建:可以执行回滚. `php artisan migrate:rollback `  --step 指定回滚次数 `php artisan migrate:rollback --step=2` 执行回滚迁移2次钱.
  
  `php artisan migrate:reset` 回滚所有迁移
  
  `php artisan migrate:fresh` 删除所有回滚
  
  
* ### 为前迁移文件修改字段,改名.
当我们上面的文件执行后,需要增加一个字段,那么就需要新建一个迁移文件.不要直接操作数据库.

    `php artisan make:migration add_id_to_users_table --table=users` 
    
    这里为users表新增身份证字段.
    
    打开文件 新建身份证id列. 
    
    之后我们执行迁移文件.成功后就能看到新得ID字段已经出现.对于编辑和修改都可以.

    `$table->renameColumn('ID', 'id')` 改名id
    
    对应dorop 也需要dropColumn 此字段.
    
    最后执行此迁移文件.


    
