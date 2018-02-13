
---
title: 在使用Redux重构应用之前:从React的context属性说开去
date: 2018-01-01
tag: ["Redux","React"]
---
## 前言:
### 和Redux不得不说的故事
（这一段是生活流水账 也就是写给自己看的废话 可以跳过不看 Redux直走下一个加粗小标题就可以~）       

第一次使用RN构建完整的上架应用,是去年四月份开始的[一图：图书馆的另一半](https://1ibrary.github.io)（[开源代码地址在这里](https://github.com/1ibrary/1ibrary-front-end)）这个项目。当时学React的时候搜索到的资源,基本上多多少少都会把Redux,React-Router捎带着讲一下。但是因为初次开发,我的思路比较原始,一直到重构的时候,才脱非入欧用起了方便快捷的[react-native-router-flux](https://github.com/aksonov/react-native-router-flux)这个插件,后面Redux刚开了个头,就把下面重构任务拆分给新加入的队友做了,于是和Redux的第一次理论到实践的飞跃就这么白白了。这两天开始和小伙伴做另一个叫做 [双生:遇见另一半的美好：）](https://github.com/oh-bear/2life)（众：踏马为啥总在写另一半？？？？：））这么一个新的无比文艺的开源项目,刚好前端需要做比较大规模的重构,终于有了写Redux的机会。肥肠激动。      
使用Redux写项目,最初思路是依赖于“模仿”,就是cosplay别人的项目或者看着文档来写demo。但是其实有一些地方我是不知道它在干啥的,只知道“没有它就跑不起来”,今天坐下来把迷糊的地方梳理了一遍,供日后的小丸子和小丸子的伙伴们回忆~   

### 为什么要用Redux

**因为有用相对更合适的办法管理全局状态的需要。**
React项目中,状态这一块可以分为本地状态和全局状态。本地状态我们直接用React实现给我们的state就够了。但是有一些状态,我们希望“牵一发而动全身”,就是说我在A组件里改了状态a,遥远彼岸的B组件里其实有个和状态a同步的数据,我们希望它一起变。以前,我们要想实现遥远的组件与组件间的数据传递,需要把props一级传一级,哪怕中间的那些层级根本不关心你A组件想对B组件说什么奇怪的悄悄话,它也必须完成传声筒的使命,把状态a附着在自己的props里,而苦逼的程序员（我们）也就不得不手写一堆a=this.props.a这样的垃圾代码。这样一个是**非常麻烦很伤手腕**,另一个就是乱乱乱乱乱,代码没有章法,读起来也很累很**伤眼睛** 。        
起初我是不想用它的。。。因为觉得能手动撸出来的事情,干嘛要花时间去学一个新的框架,实现完全一样的效果。 但是反复检查了几个demo并且请教了身边的大佬之后,我认为Redux至少有以下的好处: 
- 省时省力（如上文） 因为它实现了数据的跨层级传递 以前你只会一步步走 现在你会跳了！！！     
- Redux自身有很多规矩,这些规矩可以用来约束程序员,使得每一次数据的改变都有迹可循（因为有Action这个线索),并且每一个版本的state都会有一个版本的记录,一切都是有线索的,可以预测的,不会是杂乱无章的,神秘兮兮的,不会是写代码的人自己最后写迷了都不知道自己的props到底是从哪传哪了（比如我）。

下面的思路是用一个demo,来自问自答那些我纠结过的原理：     

```jsx
const App = () => (
  <Provider store={store}>
    <Counter/>
  </Provider>
);
````


<!-- more --> 
## Redux实现过程中的为什么们
### 从React的context属性来理解,为什么要在Provider里传store={store},Provider做了啥？？
我就不信就只有我自己是蒙圈的！！！   
我确实是蒙圈的,因为我之前对React的context的属性一无所知。    
context是这样的：[React中的context](https://doc.react-china.org/docs/context.html)    
然后,Redux其实很重要的一点是希望帮助我们实现这样一个效果,即我站在组件的第n层（n>=3）,仍然可以拿到第一层的store里的数据。     
于是就需要context来帮忙了,Provider包起里面的组件后Redux其实做了这么个事情,它把自己的childContextTypes设置了一下,如此一来,后面的第n层组件,如果想要拿到最外层的数据,不用大费周折,设置自己的contextTypes属性就可以了,就像楼上给出的文档里的示例那样。   
在实际开发中,我们是不需要知道context的存在的,因为Redux已经把一切都做好了。  
但是还是知道一下比较好。。。。😂    
    
### connect做了啥？？？两个参数是干啥的？为啥是它们俩不是别人？？？

connect的操作如下：    
   
```jsx
const mapDispatchToProps = (dispatch, ownProps) => {
  return {
    increase: (...args) => dispatch(actions.increase(...args)),
    decrease: (...args) => dispatch(actions.decrease(...args))
  }
}

class MyComp extends Component {
  render(){
    const {count, increase, decrease} = this.props;
    return (<div>
      <div>计数：{this.props.count}次</div>
      <button onClick={increase}>增加</button>
      <button onClick={decrease}>减少</button>
    </div>)
  }
}

const Comp = connect(mapStateToProps, mapDispatchToProps)(MyComp);
```

这是另一篇博客里截下的demo,我主要的关注点在connect函数做了什么,以及两个参数的意义和必要性。    
- mapStateToProps: 这个东西**有选择地取一部分状态,使其变成指定组件的props**。
为什么要有选择,因为props这个东西,每次更新,默认都会触发一次新的渲染。渲染开销很大（除非在生命周期函数里手动重新写个shouldUpdateComponent来阻止不必要的渲染,但是这样难道不是很累吗）。为了避免这些不必要的开销,那么不该出现在props里面的数据我们就让它安静,只添加那些值得我们为它花力气渲染的数据进去。     
- mapDispatchToProps: 这个东西意义很简单。有时候我们不止一次地需要派发同一个action,但是派发的代码有点长,而且重复的dispatch看的人眼花缭乱,所以我们不如**给这样的派发调用起个新名字**,也就是封装一下。楼上把dispatch(actions.increase(...args))长长的一句封装成了简单的increase,节约重复劳动的同时,代码也更加好看了,噢耶！   
    
tips：之前我疑问dispatch是啥时候从store上拿下来放进去的,其实这是connect的一个默认操作。state的绑定是需要我们选择的,所以我们手动绑定,但是dispatch没它不行,所以直接给你悄悄默认绑定了。     
    
## 后记
今天这篇,算是在开工之前,“从理论到实践的第一次飞跃”的开工报告～         
现在要立一个写完之后再回来看一遍然后再写一遍的的flag。。。。希望对Redux的误解和困惑能越来越少。。。     
当然,也希望我今天搞明白的事情,明天不要忘。。。😂     
    
新年快乐呀大家～～～～^_^       


