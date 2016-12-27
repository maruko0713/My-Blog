---
title: jQuery复习0:jQuery选择器一锅端
date: 2016-12-13
tag: "jQuery"
---
## 前言:
jQuery是我接触的第一个前端库，在学习的过程中，我借助了jQuery文档，《锋利的jQuery》和《jQuery基础教程》这三个学习材料，同时参考一本叫做《精通jQuery》的厚厚的大部头。    
然而，jQuery知识点比较碎，所以我还是决定写一个系列博客，既是总结，也是加强自己的记忆。    

## jQuery选择器概述
jQuery选择器就是一个用来查找并且返回指定DOM元素的东西，它是跨浏览器兼容的。     
它比css选择器厉害多了。    
    
**前方高能**    
虽然可能会又臭又长但是我觉得这些都有必要过一遍。    
还是决定默写一下。。。。。。😂      
   
### jQuery选择器一锅端

## 基本选择器
   
```js
$("#id");
$(".class");
$("element");
$("*");
$("xx,xx,xx");
```
首先id选择器，类选择器和element选择器，通配符选择器，都是直接传入对应的css选择器就好。
最后这个“xx”系列，它的意思是，可以同时选择多个元素，比如$("#id, .class, element"),它可以将你选择的所有元素组成一个数组，然后返回给你。     
    
## 层次选择器
   
```js
$("ancestor descendant");
$("parent > child");
$("prev + next");
$("prev ~ sibilings");
```

其实还是css选择器的知识点，中间是空格的时候表示选中所有的后代子元素，箭头表示选中所有的子代元素，加号表示选中它紧邻的后面的next元素， 波浪线表示选中它所有的后面的元素。    
    
其中,ancestor,descendant,parent,child,prev,prev,sibilings本身通通都是基本选择器。    
## 过滤选择器

### 基本过滤选择器
```js
$("xxx:first");
$("xxx:last");
$("xxx:not(selector)");
$("xxx:even");
$("xxx:odd");
$("xxx:eq(index)");
$("xxx:gt(index)");
$("xxx:lt(index)");
$(":header");
$("xxx:animated");
$(":focus");
```
   
"xxx"本身也是选择器。啥也不写的时候表示全局。        
first,last,分别选中第一个和最后一个元素。    
not(seletor)选择器在已有的选择器中过滤掉匹配到selector的选择器。    
even和odd选择器在选中的元素中筛选出索引为偶数和奇数的元素，返回数组。    
eq(index)这个直接在当前数组中返回index所指向的元素。    
gt,lt分别返回大于和小于里面传入的index的元素集合。   
header选择器是专门用来选择标题的，也就是h1，h2, h3....这样的。     
animated就是在当前元素集合中选出正在运动的元素集合。    
focus,获取页面中当前获取焦点的元素。    
    
### 内容过滤选择器   
 内容过滤过滤的是子元素和文本内容。    

```js
$("xxx:contains(text)");
$("xxx:empty");
$("xxx:has(selector)");
$("xxx:parent");
```
   
其中xxx本身也是选择器，啥也不写的时候表示全局。    
contains用来选中拥有指定**文本内容**的元素。   
empty选取不包含任何子元素和文本元素的空元素。    
parent选取**自己本身拥有子元素**的元素。     
    
### 可见性过滤选择器    
   
```js
$("xxx:hidden");
$("xxx:visible");
```

分别选中当前页面所有不可见和可见的元素。   
其中不可见，是说你设置了type="hidden"或者display="none"，它都是不可见的。    
   
### 属性过滤选择器
  
```js
$("xxx[属性名]");
$("xxx[属性名＝值]");
$("xxx[属性名!=值]");
$("xxx[属性名^=值]");
$("xxx[属性名$=值]");
$("xxx[属性名*=值]");
$("xxx[属性名|=值]");
$("xxx[属性名~=值]");
$("xxx[属性选择器][属性选择器][....]");
```

