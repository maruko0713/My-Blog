---
title: RN初相见,哇你好厉害呀&React前置知识
date: 2017-03-19
tags: ["React Native"]
---
## 前言
再次回到博客看下日期真是惊出一身汗我。。。。😂，好久没有记东西了。    
这两个月学算法去了。。。。。。      
忙成狗的三月，在被一个巨麻烦的专业课老师禁止我带电脑上课之后，写代码的时间就更紧凑了。。。。于是也好久没有弹琴，每天都处于炸毛状态～～    
忍过去就好了。。。。。       

扯远了。。。。。    
最近为了做一个app于是顺势开始接触rn,因为每次接触一个东西喜欢先把内容整理一下，于是我又开始写博客了。。。😂。   
其实算法也想写博客的，但是盯着屏幕良久发现自己竟然不知道除了贴代码还能说啥?????😂遂放弃。    
这大概是传说中的只可意会不可言传？？？？   
不明白。。。😂      
     
## 关于RN
这个东西简单来说就是一个基于React的前端框架，用它就能做出跨平台的app，既能在安卓系统上跑又能在ios系统上跑～～～   
虽然说是基于React，我之前粗略翻了些资料以为就是React语法换了个运行环境，然而现在看来不是这样。RN有许多方面是和移动开发相关的，这也是为什么有的人本身没有开发过前端项目，大概了解一些js，但因为很熟悉安卓或ios的开发模式，于是很顺利地上手了RN的原因。
   
## RN中很React的地方
这部分就是RN中React原封不动移植来的东西。  
可以算是预备知识吧～～      
   
### 组件化
复习！   
如何在React|RN中创建一个组件？？？？     
如果是ES6(大势所趋....这个是重点):    
   
```js
class HelloComponent extends Component {
    xxx() {

    }
    xxx() {

    }
    ...
    xxx() {

    }
}

export default HelloComponent;
```
继承Component类，然后在里面写生命周期方法。    
xxx就是方法名，比如render啊，constructor啊啥的。     
啥都能没有,render必须有～～    
    
<!-- more --> 

ES5就是这样:    
    
```js
var HelloComponent = React.createClass({
    xxx: function() {

    },
    xxx: function() {

    }
});

module.exports = HelloComponent;
```

这种注意就是用ES5对象的形式建立React组件了。    
而ES6中组件则是类的形式。     
    
### props和state

#### props
props,组件的属性，用来描述组件的特征。  

props进来的时候第一个处理是父类Component对它的处理:    
   
```js
class xxx extends Component {
    constructor(props) {
        super(props);
    }
} 
```

***props可以在父子组件之间传递***    
***props是只读的***    
虽然是只读的，但是万一这个属性压根没传，我们也可以给它先来个默认属性:    

```js
static defaultProps = {
    name: "miemie"
}
```
像这样，name属性如果没有给出来，我们就使用默认属性miemie。     

组件的属性本身可以是任意的值，但是有时候我们希望在组件属性被赋值的时候，能够对值的类型加以限制，这时候可以利用PropTypes这个属性进行类型检查：  
   
```js
static propTypes = {
    name: PropTypes.string
} 
```

这样一设定，如果你敢给name设置个数字比如233啥的，那就会呵呵噢～～   
当然，这个类型可是多了去了，详情请戳：     
[React官方文档对类型的罗列](https://facebook.github.io/react/docs/typechecking-with-proptypes.html)     
文档的语法使用了ES5,不过既然没有刻意更改，那么类型应该就还是那些～～    
   
#### state
props是不可改变的，就是说props一旦传进来，在组件内不可写。    
props是死的，不灵活的。     
对一个组件来说，固然有一些特性是死的，不可改变的，但是它一定也有一些总在变动的东西，我们称它为状态(state)。    
state就是喜欢变来变去～～～～     
state进来也是一般先被constructor处理一下:     
    
```js
class xxx extends Component {
    constructor() {
        super(props);
        this.state = {
            xxx:xxx,
            xxx:xxx,
            ....
        }
    }
}
```
你要是不想这么干，也可以在外面写:    
    
```js
class xxx extends Component {
    state = {
        xxx:xxx,
        xxx:xxx,
        ....
    }
    constructor() {
        super(props);
    }
}
```
都是对的。     
   
state没有那么多屁事，你想怎么改怎么改，也没有类型检查，它适合存储那些容易发生变化的变量。     
state是一个组件所独有的，就好像你突然感到头疼，不可能带着你的娃一起头疼一样，state是无法在父子组件之间传递的。    
只有根深蒂固的东西，对人来说的话就是基因，类比到react里面就是props,它是可以一代一代传递的～～～     
    
### 虚拟DOM和ref
React中的组件不能和DOM节点划等号，它是虚拟DOM,正是因为虚拟，所以性能才高。     
关于虚拟DOM这块，有很多人讨论过，但是很少有人能说清楚。有个文章不错，请戳：  
[React的虚拟DOM是个什么套路?](http://mt.sohu.com/20161019/n470722901.shtml)    
更深入一点，关于虚拟DOM背后的diff算法，可以戳:    
[React-为什么要使用虚拟DOM](http://blog.csdn.net/qiqingjin/article/details/51804138)     
   
然后说说ref,它是组件被渲染后，指向组件的一个引用，是一个特殊的属性。  
注意ref指向的是真实的DOM节点～～～            
在React中，我们可以这样用:     
    
```js
var MyComponent = React.createClass({
  handleClick: function() {
    this.refs.myTextInput.focus();
  },
  render: function() {
    return (
```
```
      <div>
        <input type="text" ref="myTextInput" />
        <input type="button" value="Focus the text input"         onClick={this.handleClick} />
      </div>
```
```js
    );
  }
});
```
通过this.refs.refName就可以获取一个真实的DOM节点。      
     
## 总结
这篇文章好像还是在说React.....    
不过也没差，这些东西到底还是要用的。      
下一篇博客会展开一下“RN中很RN的地方”～～～～ ^_^





