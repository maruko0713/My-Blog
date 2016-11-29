---
title: Laravel笔记4:laravel中的Model的创建和数据库表的基本操作
date: 2016-11-20
tag: "laravel"
---

## 创建一个model
(前提是你已经创建了一个articles表)    
命令行:    

```php 
php artisan make:model Article
```

执行完之后，你会在laravel的app目录下发现一个叫做Article.php的文件，这个文件是和你的articles表呼应的，它用来对article表进行操作。    
   
在laravel的环境下打开一个php解释器：    

```php
php artisan tinker
```
   
输入:    

```php
$article = new App\Article;
```

你就得到了一个Article对象。    
你可以对它进行一些操作:    
   
### 增加
```php
$article->title = "My first Title";
$article->content = "content";
```

存储这个元组:    

```php 
$article->save();
```

创建一个新的元组(一般还都是这么做的,快):    
   
```php
$article=App\Article::create(["title"=>"Second title","content"=>"second content"]);
```


### 查找    
   
```php
$first = App\Article::find(1);
```
这里按照id为1进行查找。

```php
$second = App\Article::where("content","=","content")->get();
```

取出content为content的元组们。    
这里get()得到一个**collection**对象。    
这个对象很重要，这里留个坑，以后写笔记会着重纪录。  
当然你可以这样写:    

```php
$second = App\Article::where("content","content")->get();
```
效果是一样的。     
或者有时候你只需要一条数据，一条类型为App\Article的数据:    
   
```php
$third = App\Article::where("content","content")->first();
```
直接对它进行Article对象的操作就可以啦。

  

    
  
### 更新
   
```php
$first->title = "update";
$first->save();
```

用数组去更新（更新多个的时候）:    
   
```php
$first->update(["title"=>"update"]);
```

### 删除   
两种方法:    
   
实例方法：delete方法:    
   
```php 
$first->delete();
```
    
类方法：destroy方法:    
   
```php
App\Article::destroy(1);
App\Article::destroy([1,2,3]);
App\Article::destroy(1,2,3);
```



