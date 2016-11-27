---
title: Laravel笔记0:Laravel的安装和相关的工具
date: 2016-11-11
tag: "laravel"
---
## Laravel 
一个php框架，百科里说它：    
> 是一套简洁、优雅的PHP Web开发框架(PHP Web Framework)。它可以让你从面条一样杂乱的代码中解脱出来；它可以帮你构建一个完美的网络APP，而且每行代码都可以简洁、富于表达力。

它是一个很受欢迎的php框架，也是我学习的第一个php框架。    

## Composer
php的包管理工具，是现在比较好用的一个。    
一个很重要的工具，可以下载一些很好用的php的轮子，嘿嘿。   
想要的包可以在这里找到:    
[packgist网站](https://packagist.org/)    
下载Composer的网站:   
[getcomposer](https://getcomposer.org/)    

## 利用Composer创建laravel项目
    
```
composer create-project laravel/laravel LaravelTest    
```

LaravelTest是我随便起的名字，不用在意。    
    
## 看看我们的laravel项目长什么样吧^_^！    
开启一下php自己的服务器:

```
php -S localhost:8888
```

端口号也是随便写的，8888，发发发发～～～     
    
然后你会发现，出现了404错误，这是为什么?    
因为你现在的根目录是总的那个最大的LaravelTest目录，你需要改变根目录，这样:    

```
php -S localhost:8080 -t public 
```

它会自己在根目录下查找index名字的页面哦。    
加上-t 就可以修改php服务器的根目录啦。    
然后你会发现，出现了一个很简单，但是很好看的页面，那就是laravel的默认主页面啦.    
    
## laravel的命令行工具    
```
php artisan + 各种各样的命令     
```

## 总结
这篇博客记录了一个laravel小demo诞生的全过程，熟悉了几个命令行，接下来就要走进laravel里面的世界去看一看啦！    
