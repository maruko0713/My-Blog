
---
title: JSONP全接触：JSONP原理，在js与jQuery中的实现
date: 2016-09-06
tags: ["jQuery","ajax"]
---
## 前言：
让我来记住这个历史性的事件：《jQuery基础教程（第4版）》第133页下面介绍了一种用$.getJSON方法实现跨域请求的办法，这种办法的大概意思就是把方法的第一个参数传递为所需的url地址＋"?callback=?"的形式，于是我们就能实现jsonp了。这个书很多地方都是一笔带过，这个地方也是半页纸就写完了，于是从今天上午到现在（下午四点）我断断续续一直在揣摩这个问题，顺便专门复习了一遍jsonp的原理（泪目），网上查出来的文章有不少，但是真正有用的不多，用这篇文章整合一下，也算没白费这么大功夫😂.

## JSONP是啥
JSONP(JSON with padding,填充式JSON),是一种利用script标签从远程获取JSON数据的手段,这个技术的出现很好地解决了ajax无法进行跨域请求的弊端。JSONP的格式是把标准的JSON文件包装在一对圆括号中，然后圆括号前置任意一个字符串（当然也可以是空字符串），乍一看有一种函数调用的即视感，这个字符串就是所谓的填充padding啦。这样数据传递回来并且被解析时，就可能会导致一次函数调用。那么JSONP这种格式在服务器端的实现是咋整的呢,这样: 
### JSONP格式的服务器端实现:   
```php
<?php
  print($_GET['callback'].'('.$data.')');
?>
```
看不懂PHP也没关系，这段的意思是说，利用$\_GET方法从客户端请求过来的url里面截取callback参数，注意这个参数也可以是fn，fx啥的，叫啥名字都行，只要你客户端写url的时候能够照上这个名字，它就能获取过来，然后data就是你想要的数据，中间的小点点是字符串拼接的意思，这整个是说输出一个前面拼接了客户端url指定名称的函数的函数调用（如果被解析成js格式的话）。

## js中的JSONP实现
因为标签和前言里面也写了这个主要是针对jQuery写的文章，所以简要谈谈在原生js里面怎么搞就行了。原生js里面我们一般是把这个JSONP请求绑定到某个事件（比如点击事件）上，当这个事件被触发的时候，我们在事件处理程序里面创建一个新的script标签，并且把它丢进head标签或者body标签里去，这个新的script标签的src属性是指向我们目标的那个资源地址的，末尾跟上类似于?callback=xxx这样的参数内容，于是我们就能从远程得到一个内容形如"xxx(data)"的响应，这个内容被当作js解析出来，于是data就会根据xxx函数的定义被操作。因此，我们在实现JSONP格式的数据的请求的之前，务必要记得定义一个与xxx同名的函数，不然就会报错说xxx is undefined啥的，那样就很尴尬啦。

## jQuery中的JSONP实现
这段是这篇文章的重点，因为我就是从这段入坑的,我们先来看我入坑的这一段书上的示例代码:  
用getJSON方法实现:  
```js
$.getJSON(url+"?callback=?",function(data){
    ....
});
```
省略了很多，但这已经够了，这段的意思就是在请求的URL后面加上"callback"(服务器段所需的参数名字)=?这部分，jQuery就不会报错说你跨域失败了，而会把它当做一个jsonp类型的请求去处理，注意这个?非常重要，jQuery会把它替换成一个长长的字符串，然后会把服务器返回内容的data部分（即去掉padding的纯json部分）传递给后面的success函数，于是success函数就可以使用data了，ok,大功告成！    
这里有两个要注意的地方:    
- callback不能少 也可以是别的名字 这里用callback是因为服务器端所需的函数名是callback 你也可以提供其他的，前提是和服务器端的要求相符
- =号后面必须跟问号，不然就会报错说你跨域请求失败了，可见jQuery需要通过这个问号是否存在来决定你这是不是一个JSONP请求.    

第二种方法更加灵活,用$.ajax()方法实现:    
```js
$.ajax({
     type: "get",
     url: "http://examples.learningjquery.com/jsonp/g.php",
     dataType: "jsonp",
     jsonp: "callback",
     jsonpCallback: "hehe",
     success: function(json){
        console.log(json);
    }
});
```
$.ajax()相对上面来说，是一种更为底层的方式，所以我们能够自定义操作的内容更多。这里在我们把dataType设置为"jsonp"之后，它就会自动执行一些针对jsonp的规则，比如它不会再报错给你说你跨域了，然后这个jsonp属性设置的就是服务器端需要的那个函数名参数对应的名称，比如我们上文的callback就是这样一个参数名（形参），jsonpCallback里面对应的是函数名参数对应的值（实参），这里我们这样操作，会把一个"callback=hehe"传递给php页面，然后php页面get到的padding前缀就会是hehe,我们得到的jsonp数据就会是hehe(data)这个样子了。   
注意：不管我们传入的函数名存不存在，都不会报错.如果存在，那么就执行，如果不存在，也没关系^_^      
相对于第一种方法，我们不必一定要把callback设置为问号了，可以传入任意你想传的函数名，同时，这个jsonp格式的数据（形如hehe(data)）请求回来之后，hehe函数内部的data内容会被当做第一个参数传入success方法，这里，我们进行的操作是在控制台输出返回的data结果.        
    
写到这里，已经有点累了，但是还没完，我们还有最爽的一种方式，就是用插件！！！！    
传送门：[jquery的jsonp插件](https://github.com/jaubourg/jquery-jsonp).    
直接上代码,请求同样的数据:    
```js
$.jsonp({
    url:"http://examples.learningjquery.com/jsonp/g.php",
    success:function(data){
        console.log(data);
    },
    callback:"xixi"
});
```
是的，你没有看错，从请求到输出，我们只用了两行。       
两行啊！！！！！    
下面的callback要提一下，同样的，即便我们根本没定义xixi也没关系，不会报错，但是如果我们直接在url后面写?callback=xixi，而下面callback键后面没有定义值为xixi的话，照样会报错。这里我猜是对callback里面定义的函数名会有一些特别的处理吧，不过暂时不重要，现在熟悉它的用法就可以了。    
    
综上所述，我们总结jQuery中jsonp的三种实现方法，最推荐的就是最后一种，jsonp插件，简单，快捷，强大，前两天我做了一个阅读器应用，这货连加密过的url都不怕，因为它会自己解码！！总之就是超级好用啦！当然，前两种也是有必要理解的。不过在清楚了原生js原理的基础上，这应该不会很麻烦^_^_





