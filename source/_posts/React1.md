---
title: React组件生命周期的总结
date: 2017-01-04
tags: ["React"]
---
## 前言:
在React组件的整个生命周期中，随着该组件的props或者state发生改变，其DOM表现也会有相应的变化。一个组件就是一个状态机，对于特定地输入，它总返回一致的输出。    
一个React组件的生命周期从广义上看分为三个部分:   
- 挂载
- 渲染
- 卸载
当渲染后的组件需要更新时，我们会重新去渲染组件。因此，我们也可以把React生命周期分为两类:    
- 当组件在挂载或卸载时
- 当组件接受新的数据，即组件更新时
    
不过我还是更加喜欢这个版本的生命周期解释哈哈哈哈哈：    
   
- 实例化
- 存在期
- 销毁&清理
   
一图胜千言：    
   
![react生命周期](http://7xl4oh.com1.z0.glb.clouddn.com/react%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)
<!--more-->

## 相关函数
在ES6中，一个React组件是用一个class来表示的：    
    
```js
class MyComponent extends React.Component {
    ...
}
```
只有你继承了Component类，其中定义的一些生命周期的相关函数才有意义。    
相关的函数有：    
   
```js
constructor(props, context)
```
这个是构造函数，在创建组件的时候调用。    
    
```js
void componentWillMount()
```
这个是在组件挂载之前调用一次。    
注意如果在这个函数里面调用setState,render函数就能看到更新后的state，并且只渲染一次。    
    
```js
void componentDidMount()
```
这个是在组件（包括子组件）挂载后调用的。    

```js
void componentWillReceiveProps(nextProps)
```
props是父组件传递给子组件的。    
父组件每次render子组件都会调用这个方法。   
这与props有没有更新，父子组件之间有没有数据交换是没有关系的。    
    
```js
bool shouldComponentUpdate(nextProps, nextState)
```
组件挂载之后，每次调用setState都会调用这个方法。   
这个方法用来判断是否需要重新渲染组件。   
默认返回值是true。    
如果是true，那么就再次render。
    
```js
void componentWillUpdate(nextProps, nextState)
```
当shouldComponentUpdate返回true活着调用了forceUpdate之后，这个方法会被调用。    
   
```js
void componentDidUpdate()
```
除非是第一次render之后调用了componentDidMount。   
其它render结束之后都要调用这个方法。    
    
```js
ReactElement render()
```
这个方法很重要，它是核心函数。   
上面的其它函数都不是必须的，但是它是必须的。    
***render里面不能修改state***

```js
void componentWillUnmount()
```
组件被卸载的时候调用的。   
一般componentDidMount里注册的事件会在这里删除。    
    

## 如何触发核心函数render?
一般有四种方法:    
   
- 首次渲染
- 调用this.setState
- 父组件发生更新
- 调用this.forceUpdate
    
一图胜千言:    
    
![触发render的方式](http://7xl4oh.com1.z0.glb.clouddn.com/%E8%A7%A6%E5%8F%91render%E7%9A%84%E6%96%B9%E5%BC%8F.png)
    
   
## 小结
这里的方法这块，我们可以做一个对应。    
componentWillMount, componentDidMount和componentWillUpdate,componentDidUpdate可以对应起来。    
区别是，前者只在挂载（也就是初次见面）的时候会被调用。    
后者在每次更新渲染之后都会被调用。   
另外之前看到的一个很不错的小🌰可以解释生命周期的，贴一下:    
   
(我真的要绝望了求助markdown如何正确显示es6代码？？？？？)    
(朋友们实在不行就选中点亮了再看吧！！！（捂脸逃。。。。。）)

```js
class LifeCycle extends React.Component {
    constructor(props) {
        super(props);
        alert("Initial render");
        alert("constructor");
        this.state = {str: "hello"};
    }

    componentWillMount() {
        alert("componentWillMount");
    }

    componentDidMount() {
        alert("componentDidMount");
    }

    componentWillReceiveProps(nextProps) {
        alert("componentWillReceiveProps");
    }

    shouldComponentUpdate() {
        alert("shouldComponentUpdate");
        return true;        // 记得要返回true
    }

    componentWillUpdate() {
        alert("componentWillUpdate");
    }

    componentDidUpdate() {
        alert("componentDidUpdate");
    }

    componentWillUnmount() {
        alert("componentWillUnmount");
    }

    setTheState() {
        let s = "hello";
        if (this.state.str === s) {
            s = "HELLO";
        }
        this.setState({
            str: s
        });
    }

    forceItUpdate() {
        this.forceUpdate();
    }

    render() {
        alert("render");
        return(
            <div>
                <span>{"Props:"}<h2>{parseInt(this.props.num)}</h2></span>
                <br />
                <span>{"State:"}<h2>{this.state.str}</h2></span>
            </div>
        );
    }
}

class Container  extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            num: Math.random() * 100
        };
    }

    propsChange() {
        this.setState({
            num: Math.random() * 100
        });
    }

    setLifeCycleState() {
        this.refs.rLifeCycle.setTheState();
    }

    forceLifeCycleUpdate() {
        this.refs.rLifeCycle.forceItUpdate();
    }

    unmountLifeCycle() {
        // 这里卸载父组件也会导致卸载子组件
        React.unmountComponentAtNode(document.getElementById("container"));
    }

    parentForceUpdate() {
        this.forceUpdate();
    }

    render() {
        return (
            <div>
                <a href="javascript:;" className="weui_btn weui_btn_primary" onClick={this.propsChange.bind(this)}>propsChange</a>
                <a href="javascript:;" className="weui_btn weui_btn_primary" onClick={this.setLifeCycleState.bind(this)}>setState</a>
                <a href="javascript:;" className="weui_btn weui_btn_primary" onClick={this.forceLifeCycleUpdate.bind(this)}>forceUpdate</a>
                <a href="javascript:;" className="weui_btn weui_btn_primary" onClick={this.unmountLifeCycle.bind(this)}>unmount</a>
                <a href="javascript:;" className="weui_btn weui_btn_primary" onClick={this.parentForceUpdate.bind(this)}>parentForceUpdateWithoutChange</a>
                <LifeCycle ref="rLifeCycle" num={this.state.num}></LifeCycle>
            </div>
        );
    }
}

ReactDom.render(
    <Container></Container>,
    document.getElementById('container')
);
```    
