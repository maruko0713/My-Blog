---
title: 状态更新策略对比：React setState vs Vue nextTick
date: 2018-02-07
tags: ["React"]
---

## 前言
可能会是一篇有点长的源码阅读笔记。。。。。。      

之前Medium上有一位兄台曾经写过这样一篇文章:     

![medium博客标题](http://7xl4oh.com1.z0.glb.clouddn.com/1.png)   
这个标题说出了包括曾经的我在内的React开发者的心声。     
但是在读完[知乎杨森：React 源码剖析系列 － 解密 setState](https://zhuanlan.zhihu.com/p/20328570)这篇文章后，我不这么想了。    
具体setState的机制，在文中也有讲解，我之前也开博记录过自己的理解（[理解React中的setState](http://mieruko.cn/2017/09/25/setState/))。   
今天这篇文章意在介绍vue的异步更新策略，然后把两者放在一起做个对比。  


## setState源码细节拾遗
上一篇写setState的文章还是比较粗糙，其实setState源码里有很多~~陈独秀同学~~值得关注和学习的小细节，今天我就先把它们po一下~~~     
  
- Instance 分为 public 和 internal 两种。从而避免部分内部状态直接暴露于外部，有被修改的风险
- 状态队列在需要时才会被初始化，避免了不必要的开销
（以上两点见下图）     
![enqueueUpdate函数](http://7xl4oh.com1.z0.glb.clouddn.com/5.png)

- 通过批量更新的方式提高性能(如下)
![批量更新](http://7xl4oh.com1.z0.glb.clouddn.com/12.png)
  
<!-- more -->  
 
## Vue状态更新策略分析
我们知道React的状态更新是否异步，是需要看锁的状态的。如果锁是死的，那么就是异步；如果锁是开的，就可以即刻更新。     
但是在Vue里面，就简单粗暴许多了，没有什么锁不锁的，全部都是异步。     
大概的思路是这样子的：     
![Vue异步更新概述](http://7xl4oh.com1.z0.glb.clouddn.com/13.png)   
 
   
异步任务有两种，macro（宏任务）和micro（微任务），如果对这个不熟悉，[重新理解事件循环和异步队列：区分macro和micro
](http://mieruko.cn/2017/10/02/macro/)这篇文章也可以帮到你~~~~     

具体来说，Vue每次想要更新一个状态的时候，会先把它这个更新操作给包装成一个异步操作派发出去。  
这件事情，是在一个叫做nextTick的函数里完成的~~~   

以macro和micro的特性为前置知识，我们来看一下Vue的nextTick这部分源代码（分析在注释里）：  
 
```js
export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  // 当前的任务队列是否未派发为异步任务
  if (!pending) {
    pending = true
    // 是否要求一定要派发为macro任务
    if (useMacroTask) {
      macroTimerFunc()
    } else {
      // 如果不说明一定要macro 你们就全都是micro
      microTimerFunc()
    }
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
```

macro和micro任务的派发具体是这么实现的:     

macro:    
```js
// macro首选setImmediate 这个兼容性最差
if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  macroTimerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else if (typeof MessageChannel !== 'undefined' && (
    isNative(MessageChannel) ||
    // PhantomJS
    MessageChannel.toString() === '[object MessageChannelConstructor]'
  )) {
  const channel = new MessageChannel()
  const port = channel.port2
  channel.port1.onmessage = flushCallbacks
  macroTimerFunc = () => {
    port.postMessage(1)
  }
} else {
  // 兼容性最好的派发方式是setTimeout
  macroTimerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}
```

micro:
```js
// 简单粗暴 不是ios全都给我去Promise 如果不兼容promise 那么你只能将就一下变成macro了
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  microTimerFunc = () => {
    p.then(flushCallbacks)
    // in problematic UIWebViews, Promise.then doesn't completely break, but
    // it can get stuck in a weird state where callbacks are pushed into the
    // microtask queue but the queue isn't being flushed, until the browser
    // needs to do some other work, e.g. handle a timer. Therefore we can
    // "force" the microtask queue to be flushed by adding an empty timer.
    if (isIOS) setTimeout(noop)
  }
} else {
  // fallback to macro
  microTimerFunc = macroTimerFunc
}
```

ps这里提一下，其实nextTick这里micro的实现方法，以前有个文章分析过。当时我记得里面描述了好多种包括MO在内的micro派发方式。    
但是我刚去看最新版本的vue源码，确实只有promise，可能是因为浏览器们现在对promise支持得比较好了吧。。。。     
    
## 为什么Vue的nextTick优先派发micro任务
思考这个问题之前，我们需要一点更细致的前置知识:    
事件循环并非只有macro和micro的轮流交替，事实上，每一轮事件循环分三步走（macro,micro,渲染UI)。准确地说，是四步走，最后一步是对workerGlobal对象的一个判断。
但是这一步跟我们今天讲的东西没啥关系，大家有兴趣可以去了解一下[详细的事件循环模型](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model)。
    
优先派发micro任务，并不是空穴来风，而是有所权衡。比起macro任务，micro的优点有:     
  
- 优先级高，可以尽快的执行
- 考虑时间循环三步走的顺序。定义一个异步的 UI 修改任务，如果用Microtask，则可以在当前循环结束时见到最新的UI，而MacroTask中则需要等待渲染两次才能拿到最新的UI     
    
 
## setState vs nextTick
了解了React和Vue各自的状态更新机制之后，我们可以察觉到它们各自的特点:   
   
Vue:     
- 依赖浏览器Api与事件处理队列
- 不可控(我们无法通过编码改变它)
- Vue自动优化，无论是否是Vue发起的调用（全都是异步的）

React:
- 纯JS实现，不依赖浏览器Api
- 可控性强，可手动调用(因为可编码)
- 是否异步需要看具体场景，易出错(需要对源码有了解)    
    
## 后记
这篇文章是基于我在团队分享会上讲的一个ppt来改制而成的。   
当时我刚把ppt讲完的时候，说Vue状态更新这一点决定它不适用于较大规模的项目。    
但是"较大规模"到底有多大，难以定义。兵哥说我们现在开发的这个应用的规模，Vue是可以支撑的。    

从状态更新这一点我们也可以看出，框架各有千秋。Vue轻，React全，并不只是说说而已。     

