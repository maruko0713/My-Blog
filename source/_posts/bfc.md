---
title: 从实际应用的角度认识BFC
date: 2017-11-25
tags: ["css"]
---
## 前言:
BFC，即块级格式化上下文。    
对BFC好奇起源于工作当中的两个现象，一个是垂直方向上的边距重叠问题，一个是清除浮动。   
其实这俩现象在曾经的我眼里都是非常正常并可以理解的现象，前两天同事突然提到说这个其实是可以用BFC解决的。今天没啥事，刚好有空坐下来瞅瞅这个BFC。     

帮助与支持：    
感谢网易考拉前端团队的[学习 BFC (Block Formatting Context)](https://juejin.im/post/59b73d5bf265da064618731d)这篇文章。

## 什么是BFC
>Formatting context(格式化上下文) 是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。

具有BFC特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。

从应用的角度出发，我对BFC有以下认知：   
- 同一个BFC下外边距会发生重叠
- BFC可以包含浮动的元素（清除浮动）
- BFC可以阻止元素被浮动元素覆盖

## 触发BFC

满足下列特点的元素可以被创造出BFC：   
- body 根元素
- 浮动元素：float 除 none 以外的值
- 绝对定位元素：position (absolute、fixed)
- display 为 inline-block、table-cell、flex
- overflow 除了 visible 以外的值 (hidden、auto、scroll)

## BFC的应用
### 解决边距重合问题
举个例子：    
```html
<div class="father">
  <p></p>
  <p></p>
  <p></p>
</div>
```

对应style我们这么写:    
   
```html
<style>
.father > p {
  margin-top:10px;
  margin-bottom:10px;
  height:100px;
  width:100px;
  background: red;
}
</style>
```

我们看到的效果是这样的：  
![外边距重叠](http://7xl4oh.com1.z0.glb.clouddn.com/3A187677-A963-45B3-BC26-74734A3805C1.png)
这是因为，同一个BFC（body根元素）下的元素会发生外边距重叠。
我们把p放在不同的BFC里即可：    
   
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
    p {
      margin-top:10px;
      margin-bottom:10px;
      height:100px;
      width:100px;
      background: red;
    }
    .child {
      overflow: hidden;
    }
  </style>
</head>
<body>
  <div class="father">
    <div class="child">
      <p></p>
    </div>
    <div class="child">
      <p></p>
    </div>
    <div class="child">
      <p></p>
    </div>
  </div>
</body>
</html>
```

如此，我们就避免了外边距重叠的问题： 
![外边距重合的修正](http://7xl4oh.com1.z0.glb.clouddn.com/2FEC6BDE-8D41-4301-A010-9353D1C7B1EF.png)

### 清除浮动   
🌰：   
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
    .left {
      float:left;
      height: 200px;
      width:200px;
      background:yellow;
    }
    .father {
      border: 1px solid black;
    }
  </style>
</head>
<body>
  <div class="father"> 
    <div class="left"></div>
    <p>我是一只小毛驴</p>
  </div>
</body>
</html>
```

呈现的效果如下:    
![清除浮动前](http://7xl4oh.com1.z0.glb.clouddn.com/1647E7C5-144B-4B5E-930A-D677E0372E37.png)
因为浮动元素脱离了文档流，于是父元素的高度是由p标签撑起来的。    
   
我们在father类里增加一行overflow:hidden;即可看到：     
![清除浮动后](http://7xl4oh.com1.z0.glb.clouddn.com/4BB3E89F-FB12-471C-A3CC-3B9297B43AFF.png)
用overflow设置为非visible的方式清除浮动，原理就是创建一个BFC，这里是利用了BFC内部可以包住浮动的元素这个原理。    
    
### 使元素不被浮动元素覆盖
我们知道浮动元素的特点是，脱离文档流，但不脱离文本流。    
看一个例子：    
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
    .left {
      height: 100px;
      width: 100px;
      background: yellow;
      float: left;
    }
    .normal {
      height:200px;
      width:200px;
      background:red;
    }
  </style>
</head>
<body>
  <div class="left"></div>
  <div class="normal">我是一个测试</div>
</body>
</html>
```
效果如图：   
![增加BFC之前](http://7xl4oh.com1.z0.glb.clouddn.com/0A33FAAA-A2F4-4E4F-BDB4-3C375C9B74A5.png)
这符合我们对浮动的认知。   
但如果我们希望右侧元素完全不受左侧元素浮动的影响，我们可以利用BFC，比如说，我们可以尝试在normal元素的样式里写一个display:inline-block。
效果如下:    
![增加BFC之后](http://7xl4oh.com1.z0.glb.clouddn.com/7695A23C-C1B5-4E9D-8438-A02A11FFCB34.png)    
如此，我们便利用BFC实现了对浮动元素覆盖的避免。     
