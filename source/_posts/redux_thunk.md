---
title: 从redux-thunk的14行源码和dispatch函数的实现过程，说说我眼中的redux中间件
date: 2018-01-04
tags: ["redux","middleware"]
---
## 缘起
我是在去年暑假第一次有了“该用redux代替烦死人的props传递了”的概念后，在github上扒拉别人的redux项目看，然后第一次见到thunk这个中间件。后来一图重构，我记得我们的redux里是用了这个中间件。选用的契机是，文档和别人的开源项目告诉我们，它有用。。。。         
然后最近总是遇到一个相同的问题：如果我有一个异步请求，需要把请求回来的数据给放进store里，我该咋办？？？    
嗨呀，既然我都这么写了，肯定是用thunk中间件呀。。。。。。😂    

## Redux中间件
本来想贴个定义，结果查完百科上的定义，我感觉我可能用的都是假的中间件。。。所以这里就说说我自己的理解吧。      
中间件我一般是在后端里用的比较多，比如express框架，django框架里的中间件。“中间”，我的理解是，从服务器接收到request，到路由开始处理request这中间的“中间”。     
再说说这个redux中间件吧，类比来理解，重点说说“中间”。这里的“中间”，指的是从redux的action派发开始，到到达reducer之前的这段时机。    
   
### 为什么需要Redux中间件    
在redux-thunk的文档里，给了我们一个stackoverflow的链接，试图向我们解释，为啥我们需要使用这玩意。其中有一个回答开头这样说道：“Don’t fall into the trap of thinking a library should prescribe how to do everything.”。
**翻译过来就是，不要认为说一个库（redux）可以帮我们做任何事**     
说得再直接一点，redux还不够强，并不能满足所有的开发情境，要带上它的中间件，它才够强。   

    
## 初识：14行源代码
**Thunk中间件可以帮我们做什么**
回到正题，Thunk中间件做了什么，我们得从它的源代码说起：  
（之所以一直说14行 是因为我第一次看到的时候，被这段源代码惊到了。。。不仅短，而且容易懂，看完彻底不抓瞎了，特别开心）    
    
```js
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => next => action => {
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }

    return next(action);
  };
}

const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;

export default thunk;
```

<!-- more --> 
我来解释一下这段代码（虽然可能并不需要我解释。。。。/捂脸）
首先，thunk中间件抓住了我们派发的action对象，然后给它裹一层，在它的外层，把dispatch和getState方法传进来了。随后，对action的种类进行判断，如果action是一个函数，那么就把dispatch和getState都传进去，然后执行这个函数。     

    
然后说说这段代码的用意吧。它希望我们把action写成一个函数，于是它就可以利用裹一层这种方式把dispatch和getState给传进去了，于是我们就可以在action里派发对象和查看state状态了，于是action从一个手无缚鸡之力的对象，变成了一个可以操作state树的函数~      
    
所以Thunk的目的就是，帮助我们把action变成函数，从而把dispatch放在action这一步。我们可以注意到，Thunk中间件最后直接return了action的结果，根本没把action往reducer那里发送了。在action是function的情况下，reducer只能在一边自己玩。     
    
说得再清楚一点，Thunk把reducer踹开了，于是我们在action函数里为所欲为（也就是可以实现异步操作）。    
   
## 更进一步：为啥这样做（理解dispatch函数到底做了什么）
读完Thunk的源代码，我只想问：为啥要把reducer架空？reducer做错了什么？？？？      
对呀，因为我们以往的state更新操作，是在reducer里计算返回的。      
简单地理解的话，问题是在于，reducer这个东西，规矩比较多，它是一个纯函数，只允许我们在里面计算，state都不让你改，你还想发异步请求？？？？naive！/微笑       
    
退一万步说，就算没有这个规定了，你在reducer里发送个请求试试？要是能拿到数据，Thunk中间件原地吃翔好不好？？？     
为啥这么说，是因为reducer是一个同步的函数。要是还不清楚（当初我就没清楚），就得看看redux源代码里对reducer的一个处理了，这里我们贴一下dispatch函数的源码：    
    
```js
  function dispatch(action) {
    if (!isPlainObject(action)) {
      throw new Error(
        'Actions must be plain objects. ' +
          'Use custom middleware for async actions.'
      )
    }

    if (typeof action.type === 'undefined') {
      throw new Error(
        'Actions may not have an undefined "type" property. ' +
          'Have you misspelled a constant?'
      )
    }

    if (isDispatching) {
      throw new Error('Reducers may not dispatch actions.')
    }

    try {
      isDispatching = true
      currentState = currentReducer(currentState, action)
    } finally {
      isDispatching = false
    }

    const listeners = (currentListeners = nextListeners)
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i]
      listener()
    }

    return action
  }
  ```

  为了突出重点，其实可以只看这一段：      

```js
try {
  isDispatching = true
  currentState = currentReducer(currentState, action)
} finally {
  isDispatching = false
}
```
    
或者只看这一句：      
    
```js
currentState = currentReducer(currentState, action)
```

是的，死心吧，currentState要的是currentReducer的同步的返回值。reducer只能是个同步的，不可能async/await(这样currentReducer这位哥就是一个async函数了，它调用时前面必须有个await，然而没有），也不可能Generator/yield（那样返回的就是一个生成器对象，不是currentState想要的，就更离谱了，可拉倒吧）。        
     
综上所述，reducer只能是一个同步的东西，即刻返回，不会等任何人。  
    
而使用Thunk提供给我们的方案，action变成函数，这个函数随便你怎么折腾怎么调用，异步不在话下，歪瑞ok呀~~~！   

     
## 后记
是的，**因为reducer不能帮我们做太多**，所以我们要用中间件。   
这是Thunk中间件所教会我的，redux中中间件的意义。翻阅[redux文档的中间件部分](http://www.redux.org.cn/docs/advanced/Middleware.html)，我们可以看到中间件的作用，不仅仅是封装dispatch这么简单。这篇文章，也只是针对实际的需要，谈谈自己的体会~    

中间件可以认为是redux功能上的一个补充。有了它，redux的功能更加完整，也更能适应我们实际开发的需要~肥肠的棒！    









