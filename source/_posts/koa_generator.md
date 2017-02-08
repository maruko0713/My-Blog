---
title: koa框架核心--中间件的使用与实现
date: 2016-10-07
tags: [Node,"ES6"]
---

## 前言
当当当当！我又来说node啦！这次来聊一聊一个可爱的node框架------koa。koa框架的特点就是小，轻量。它不绑定任何中间件（就是说中间件你得全自己写啊），而它的核心也就是这个中间件怎么写的问题。所以今天我们来讲讲这个中间件。    
    
## 中间件的demo
额，这个我就直接上官网的例子嘛，它的形式大概是这样的：    

```js
var koa = require('koa');
var app = koa();

app.use(function *(){
  this.body = 'Hello World';
});
```

<!-- more --> 

让我们看向楼上，这个use函数，里面写入的这个回调就是一个中间件。这是中间件“安装”的办法，中间件的关键特性在这里:    

```js
var app = require('koa')(),
    router = require('koa-router')();

app.use(function *(next){
    console.log(1);
    yield next;
    console.log(5);
});
app.use(function *(next){
    console.log(2);
    yield next;
    console.log(4);
});
app.use(function *(){
    console.log(3);
});

app.listen(3000);
```

楼上的接收到请求后的输出结果，熟悉koa和ES6规范中生成器的小伙伴应该知道，是12345.
当遇到yield语句时，中间件往下跳，跳进第二个安装上去的中间件，输出一个2又往下跳一下，依此类推。    
那么中间件机制是咋实现的呢？为啥我输个yield就能往下跳了呢？    
其实这个next，这里我们可以看作是next(),而next就是一个生成器函数，它返回一个iterator。

## koa中间件的实现－－compose
compose是koa源码中的一个模块.
compose函数中有这么个玩意儿:    

```js
while(i--){
    next = middleware[i].call(this, next);
}
yield *next;
```



看到了吧,next就是一个迭代器对象，哈哈，所以我们yield的时候自然可以进入这个迭代器对象啦。
详细来说，compose中把所有待执行的中间件写入一个数组，然后next就是数组中下一个中间件产生的一个迭代器对象，因此当我执行yield next时，可以跳进下一个中间件执行里面的代码，直至执行到一个中间件它里面的代码不再往下跳，执行完这个中间件的内容之后，它就开始回跳，逐层向上去执行离它最近的调用方的代码，一直到执行回第一个中间件yield next下面的代码为止。    


