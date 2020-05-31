---
title: 项目工程化——Gulp简单使用
date: 2020-06-01
tags: [工程化, Gulp]
categories: 
- 工程化
series: 工程化
---

项目Git地址：[https://github.com/beat-the-buzzer/gulp-test.git](https://github.com/beat-the-buzzer/gulp-test.git)


### 前言：认真审视自己的职业

如果有人问你的职业是什么，你该如何回答？

码农、程序员、程序猿、程序媛、IT行业、计算机的、软件开发、写页面的、做网站的、前端开发、Java开发等等。

看一下自己职业的全称：前端开发工程师。

我们应该是个工程师，即使自己现在做的工作可能和“工程师”三个字相距甚远，但是对于项目的工程化，我们已经没有办法回避了。什么？你说你每天只想做好自己的工作，这些工程化的东西都交给高级开发人员或者架构师。难道你不是以高级开发为目标的吗？

### Gulp初探

#### Gulp是什么

Gulp是基于流的自动化构建工具。它不仅能对资源进行优化，而且在开发过程中能够通过配置自动完成很多重复的任务，让我们可以专注于代码，提高工作效率。

Gulp是基于Nodejs的自动任务运行器， 它能自动化地完成 javascript/coffee/sass/less/html/image/css 等文件的的测试、检查、合并、压缩、格式化、浏览器自动刷新、部署文件生成，并监听文件在改动后重复指定的这些步骤。

#### 为什么要使用Gulp——为什么要使用自动化工具

可以交给机器去做的事，我们都可以交给机器去做。比如，我们想给我们写好的CSS加上浏览器前缀，我们不可能手动一个一个去加，Gulp可以轻松解决这个问题。

#### Gulp可以解决哪些问题

 - 编译ES6
 - 压缩 html、css、js
 - 优化图片
 ……

#### Gulp的优缺点

我们知道，Gulp是基于流的自动化构建工具，所以Gulp很适合像流水线一样处理文件，给文件新增一些处理之后再输出；

Gulp不适合处理文件之间的依赖关系；

#### 其他工具

 - Webpack
 - Grunt

### Gulp实战操作

> 本项目中的gulp的版本是3.9.1

#### 文件复制——熟悉Gulp语法

```js
gulp.task('copy', function() {
  return gulp.src('src/**/*')
    .pipe(gulp.dest('dist'))
});
```

1. src() 读取文件，也就是我们即将处理哪些文件

2. pipe() 用于文件流的处理和转换

3. dest() 最终文件的输出目录

#### 处理HTML

```js
var htmlmin = require('gulp-htmlmin'); // 压缩html文件
// 压缩 *.html文件，把src文件夹下面所有后缀为.html的文件进行压缩操作
gulp.task('compress_html', function () {
  return gulp.src('src/**/*.html')
    .pipe(htmlmin({ collapseWhitespace: true }))
    .pipe(gulp.dest('dist'))
});
```

#### 处理CSS

```js
var autoprefixer = require('gulp-autoprefixer'); // 给CSS新增浏览器前缀
var cssmin = require('gulp-clean-css'); // 压缩css文件
// 先给*.css添加浏览器前缀，然后压缩 *.css
// autoprefixer会自动读取浏览器列表
gulp.task('compress_css', function () {
  return gulp.src(['src/**/*.css'])
    .pipe(autoprefixer())
    .pipe(cssmin())
    .pipe(gulp.dest('dist'))
});
```

autoprefixer()会自动读取.browserslistrc里面的配置，从而选择性新增CSS前缀。

#### 处理JS

```js
var babel = require('gulp-babel'); // 编译es6
var uglify = require('gulp-uglify'); // 压缩丑化js文件
//先编译es6,再压缩js，会读取配置
gulp.task('compress_js', function () {
  return gulp.src(['src/**/*.js']) // 获取.js
    .pipe(babel()) // 编译es6 会自动读取.babelrc里面的配置
    .pipe(uglify()) // 压缩js
    .pipe(gulp.dest('dist'))
});
```

babel()会自动读取.babelrc文件里面的配置。presets是ES6的编译集合。例如，我们要编译解构赋值的语法，如果不加`stage-0`，编译就会报错。如果我们需要更高级的语法，就需要装其他的编译插件，例如，我们如果使用装饰器语法，就需要额外的编译插件：babel-plugin-transform-decorators-legacy。

#### 处理图片

```js
var imagemin = require('gulp-tinypng-nokey'); // 压缩图片
// 压缩图片
gulp.task('compress_img', function () {
  gulp.src('./src/**/*.{png,jpg,jpeg,gif,ico}')
    .pipe(imagemin())
    .pipe(gulp.dest('dist'))
});
```

#### 整合所有任务

```js
gulp.task('default', ['compress_html', 'compress_css', 'compress_js', 'compress_img']);
```

以上就是Gulp的初级用法。
