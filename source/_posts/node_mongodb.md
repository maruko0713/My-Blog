
---
title: 在node应用中使用mongodb来存储数据
date: 2016-10-11
tags: ["Node","数据库"]
---
## 前言： 
这两天用node做了一个[在线笔记demo](https://github.com/maruko0713/node-demos/tree/master/louNote),对于笔记信息和session信息的存储都用到了mongodb。     
<!-- more --> 
    
## 什么是MongoDB 
MongoDB是时下流行的noSQL数据库之一，MongoDb中有三个非常重要的概念:    
   
- 数据库: 数据库是一个物理容器集合。一个单一的MongoDB服务器通常有多个数据库.    
- 集合: 集合是一组MongoDB文档，相当于我们之前在MySQL中表的概念。    
- 文档: 文档是一组键值对。很多个文档构成一个集合。 
    
从关系上来看，文档组成集合，集合组成数据库，多个数据库可以运行在同一台服务器中。噢耶！
     
## node中的MongoDB    
node中对MongoDB数据库的操作是通过mongoose库实现的。主要有以下操作：    
    
### 连接数据库    
    
```js
var mongoose = require("mongoose");
mongoose.connect("mongodb://localhost:27017/dbName");
```

通过connect方法的调用可以实现对数据库的连接，楼上的代码中dbName是你想要打开的数据库的名字，如果没有这个数据库，那么就自动创建一个并且打开，不会报错^_^。    
    
### 建立一个表，存储数据    
mongoose中建表是通过mongoose.Schema这个构造函数。     
比如我想建立一个用户表:     
    
```js

// 引入mongoose
var mongoose = require("mongoose");
// 把构造函数搞出来
var Schema = mongoose.Schema;
// 建立一个模版
var userSchema = new Schema({
    username: String,
    password: String,
    email:String,
    createTime: {
        type:Date,
        default:Date.now
    }
});
// 针对上面的集合建立一个模型
var User = mongoose.model("User",userSchema);
// 用User模型创建一个新用户
var user = new User({
    username:"xiaowang",
    password:123,
    email:"hehe@qq.com"
});
// 保存这个用户
user.save(function(err) {
    if(err) {
        console.log("fail to save");
        return
    }
});
```
    
## Schema与Model    
Schema->创建集合     
Model->操作集合
我们可以这么想,Schema对应的是集合。Model把这个集合抽象出来，通过model可以创建，修改，删除和更新表中的文档。
Model是活的，Schema对象作为参数传入model方法后，会作为Model对象的实现标准，但是Model对象本身还具有其它的属性和行为，可以进行数据库操作，而Schema不可以。  

## Model对象的基本方法    
楼上介绍了一个save方法是增加操作。除此之外还有：    
    
### 更新操作:    

```js
// 更新操作   
User.update({_id:someone.id},{
    $set:{username:xxxx,password:xxxx}
    },function(err) {
        if(err){
            console.log(err);
            return
        }
        console.log("更新成功");
});
```
    
### 删除操作:    
    
```js
User.remove({
    _id:id
},function(err){
    if(err) {
        console.log(err);
        return
    }
    console.log("删除成功！");
});    
```

### 查找操作:
```js
User.find({_id:xxx})
    .exec(function(err,arts){
        if(err){
            console.log(err);
            return 
        }
        console.log("查找成功！")
    });
```

如此，就是mongoose为我们提供的增删改查啦！     


    



