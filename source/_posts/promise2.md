---
title: node碎碎念之Promise之静态方法promisify,resolve和reject
date: 2016-10-04
tags: ["Node","ES6"]
---
## 前言
之前写过一篇关于Promise的文章，介绍了Promise对象的创建，特性等一些基本的知识点。最近用node进行微信公众号实际开发的时候才发现原来Promise的应用到了node这边真是漫天飞呀，今天先说几个用的多的。    
<!-- more --> 
    
## Promise.resolve和Promise.reject
先从好啃的写起，抛砖引玉！    
这是两个静态方法，第一次见到它们，我是懵比的。因为我对Promise的认知仅仅局限于在构造函数里面输入一个小小的"resolve/reject（可选参数）"然后就改变Promise对象的状态，长成这样的我真没见过。感谢万能的吃瓜群众，我找到了它们的内涵:    
Promise.resolve()等价于这样:    
   
```js
new Promise(function(resolve){
    resolve();
})
```

同理Promise.reject()等价于下面这样:    
    
```js
new Promise(function(resolve,reject){
    reject();
})
```
    
看到这里，是不是恍然大悟了？    
因此：     
Promise.resolve()/Promise.reject()的作用即使创建一个Promise对象并将其状态置为resolved/rejected。    
用处：    
好处大大的有啊！想一想，有的时候我们并不care你的Promise构造函数内的主体是干啥的，只是关心then里面的内容是干啥的，这种情况下，我们省略Promise构造函数内参数的书写，直接来一个静态方法就把它的状态整出来了，这样我们是不是就只用操心我们该操心的then了？？      
    
    
## Promise.promisify    
(论科学上网的重要性hhhh)虽然查这个东西花去不少时间，但是最后还是有搜获滴！让我们来看看这玩意儿到底是个啥！    
    
### promisify方法的用途    
>prmoisify的作是：将一个 nodeCallback 形式函数调用转为 promise 对象。
   
写到这里有人要问，啥是nodeCallback?    
   
### nodeCallback形式的函数    
nodeCallback 是Nodojs中的一个常用词。表达是意思是：Nodejs中，以错误优先的回调函数的总称。    
这么描述还是有点反人类，它应该长这样：    
   
```js
function fooCallBack(err, a, b){
  // something
  console.log(a,b);
}
```

重点是啥？    
重点是第一个参数是err!! 也就是说,noodeCallBack 函数的第一个参数__一定__传入的是 err ，而不能是别的参数。只要满足了这个条件，同时满足了回调函数出现的顺序在异步函数的最后一个，都能称之为nodeCallback函数。    
   
比如我们经常见面额readFile小姐:    
   
```js
fs.readFile('./test.js',function(err,data){
  console.log(data);
});
```
   
看到没？回调函数位于最后一个参数，回调函数的第一个参数为err,这样的函数就叫做nodeCallback函数.    
    
### promisify方法的好处    
好处大大的有啊！有了可爱的promisify方法，我们就可以把nodeCallback函数转换为高级的Promise对象，对它进行各种Promise对象的操作，使它具备各种Promise对象才有的高级操作，代码更优雅，书写更舒服。。。。等等等等，总之，还是要善待这个方法！    
    
### promisify的加强版promisifyAll    
promisify 只能一次转一个函数，那 promisifyall 的作用就是一次把一个库的方法转完,并且给他们都加上一个叫做Async的后缀。     
    
拿nodeCallback函数云集的fs库开刀:   
   
```js
const Promise = require('bluebird');

const fs = Promise.promisifyall(require('fs'));

fs.readFileAsync('./test.js').then(function(data){
  console.log(data);
}).catch(console.log);
```

楼上的代码运行是没有问题的，由此我们确信，promisify方法可以把传入的对象的所有nodeCallback方法全部转换为promise对象并且给它们取一个有Async后缀的新名字～～    
    
 

