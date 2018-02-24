
---
title: 笔记：原型链，继承，闭包和作用域概念梳理
date: 2017-03-25
tags: ["原型链"]
---

## 前言
继承是ES5中非常重要的一个点。    
即便现在我们有了ES6，有了类，有了更轻松的写法，但继承的本质是不变的。要理解继承我们就必须理解原型链。  
## 构造函数
写一个构造函数:    
```js
function Foo(name, age) {
  this.name = name
  this.age = age
  this.class = 'class-1'
  // return this  默认有这一行
}
var f = new Foo('miemie', 20)
```
构造函数一般以大写字母开头。    
构造函数tips:    
- var a = {} 其实是 var a = new Object()的语法糖
- var a = [] 其实是 var a = new Array()的语法糖
- function Foo(){}其实是var Foo = new Function(...)的语法糖
- 使用instanceof判断一个函数是否是一个变量的构造函数

## 原型规则
原型规则是学习原型链的基础
- 所有的引用类型，都具有对象特性，即可自由扩展属性（null除外）
- 所有的引用类型，都有一个__proto__属性，属性值是一个普通的对象(隐式原型)
- 所有的函数，都有一个prototype属性，属性值也是一个普通的对象(显式原型)
- 所有的引用类型， __proto__属性指向它构造函数的prototype属性

## this的指向
this的指向是在执行时确定的。  
- 作为构造函数执行
- 作为对象属性执行
- 作为普通函数执行
- call/apply/bind

```js
function Foo(name) {
  this.name = name
}
var f = new Foo('miemie')
// 指向新生成的对象
``` 

```js
var obj = {
  name: 'A',
  printName: function() {
    console.log(this.name)
  }
}
obj.printName()
// 指向obj
```

```js
function fn() {
  console.log(this) 
}
fn()
// 指向window
```

```js
function fn(name) {
  alert(name)
  console.log(this)
}
fn.call({x: 100}, 'name')
// 指向call里的对象
```

## 作用域
作用域只是一个“地盘”，其中没有变量。变量是通过作用域对应的执行上下文环境中的变量对象来实现的。
作用域是一个范围，上下文是这个范围里可访问的数据集合

### 自由变量
首先要知道一个叫**自由变量**的概念
当前作用域没有定义，但是因为父级作用域定义了它，故而我可以在当前作用域引用到它，这样的变量叫做自由变量。    

## 闭包
闭包是引用了自由变量的函数。自由变量即是说当前作用域内为定义的变量，我们可以通过一层一层地向父级作用域查找来访问它。
它形成的条件是函数作为返回值返回。
它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。

## ES5实现继承
```js
function Parent() {
  this.name = 'parent'
}

function child() {
  Parent.call(this)
  this.type = 'child'
}
Child.prototype = Object.create(Parent.prototype)
// Create 创建一个新的对象 该对象的原型是Parent
Child.prototype.constructor = Child
```



