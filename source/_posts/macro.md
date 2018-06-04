---
title: 重新理解事件循环和异步队列：区分macro和micro
date: 2017-10-02
tags: ["javascript"]
---
## 前言
前两天看到一段代码，代码具体长啥样记不起来了，重点里面有个setTimeout（A君）,有个promise.then（B君）调用。我琢磨着大家都是异步，异步就是进队列等着，先进先出，那么肯定是A君的回调先执行，然后B君。结果那段代码跑出来是promise的调用先执行了。由此发现哈哈哈哈哈我自以为理解得很清楚的javascript异步队列其实理解得就是一坨。。。。。。:)      
感谢万能的知乎和简书，这次在没翻墙的情况下就读到了不少好文章。昨天晚上加今天早上，终于在吃早饭之前把异步队列这个micro和macro的辨析给整明白了。赶紧记下来。。。。    
    
## 重新理解事件循环：macro, micro 大不同
理解js的事件循环，不仅要知道有异步队列这么个东西，还要知道队列有两种，一种是macro(宏任务)，一种是micro(微任务)。在[简书这篇文章](https://www.jianshu.com/p/d2f672502966?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation)里，还给我们科普了：
>任务队列又分为macro-task（宏任务）与micro-task（微任务），在最新标准中，它们被分别称为task与jobs。

<!-- more --> 

ok,先记住这俩名字。然后我来梳理一下事件循环的过程:   
    
1. 全局上下文被推入调用栈，同步代码执行
2. 同步代码执行完了，栈空。然后把所有的micro任务给执行了（队列清空）。
3. micro执行完了。开始执行macro。队列里的任务可以有多个，这里只要执行完其中的一个就行了。
4. 然后，再开始执行micro，执行完。
5. 然后再开始执行macro
...    
...    
...    
一直执行到队列也彻底空了，啥也没有了，ok，循环结束。    
    
大致就是这么个流程，然后需要注意的是下面几点:    
   
>具体的分类规则：macro-task大概包括：script(整体代码), setTimeout, setInterval, setImmediate, I/O, UI rendering。
micro-task大概包括: process.nextTick, Promise, Object.observe(已废弃), MutationObserver(html5新特性)

(上面这段摘自知乎柳兮的一个[专栏文章](https://zhuanlan.zhihu.com/p/26238030)，这篇文章非常好，还给出了详尽的例子，帮了我大忙了。)   

 

## 后记
经过这么一折腾，异步队列，事件循环这一块，可算是整明白了。     
所以说平时不应该不舍得花时间理解原理，不然代码跑出来都不知道是咋错的，说不定还会怀疑电脑坏了。。。。/捂脸     
（逃。。。。。。。。。。。。。      

     




