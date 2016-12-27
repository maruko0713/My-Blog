---
title: jQuery复习2:jQuery中的事件之事件基础篇
date: 2016-12-15
tag: "jQuery"
---
## 前言:
js和html之间的交互式通过用户和浏览器操作页面时引发的事件来处理的。     
文档或者它的某些元素发生某些变化或操作时，浏览器会自动生成一个事件。    
jQuery增加并且扩展了基本的事件处理机制，增强了事件处理能力。    
   
本文着重讲解一些比较重要的api。      
事件绑定机制，我认为非常重要，所以会和事件冒泡，事件捕获等知识点一起，在下一篇文章单独写。    

## 原生js中的事件和jQuery中的事件对比
举一个最常见的🌰，就是window.onload()和$(document).ready()这两个方法。    
    
### 在执行时机上的差别
window.onload()需要在整个页面都加载完毕，包括dom树和相关的链接文件全部加载完毕之后触发事件。     
而$(document).ready()在dom加载完毕之后就会触发，不用等待相关文件的下载，比如图片，视频这些这时候都还没有加载出来，但是只要dom加载出来了，就会触发。    
    
window.onload()对应的jQuery事件是$(document).load();    
   
###  在设置机制上的差别
假如你写了两个函数：    
    
```js
function one() {
    alert("one");
}
function two() {
    alert("two");
}
```

你的本意是让window.onload()触发后执行这两个函数，于是你这样做了:    
    
```js
window.onload = one;
window.onload = two;
```

运行代码，只会弹出一个"two"。    
这个是可以想象的，因为我们重复赋值。    
   
但是如果我们利用jQuery中的事件，就可以轻易达成我们的目的：    
    
```js
$(document).ready(one);
$(document).ready(two);
```

触发后两个函数会接连运行，成功！    
   
tip: 我们要注意$(document).ready()有一个简写是$()这样的，因此我们常常看到：    
   
```js
$(function(){
    函数内容
});
```

另外.$()里面在不传参数的时候，默认参数是document,此时它就是$(document)这个jQuery对象。    
## 原生js里面的特殊绑定事件在jQuery里面形式的改变   
在原生js里面，如果我们想要为一个元素绑定事件，我们一般是这样做的：  

比如点击事件:    

```js
element.onclick = 一个函数;
```
   
在jQuery里面是这样的：    
   
```js
$element.click(一个函数);
```

好处就是可以重复调用这个函数，然后多次添加事件触发函数的内容。    
同样的，其它的一些我们熟知的常用事件纷纷变成了这样：    
    
```js
$element.mouseover(一个函数);
$element.mouseout(一个函数);
```

有效减少了代码量～～～～^_^。    
    
## jQuery的两个合成事件   
jQuery中有两个合成事件:     
hover()方法和toggle()方法。      
和ready()方法一样,hover()方法和toggle()方法在原生js中没有对应的原生事件，因此它是jQuery自定义的事件。    
    
### hover()方法
hover()方法的语法结构为：    
    
```js
hover(enter, leave);
```

它模仿的是光标的悬停事件，当光标移动到元素上时，会触发指定的第一个函数即enter函数，当光标离开时，会触发指定的第二个函数即leave函数。     
    
### toggle()方法
toggle()方法的语法结构为：    
   
```js
toggle(fn1, fn2, ..., fnN);
```

它模仿的是鼠标连续单击事件，第一次单击元素，触发fn1,第二次单击，触发fn2,然后一直触发到fnN。  
如果连fnN也被触发了，那么就从头开始，再来一遍。   
就是这样以此类推，循环往复。     

## 特殊性质的事件one()
对于只需要触发一次，随后就要立即解除绑定的情况，jQuery提供了一种简写方法：one()方法。    
one()方法可以为元素绑定一个只能执行一次的处理函数。     
   
one()的语法结构如下：    
   
```js
one(type, [data], fn);
```

一般我们都这样调用:    
   
```js
element.one("click", function(){
    函数内容
});
```

这样就实现了只在第一次点击就执行的效果，其它事件亦然。    
    
## jQuery中的模拟事件
所谓模拟事件，就是说模拟用户的操作，来达到触发事件的效果。    
在jQuery中，这个效果用trigger(type, [data])方法来实现。    
如下：    
   
```js
element.trigger("click");
```

传入事件名，就可以模拟触发这个事件的操作，楼上就触发了一次点击操作。    
   
有时候我们需要给一个元素绑定一些自定义事件，比如我给我的事件取名字叫做“miemiemie”,那么这个事件原生js里面是没有的，于是我不管怎样操作浏览器，点鼠标也好，砸键盘也好，这个事件都不理我。   
此时用trigger:    
   
```js
element.trigger("miemiemie");
```

就可以成功触发我的这个事件处理函数，哈哈哈哈哈哈。    
trigger的第二个可选参数是data,可以用来传递参数给事件处理函数。    
比如：    
   
```js
element.on("miemiemie",function(content) {
    console.log(content);
});
```

我在触发的时候，处理函数渴望得到一个content,于是我这样做：    
    
```js
element.trigger("miemiemie","miemie is a pig");
```




   
## 结语
这一篇文章的信息量比较小，下一篇文章我会着重讲事件的机制，包括jQuery中事件的绑定，事件本身的冒泡和捕获等等。







   


    
