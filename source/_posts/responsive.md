---
title: 响应式布局核心招数总结
date: 2016-09-11
tags: ["响应式"]
---
## 前言
暑假做了一个响应式页面的练习，然而前两天随着我硬盘的狗带，这个小练习也不见了，以至于现在要开始重写一遍作为复习。在重写的过程中，针对响应式这个概念就自己的理解做一下总结。    

## 响应式布局的概念
响应式布局(Responsive Web Design),这个概念在2010年由Ethan Marcotte提出，指的是
>可以自动识别屏幕宽度、并做出相应调整的网页设计

## 响应式布局的特性
### 自适应屏幕的网页宽度
牛逼哄哄的响应式布局第一步，就是这一行:    
```html
<meta name="viewport" content="width=device-width,initial-scale=1" />   
```
这行代码是对视口的宽度做一个设定，这个视口指的是布局视口(layout viewport)，其实就是你页面本身的宽度，这个宽度要和设备宽度(device-width)贴合，随着屏幕的变化而变化,initial-scale=1是说这个页面的初始化状态是原有大小（因为没有设置不允许用户缩放，后期用户可以缩放这个页面）。    
当然，可爱的IE6,7,8是不支持这玩意儿的:  
```html   
<!--[if lt IE 9]>
<script src="http://css3-mediaqueries-js.googlecode.com/svn/trunk/css3-mediaqueries.js"></script>
<![endif]-->
```
需要我们用上面的js文件来弥补这个特性。    
    
### 使用相对单位和相对宽度
既然是变化的网页，那么宽度和元素大小必定都是随着我们布局大小的变化而变化，所以绝对不能再用px把大小写死，要使用灵活的相对单位和百分比来解决:    
```css
width:x%;
```
或者让width自动继承宽度:    
```css
width:auto;
```
当然还可以用em，rem。    
总之别用px就行😂.    
```css
font-size:1.5em;
```
或者 
```css
font-size:150%;
```
上面两行一个意思，是说字体大小是其父元素的1.5倍大。    
这里提到一下rem这个单位，rem永远相对于html设定的字体大小，而em相对于它父元素的元素大小。  
#### chrome下的字体设置bug
在对chrome设置html根元素字体时，我试图把它设置为62.5％(10px),然而最后出错了，因为chrome下字体设置的最小值就是12px，它不会比12px更小。针对这个现在我有两个办法.不过亲测后感觉，chrome下表现的还是挺不错的，暂时不妨碍。

### 通过媒体属性确定加载样式
有时候一个html结构会在不同的设备大小下对应不同的css样式，而且不同样式之间差别也许比较大，你需要根据布局大小的不同对css文件进行选择:     
```html
<link rel="stylesheet" href="style1.css" media="screen and (max-device:400px)" />
```
增加一个限制:   
```html
<link rel="stylesheet" href="style2.css" media="screen and (min-device-width:400px) and (max-device-width="600px") />
```
上面两段代码分别用于识别宽度小于等于400px时对应的css文件和宽度在400px在600px之间加载的css文件。    

### 媒体查询
这个应该算是比较核心的概念啦，媒体查询是css3引入的新特性，语法如下:    
```css
@media mediatype and|not|only (media feature) {
    CSS-Code;
}
```
其中这个mediatype指的是你网页此刻需要应对的媒体类型，对于开发者来讲，一般都是应对screen类型，后面跟的是你需要页面满足的条件，举个例子:    
```css
@media screen and (max-width:800px) and (min-width:600px){
    font-size:0.8em;
}
```
当然，css内容是瞎写的，关注media后面的即可。    
这里，我指定你的模式为screen(浏览器设备模式)，并且需要同时满足(and就是同时满足)布局宽度大于600px小于800px。    
还有其它属性（类似于Orientation这种规定方向的属性设置，它可以设置为横向（landscape）和 竖向（portrait）），这个需要的时候可以自己查文档。
    
### IE6，7，8不支持媒体查询怎么办    
不支持，就不让它用！    
我们的办法是:    
```css
@media only screen and (max-width:800px) and (min-width:600px) {
    css内容
}
```
这个only很有用。在低版本IE中，虽然不支持@media，但它还是会读，它读什么都返回true,这样就乱套了。为了避免IE乱搞，我们就用一个只有高版本浏览器才懂的only，屏蔽掉低版本浏览器作怪。    
     
### 流式布局
流式布局时说各个区块的位置都是浮动的，比如当前有两个元素在同一行，我们缩小宽度，导致一行放不下两个元素了，后面的元素会自动滚动到前面元素下面，而不会被遮去，避免了滚动条的出现.    
    
### 响应式图片
图片自适应我们当然可以进行width:100%的设置进行自适应，但是注意有时候图片随着放大缩小会失真，所以最好的办法还是一个图片准备多个大小适应多个页面，根据页面的变化进行不同的加载，这个我们可以用html5新增的picture标签:    
```html
<picture>
    <source srcset="img/ad001-l.png" media="(min-width:50em)">
    <source srcset="img/ad001-m.png" media="(max-width:50em)">
    <img srcset="img/ad001.png" alt="年度报告">
</picture>
```
picture标签中，我们可以提供各种各样的source标签来设定不同宽度下的资源的加载。上面这段代码的意思是说，当宽度大于50em的时候，就加载ad001-l图片，当宽度小于50em时，就加载ad001-m图片。下面为啥还有个img标签，这个必须写，它是图片的初始值，source里面的相当于是可选项。如果没有img只有source的话，会什么也显示不出来.

## 总结
这里是针对我之前做过的一个响应式demo做的一次学习总结,但是遗漏了一个网格系统的介绍，网格系统我第一次是在bootstrap里面看到的，它其实也可以用css手写来实现，这里我们先不讨论。等专门写bootstrap的时候再说。 




