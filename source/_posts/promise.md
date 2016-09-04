---
title: 告别回调地狱－－ES6标准中的Promise对象
date: 2016-08-30
tags: ["ES6", "javascript"]
---

## 前言:    
最近在阅读一本很好的开源电子书------[阮一峰老师的ES6入门](http://es6.ruanyifeng.com/#docs/promise#Promise-all),其中对这个Promise对象印象很深刻，而且刚好最近写一个小demo用到了，今天来研究一下它。  

## 初识Promise对象
正如标题所说,Promise对象的出现，是为了解决层层嵌套的回调地狱。当回调函数太多的时候，会使代码的结构变得糟糕，读代码的时候，你得一层一层扒着往里找半天才能找到自己想看到那个功能效果对应的回调，改代码的时候更糟心，删除一个功能还得瞻前顾后连着外函数和内函数一起修改。总之，回调一多，事情就变得麻烦了。    
上面blabla这么多，我们来看阮一峰老师对Promise的语法上的定义:    
>从语法上说，Promise是一个对象，从它可以获取异步操作的消息。Promise提供统一的API，各种异步操作都可以用同样的方法进行处理。

更进一步地，Promise对象代表一个异步操作，它有三种状态（这一点等一下会在代码里演示）,这三种状态分别是:     
- Pending(进行中)
- Resolved(已经完成)
- Reject(已失败)
至于现在你到底处于什么状态，要看异步操作执行到哪个结果。

关于状态的重要一点:    
一旦状态改变（从Pending变为Resolved或者从Pending变为Rejected），那么就走到终点了，状态就定格在这里，不会再改变了。

## 自己对Promise的感性认识
其实Promise并没有为我们省去多少代码，也没有实现什么功能，但是Promise好就好在它使我们的代码更加“好看”，没有那么多“套路”了，一条直线贯穿了整个异步操作的所有操作流程，同时每个流程内部的代码从视觉上来看是相互独立的，不会说修改的时候“牵一发而动内外”，让代码更加好读，也更加容易维护。总而言之，Promise为我们编写具有多层回调的函数提供了一种更优雅的书写方式。

## Promise特性在代码中的体现
让我们来实现一个小小的Promise实例：    
```js
 var promise = new Promise(function(resolve,reject) {
    console.log("promise");
 });
 ```
 这里我新建一个Promise对象，里面传进的参数就是我第一步首先要执行的那个最外层函数的内容，resolve参数表示执行成功的情况下传入的嵌套函数1号，对应Resolved状态，reject表示执行失败的情况下传入的嵌套函数2号，对应Rejected状态。当然，我现在啥也没传，所以这两个形参暂时是个摆设。让我们来看一下运行的效果:    
 ![promise1](http://7xl4oh.com1.z0.glb.clouddn.com/promise1.png)    
 看，我们仅仅是new了一个实例出来，它居然顺便直接把我们传进去的函数内容给执行了！从这里我们可以看出，当我们创建Promise实例时，传入的函数内容是可以立即执行的，此时因为没有执行resolve函数和reject函数，我们的Promise对象状态就是Pending.
 那这个构造函数可以把我们的函数内容执行到什么程度呢？先看下面一段代码:    
 ```js
 var promise = new Promise(function(resolve,reject) {
    console.log("promise");
    resolve();
    console.log("没有执行resolve");
 });
```
执行结果:    
![promise2](http://7xl4oh.com1.z0.glb.clouddn.com/promise2.png)    
神奇的事情出现了，我们调用了一个根本不存在的函数，正常情况下，应该是要报错的，但是并没有，反而执行完了除不存在函数之外的所有代码。这是因为，当我们调用Promise对象的构造函数时，它会把未定义函数的执行代码孤立出来，跳过它们，然后执行别的代码。那么如何执行resolve函数和reject函数呢？这时候就需要请出它的then方法了：    
```js
 var promise = new Promise(function(resolve,reject) {
    console.log("promise");
    var value = "resolve";
    if(value=="resolve"){
        resolve(value);
    } else {
        reject(value);
    }
    console.log("我还会输出哦");
 });
promise.then(function(word) {
    console.log(word);
 },function(word) {
    console.log(word);
 });
 ```
 在上面的then方法中，我们传入了resolve和reject两个函数的定义,于是Promise对象就把对应的resolve函数的执行结果执行出来了:    
 ![promise4](http://7xl4oh.com1.z0.glb.clouddn.com/promise4.png)    
 由此我们可以推断这个过程中发生的事情:构造函数一气呵成跑完了传入函数的所有代码，当跑到调用未定义函数的地方，不管这个未定义函数是谁（po主尝试传了一个未定义的xixi(),也没有报错，说明根本不执行它）,先把整个函数跑完再说,如果这个未定义函数名为resolve或者reject,它内部应该就会给一个属性赋值应予执行的那个resolve或者reject函数的函数名，在上面这段代码中，这个属性存储的应执行函数名是resolve，当调用then方法的时候，then方法会把这个属性值对应的函数给执行了，当然同时也会执行构造函数中乱入的一些其它回调函数（比如xixi(),这样就会报错了,当然一般我们是不传其他乱七八糟的函数的）,于是大功告成，皆大欢喜～～     
 现在我们更进一步，研究一下resolve函数和reject函数的关系。     
 上面那段代码中，我们的if语句人为控制了resolve函数和reject函数的执行，使它们只能执行一个。那如果我这样写呢:    
 ```js
var promise = new Promise(function(resolve,reject) {
  console.log("promise");
  var result = "result";
  resolve(result);
  reject(result);
  });
promise.then(function(result) {
    console.log(result+":resolve");
 },function(word) {
    console.log(result+":reject");
 });
 ```
 在这段代码里，我们很强势地告诉Promise对象，你既要给我执行出个resolve,也要给我执行出个reject！    
 那么Promise对象是怎么想的呢：    
 ![promise5](http://7xl4oh.com1.z0.glb.clouddn.com/promise5.png)    
 它说，你想多了，我只能执行一个，我就不干了。    
 这段代码用事实说话，告诉我们，状态只要改变一次，你就别想让它改变第二次了，那么这个特性背后隐藏着什么原因呢？    
 这要从Promise中then方法的返回对象说起了:    
 我们在上面一段代码的后半段改成这样，输出then方法的返回对象p:     
 ```js
var p = promise.then(function(result) {
    console.log(result+":resolve");
},function(word) {
   console.log(result+":reject");
});

console.log(p);
```
输出：    
![promise6](http://7xl4oh.com1.z0.glb.clouddn.com/promise6.png)
有个小细节是p在then方法之前输出了，因此也印证了Promise对象代表一个*异步操作*，不影响后续代码的执行.因此我们这里输出p的时候，p还没执行完，于是返回了一个pending中的Promise对象，我们给它点时间缓缓:    
```js
setTimeout(function(){
    console.log(p);
},1000);
```
输出:    
![promise7](http://7xl4oh.com1.z0.glb.clouddn.com/promise7.png)    
我们看到，p仍然返回一个promise对象，不过等等，为啥你给我的描述是个undefined？？？  
![黑人问号](http://7xl4oh.com1.z0.glb.clouddn.com/bq.jpg)    
莫慌，我们修改一下resolve函数的返回值:    
```js
var p = promise.then(function(result) {
   console.log(result+":resolve");
   return  "我是resolve的return值";
},function(word) {
   console.log(result+":reject");
});
```
输出:    
![promise8](http://7xl4oh.com1.z0.glb.clouddn.com/promise8.png)    
机智的我们发现，返回的都是Promise对象，区别在于Promise对象的描述内容，也就是valueOf()的返回值,如果状态为Pending，返回为Pending,如果是resolve或者reject，那么根据resolve或者reject的return值进行返回。(如果resolve或者reject返回的也是一个Promise对象，那么里面的描述还是undefined~~~,因为undefined是一个非pending状态的Promise对象的valueOf()返回值).
综上所述，改变状态后，Promise对象就立刻return了，谁还给你往下走啊！（冷漠脸）    

“返回值都是Promise对象“这一点，使得Promise对象具有链式调用的优良特性:  
```js
var promise = new Promise(function(resolve,reject) {
    console.log("promise");
    var result = "result";
    resolve(result);
    reject(result);
});

promise
    .then(function(result) {
        console.log(result+":resolve");
        return  new Promise(function(resolve,reject) {
            console.log("resolve1号返回了Promise对象，我是它的构造函数内容");
            resolve();
        });
    },function(word) {
        console.log(result+":reject");
    })
    .then(function(){
        console.log("resolve1号嵌套的resolve2号执行啦");
    });
```
输出:  
![promise9](http://7xl4oh.com1.z0.glb.clouddn.com/promise9.png)    
这样一来，不管有多少层嵌套，我们只需要调用层层返回的Promise对象的then方法就可以啦!    










