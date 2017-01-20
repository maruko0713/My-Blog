---
title: Javascript系统复习：Javascript语句和严格模式
date: 2016-08-14
tags: ["javascript"]
---
## 前言:
这篇博客的两大块，分别是语句和严格模式。    
Javascript程序由***语句***组成，语句遵守特定的语法规则。    
例如: if语句， while语句， with语句等。    
详细说的话，语句可以分为非常多的种类，这里拣几个重要的来说一下。    
    
***严格模式***是一种特殊的执行模式，它修复了部分语言上的不足，提供更强的错误检查，增强安全性。    

## 块 block
块语句常用于组合0～多个语句。    
块语句用一对花括号定义。    
    
```js
语法：    
{
    语句1；
    语句2；
    ...
    语句n;
}
```
一般和if或者while这样的结合起来使用。 
关于这个花括号的用途，我们要结合语境来看:   
    
```js
{a:1, b:2};
var o = {a:1, b:2};
```
楼上 第一句会报错，因为当花括号单独出现在句首的时候，就被认为是一个块语句的开头，往下读发现一个冒号，解释器就不明白了，因为冒号在此处并不能被理解为一个合法的运算符，于是报错。    
（第二句没毛病）。    

***注意: 没有块级作用域***
(ES6里面是有的)    
    
## 声明语句
就是声明变量的语句。   
   
```js
var a = 1;
```
这就是一个标准的声明。    
   
这里有个小点：    
   
```js
function foo() {
    var a = b = 1;
}
foo();
console.log(typeof a); // 输出 "undefined"
console.log(typeof b); // 输出 "number"
```

楼上，我们看到a是正常的，b很奇怪，明明是在函数里面定义的，出了这个作用域怎么还有呢。    
当我们在声明的同时连续赋值的时候，这个赋值的顺序是从右往左进行的，于是先给b赋值1，b前面并没有var,非严格模式下，b被认为是全局变量赋值了，随后被声明的a才被赋值，于是就有了这样的结果。   
如果想要实现它们全都在自己当前作用域下，这样做：   
   
```js
var a = 1, b = 1;
```

## try-catch语句
try-catch是常见的异常捕获机制。    
先尝试执行try中的代码，然后如果过程中出了错，那么catch语句就去捕获这个错误。   
js中的三种try-catch有三种形式：    
- try&catch
- try&finally
- try&catch&finally

我们需要关注的是三者嵌套时的顺序:    
    
```js
try {
    try {
        throw new Error("oops");
    }
    finally {
        console.log("finally");
    }
} catch(ex) {
    console.error("outer", ex.message);
}
```
比如这段代码怎么走呢？    
输出的结果是:  先"finally", 后"outer" "opps"。    
为什么呢？就是说里面的那个错误在被抛出之前，必须先把自己的这个系列的代码执行完，然后才能跳出来。    
再看一种情况:    

```js
try {
    try {
        throw new Error("oops");
    }
    catch(ex) {
        console.error("inner", ex.message);
    }
    finally {
        console.log("finally");
    }
} catch(ex) {
    console.error("outer", ex.message);
}
```
怎么走？    
肯定是先"inner" "oops",然后"finally"啊！   
"outer"不会再输出了，因为错误已经被内部捕获过了^_^。    
   
## function 函数
function语句用来定义函数^_^。    
看一段代码:    
   
```js
function fd() {
    // do sth
    return true;
}
```
这玩意儿叫做函数声明。    
   
```js
var fe = function() {
    // do sth;
}
```
这玩意儿叫函数表达式。    
区别是啥？   
函数声明会被前置，而函数定义表达式不会。    
在函数声明前面调用这个函数也没差，但是函数定义表达式不行。   
这个写到函数的时候再细说。    
   
## 循环
### for in 用来遍历对象的属性。    
   
```js
var p;
var obj = {x:1, y:2};

for(p in obj) {
    // todo
}
```
楼上就可以遍历obj的所有属性了。   
需要注意的几点：    
- 顺序是不确定的
- enumerable为false时不会出现（这个是对属性的配置 写到对象的时候再复习一遍）
- for in 对象属性受原型链的影响

### switch语句
这个其实没啥好说的，哈哈。    
   
```js
var val = 2;
switch(val) {
    case 1:
      console.log(1);
      break;
    case 2:
      console.log(2);
      break;
    default:
      console.log(0);
      break;
}
```
此处输出的就是2。
如果我不写break,它会继续走下去，走下面的default,然后输出2 0。   
switch如果合写的话：   
   
