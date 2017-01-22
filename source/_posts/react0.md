---
title: React中的JSX和数据流
date: 2017-01-02
tags: ["React"]
---
## 前言:
新年快乐!^_^    
敲代码，看书，写博客，一个学期不知不觉要过去了，考试周也如期而至。这学期主要的精力还是在做自己比较感兴趣的一些事情，包括整个后半学期都在看算法（因为暂时还没想好算法这块的博客该怎么展开叙述，就先忍忍了，还是安静地刷题吧），所以现在开始必须好好看学校的课程了。这几天会适当减少写文章和看技术书籍的时间，不过该看的还是要看的，嗯！      
然后说说这个React，React,Vue和AngularJs应该是现在比较主流的几个框架了，能活到这一步的框架都有自己厉害的地方，所以我的想法是应该尽量地去了解。从这一篇开始，我会对框架的学习做记录，在此之前我用Angular写过一个小项目，但是在那个项目中，仅仅是用了一些皮毛，目的在于快速地给一个系统写出前端。所以，React是我接触的第一个前端框架。  
   
## JSX
JSX在React中主要是render方法中写模版的时候要用到。    
<!-- more --> 
我第一次见到JSX的时候，只是觉得它把js和html揉在一起了，事实上也确实是这么回事。   
JSX的官方定义是***类XML语法的ECMAScript扩展***。   
看一个🌰：    
   
```html
const List = () => (
  <div>
    <Title>This is title</Title>
    <ul>
        <li>list item</li>
        <li>list item</li>
        <li>list item</li>
    </ul>
  </div>
);
```
在React中写JSX的时候，要注意:   
- 定义标签的时候，只允许被一个标签包裹
- 标签一定要闭合
    
### JSX的元素类型
在React中，我们有组件这一说，元素分为DOM元素和组件元素。   
组件元素就是我们自己定义的元素。    
在JSX中，DOM元素的元素标签首字母是小写，组建元素的标签首字母是大写。    
  
🌰：   
   
```html
const Title = (children) => (
  <h3>{children}</h3>
);
```
其中Title就是我们定义的一个组件，然后h3就是html本身的DOM元素。   
   
### JSX元素属性
在JSX中，无论DOM元素还是组件元素，它们都有属性。   
DOM元素的属性是标准规范属性，但是有两个例外，就是class和for。  
在js中，这两个单词是关键字，所以需要一些替换，把class替换为className,把for替换为htmlFor。    
组建元素的属性其实就是参数：     
   
```html
const Header = (title, children) => (
  <h3 title={title}>{children}</h3>
);
```
使用的时候:   
   
```html
<Header title="hello world">helloword</Header>
```

### 自定义属性
在JSX中往DOM元素中传入自定义属性的时候，React是不会渲染的。   
如果要使用HTML自定义属性，就要使用data前缀。    
🌰：    
   
```html
<div data-attr="xxx">content</div>
```

而自定义标签中，任何属性都是被支持的：    
   
```html
<MyComponent custom-attr="foo">我是自定义标签</MyComponent>
```


## React中的数据流
React中的数据流主要说的是state和props。  
顾名思义，state就是状态,props是属性。状态多变而属性相对稳定～～～   
如果顶层组件初始化props，那么React会向下遍历整棵组件树，重新尝试渲染所有相关的子组件。   
而state只关心每个组件自己的内部的状态，这些状态只能在组件内改变。
   
### props
属性一个主要的用法就是利用属性给组件元素传递数据。    

🌰：    
我的子元素这么写：   

```html
"use strict"
import React from "react";

class Comment extends React.Component {
  render() {
    return (
    <div className="comment">
      <div className="content">
        <span className="author">{this.props.author}</span>
        <div class="metadata">
          <span className="date">{this.props.data}</span>
        </div>
        <div className="text">{this.props.children}</div>
      </div>
    </div>
    );
  }
}

export { Comment as default };
```

它的父元素是一个叫做CommentList的东西：    
   
```html
"use strict";

import React from "react";
import Comment from "./Comment";

class CommentList extends React.Component {
  render() {
    return (
      <div>
        <Comment author="小丸子" date="5天前">热死啦</Comment>
        <Comment author="大丸子" date="5天前">热死啦</Comment>
      </div>
    )
  }
}
```

在父元素中，author，date属性都是在元素开始标签中附加的，this.props.children就是它开始和闭合标签之间的内容。    
如此，我们便利用属性把数据传递给了子元素。   
    
### state
在组件内部我们可以用state这个属性来访问到它的状态。    
我们还可以调用this.setState方法来为组件设置状态。   
举个🌰：    
    
比如有个组件叫做CommentBox:   
   
```html
"use strict";

import React from "react";
import CommentList from "./CommentList";
import CommentForm from "./CommentForm";

class CommentBox extends React.Component {
  constructor(props) {
    super(props);
    this.state = {data: "test"}; //初始化state
  }
  render() {
    return (
      <div className="ui comments">
        <h1>评论</h1>
        <div className="ui divider"></div>
        <CommentList data={this.state.data} />
      </div>
    );
  }
}
```

如此，我们就完成了一次对state的操作。    
   
#### setState方法
当我们使用setState方法的时候，最大的表现行为就是该组件会尝试对自己进行重新的渲染。    
举个例子：   
   
```html
import React, { Component } from "react";

class Counter extends Component {
  constructor(props) {
    super(props);

    this.handleClick = this.handleClick.bind(this);

    this.state = {
      count:0;
    }
  }

  handleClick(e) {
    e.preventDefault();

    this.setState({
      count: count - 1;
    });
  }

  render() {
    return (
      <div>
        <p>{this.state.count}</p>
        <a href="#" onClick={this.handleClick}>更新</a>
      </div>
    )
  }
}
```

像这样做出来一个组件，每当我们点击a链接的时候，数字都会发生改变。    
注意，setState是一个异步方法。    
    
## 总结
React中，props让组件相互联系，state关注组件自身的变化，props可以看作是构造函数的参数一样，state则是内部的一种状态。     
下一篇，可以聊聊React的生命周期和事件系统～^_^


