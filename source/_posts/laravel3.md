---
title: Laravel笔记3:Laravel中的Blade模版引擎
date: 2016-11-15
tag: "laravel"
---
## Blade模版引擎
> Blade 是 Laravel 提供的一个既简单又强大的模板引擎。和其他流行的 PHP 模板引擎不一样，Blade 并不限制你在视图（view）中使用原生 PHP 代码。所有 Blade 视图页面都将被编译成原生 PHP 代码并缓存起来，除非你的模板文件被修改了，否则不会重新编译，这就意味着 Blade 基本上不会给你的应用增加任何额外负担。Blade 视图文件使用 .blade.php 文件扩展名，并且一般被存放在 resources/views 目录。
   
<!-- more --> 
## Blade实现模版的继承
如果你想重用一个已经存在的blade.php文件(假设它叫做app.blade.php)，那么你这么做:
用这个命令：    

```php
@extends('app')
```

你就把它引用进来啦。       
既然引用了这个文件，那么在文件的基础上如果想要做一些改动，比如说我想要在这个文件内部插入一段自定义的代码(假如是在body标签之间进行插入)，那么在app.blade.php里这样写:        
    
```html
<body>
    @yield('content');
</body>
```

这就告诉了blade，content这个区域是可以自定义的。   
回到我们的新文件里，我们可以这样写:    
    
```php
@extends('app')
@section('content')
<h1>I am maruko0713</h1>
@stop
```

注意，每个section末尾都要跟一个stop。      
    
## Blade里面插入逻辑语句    
格式如下：    

判断:    

```php
@if(判断条件)
html代码
@else
html代码
```
    
循环:    

```php
@foreach($people as $person)
<li>{{ $person }}</li>
@endforeach
```




