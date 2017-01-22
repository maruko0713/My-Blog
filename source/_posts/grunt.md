---
title: grunt与gulp的对比和思考 
date: 2016-09-22
tag: "前端工具"
---
## 前言:
虽然对于自动化工具，之前只接触过gulp，但我早就听说过grunt的大名。虽然网上对比这两者的文章也不少，但比起道听途说，经过自己的测试和思考的结论才更能有说服力。今天我拿最近做的一个[给旅游景点做评论的移动端项目](https://github.com/maruko0713/Travel_comment_website)做了一下grunt的测试，对grunt有了一个基本的认识，然后结合一些文章，对这两个自动化工具有了些自己的想法，趁热打铁做个记录。

<!-- more --> 
## 关于gulp
关于gulp，我写过一篇[比较详细的入门博客](https://maruko0713.github.io/2016/08/26/gulp/),里面有概念介绍，也有示例，这里就不再赘述了^_^      

## 关于grunt
同样是一套前端自动化工具，它和gulp相似，一般用于:
- 压缩文件
- 合并文件
- 简单的语法检查

### grunt怎么用
重点来啦！上code！    
下面这段代码，可以实现对指定文件的压缩，合并以及语法检查，具体怎么回事，请看注释:
```js
module.exports = function (grunt) {
  // 项目配置
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    //代码语法检查
    jshint: {  
    //定义用于检测的文件  
    files: ['js/main.js'],  
    //配置JSHint (参考文档:http://www.jshint.com/docs)(重写jshint的默认配置选项 )
    options: {  
       //全局白名单的设置
        globals: {  
          jQuery: true,  
          console: true,  
          module: true ,
        }
      }
    },
    //压缩
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      // 对于文件不多的情况,你可以这么写:
      // build: {
      //   src: 'js/*.js',
      //   dest: 'dest/<%= pkg.file[0] %>.min.js'
      // }
      // 如果文件很多，那就这么写：
      dist: 
      {
        files: {
          "dest/js/main.min.js":"js/main.js"
        }
      }
    },
    //压缩css文件
    cssmin: {
      //给压缩后的文件加上头部的一行注释 显示包名和日期
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      // 配置压缩后的去向
      target : {
        files: {
         "dest/css/main.min.css":["css/main.css"]
       }
     }
   }
 });
  // 加载提供每个任务的插件
  grunt.loadNpmTasks('grunt-contrib-uglify');
  grunt.loadNpmTasks('grunt-contrib-cssmin');
  grunt.loadNpmTasks("grunt-contrib-jshint");
  // 默认任务设置
  grunt.registerTask('default', ["jshint","uglify","cssmin"]);
}
```

## grunt与gulp
至少从写法上来看，grunt和gulp差别还是蛮大的^_^。    
首先说一下自己的想法吧，相比之下，我是比较倾向于用gulp的，但并不完全否定grunt(事实上，许许多多的事情，存在即合理，技术也不例外，更别说是现在还在被广泛使用的grunt了，所以对于一些文章用类似于“抛弃grunt，拥抱更强的gulp”这样的描述做标题，我是没办法认同的)。    
好，进入正题。我们先说说gulp比grunt强在哪里。我觉得gulp最亮的有三个地方，一个是基于流的工作模式，另一个就是它的异步处理，还有就是它的插件短小精悍，实现了功能上的低耦合。我们着重对比一下grunt和gulp在这几个方面上的表现:    
- 基于流的工作模式：  gulp操作一个文件的时候，先把它读进来，然后按照pipe函数设置的顺序使它“流”向各种各样的操作，比如压缩，比如拼接等等，直到所有的操作结束了，你调用了dest函数，此刻它才会把文件流丢回硬盘里去。而grunt就没这么聪明了，比如你想对某一个文件既压缩又检查，那么压缩的时候,grunt会从硬盘里读一次，然后把新生成的文件写进硬盘，读入的文件被释放，然后检查的时候，又读取一次，然后再处理，然后再释放，也就是会在每次操作都会重新读取一遍同一个文件。重复的读取会导致程序的运行速度相对一站到底更慢。    
- 异步处理： gulp中，对于注册的不同task默认是是异步的（但可以利用插件实现同步）,对于同个文件（也就是同一个流里）的操作是同步的。而grunt中，操作与操作之间总是同步的，也就是说我在task中这样注册["uglify","jshint"],那么它将严格按照这种顺序执行。其实两者的task都是可以通过一定方式实现同步和异步两种执行模式的。此处区别没有其它两处明显。
- 短小精悍，功能单一的插件： grunt中，存在一个插件实现不止一个功能的情况，比如uglify既能压缩，又能进行写文件操作，concat也是如此。那么如果我既uglify又要concat,这个文件就会被先压缩然后写入，然后再读过来进行拼接，再写入，多麻烦呀。gulp就不这样，它的压缩就是压缩，拼接就是拼接，至于放回，是单独的一个功能，这样，压缩也好，拼接也罢，功能再多，我等你所有操作结束后，尘埃落定，再把终极文件一次写入，省去许多不必要的中间环节，运行速度会更快.    

写了这么多，不知道大家现在对gulp和grunt有没有点想法了？其实grunt也有它强悍的地方，它很成熟，插件很多，有时候你用gulp做不到的事情，grunt就可以帮你实现。因此在自动化处理的需求比较复杂的情况下，grunt还是很有威力的！  

## 总结
gulp与grunt，各有千秋，grunt更成熟更丰富，而gulp更轻量更快速。因为我现在还是学生，对前端这块，更多的是自己摸索，做一些规模不大的小项目来熟悉知识点和开发流程，所以在今后的开发中，我会把gulp作为首选，也会继续对gulp做更多的了解^_^

