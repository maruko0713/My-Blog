---
title: Laravel笔记2:Laravel向视图传递变量
date: 2016-11-14
tag: "laravel"
---

## 填坑
前面(laravel1里面)介绍控制器的创建方法:      

```
php artisan make:controller WelcomeController
```

然后点开controller目录下的WelcomeController文件，我们会发现里面预写好了很多东西。     
有时我们不需要laravel为我们做这么多，于是我们可以这样:    
   
```
php artisan make:controller WelcomeController --plain
```

现在再打开看看，是不是除了一个Controller对象，里面啥也没有，空空如也了？^_^    

## 向视图传递变量
视图不是一个写死的文件，它是一个“模版”，所谓模版，就是我们可以往里面动态写入信息，因此就需要向视图传递变量啦。     

### 在controller里做工作 
比如我们现在controller里有这样一段逻辑:    

```php
public function index() {
    $name = 'maruko0713';
    return view("parent.welcome");
}
```

我想把$name传递进去，此时只要:    

```php
public function index() {
    $name = 'maruko0713';
    return view('parent.welcome')->with('name',$name);
}
```

其中,$name,就是我们定义的那个变量，'name'是我们在视图文件中新给它起的名字。    
    
## 在视图文件里做工作 
在视图文件里，我们只需要找个地方:    

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <h1><?= $name ?></h1>
</body>
</html>
```

注意通配符的格式。这是原生php的写法。  
看那个“name”现在被放进h1里去啦，打开浏览器，你就可以看到我的名字出现在里面～～～     
还可以这样，用blade模版引擎的写法:    
   
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <h1>{{ $name }}</h1>
</body>
</html>
```

如果不需要转义(就是直接把对方当html输出)，就加上两个叹号:   

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <h1>{!! $name !!}</h1>
</body>
</html>
```
   
### 传递很多变量
把with里面的参数变成数组就行:    
   
```php
public function index() {
    return view('parent.about')->with([
        'first' => 'maruko',
        'second' => 'xiaowanzi'
        ]);
}
```

在视图里：    
    
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <h1>{{ $first }}</h1>
    <h2>{{ $second }}</h2>
</body>
</html>
```

## 小提示
在传递变量的时候，尤其是多个变量的时候，我们可以利用php的compact函数:    
   
```php
compact('first','second');
```

它会直接创建一个包含变量名（我们传入的就是变量名去掉$的样子，php会自己给它加上$）和它对应的值的数组。






