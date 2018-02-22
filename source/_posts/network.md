---
title: 异步网络请求进化史：从ajax到fetch到axios
date: 2018-02-22
tags: "ajax"
---

## 不是前言的前言
其实原生ajax和fetch已经很久没用了。。。毕竟axios真是太好用了。   
    
## 原生ajax
其实就是XMLHttpRequest。
或许每个前端在入门js基础的时候都见过这样一段代码:    
    
```js
function success(text) {
    var textarea = document.getElementById('xxx');
    textarea.value = text;
}

function fail(code) {
    var textarea = document.getElementById('xxx');
    textarea.value = 'Error code: ' + code;
}

var request = new XMLHttpRequest(); // 新建XMLHttpRequest对象

request.onreadystatechange = function () { // 状态发生变化时，函数被回调
    if (request.readyState === 4) { // 成功完成
        // 判断响应结果:
        if (request.status === 200) {
            // 成功，通过responseText拿到响应的文本:
            return success(request.responseText);
        } else {
            // 失败，根据响应码判断失败原因:
            return fail(request.status);
        }
    } else {
        // HTTP请求还在继续...
    }
}

// 发送请求:
request.open('GET', '/api/categories');
request.send();

```

这就是最最最原始的ajax请求发送办法了。先new一个xhr对象，然后各种配置。    
open方法配置包括method和url在内的几种参数，send方法发送出去。然后onreadystatechange设置回调监听请求抛出之后发生了什么，走到了哪一步。         
当然这是最简单的情况，有时候你为了设置头信息，还不得不调用一个setRequestHeader()。    
这么多属性和api，也记不住呀。。。😂     
xhr对象的配置繁琐而冗长，不太可爱。   

<!--more-->  
     
于是我们进化出了jQuery的$.ajax方法。这个方法对xhr对象做了封装，允许我们用相对好看一点的方式去做同样的事情。但是写法仍然不够优雅，并且配置项也没进化到哪里去。    
    
## 优雅的fetch
fetch真的很优雅。     
有多优雅呢？     
因为它支持了优雅的promise和箭头函数等新特性， 它可以这样写：    

```js
fetch(url).then(response => response.json())
  .then(data => console.log(data))
  .catch(e => console.log(e)
```
哇，这么短，这么好读，这么好写~~      
并且因为它支持了优雅的promise，所以它还可以更优雅地搭配async/await：     
  
```js
async test() {
  try {
    let response = await fetch(url);
    let data = await response.json();
    console.log(data);
  } catch(e) {
    console.log(e);
  }
}
```

相比之下，fetch的优点显而易见，语法简洁，好读，并且具备新特性带来的各种福利~~    
    
## 更好用的axios
fetch都这么美了，为啥还要用axios?    
读了[这篇文章](https://zhuanlan.zhihu.com/p/28206325), 你可能会做出同样的选择。    
文章本身讲解得非常清楚，这里我只是做个总结:    
总体来说，fetch是一个较为底层的api，它没有细致的错误处理，没有贴心的默认配置，没有简单易用的传参机制。    
   
具体而言：    
   
- 错误处理：fetch眼里，只要能成功拿到一个response，这个请求就可以resolve。这是什么道理？我404，500的请求结果，也能算resolve了？    
axios帮我们解决了这个尴尬。它处理得更加细致，只要是状态码不在200-299之间的响应，全部给你reject掉。   
                    
- 默认配置：因为fetch太底层了，cookie你自己传，csrf配置你自己传，总之啥都自己传吧。不会有人帮你写的。啥？你说默认值？不存在的，默认值就相当于没有。而作为成熟的网络请求库， axios为你考虑好了这些。它不仅帮你写好了一部分合理的默认配置，还允许你在全局自定义默认配置。     
   
- 传参机制： fetch请求的body部分必须是个字符串，并且返回的response也是个流对象你不得不json()一下它才可以变成你想看到的样子。     
- 其它： 以及类似于fetch无法取消一个请求而axios可以这样的特点，其实是因为axios内部封装的也是xhr对象，该对象虽然配置繁琐，但贵在灵活~~    
    
## 后记
如此，~~陈独秀同学~~axios完胜fetch和ajax！       
axios你站在此处不要走，我去给你买橘子！！！！！     
（逃。。。。。。     




