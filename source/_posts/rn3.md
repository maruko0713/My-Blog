---
title: RN|很RN的部分|其它组件篇
date: 2017-03-25
tags: ["React Native"]
---
## 前言
RN中有一些提前为我们写好的各种各样的其它组件。它们是:   
- TextInput 文本框组件
- ScrollView 滚动视图组件
- Touchable触摸交互组件

    
## TextInput
这个组件可以类比为HTML标签里的input标签。    

TextInput的常见属性有：
- placeholder 就和input标签的placeholder是一个东西
- value 是默认的文本框内的文本值
- multiline 默认为false 是说文本框内不会自动换行 设置为true反之
- password 表示是否是用*这个符号来显示你输入的内容
。。。。。。
常见事件比如:     
- onTextChange事件 接受一个函数，而此函数会在文本变化时被调用 改变后的文本会作为参数传入
- onChange事件 和上面那个唯一的区别就是 改变后的文本不会传入
其它各种乱七八糟事件都在这里:    
[react native文档之TextInput部分](http://reactnative.cn/docs/0.20/textinput.html)
   
tip: 注意这个在写的时候必须手动设置高度，不然高度默认为0，它几乎没用。。。。     
    
🌰：    
   
```xml
<TextInput
    style={{height:40,borderWidth: 1}}
    onChangeText = {(text)=>console.log(text)}
    placeholder={"测试一下"}
    multiline={true}
    />
```
    
    
<!-- more --> 


## ScrollView
>ScrollView是一个通用的可滚动的容器，你可以在其中放入多个组件和视图，而且这些组件并不需要是同类型的。ScrollView不仅可以垂直滚动，还能水平滚动（通过horizontal属性来设置）。
ScrollView适合用来显示数量不多的滚动元素。放置在ScollView中的所有组件都会被渲染，哪怕有些组件因为内容太长被挤出了屏幕外。如果你需要显示较长的滚动列表，那么应该使用功能差不多但性能更好的ListView组件。

其实这个应该和ListView放在一起写的。但是既然都分开了，就不改了。。。。😂      
SrcollView在使用的时候有两个要点:
- 必须有一个确定的高度才能工作(不建议直接给scrollView加高度，建议给它的父控件加高度)
- 响应事件时 它内部的其它响应者无法阻止它本身成为响应者    
   
常见的属性:
- contentContainerStyle  样式风格属性(传入StyleSheet创建的Style文件)。该样式会作用于被ScrollView包裹的所有的子视图。
🌰：    
    
```xml
return (
      <ScrollView contentContainerStyle={styles.contentContainer}> </ScrollView>
      );
   …
 var styles = StyleSheet.create({
          contentContainer:{
                   paddingVertical:20
               }
         });
```

常用的属性:    


- keyboardDismissMode：用户拖拽滚动视图的时候，是否要隐藏软键盘。
none（默认值），拖拽时不隐藏软键盘
on-drag 当拖拽开始的时候隐藏软键盘
interactive 软键盘伴随拖拽操作同步地消失，并且如果往上滑动会恢复键盘。安卓设备上不
项，会表现的和none一样。
- horizontal：当此属性为true的时候，所有的的子视图会在水平方向上排成一行，而不是默认的在垂直方向上排成一列。默认值为false
- showsHorizontalScrollIndicator：当此属性为true的时候，显示一个水平方向的滚动条
- showsVerticalScrollIndicator：当此属性为true的时候，显示一个垂直方向的滚动条
     
## Touchable触摸交互组件
Touchable 一共有四种形式：

- TouchableHighlight: 当按下的时候，封装的视图的不透明度会降低，同时会有一个底层的颜色透过而被用户看到，使得视图变暗或变亮。

- TouchableNativeFeedback:(仅限Android平台) 在Android设备上，这个组件利用原生状态来渲染触摸的反馈。

- TouchableOpacity: 当按下的时候，封装的视图的不透明度会降低。这个过程并不会真正改变视图层级，大部分情况下很容易添加到应用中而不会带来一些奇怪的副作用。

- TouchableWithoutFeedback: 所有能够响应触屏操作的元素在触屏后都应该有一个视觉上的反馈,然而本组件没有任何视觉反馈,仍会触发触摸事件的响应
    
触摸组件通用的常见属性\事件:   
- onPress事件 当触摸操作结束时调用，但如果被取消了则不调用（譬如响应者被一个滚动操作取代）。     
- onLongPress事件 当用户长时间按压组件(长按效果)的时候调用该方法。
- disabled属性 布尔值 如果设置为true 则禁止此组件的一切交互
- onPressIn/onPressOut 这两个方法分别是当用户开始点击按钮时与点击结束后被回调。
   
扩展出来的属性\事件：     
### TouchableHighLight
- underlayColor 设置TouchableHighlight 被按下去的颜色，默认状态下为balck黑色。
- onHideUnderlay事件 当衬底被隐藏的时候调用。
- onShowUnderlay 当衬底显示的时候调用。
 
### TouchableOpacity
- activeOpacity属性 同TouchableHighlight的activeOpacity。另外我们也可以通过TouchableOpacity的setOpacityTo(value, duration)方法来动态修改TouchableOpacity被按下去的不透明度。
   
注意:    
***无论是TouchableWithoutFeedback还是其他三种Touchable组件，都是在根节点都是只支持一个组件，如果你需要多个组件同时响应单击事件，可以用一个View将它们包裹着，它的这种根节点只支持一个组件的特性和ScrollView很类似。***
***默认的不透明度是0.85***

## 总结
今天这篇博客虽然没有很长，但我确实写了很久。。。。有一些不太确定的，都反复查了源码，参考了别人的文章。。。。。     
也是累瞎😂    
下一篇大概还是讲一下组件这块的东西。嗯嗯～～   
    
## 总结