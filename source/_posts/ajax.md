---
title: Ajax全接触：原生js中的ajax和jQuery中的ajax
date: 2016-09-25
tags: ["jQuery","ajax","javascript"]
---
## 前言:
我之前写过一篇针对jsonp的文章[JSONP全接触：JSONP原理，在js与jQuery中的实现](https://maruko0713.github.io/2016/09/06/jsonp/),主要是因为一个阅读器app [reader](https://github.com/maruko0713/Reader)引发了一些思考。今天把ajax知识点做个整合。    
    
## Ajax是什么
Ajax(Asynchronous Javascript and XML 异步javascript和xml),这个概念是2005年提出的，它把静态网页转换成具有交互性能的web应用。  
  
## Ajax在原生javascript中的实现    
什么都不如直接写代码，让我们用原生js来封装一个简陋版的ajax函数(get方法)：    

```js
function ajax(url,success,fail) {
    var oAjax = null;
    if(XMLHttpRequest) {
        oAjax = new XMLHttpRequest();
    }   else {
        oAjax = new ActiveXObject("Microsoft.XMLHTTP")
    }
    oAjax.open("GET",url,true);
    oAjax.send();
    oAjax.onstatechange = function() {
        if(oAjax.readyState == 4) {
            if(oAjax.status == 200) {
                success(oAjax.responseText);
            } else {
                if(fail){
                    fail();
                }
            }
        }
    }
}
```

好的,这是我们调用ajax(url地址，请求类型，成功回调，失败回调)就可以搞定一个ajax请求啦！    
下面，让我们仔细研究一下原生ajax api中的猫腻:     

### ajax对象构造函数:    
在原生js中，我们一般采用XMLHttpRequest对象来构建一个ajax对象，但是低版本ie中没有这个对象，因此我们在else中指定了另一个对象ActiveXObject来构造ajax对象，注意里面需要传入参数，写出来就是这样: new ActiveXObject("Microsoft.XMLHTTP").      

### ajax对象常用的api和属性:
- open方法 （open(请求类型, url, 是否异步)）: 配置要发送的ajax请求，第一个参数传入你的http请求类型，第二个参数传入你想要请求的那个url地址，第三个参数设置为true时，该请求就是一个异步请求，false时是同步的，百分之九十九的情况都是true.    
- send方法:发送http请求。send方法使用get请求时不传参，一旦使用post方法，那么send中就可以传入一个参数作为post的内容，也就是http请求的正文部分。注意，这种情况下，必须在配置完open方法之后，调用send方法之前，写上一句oAjax.setRequestHeader("Content-Type","application/x-www-form-urlencoded;charset=UTF-8");，设定http请求的头部，然后这个请求内容才能成功地被服务器识别解码.
- onstatechange属性:这个属性可以告诉我们现在ajax请求进展到了那一步
- status属性: 服务器端的响应状态码
- responseText:服务器端响应的正文部分    

### ajax请求的五个状态:    
这里详细介绍一下onstatechange的5个值对应的ajax请求状态:    

- 0:还没有配置,未调用open方法  
- 1:send方法已调用，请求在路上    
- 2:send方法已经完成,ajax对象已经收到服务器端的响应内容   
- 3:ajax对象正在解码服务器端的响应内容    
- 4:服务器端的内容解码完成，大功告成    

## jQuery中的ajax    

### 最简单的load方法

```js
$(选择器).load(“url地址”,requestData);
```
这个方法把url地址返回的响应内容全部加载进选择器对应的dom节点中，简单粗暴，如果url的返回值恰好是html，那皆大欢喜，然而如果返回个json或者xml啥的，恐怕用户要懵比一会儿了。    
注意第二个参数可选，没有第二个参数时默认是get请求，有第二个参数时自动变成post请求.
    
### 取得json数据的getJSON方法    

```js
$.getJSON("json文件地址",function(data) {
   对data的操作啦啦啦～～ 
});
```

这个方法也不难，第一个参数是json文件地址，第二个是返回成功的回调函数.    
注意getJSON对jsonp的实现，比如我的jsonp请求地址是 "http://www.xixi.com/jsonp.php" ,服务器端定义了回调函数名应该储存在url的callback参数里，那么我这么写:    

```js    
$.getJSON("http://www.xixi.com/jsonp.php?callback=?")
```

就可以实现一个jsonp请求，而不会报跨域的错误，回调函数里的data就是jsonp加载进来的函数调用代码段中的实参。    
    
### get方法，post方法  

```js
$.get("url地址",requestData,function(data){
    回调函数内容
});
```
第一个参数不多说，第二个参数是我们需要get来通过url传递数据的时候，把想传递的数据当作第二个参数传入，第三个参数就是回调函数。   

```js
$.post("url地址",requestData,function(data) {
    
});
```

同上，只是请求方法变了，对应的requestData的位置发生变化（从url里变成了请求正文里）。    

### 底层ajax方法
上面所有方法，都是底层ajax方法的变体。    

```js
$.ajax({
    url:url地址,
    type:请求类型,
    data:请求信息,一般是序列化的值或者是json,
    dataType:希望接收的数据格式,
    async: 默认为true,是否异步,
    success:function(result,status,xhr){
        result是返回的响应正文     
        status是响应状态码     
        xhr是原生的ajax请求对象
    },
    error:function(xhr,status,error){ 
        同上
        error是错误类型
    }
});
```

这就是全部了吗？当然不，ajax的配置项多如天上繁星，这里只是列举几个必须记死的，其它的如果想要了解，查文档也会上手很快。    
    
### 全局ajax方法
也就是传说中的"观察员"函数.    
比如$.ajaxStart()和$.ajaxStop(),$.ajaxError().
这些方法比较特别，因为无论它们在何处创建，在ajax通信发生时都会调用它们，并且它们只能由document调用.
当ajax请求开始并且尚未进行其他传输时，ajaxStart方法被调用;当页面的最后一个ajax请求结束时,ajaxStop方法被调用;当页面中任何一个ajax请求出错时,ajaxError方法被调用，并且ajaxError方法会传递给回调函数一个xhr对象，帮助我们获取相关信息。



