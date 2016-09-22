---
title: 自动化管理工具gulp的初体验
date: 2016-08-26
tag: "前端工具"
---
前言:
  
最近写了一个响应式的demo，推在github上[响应式demo](https://github.com/maruko0713/To_Be_Responsive),在这个项目里，我第一次接触前端自动化管理工具，结合我自己的情况，我选择了gulp。这篇文章主要是对gulp的学习和使用做一个记录，同时文章后面会做一些gulp的比较好的插件的推荐.    

## 什么是gulp
> gulp是前端开发过程中对代码进行构建的工具，是自动化项目的构建利器；她不仅能对网站资源进行优化，而且在开发过程中很多重复的任务能够使用正确的工具自动完成；使用她，我们不仅可以很愉快的编写代码，而且大大提高我们的工作效率。    
    
## gulp如何使用
使用gulp之前，我们首先要安装gulp    
因为我是mac的系统，所以采取了命令行安装的形式，如果需要其他帮助，请查看[gulp官方网站](http://www.gulpjs.com.cn/)的说明    
```
npm install gulp --g
```
通过这个操作，可以完成对gulp的全局安装     
    
之后，我们需要在根目录下建立一个名为gulpfile.js的文件，这个文件非常重要，因为我们在后续的更新和重新操作的过程中会反复使用它，因为我的项目不大，所以采取了一些比较基础的操作，如下:    
```js
var gulp = require("gulp"); //它是大boss
var rev = require("gulp-rev"); // 插件 用于在更新文件内容后更新文件名
// 插件(用于在目录下的文件名更新后更新html文件中的文件名)
var revReplace = require("gulp-rev-replace"); 
// 插件(用于分析html文件中带有注释的css文件和js文件)
var useref = require("gulp-useref"); 
var filter = require("gulp-filter");// 插件 用于筛选出已标记的css文件和js文件
var uglify = require("gulp-uglify"); //压缩js文件
var csso = require("gulp-csso"); //压缩css文件
gulp.task("default", function() {
    //filter操作可以筛选出相应的文件 restore参数表示该文件可以被返回到dist目录下
    var jsFilter = filter("**/*.js",{restore:true});
    var cssFilter = filter("**/*.css", {restore:true}); 
    //感叹号代表排除 filter内第一个参数可以为字符串也可以为数组
    var indexHtmlFilter = filter(["**/*","!**/index.html"], {restore:true});
    return gulp.src("src/index.html") //找到要处理的文件
        .pipe(useref()) //分析文件中带有注释标志的地方
        .pipe(jsFilter) //筛选出标记的js文件 丢进文件流中
        .pipe(uglify()) //压缩操作
        .pipe(jsFilter.restore) // 将丢入文件流的文件放回原有的位置
        .pipe(cssFilter) //将筛选出的css文件丢进文件流 
        .pipe(csso()) //压缩
        .pipe(cssFilter.restore) //再丢回去
        .pipe(indexHtmlFilter) // 把index.html文件取出 剩余的匹配文件丢入文件流
        .pipe(rev()) //生成此刻文件流中所有文件的版本号
        .pipe(indexHtmlFilter.restore) //丢回去
        .pipe(revReplace()) //改名字（名字后面加上文件版本号）
        .pipe(gulp.dest("dist")); // 把文件流扔进dist文件夹
});
```
    
上面提到要在html文件中进行标记，格式如下:    
```html
<!-- build:css css/combined.css -->
     <link rel="stylesheet" href="css/normalize.css">
     <link rel="stylesheet" href="css/main.css">
     <link rel="stylesheet" href="css/owl/owl-carousel/owl.carousel.css">
     <link rel="stylesheet" href="css/owl/owl-carousel/owl.theme.css">
<!-- endbuild -->
```

js也是相同的格式，不过需要把css改成js:    
```html
<!-- build:js js/combined.js -->
    <script src="js/vendor/jquery.min.js"></script>
    <script src="css/owl/owl-carousel/owl.carousel.js"></script>
    <script src="js/vendor/picturefill.min.js"></script>
    <script src="js/main.js"></script>
<!-- endbuild -->
```
运行命令:    
```
gulp
```
(因为我们的任务名叫做default 所以gulp后面不用跟任何参数就会执行我们的文件)    
如此一来，我们就可以把多个文件压缩到一个文件里面.    
需要注意的是，在压缩的过程中，注释会被丢掉，如果在css文件中有不想被丢掉的注释采取在注释中星号后面添加一个叹号的方式注释，就不会被压缩掉了:    
    
![gulp1](http://7xl4oh.com1.z0.glb.clouddn.com/gulp1.jpg)    
    
加上叹号后，叹号下面的炮灰注释在压缩文件中全都找不见了，但是叹号注释的内容还在:    
    
![gulp2](http://7xl4oh.com1.z0.glb.clouddn.com/gulp2.jpg)    
    
压缩后，我们可以看到css文件和js文件都会变成我们给它的新名字+版本号的形式:    
    
![gulp3](http://7xl4oh.com1.z0.glb.clouddn.com/gulp3.jpg)  
      
如果对文件内容进行了改动，便会生成一个新的版本号:    

![gulp4](http://7xl4oh.com1.z0.glb.clouddn.com/gulp4.jpg)  
  
当然，html文件里面对应的名字也会跟着变化:    

![gulp5](http://7xl4oh.com1.z0.glb.clouddn.com/gulp5.jpg)    
    
好了，这样一来，我们就成功地使用gulp实现了我们的一个自动化压缩文件，自动化处理文件名的一个流程，当然啦，gulp的本领还不只这些，它有许许多多的插件，比如:     

- gulp-watch插件 它可以监听文件变化 自动执行gulp命令
- gulp-postcss插件 它可以自动给css属性添加浏览器的兼容前缀（比如-webkit-
-moz-之类的）
- gulp-concat插件 单纯地合并多个文件但不压缩
- gulp-responsive插件 自动生成响应式图片
     
等等等等，我们可以在gulp-npm命令行的指引下寻找到更多地插件，gulp官方网站上为我们提供了各种各样的插件的使用说明，甚至还教授我们如何为gulp编写插件.   
好啦，今天的gulp学习记录就到这里，自动化管理工具并非gulp这一种，在接下来的学习中，也应该学习和接触更多的工具，学会根据不同的场景转换工具的使用，使自己的工作更有效率^_^    
