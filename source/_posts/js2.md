---
title: Javascript系统复习：Javascript表达式和运算符
date: 2016-08-12
tags: ["javascript"]
---
## 前言:
今天对表达式和运算符做总结。   

***表达式***是指能计算出值的任何可用程序单元。    
在js中，表达式是一种js短语，可以使js解释器用来产生一个值。   

***运算符***在js里面也是比较基础的一个东西，它一般用于表达式和表达式之间，做一些运算。   

    
## 表达式
一共有六种：    
- 原始表达式
- 初始化表达式
- 函数表达式
- 属性访问表达式
- 调用表达式
- 对象创建表达式

<!-- more --> 

具体的🌰：

### 原始表达式
- 常量，直接量： 3, "test"    
- 关键字： null, this, true, false
- 变量： i, k, j

### 复合表达式
原始表达式和原始表达式加上一个运算符，就是复合表达式。   
    
### 数组，对象的初始化表达式
- [1, 2] 等价于 new Array(1, 2)
- {x:1, y:2} 等价于 var o = new Object();o.x=1;o.y=2;
 
### 函数表达式
```js
var fe = function() {};
(function(){})();
```
匿名函数赋值，函数直接调用，这都是函数表达式。    
   
### 属性访问表达式
```js
var o = {x:1};
o.x;
o["x"];
```

访问x的两个表达式就是属性访问表达式。   
   
### 调用表达式
简单粗暴就是：   
  
```js
funcName();
```

调用一个已经定义的函数。    
    
### 对象创建表达式
   
```js
new Func(1, 2);
new Object;
```

调用构造函数，如果不需要传参，那么括号可以省略。    

## 运算符
运算符按照操作数的数量可以分为:    
- 一元运算符 比如 +num
- 二元运算符（最常见） 比如 a + b
- 三元运算符 比如 c ? a : b

按照功能分类:
- 赋值运算符 x += 1
- 比较运算符 a == b
- 算术运算符 a + b a - b啥的
- 位运算符 a | b
- 逻辑运算符 exp1 && exp2
- 字符串拼接  "a" + "b"
- 特殊运算符 delete obj.x等等

### 特殊运算符举🌰
比如:    
   
#### 条件运算符:   

```js
c ? a : b;
var val = true ? 1 : 2;
```
val=1。

#### 逗号运算符:   
   
```js
var val = )(1, 2, 3);
```
用逗号隔开表示每个表达式都会被计算，最后取最右面的值。   
（val＝3）。    
   
#### delete 运算符:
   
```js
var obj = {x:1};
obj.x;
delete obj.x;
obj.x
```
用于删除一个对象里面的属性。    
   
***并不是所有的属性都可以被删除***
从IE9开始有了一个新方法:    
    
```js
var obj = {};
Object.defineProperty(obj, "x", {
  configurable: false,
  value: 1
});
delete obj.x;
obj.x;
```

注意这个defineProperty方法啊。   
里面的configurable的标签只有是true,属性才可以被删除。    
   
#### in 运算符
```js
window.x = 1;
"x" in window;
```
返回true，用于判断一个属性是否存在于一个对象中。    
    
#### instanceof typeof啥的
在上一篇文章中回顾过了，它们是用来判断类型的。    
还是举一些🌰：    
    
```js
{} instanceof Object; //true 基于原型链判断对象的类型
typeof 100 === "number"; //true 多用于判断原始类型或者函数的类型
```

#### new 运算符
```js
function Foo() {};
Foo.prototype.x = 1;
var obj = new Foo();
obj.x; //1
obj.hasOwnProperty("x"); // false
obj.__proto__.hasOwnProperty("x"); //true
```

new 根据指定的构造函数生成一个对象，这个对象的原型就是这个构造函数。   
    
#### this 运算符
在浏览器里面:   

```js
this; // 指向window
var obj = {
    func: function() {
        return this;
    }
};
obj.func(); //obj
```
this 在不同情况下会有不同的值，这个需要展开讨论，像原型链一样，我会单独写文章。    
    
#### void 运算符
很少见，一元运算符。   
不管后面的操作数是谁，最后都返回undefined。    
    
### 运算符的优先级
这个从学c语言的时候就开始强调，很杂，我的感觉一个是多写多熟悉，另一个就是实在不行上括号，括号万岁，通俗易懂，省时省力。    
    
上图！      
![运算符优先级](http://7xl4oh.com1.z0.glb.clouddn.com/%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BC%98%E5%85%88%E7%BA%A7.png)
   
## 总结
哈哈，写这篇总结，让我有一种回到大一的感觉。   
真想念熊老师的c语言课啊啊啊啊啊啊～～～   
好！现在去看会儿别的书～～～^_^





    




