---
title: jQuery复习1:jQuery的DOM操作
date: 2016-12-14
tag: "jQuery"
---
## 前言:
所谓DOM的行为，其实就是api。     

## 原生js中的DOM
DOM操作一般分为三个方法面，即DOM Core（核心），HTML－DOM和CSS－DOM

### DOM Core
DOM Core并不专属于Javascript,任何一种支持DOM的程序设计语言都可以使用它。    
我们用的比较多的DOM Core主要是getElementById(),getElementsByTagName(),getAttribute()和setAttribute()等方法。    
   
### HTML-DOM
用于描述HTML的各种属性。    
比如：    
   
```js
element.src;
```


### CSS-DOM
就是针对CSS的操作。    
js中CSS-DOM一般是利用style获取和设置各种样式。    
比如：    
   
```js
element.style.color = "red";
```

## jQuery中DOM

### 查找元素节点

查找元素节点：

```js
var $dom = $("选择器");
var $target = $dom.eq(index);
```

eq方法用于获取数组中的一个元素，注意不能用$dom[index]或者$dom.get(index)来获取，这两者都是用来得到原生DOM对象的。    
    
### 创建元素节点
这个很简单：    
   
```js
var new_node = $("<div>我是新的</div>");
```

只要像上面那样，往括号里面写入html代码就可以了。    
   
### 插入元素节点
   
内部尾部追加：    

```js
target.append(html代码｜jQuery对象| 选择器);
element.appendTo(选择器 | jQuery对象);
```

第一个是往调用方法的元素内部添加，第二个是调用方法的对象被添加进参数元素的内部。    
   
内部前置：   

```js
target.prepend(html代码 | jQuery对象| 选择器);
element.prependTo(jQuery对象 | 选择器);
```
和楼上的机制一样，只不过是把内部后置变成了内部前置。    
   
外部后置:    
    
```js
target.after(html代码 | jQuery对象 | 选择器);
element.insertAfter(jQuery对象 | 选择器);
```

把元素插入到指定元素的后面，两者在同一层。比如:    
   
```html
<p>美少女</p>
```

```js
$("p").after("<h1>你有毒</h1>");
```

楼上两段代码结合，成功得到:    
   
```html
<p>美少女</p><h1>你有毒</h1>
```
   
同样的道理，我们有before和insertBefore方法对应。    
   
### 删除节点
jQuery提供了三种删除节点的方法：    
- remove()
- detach()
- empty()
   
#### remove方法
remove方法的返回值是所删除的那个节点对应的jQuery对象，因此删除后可以用一个变量承接这个节点，然后可以继续使用这个节点。    
然而，remove方法的返回值仅仅保留这个节点的内容，而不会保留这个节点绑定的事件。    
   
#### detach方法
detach的特别之处就在于，它在删除后返回给你的那个jQuery对象，是保留绑定事件的，你再次把这个东西附加到某个元素后，恢复它在DOM树中的存在，它仍能还原删除前的行为。    
   
#### empty方法
empty方法是有删除的作用，但它其实是清空。   
它的作用是删除调用方法的对象的所有后代节点，把它变成一个空节点。    
   
### 复制节点
注意我们前面所有的前置添加也好后置添加也好，我们其实是在干啥？？    
是在移动节点，我们把元素添加过去了，原来的那个位置就没有这个元素了。    
有时候我希望的是在保留这个元素的基础上，把它的副本拿出去。    
这时候复制节点就很有必要了。    
   
### clone方法：    
复制的实现通过clone方法：    
   
```js
element.clone();
```

注意我们克隆下来的仅仅是一个静态的元素，它没有任何的行为，不监听任何的事件。   
如果我们需要连行为一起复制，就这样做：    
   
```js
element.clone(true);
```

记得传参数就行。    
   
### 替换节点
replaceWith和replaceAll方法都可以实现节点的替换。   
它们的功能一模模一样。    
不过要注意它们用起来的顺序是相反的：    
   
```js
element.replaceWith(target);
target.replaceAll(element);
```

tip!注意节点一旦被替换，它事先被绑定的事件会随着被替换掉的元素一起消失，因此我们需要重新绑定事件。    
    
### 包裹节点 
先来看一段html代码：    
   
```html
<li>咩咩是猪</li>
<li>咩咩是猪</li>
<li>咩咩是猪</li>
<li>咩咩是猪</li>
```

