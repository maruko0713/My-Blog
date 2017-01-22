---
title: css3碎碎念之好基友background-clip和background-origin
date: 2016-10-03
tags: ["css3","css"]
---
## 前言
额好吧其实这次前言没有什么可写的，如果真的要写些什么的话－－最近在复习css3啊哈哈哈哈。好的嘛其实这是一篇梳理文，也不一定哪天复习到哪里对那个属性来劲了，就写一篇。嗯，就是这样！下面让我们来近距离接触一下backgound-clip和background-origin这对好基友！    
<!-- more --> 
    
## 别忘了background-size  
是的是的，css3中一个很有用的一个可爱的background属性还有它background-size!虽然在话题之外，还是要点一下，这个属性用来设置背景图片的大小，很好用，谁用谁知道！    

## 所谓好基友
所谓好基友，就是说它们很像，不仅长得像（background-前缀），而且功能也类似嘿嘿。   
### background-clip    
用于设置背景*颜色*的占据面积。有三个属性:
- border-box(默认)
- padding-box
- content-box
   
上代码之前先来个解释吧:第一个border-box这个是默认的，就是说你不设置它，它背景颜色就默认显示颜色占据到border的外边沿，第二个padding-box这个是说占据到padding的外边沿，第三个content-box是说占据到内容区的外边沿。       
    
下面上码:    

html的body结构:    
~~~html 
<body>
    <div class="div1">嘻嘻哈哈好我是一个测试</div>
</body>
~~~
ps文字是在最后一个例子才有的，前面两个不需要。    
    
默认(border-box的情况)style:    

```css
.div1 {
    height:100px;
    width:100px;
    background:red;
    border:10px dotted blue;
    background-clip:border-box;
}
```
效果:    
![border-box](http://7xl4oh.com1.z0.glb.clouddn.com/border-box.png)
    
padding-box的style :    

```css
.div1 {
     height:100px;
     width:100px;
     background:red;
     border:10px dotted blue;
     background-clip:padding-box;
}
```
效果:    
![padding-box](http://7xl4oh.com1.z0.glb.clouddn.com/padding-box.png)    
看到没有，背景颜色是不是收回来了?    
    
content-box的style:    
   
```css
.div1 {
     height:100px;
     width:100px;
     background:red;
     border:10px dotted blue;
     background-clip:content-box;
     padding:20px;
}
```
效果:     
![content-box](http://7xl4oh.com1.z0.glb.clouddn.com/content-box.png)    
    
以上，这就是background-clip的用法啦！    
    
下面我们来讲讲background-origin的用法.     
三个属性:    
- padding-box(默认属性，注意和上面的border-box区分)   
- border-box 图片布局从border的最外侧开始
- content-box 图片布局从内容的最外侧开始      
     
html部分:     

~~~html
<body>
    <div class="div1"></div>
</body>
~~~

padding-box(默认)的css:     

```css
.div1 {
    height:100px;
    width:100px;
    background:url(http://img5.imgtn.bdimg.com/it/u=4155302816,15&fm=21&gp=0.jpg) no-repeat;
    border:10px dotted blue;
    background-origin:padding-box;
    background-size:100%;
}
```

效果:    
![padding2](http://7xl4oh.com1.z0.glb.clouddn.com/padding-box.png)
    
border-box的css：    
    
```css
.div1 {
    height:100px;
    width:100px;
    background:url(http://img5.imgtn.bdimg.com/it/u=4155302816,15&fm=21&gp=0.jpg) no-repeat;
    border:10px dotted blue;
    background-origin:border-box;
    background-size:100%;
}
```
    
效果:    
![border2](http://7xl4oh.com1.z0.glb.clouddn.com/border-box2.png)    
    
content-box的css:    

```css
.div1 {
    height:100px;
    width:100px;
    background:url(http://img5.imgtn.bdimg.com/it/u=4155302816,1201715785&fm=21&gp=0.jpg) no-repeat;
    border:10px dotted blue;
    background-origin:content-box;
    background-size:100%;
    padding:10px;
}
```
   
效果:    
![content2](http://7xl4oh.com1.z0.glb.clouddn.com/content-box2.png)    
    
## 总结    
俗话说，实践是检验真理的唯一标准。（啊怎么画风变得有点奇怪嘿嘿）。经过实践，虽然background-clip和background-origin是一对好基友，但是前者的实用性远远大于后者。原因很明显，我要定位图片，直接用background-position,不仅更加灵活，而且可以兼容更多浏览器啊，毕竟不是每个人都在用支持css3的浏览器嘛。所以在今后的开发中，我会更多的使用background-position而非新特性background-origin，以此来避免不必要的兼容性处理问题.    
