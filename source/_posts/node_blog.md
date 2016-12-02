---
title: 用nodejs搭建一个完整的博客系统
date: 2016-10-22
tags: ["Node","express"]
---
## 前言:    
接触node一段时间了，最近在利用node做一些[小demo](https://github.com/maruko0713/node-demos)和一个功能相对复杂一些完整一些的[博客系统](https://github.com/maruko0713/node-blog).后者走心一些，实现了增删改查，页面也多一些，这一篇是对重要技术点的一个总结.    
    
## 新工具
在开发的过程中，我们经常需要修改代码进行debug，但是一旦修改了我们就不得不终止node服务器重启后才可以在浏览器里看到修改后的效果，这样很麻烦，因此，我们可以使用一个叫做supervisor的工具~~~~     
安装supervisor^_^:    
     
```
sudo npm install -g supervisor
```

启动supervisor：    
    
```
supervisor app.js
```

于是我们就可以实现修改后自动刷新啦！
    
## 选择一个喜欢的模版渲染引擎    
在进行开发的时候，我选用了比较经典的express框架，express框架默认使用jade作为它的渲染引擎。jade的语法虽然有很多人追捧，但是我不太喜欢，我觉得每一行都要小心翼翼地缩进对齐，tab和空格还不能一起用，太痛苦了。我更喜欢ejs,它是一个和原生html长得很像的东西，有了它，我们就可以在html里面嵌入一些js语句了，它的核心用法如下:    
    
```js
<% code %>
```
在两个百分号之间，嵌入的是纯js语句，express会帮你去解释它.    
    
```js
<%= code %>
```
中间的code仍然是js语句，但是这个js语句会有一个返回值，返回的内容即使你传入的内容，不会进行html的解析.    
    
```js
<%- code %>
```
与楼上很像，但是它返回的内容会被当作html解析。    
    
## 用bootstrap做一个快速的页面布局
bootstrap是一个非常好用的前端框架，它的用法网上有很多，我就不啰嗦啦，如果不太熟悉boostrap的小伙伴，吃我一发安利噢～～～用力点这个------[玩转boostrap](http://www.imooc.com/learn/141)  ,这是大漠老师在慕课上的bootstrap基础课程，学完它，你就可以自己飞快地搭建漂亮的页面啦！     
因为懒（羞羞），也因为还没有用bootstrap进行过真正的项目开发，所以这次我用了一下boostrap。哎呀，不用不知道，一用吓一跳啊！真的好用～～～（捂脸）    

### bootstrap重要功能点复习
这个模块我要讲一讲开发过程中我遇到的比较厉害的boostrap的用法噢！
#### 响应式导航条
我先给你变个魔术吧！^_^    
如果你想自己玩，可以把我的项目代码克隆到本地，然后这么做:  

克隆项目到本地:    

```
git clone https://github.com/maruko0713/node-blog.git node-blog
```

进入项目根目录:    

```
cd node-blog
```

安装npm依赖:    

```
npm install
```
   
进入public文件夹:    

```
cd public 
```

安装bower依赖:    

```
bower install 
```

回到根目录:    

```
cd ..
```

运行入口文件^_^：    

```
node app
```

然后在你的浏览器里输入:    
    
```
localhost:3000
```
是不是就可以看到啦？^_^  
    
我登录了我的xixi账号，然后我的导航条在浏览器窗口大于等于768px的时候，长这个样子:    
![导航条](http://7xl4oh.com1.z0.glb.clouddn.com/node-project1.png)    
    
如果我把窗口缩小一下呢？    
![小的导航条](http://7xl4oh.com1.z0.glb.clouddn.com/node-project2.png)    
    
发生了啥????    
![小小的导航条](http://7xl4oh.com1.z0.glb.clouddn.com/node-project22.png)    
    
嘻嘻，如果你点一下这里，之前消失掉的导航条还是会出现噢！    
    
请出前端(ejs)代码:    

```html
<div class="navbar-header">
    <button class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
    </button>
    <a href="/" class="navbar-brand">WanBlog</a>
</div>
<div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
    <% if(user) { %>
    <ul class="nav navbar-nav navbar-left">
        <li><a href="/post">发表</a></li>
    </ul>
    <% } %>
    <ul class="nav navbar-nav navbar-right">
        <% if(user) { %>
            <li class="dropdown">
                <a href="#" class="dropdown-toggle" data-toggle="dropdown">
                    <%= user.username %>
                    <span class="caret"></span>
                </a>
                <ul class="dropdown-menu">
                    <li><a href="/u/<%= user.username %>">账户信息</a></li>
                    <li><a href="/logout">退出登录</a></li>
                </ul>
            </li>
        <% } else { %>
        <ul class="nav navbar-nav">
            <li><a href="/login">登录</a></li>
            <li><a href="/reg">注册</a></li>
        </ul>
        <% } %>
    </ul>
    <form class="navbar-form navbar-right" action="/search" method="get">
        <div class="form-group">
            <input type="text" class="form-control" placeholder="Search" name="title">
        </div>
        <button type="submit" class="btn btn-default">搜索</button>
    </form>
</div>
```

有点长，别害怕...    
因为我们给按钮添加了"navbar-toggle"类名，这使它在宽度小于768px的时候会显示，大于即消失。    
而data-toggle="collapse"这句，是写给bootstrap的js代码看的，它会实现我们在点它一下的时候，消失的菜单重新跳出。    
    
我们对菜单模块添加了类名collapse navbar-collapse,前者保证它在宽度变小时消失，后者可以实现它小屏和大屏显示下不同的样式变化。    
于是，响应式导航条就诞生啦！    
    
## mongodb的使用
mongodb的使用其实还是那几个套路，在[上一篇博客](https://maruko0713.github.io/2016/10/11/node_mongodb/)讲过的噢。    
    
## crypto模块的使用   
crypto模块可以帮助我们对密码进行加密.    
    
引入crypto模块:    
    
```js
var crypto = require("crypto");
```

加密:    

```js
var md5 = crypto.createHash("md5");
md5password = md5.update(password).digest("hex");
```
第一步我们设置加密的编码格式。第二步我们进行加密，digest中传入参数表明我们想要的返回格式，hex返回十六进制数据，latin1返回字符串，base64返回base64格式，什么都不传就返回buffer对象。    
## moment.js模块的使用    
momentjs可以用于时间日期的格式化～～    
    
引入momentjs:    

```js
var moment = require("moment");
```

我想格式化成中文:    

```js
var  time = moment(date参数).locale("zh-cn").format("LL")
```

打开终端 输入node 在解释器里自己试一下吧！^_^
    
locale返回的是一个所有信息都被中文化的moment对象，然后format是它的一个格式化方法～～～    