我想要针对每个li标签进行包裹，裹上一个ul,我可以这样做:    
   
```js
$("li").wrap("ul");
```

于是我得到:    
   
```html
<ul><li>咩咩是猪</li></ul>
<ul><li>咩咩是猪</li></ul>
<ul><li>咩咩是猪</li></ul>
<ul><li>咩咩是猪</li></ul>
```

如果我想要把所有的li包裹在一起:    
   
```js
$("li").wrapAll("ul");
```

就会得到:    
   
```html
<ul>
    <li>咩咩是猪</li>
    <li>咩咩是猪</li>
    <li>咩咩是猪</li>
    <li>咩咩是猪</li>
</ul>
```

如果我想要只包裹内部的文本:    
   
```js
$("li").wrapInner("b");
```

我就可以得到：   
   
```html
<li><b>咩咩是猪</b></li>
<li><b>咩咩是猪</b></li>
<li><b>咩咩是猪</b></li>
<li><b>咩咩是猪</b></li>
```

## jQuery中的DOM属性操作
在jQuery中，用attr()方法来获取和设置元素属性,removeAttr()方法来删除元素属性。    
    
### 获取元素属性：    
    
```js
var $target = element.attr("属性名");
```

设置元素属性:    
   
```js
element.attr("属性名","属性值");
```

一口气设置很多个属性值：    
    
```js
element.attr({"属性名": "属性值", "属性名": "属性值"});
```

### 删除元素属性
用removeAttr()实现。    
    
```js
element.removeAttr("属性名");
```

   
## jQuery中的css操作    
    
### 针对class的操作 
#### 添加class:    
   
```js
element.addClass("类名");
```

#### 删除class:    
    
```js
element.removeClass("类名");
```

如果没有这个类，也不会报错。如果想要删除全部：    
    
```js
element.removeClass();
```

#### 切换class(在有和没有之间切换):    
    
```js
element.toggleClass("类名");
```
   
#### 判断是否含有某个class

```js
element.hasClass("类名")
```
   
### 对CSS-DOM的操作
获取和修改元素的样式属性
   
#### 用css()方法：    
   
```js
element.css("样式属性名");   
element.css("样式属性名", "样式属性值");
element.css({"样式属性名": "样式属性值", "样式属性名": "样式属性值"});
```
    
#### 另外高度和宽度可以单独获取：    
   
```js
element.height();
element.width();
```
括号里传入参数可以对height或width进行设置。（下同）。    
   
#### 获取元素在当前视窗的相对偏移

```js
var offset = element.offset();
```
它返回的是一个对象，对象里有left(左偏移)和top(顶部偏移)。    
    
#### 获取元素的position定位
   
```js
var position = element.position();
```
返回一个对象，对象里有相对于定位父元素的左偏移和顶部偏移。    
   
#### 获取元素滚动的距离    
   
```js
var scrollTop = element.scrollTop();
var scrollLeft = element.scrollLeft();
```
分别用来获取滚动条距离顶端和距离和滚动条距离左侧的距离。    
    
   

## 设置和获取html,文本和值
   
### 获取html：

```js
var html = element.html();
```
   
设置只需要往html里面传参：    
    
```js
element.html("html代码");
```
   
### 获取text:    

```js
var text = element.text();
```
   
设置text也是一样的道理：    
    
```js
element.text("text内容");
```
    
### 获取和修改一个元素的value属性也是一样的道理：    
    
```js
var value = element.val();
element.val("value属性值");
```

## 遍历DOM节点
    
- children()方法 用于获取节点的子元素的集合    
- next()方法 用于获取匹配元素后面的紧邻同辈元素
- prev()方法 用于获取匹配元素前面的紧邻同辈元素
- siblings()方法 用于获取匹配元素前后所有的同辈元素
- parent()方法 获取匹配元素的父元素
- parents()方法 获取匹配元素的所有祖先元素
- closest()方法 获取离匹配元素最近的上层元素    
  
tip: 在上面的所有方法的括号里，都可以传参数进去，用于进一步筛选元素～～～    

    
## 结语
累死了累死了哈哈哈哈。    
在jQuery里面，其实有很多api，是在重复选择器的操作。    
嘛～～不过这样也好，选择会变多。    
就是要记熟要记熟要记熟啊！！！     





