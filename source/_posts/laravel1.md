---
title: Laravel笔记1:Laravel的路由和控制器
date: 2016-11-12
tag: "laravel"
---

## 这篇博客
是用来介绍laravel的路由和控制器的。

## Http目录

就是用来处理http请求用的   
路由文件和控制器目录都在Http这个目录下。    


### 路由文件routes.php

```php
Route::get('/',function() {
   return "hello world!"; 
});
```

它的意思是，当一个get请求访问根目录的时候，我们回应的方式是，把hello world写进你的响应体里去，呈现到浏览器当中.    

如果你想呈现的是一个文件:     

```php
Route::get('/',function() {
   return view("welcome"); 
});
```

它会在resources这个文件夹下查找welcome.blade.php这个文件，不用关心后面的两个后缀名，laravel里面的模版文件都是这个后缀名，你只需要把名字告诉view函数就行了，它会帮你去找的。    

如果你的视图文件在一个名为parent的文件夹下面，那么你可以这样:    

```
Route::get('/',function() {
   return view("parent/welcome"); 
});
```
你还可以这样:    
```
Route::get('/',function() {
   return view("parent.welcome"); 
});
```
这个“.”很奇怪吧，没关系，laravel认识它就行。    

### 路由和控制器
上面，我们把路由对应的逻辑都写在了一个匿名函数里，事实上，我们一般不这么做。       
routes这个文件的作用，就是定位，这些路由对应的逻辑，应该写在控制器里。    
    
### 写一个控制器
对应命令行:    

```
php artisan make:controller welcome
```

我们写了一个叫做WelcomeController的文件,在Http目录下的Controllers目录里可以找到。    
laravel会自己给我们生成一些方法，比如有一个方法是这样的:    

```php
public function index() {
    return view('welcome');
}
```

在routes文件里这样做:  

```php
Routes::get("/","SitesController@index");
```

它的作用和上面匿名函数的作用是一样的。
   
总结一下这个用法：在controller里写逻辑，然后在routes文件里对应按这样的格式这样写：指定控制器@指定方法。    