这一块有点正则表达式的意思了。    
第一个用来选择**拥有**某个属性的元素。    
xxx[属性名=值]用来选择**属性名等于某个值**的元素。  
xxx[属性名!=值]用来选择**属性名不等于某个值**的元素。    
xxx[属性名^=值]用来选择**属性名以指定值开始**的元素。    
xxx[属性名$=值]用来选择**属性名以指定值结束**的元素。    
xxx[属性名|=值]用来选择**以指定值为前缀**的元素。    
这个可能不太好理解，举个例子：    

```js
$("div[title|='en']")
```
它选中的是形容"en"或者"en-xxxx"这样的元素。    

xxx[属性名*=值]用来选择**属性名对应的值中含有指定值**的元素。    
xxx[属性名～=值]用来选择**属性名对应的值中用空格风格的值中包含给定值**的元素。    
xxx[属性选择器][属性选择器][....]是说并列多个属性选择器从而实现复合筛选的效果。    
举个🌰：   
   
```js
$("div[id][title$='test']");
```
用以选取既拥有id属性，title属性的值又以test结尾的元素。    
    
### 子元素过滤选择器
   
```js
$("xxx:first-child");
$("xxx:last-child");
$("xxx:only-child");
```

上面三个都比较简单，就是选择**元素自身**是父元素的第一个，最后一个和唯一一个子元素的元素。    
比如:   
   
```js
$("ul li:first-child");
```

就是说我要选中所有ul下的第一个li元素。    

还有一个情况多一些的：    
   
```js
$("xxx:nth-child(index/even/odd/equation)");
```
这个分情况来看：    
- nth-child(index),index是数字，表示**把当前元素（即xxx选中的元素）看作是父元素下的指定索引的子元素选出来**   
- nth-child(even)和nth-child(odd)分别选择索引序号为偶数或者奇数的元素   
- nth-child(equation)用来根据**表达式**对元素进行选择，比如这样：nth-child(3n+1)选择符合3n+1这个表达式的数字做索引的元素。    
    
### 表单对象属性过滤选择器
    
```js
$("xxx:enabled");
$("xxx:disbled");
$("xxx:checked");
$("xxx:selected");
```

这个主要是针对表单元素进行选择：    
- enabled在指定范围的元素中选取**可用的元素**    
- disabled在指定范围的元素中选取**不可用的元素**
- checked在指定范围中选取所有被选中的元素.主要针对单选框，复选框。（勾选方式） 
- selected在指定范围的元素中选取所有被选中的元素。主要针对下拉列表。    

### 表单选择器    
  
```js
$(":input");
$(":text");
$(":password");
$(":radio");
$(":checkbox");
$(":submit");
$(":image");
$(":reset");
$(":button");
$(":file");
```

- input 选取的是一类元素，包括`<input>,<textarea>,<select>和<button>`元素
- text 选取的是单行的文本框 即 `<input type="text">`
- password 选取的是密码框 即 `<input type="password">`
- radio 选取的是单选框 即 `<input type="radio">`
- checkbox 选取所有的多选框 `<input type="checkbox">`
- submit 选取的是所有的提交按钮 `<input type="submit">`
- image 选取的是所有的图像按钮 `<input type="image">`
- reset 选取的是所有的重置按钮 `<input type="reset">`
- button 选取的是所有的按钮 必须是button标签 input不行
- file 选取所有的type属性为file的input元素 `<input type="file">`
    
## 结语
jQuery选择器其实是有一些繁琐的，而且许多细节必须去实践才能解除自己的困惑。    
（终于写完了，长出一口气）。    
另外要注意，有时候我们想要把获取到的jQuery对象转换成DOM对象，这时候应该这样做：    
比如这个元素的id为dom:   

```js
var $dom = $("#dom");
var dom = $dom.get(0);
var dom_copy = $dom[0]
```

利用get(index)或者直接写成数组获取元素的形式即可。    




