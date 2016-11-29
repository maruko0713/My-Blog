---
title: Laravel笔记4:Laravel中的数据库和Migration
date: 2016-11-17
tag: "laravel"
---
## Migration是啥
Migration是数据库的版本控制。    
    
## Migration命令
在migrations这个文件夹下面。
默认有两个，打开其中的create\_users\_table文件，里面会有一个对象，对象有up方法和down方法。    
文件代码:    

```php
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
            $table->increments('id');
            $table->string('name');
            $table->string('email')->unique();
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
        Schema::drop('users');
    }
}
```
   
在命令行里输入:    

```
php artisan migrate
```

将创建migration文件夹下面的所有表。 
这实质上是把up方法给执行了。    
   
那么啥时候执行down呢？    
这个命令行:    
   
```
php artisan migrate:rollback
```


## 创建一个新的migration文件
用这个命令行来创建新的migration文件:   
   
```
php artisan make:migration create_表名_table --create=表名   
```

其中，"create\_表名\_table"这个东西是文件名，后面的那个等号后面的表名才是你真正到时候创建出来的表名，不过事实上，它们往往是一致的。    
  
## 数据库中表的字段的设置    
在Schema::create这个方法的第二个参数的函数体里面去写你想要的数据库中的表的格式。   
举个例子：    
   
```php
$table->increments('id');
$table->string('name');
$table->string('email')->unique();
$table->timestamps();
```
楼上increments是说这个id是自增的，string就是name的格式是string类型的意思，unique是说这个字段必须是唯一的～～～    
注意这个timestamps方法，它会在表里面生成created\_at字段和updated\_at字段，这个字段会在你生成一个元组和更新这个元组的时候自动把创建和更新的时间也存进数据库。  
   
## 数据库表的结构的改变    
这个时候需要请出命令行哦。    
   
```
php artisan make:migration add_列名字_column_to_表名字 --table=表名字
```

add\_intro\_column\_to\_表名字,这依然是个文件名，没错这个命令行还是创建了一个新文件出来。 
然后在这个文件的up方法里面，写入你想要的改变，比如添加（上面写过了），删除（dropColumn）和重命名（renameColumn）等等等等。    
   
## 总结
现在明白了，所谓migration,其实就是通过创建版本文件，然后再利用migrate命令行去执行它的up方法或者利用migrate:rollback命令行去执行它的down方法，从而实现对数据库的一个操作。   
注意，每个版本文件只能被执行一次。每次migrate的时候只能去执行最新的没有被执行过的那些个up方法。    








