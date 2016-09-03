---
title: body和html到底有多高&&浏览器的背景色问题
date: 2016-08-24
tag: "css"
---
在此之前我一直以为在浏览器里html默认和浏览器窗口等高，而body默认和html等高，现在想想自己为什么会这么想，主要是因为这个:    
```css
body{
    background:blue;
}
```
当我这么干的时候，得到了这个结果：    
![css1](http://7xl4oh.com1.z0.glb.clouddn.com/gaodu_1.jpg)    
看，body的背景色占据了整个浏览器窗口，这样想来body撑开浏览器什么的也不是匪夷所思了，但书上告诉我们正常情况的高度是不会继承的，那么这个body到底有多高呢？我们添加一行:    
```css
body{
    background:blue;
    border:5px solid black;
}
```
神奇的事情发生了:    
![css2](http://7xl4oh.com1.z0.glb.clouddn.com/test_2.jpg)    
啥？？？body没高度？？那浏览器的颜色是哪里来的？？？？？    
![smile](http://7xl4oh.com1.z0.glb.clouddn.com/bq.jpg)    
还没完，还有更神奇的事情，一起来看下:    
在以上代码的基础上，我添加了这样两行style：    
```css 
html {
    background:yellow;
}
```
它又变成黄色了。。。。。    
![css3](http://7xl4oh.com1.z0.glb.clouddn.com/test_3.jpg)    
嗯，这样想的话，难道是html标签撑开了窗口???   
一探究竟:   
```css
html {
    background:yellow;
    border:5px solid red;
}
```
然后我们看到html仿佛有点高度？？    
![css4](http://7xl4oh.com1.z0.glb.clouddn.com/test_4.jpg)    
当然不，这是body的外边距啦2333让我们去掉这个东西:    
```css
body {
    background:blue;
    margin:0;
}
```
于是真相是:    
![css5](http://7xl4oh.com1.z0.glb.clouddn.com/test_5.jpg)    
    
魔性的事情出现了，html原来也是没有高度的hhhhh。    
    
不过话说回来，这不刚好和我们宽度默认继承父级和浏览器窗口等宽，而高度默认不继承为0相吻合吗？所以html和body标签默认高度为0这点，是毋庸置疑的。    
那么问题来了，body和html并没有高度，那么满屏的黄色和蓝色是怎么显示出来的？    
    
我觉得这和浏览器本身的机制有关，我们可以认为浏览器是一个会“吸收”颜色的东西，当我们给body设置颜色时，虽然body没有高，但是浏览器底板需要颜色，它看到了body的颜色，就想要拿来变成自己的，然后它把自己搞成了body设置的那个blue，当我们给html也设置了一个颜色后，浏览器发现，html好像离我更近啊，于是就就近渲染了html标签的背景色yellow给自己.    
这样一来，我们看到的浏览器窗口又变成黄色了。 
       
因此，我们所看到的那个背景色，既不属于body标签，也不属于html，它属于更底层的那个底板，是浏览器窗口本身吸收了body和html标签中更靠近自己的那个后，渲染出来的浏览器窗口底板的颜色。    


