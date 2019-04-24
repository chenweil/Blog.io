---
title: gulp笔记
date: 2019-04-22 18:08:54
categories: 前端
tags: gulp
---

### gulp
gulp.js是一个前端构建工具。

### 安装
1. npm
2. 安装全局gulp，`npm install -g gulp`。（如果没有梯子，最好安装下cnpm）
cnpm 安装 `npm install -g cnpm --registry=https://registry.npm.taobao.org`
安装完cnpm，下面所有npm操作替换cnpm 执行即可。
3. 进入项目，初始化（npm init）
4. 项目安装gulp，项目文件夹下，`npm install --save-dev gulp`。 (--save-dev 加入此项目依赖中，不需要可取消这个参数)
5. 项目根创建gulpfile.js文件，文件内创建任务测试。

```
var gulp = require('gulp');
gulp.task('default',function(){
    console.log('hello world!');
});

```
6. 运行 `gulp`，可以看到默认执行，输出 hello world! 。测试成功。

### gulp API
    上面运行 gulp 执行default ，这个是gulp API。 [文档](https://www.gulpjs.com.cn/docs/api/) 

#### gulp工作方式

`gulp.src` 获取文件流,通过`pipe`方法导入到插件，插件处理的流通过`pipe`方法导入 `gulp.dest`中, `gulp.dest` 输出目标文件。


* gulp src
  `gulp.src(globs[, options])`

  >输出（Emits）符合所提供的匹配模式（glob）或者匹配模式的数组（array of globs）的文件。 将返回一个 Vinyl files 的 stream 它可以被 piped 到别的插件中。

文档这意思看着有点费劲，理解为获取文件路径。gulp通过这个方法获取到处理的文件流。

参数： 

    globs 文件匹配模式，匹配文件路径，文件名。
    类型： string array
    
    options  额外可选参数
    类型： object
    额外参数需要看手册
    
* gulp.dest
  `gulp.dest(path[, options])`

  >能被 pipe 进来，并且将会写文件。并且重新输出（emits）所有数据，因此你可以将它 pipe 到多个文件夹。如果某文件夹不存在，将会自动创建它。

理解为写文件，写入path路径文件。

参数：

    path 文件写入路径
    类型：string function
    
    options 额外可选参数
    类型：object

* gulp.task
  `gulp.task(name[, deps], fn)`

    > 定义一个使用 Orchestrator 实现的任务（task）。
        
用来定义任务，内部使用的是Orchestrator。

参数：

    name 任务名字
    
    deps 是当前任务需要的其他任务，一个数组。依赖任务，先于此任务执行。
    类型：array
    
    fn  该函数定义任务所要执行的一些操作，把任务要执行的代码写在里面。

* gulp.watch
  `gulp.watch(glob[, opts], tasks)`
  `gulp.watch(glob[, opts, cb])`

>监视文件，并且可以在文件发生改动时候做一些事情。

参数：
    
    glob 文件匹配模式
    类型 string array
    
    opts 可选配置
    类型 object
    
    tasks 文件变动后执行的任务
    类型 array
    
    cb 一个函数，文件发生变化时调用的函数。
    类型 function
    
*Glob* 匹配模式  [(node-glob）参考语法](https://github.com/isaacs/node-glob) 

```
匹配符                             说明
      *                               匹配文件路径中的0个或多个字符，但不会匹配路径分割符，
                                      除非分隔符出现在末尾

      **                              匹配路径的0个会多个目录 及子目录 需要单独出现，
                                      即他左右不能有其他的东西了如果出现在末尾，也能匹配文件

      ？                              匹配文件路径中的一个字符（不能匹配路径分割符/）

      [...]                           匹配方括号中 出现字符的任意一个，当方括号中第一个字符为^或!时，
                                      则表示不匹配方括号中出现字符中的任意一个，
                                      类似于js中正则表达式中的用法

      !(pattern|pattern|pattern)      匹配任何与括号中给定的任意模式都不匹配
      ？(pattern|pattern|pattern)     匹配括号中给定的任意模式0次或1次
      +(pattern|pattern|pattern)      匹配括号中的至少一次
      *(pattern|pattern|pattern)      匹配括号中给定的任意模式0次或多次
      @(pattern|pattern|pattern)      匹配括号中 给定的任意模式一次

    eg：glob 匹配
      |能匹配 a.js,x.y,abc,abc/,但不能匹配a/b.js|
      |.*   a.js,style.css,a.b,x.y
      //*.js    能匹配 a/b/c.js,x/y/z.js,不能匹配a/b.js,a/b/c/d.js
      **    能匹配 abc,a/b.js,a/b/c.js,x/y/z,x/y/z/a.b,能用来匹配所有的目录和文件
      a/**/z    能匹配 a/z,a/b/z,a/b/c/z,a/d/g/h/j/k/z
      a/**b/z   能匹配 a/b/z,a/sb/z,但不能匹配a/x/sb/z,因为只有单**单独出现才能匹配多级目录
      ?.js  能匹配 a.js,b.js,c.js
      a??   能匹配 a.b,abc,但不能匹配ab/,因为它不会匹配路径分隔符
      [xyz].js  只能匹配 x.js,y.js,z.js,不会匹配xy.js,xyz.js等,整个中括号只代表一个字符
      [^xyz].js 能匹配 a.js,b.js,c.js等,不能匹配x.js,y.js,z.js
```

多种匹配模式时，使用数组
`gulp.src(['js/*.js','css/*.css','*.html'])`

数组中可以用 `！` 排除*(放在数组第一个无效)*
`gulp.src([*.js,'!b*.js'])`

### 编写一个任务
常用到压缩，写个压缩demo。
目录，根目录下有两个文件夹，dist空文件，src目录,src/js文件夹2个文件，common.js,demo.js。
任务目标，将js目录下的.js文件，压缩合并为new.min.js。之后将合并压缩的文件保存到dist/js/。

1. 我们在初始化后的项目中，先安装所需插件，gulp-rename（重命名插件）,gulp-uglify（压缩js插件），gulp-concat（合并文件插件）。
npm install gulp-rename gulp-uglify gulp-concat --save-dev
2. 编辑gulpfile.js 

```
   var gulp=require('gulp'); 
   var  rename= require('gulp-rename');  //引入插件
   var  uglify= require('gulp-uglify');
   var  concat= require('gulp-concat');

    gulp.task('js', function(){                   //创建名为 js的任务
        return gulp.src('src/js/*.js')           //读取文件流           
        .pipe(concat())                             //合并
        .pipe(uglify())                              //压缩
        .pipe(rename({suffix: '.min'}))     //重命名
        .pipe(gulp.dest('dist/js/'))          //输出到指定路径
      });

```

文件保存后，命令行执行任务： `gulp js` 。
可以看到Finshed时间，去dist目录可以看到合并压缩的文件已在里面。


### gulp插件
* CSS压缩 gulp-minify-css
* Js压缩 gulp-uglify
* 重命名 gulp-rename
* 文件合并 gulp-concat
* 自动加载 gulp-load-plugins
* less编译 gulp-less
* sass编译 gulp-sass