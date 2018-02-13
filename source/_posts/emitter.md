---
title: 不懂为什么，突然想写一个Event-Emitter
date: 2018-01-27
tags: ['vue', 'event']
---
## 前言
最近学习使用Vue进行团队项目的开发，看到main.js里有个叫bus的东西，觉得bus这个名字也太可爱了吧，想着它到底是啥呢🤔。   
读完之后知道，这里的bus就是在Vue里写一个Event-Emitter。Event-Emitter是一个很常用的东西，github有一个开源库叫做[mitt](https://github.com/developit/mitt)，就是专门来做这个事的。只有60行，读完之后觉得代码虽少，但还是有可圈可点的地方，所以有了这篇博客。

## mitt的思路
不仅是mitt的思路，其实bus也是这样，几乎所有的Event-Emitter都是这样的，理解了mitt的思路，我们就可以举一反三，mitt是这样的：    
首先创建一个对象，这个对象是一个大boss，在这里面存储我们的事件队列，队列（实现出来是js数组）主要有这么两类:     
- 按照事件类型划分，队列根据不同的事件名称为自己命名
- 特殊的'\*'队列，通配符在这里的意思是，不管你触发了哪一个类型的事件，在执行完指定事件类型对应的事件队列后，我都要执行*对应的那个队列里的事件们。它像一个小尾巴一样，只要有事件触发，不论你是谁，我都要跟在后面跑一跑。    
然后后面每当有事件触发，触发的过程就是一个函数出队的过程，依照我们绑定的顺序去一个个入栈执行它们。  
大概的思路就是这样子啦~  


## 写一个更完整的Event-Emitter
理解了mitt的思路，就可以自己尝试写一个类似的东西试试看了。     

相对于mitt的话，我下面实现的这个myEmitt增加了once（只监听一次事件的触发）和clear（清除所有的监听器）方法，具体的代码是这样的：   

<!--more-->
   
```js 
class myEmitt {
    constructor() {
        this.eventHome = {'*':[]}
    }
    on(type, handler) {
        if(!(handler instanceof Function)) {``
            throw new Error("哥 你错了 请传一个函数")
        }
        if(!this.eventHome[type]) {
            this.eventHome[type] = []
        }
        this.eventHome[type].push(handler)
    }
    emit(type, params) {
        if(this.eventHome[type]) {
            this.eventHome[type].forEach((handler, index)=> {
                handler(params)
                if(handler.once) {
                    this.eventHome[type].splice(index, 1)
                }
            })
        }
        this.eventHome['*'].forEach((handler) => {
            handler(type, params)
        })
    }
    off(type, handler) {
        if(this.eventHome[type]) {
            this.eventHome[type].splice(this.eventHome[type].indexOf(handler)>>>0,1)
        }
    }
    once(type, handler) {
        if(!this.eventHome[type]) {
            this.eventHome[type] = []
        }
        handler.once = true
        this.eventHome[type].push(handler)
    }
    clear() {
        this.eventHome = {'*':[]}
    }
}

module.exports = myEmitt
```

这段代码经测试是可以正常使用的。    
然后之所以要贴代码，是因为有一些可圈可点的地方，希望自己以后遇到类似的场景，可以想起来。     

## 可圈可点的小细节
### 无符号按位右移运算符 >>>
读过的代码还不是特别多，这是我第一次在语法书以外的地方见到这个东西。    
具体的语句是这一句:    
   
```js
 this.eventHome[type].splice(this.eventHome[type].indexOf(handler)>>>0,1)
 ```
为什么这么做?   
indexOf平时会被我们用来确定一个东西到底在不在数组里。在这行代码来看，我们的用意是在数组里找到对应的函数，然后把它给删掉。这里就有两种情况:   
- 数组里有这么个函数，我们返回了一个正常的索引，于是切除成功，万事大吉。这种情况>>>0对它不起作用   
- 数组里压根没有，这种情况不用>>>比较恐怖，因为splice方法喜欢把-1解读为当前数组的最后一个元素，这样子的话，在压根没有对应函数可以删的情况下，不管三七二十一就把最后一个元素给干掉了，有点鬼畜。    
\>\>\>就这点好，具体它干了啥，看一下运行结果：     
   
```js
let a = [1, 2, 3]
a.indexOf(4)
// 返回-1
a.indexOf(4) >>> 0
// 输出4294967295
a.splice(4294967295, 1)
// 啥也没干 a还是它自己
```

神奇，-1不见了，取而代之的是一个巨大的数，这个数是谁呢，在二进制下，它就是！     
    
```js
(4294967295).toString(2)
// 输出 '11111111111111111111111111111111'
```

就是它没错了，一个32位全是1的二进制数。 
为啥是这样，大家可以写一下-1的二进制，负数二进制这么算，先把1的二进制写出来，然后减1，取反，得到的就是11111111111111111111111111111111。    
前面说到>>>的作用是把一个数变成无符号的，然后再右移。这里明显没有右移，我们仅仅是想告诉js引擎，别再把这个数当做负数去解析了，直接返回它的二进制结果吧，于是就有了一个巨大的无聊的数。这个数splice是找不到的，找不到就不删，于是一切保持原状，皆大欢喜。     
妙不妙？    
妙呀！    
    
## 后记
今天的想法是，读一个库不能仅仅局限于读懂。    
楼上的myEmitt在写成之前改了好多次，如果仅仅是，读懂了，知道思路了，就pass，就失去了一个发现自己编码恶习的机会。比如我第一次写的时候，虽然也实现了所有的接口功能，要用forEach我用了map，且把eventHome写在全局导致每次new新对象的时候仍然只能使用唯一的一个事件队列对象，等等等等。    
要写出来才知道有没有懂。    
要写出来才知道有没有懂。 
要写出来才知道有没有懂。    
重要的事情说三遍。     
   
去吃晚饭^_^ 

    

