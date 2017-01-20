---
title: Javascript系统复习：Javascript数据类型
date: 2016-08-10
tags: ["javascript"]
---
## 前言:
这个系列的文章是对Javascript基础知识的复习和巩固，也是对这段时间读过的书的一个交代。    
文章的内容来源于对《javascript高级程序设计》，《javascript权威指南》两本书的阅读，总结和反思。很好的书，没有白买。    
   
## Javascript的六种数据类型
### 原始类型
- number
- string
- boolean
- null
- undefined

### 对象
- object

有的时候，我们把Array也归为一个单独的类型，事实上，它仍然是object的一种体现。    
    
## 弱类型语言
在js中定义变量的时候，不需要指定类型。    
   
```js
var num = 32;
num = "this";
```
上面这样，是没毛病的。   
因此，js是一个**弱类型**的语言。    
弱类型虽然在定义的时候省事，但是也因此使我们在进行**隐式转换**的时候要格外留心。    
    
## 隐式转换
当字符串和数字在一个表达式中同时出现时：   
  
### 加减号
```js
var x = "37" + 7;
var y = "37" - 7;
```

如果中间是加号，那么加号两边就会都被当成字符串处理。    
如果中间是减号，那么减号两边都会被当成数字来处理。    
因此x是377,y是30。   
   
## 等号
### 严格等于
严格等于是三个等号。   
严格等于首先会判断两遍的类型，如果类型不同，那么直接false。
如果类型相同，才会再比下去。   
对于对象，对象之间比较的是引用（其实就是内存地址）。因此对象和对象之间比的是内存地址噢，    

### 非严格等于
非严格等于是两个等号。    
当两边类型相同时，它的模式和严格等于是一样的。 
跨类型的时候，隐式转换就登场了。      
跨类型举一些🌰：    
   
```js
null == undefined
```
输出true,这个是死的。这两个类型就是这种关系～    
  
```js
"1" == 1;
```
也是true, 当等号两边是字符串和数字的时候，字符串会转换成数字再比较。    

```js
true == "1";
```
输出true, 当等号两边有一边是布尔值时，不管另一边是什么，布尔值自己先转换成number再说。    

```js
new String("hi") == "hi";
object == number | string
```
对象和基本类型做比较的时候，只有对于number和string， 它会尝试把自己转换为基本类型再做比较。        
其它全都是false。    
    
注： NaN类型和谁都不等，连NaN ==（＝） NaN都返回的是false。    
    
## 包装对象
包装对象主要是说string类型和String对象,number类型和Number对象，boolean类型和Boolean对象。    
以String对象为例：   

   
```js
var str = "string";
var strObj = new String("string");
str.t = 10;
console.log(str.length);
console.log(strObj.length);
console.log(str.t);
```

输出的是6，6，undefined。   
当我们尝试用访问String对象的方式访问string类型的时候，js解释器会自动new一个String对象给你，这个String对象的值就是string字符串的值，此时这个字符串就像被包装了一层一样，继续参与下面的运算。因此我们输出str.length，也成功了。    
正因为每次我们这样尝试调用，都会创建新的String对象，所以每次创建的都不一样。str.t=10这个赋值，赋值给了第一次包装起来的String对象，它随即被释放了，紧接着又创建了一个新的String对象，这个String对象可没有t属性，它只有String类型的内置属性，因此t是undefined。    
    
## 类型检测
在js中，检测类型的方法有很多：    
－ typeof运算符
- instanceof运算符
- Object.prototype.toString.apply()方法
- constructor
- duck type

### typeof
最常见的类型运算符。    
   
```js
typeof 100;
typeof true;
```
输出"number", "boolean",它输出的是字符串。   
特殊的是如果这样输入:   
   
```js
typeof null;
```
输出的也是object,但是其实null就是null,这个注意一下就行了。    

### instanceof
这个在对象里面用的比较多。    
instanceof是基于原型链来进行判断的。    
   
```js
obj instanceof Object;
```
它会判断obj的原型链上是否有Object这个对象。    
   
🌰：    
   
```js
[1, 2] instanceof Array == true;
new Object() instanceof Array === false;
```
两个都是正确的。    
任何一个构造函数，都会有一个prototype对象属性。   
这些属性连接起来，就是一个原型链。   
   
```js
function Person() {
  //to-do
}
function Student() {
  //to-do
}
Student.prototype = new Person();
Student.prototype.constructor = Student;
var wanzi = new Student(); 
wanzi instanceof Student;  // true
var wanzi2  = new Person();
wanzi2 instanceof Person; // true
wanzi2 instanceof Student; // false
wanzi instanceof Person; // true
```

上面这种情况，就是由原型链造就的。    
原型链这个得再写个文章了，不急。   
上面创建了一个wanzi对象，wanzi为什么student和person都是true？   
首先wanzi有个prototype，它指向的是Student。    
wanzi instanceof Person这句执行的时候，在第一次查找\_\_proto\_\_(即原型链中的第一个原型对象)属性的时候，没有找到自己想要的Person对象，它就继续向原型链上面查找。wanzi.\_proto\_返回的是Student，它继续查找Student.\_\_proto\_\_,此时找到了Person,于是就断定返回为true了。    

### Object.prototype.toString的方法们
直接看🌰：    
   
```js
Object.prototype.toString.apply([]) === "[object]";
Object.prototype.toString.apply(function(){}) === "[object Function]";
Object.prototype.toString.apply(null) === "[Object Null]";
Object.prototype.toString.apply(undefined) === "[Object Undefined]";
```
我都这么写了，这些等式肯定是都成立的。    
   
### construtor
任何一个对象都有一个constructor属性，指向它的构造函数。    
因为它很容易被改写，所以不靠谱。     

    
### duck type
duck type 是说通过一些特征来判断数据的类型。    
   
## 总结
最常见的还是下面两个:    

- typeof 适合基本类型和function的检测，碰到null就狗带
- instanceof 主要用来处理对象

欧了！数据类型get！

    