```js
var val = 2;
switch(val) {
    case 1:
    case 2:
    case 3:
      console.log(123);
      break;
    case 4:
    case 5::
      console.log(45);
      break;
    default:
      console.log(0);
}
```
楼上，几种情况如果合写的话，就是前面几个后面啥都不跟，把要作出的反应写在这一系列情况中的最后一个下面就行了。    
楼上输出123。   
    
### 其它
还有while啊， do while啊， for啊，啥的，几乎所有的编程教材都讲的， 就不再说了。    
    
## with语句
with语句可以修改当前的作用域。   
    
```js
with({x:1}) {
    console.log(x);
}
```
我们看到with后面接个对象的时候，可以直接根据属性名输出。   
当我们的代码有很多层的时候，我们就可以用with来帮我们省事。    
比如我们需要处理一个表单:
   
```js
with(document.forms[0]) {
    console.log(name.value);
}
```
楼上，是不是少些了不少代码？   
不过我们还有一个少写代码的方法:    
    
```js
var form = document.forms[0];
console.log(form.name.value);
```
   
***在js中不建议使用with***    
原因如下:    
- 让js引擎优化更难
- 可读性差
- 可以被代替
- 严格模式下被禁用
    
因此，with这块虽然是做个了解，但是以后还是忘了它吧！    
    
## 严格模式
第二个boss登场。    
如何进入严格模式？   
很简单:    
   
```js
'use strict';
```

注意，如果我想给一个函数设置严格模式，我这样做:    
   
```js
function func() {
    'use strict';
}
```

如果我想要给整个文件设置严格模式，我这样做:    
   
```js
'use strict';
...
```

从'use strict'往下的部分，就是严格模式执行的范围。   
   
***严格模式的特别之处如下：***
### 不允许使用with
严格模式下使用了with, 就会报错。    
   
### 不允许未声明变量被赋值   
看这段代码：   
   
```js
function() {
    x=1;
    console.log(window.x); //1
}
```
普通模式下，没毛病，x被当作一个全局变量被声明了。   
但是严格模式下，直接报错。     
   
### arguments变为参数的静态副本
看这段代码：    
   
```js
(function(a) {
    arguments[0] = 100;
    console.log(a);
})(1);
```
如果我这样做的话，普通模式下，参数a就会被修改为100。
但是严格模式下，arguments是一个静态副本，和参数变化不同步。   
因此，这里输出的a仍然是1。    
   
### delete参数报错
看这段代码:    
   
```js
(function(a) {
    console.log(delete a);
})(1);
```
一般模式下，如果想要删除一个参数，是会失败的，但是不会报错。    
但是如果是严格模式下，就会报错。    
   
### delete不可配置的属性报错
🌰：   
   
```js
function(a) {
    var obj = {};
    Object.defineProperty(obj,
        "a",{configurable: false});
    console.log(delete obj.a);
}  
```
一般模式下，会返回false，不会报错。   
但是严格模式下，会报错。    
    
### 对象字面量重复属性名报错
🌰：    
   
```js
(function() {
    var obj = {x : 1, x : 2};
    console.log(obj.x);
})();
```
一般模式下，是没毛病的。   
严格模式报错。   
    
### 禁止八进制字面量   
   
```js
console.log(0123);
```
一般模式下按照八进制输出，严格模式报错。    
    
### eval, arguments变为关键字， 不能作为变量或者函数名
🌰：    
   
```js
function() {
    function eval() {

    };
    console.log(eval);
}
```
一般模式没毛病，严格模式报错。    
    
### eval独立作用域
🌰：    
   
```js
function() {
    eval("var evalVal = 2");
    console.log(typeof evalVal);
}
```
非严格模式输出 "number"。    
严格模式下，eval中的代码有一个自己的作用域，此时输出undefined。    
    
还有一部分，不展开讲了，但是还是做一下记录:    
    
- 一般函数调用时，this指向null,  而不是全局对象
- 若使用apply/call, 传入null或者undefined时， this指向null或者undefined, 而不是全局对象
- 试图修改不可写属性（writable=false）时，在不可扩展的对象上添加属性时，全都报错。而不是默默地失败。    
- arguments.caller,arguments.callee被禁用
    
## 总结
这篇文章写到这里，我只想说。    
严格模式好，以后每次都加上一个"use strict"吧！^_^


    









