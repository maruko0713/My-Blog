---
title: Javascript系统复习：函数和作用域
date: 2016-08-23
tags: ["javascript"]
---
## 前言:
Javascript中的函数和由函数带出的作用域～～     
***函数***是一块Javascript代码，被定义一次，但是可以被执行和调用多次。   
JS中的函数也是对象，所以JS函数可以像其它对象那样操作和传递，所以我们也常叫JS中的函数为函数对象。   
    
## 函数
函数中的几个部分:   
- 函数名
- 参数列表
- 函数体
    
🌰：   
   
```js
function foo(x, y) {
    if(typeof x === "number" && typeof y === "number"){
        return x + y;
    } else {
        return 0;
    }
}

foo(1, 2); //3
```

<!-- more -->
楼上，foo是函数名，花括号内是函数题，圆括号内是参数列表。    
函数的几个比较重要的点：    
- this
- arguments
- 作用域
- 不同调用方式
- 不同创建方法
   
### 不同的调用方式
直接调用:     
   
```js
foo();
```

对象方法:   
   
```js
o.method();
```

构造器:    
   
```js
new Foo();
```
    
call/apply/bind:    
   
```js
func.call(o);
```
   
### 不同创建方法（函数声明与表达式和函数构造器）
看几个例子:    
    
function开头比较单纯的这种叫做函数声明：    

```js
function add(a, b) {
    //todo
}
```

这样赋值一般的叫做函数表达式:   

```js
var add = function(a, b) {
  // dosth
};
```

这样声明了之后立即调用也叫做表达式：

```js
(function() {
  //todo
})();
```
   
这样的也叫做表达式：

```js
return function() {
    //osth
}
```

这样有名字的赋值叫做命名函数表达式：   

```js
var add = function foo(a, b) {
    // dosth
}
```

命名函数表达式我们要注意，右边这个名字，在比较新的浏览器里面，我们是访问不到的。   
在一些老的浏览器里面，可以访问的到，但是它指向的和左边的竟然不是一个东西。   
这个很诡异，注意一下就好了。   

   
***函数声明与函数表达式最大的区别就是函数声明会被前置***

还有一种方法是用Function构造器来创建一个函数:   
   
```js
var func = new Function("a", "b", "console.log(a+b)");
func(1, 2); //3
```
或者：    
   
```js
var func = Function("a", "b", "console.log(a+b)");
func(1, 2); //3
```

楼上这样的，构造器的前面是形参，最后一个参数是函数体。  
***Function构造器创建出的函数，可以拿到全局变量，但是拿不到它外层的函数中的变量，因此很少使用***   
用个图来总结一下:    
   
![函数创建方式定义图](http://7xl4oh.com1.z0.glb.clouddn.com/js8.png)   
   
## this
this比较灵活，根据环境的不同，this会发生变化。    
   
### 全局的this
全局下的this就是指向全局对象，浏览器中，就是window。   

🌰：

```js
console.log(this.documeng === document); // true
console.log(this === window); // true
 
this.a = 37;
console.log(window.a); // 37
```
   
### 一般函数的this(浏览器)
一般函数中的this仍然指向全局对象window:    
   
```js
function f1() {
    return this;
}   
f1() === window; //true
```
在nodejs中，就是global object啦～～～   
   
需要注意，严格模式下，函数中的this指向undefined～～    
   
### 作为对象方法的函数的this
指向调用它的对象：    
    
```js
var o = {
    prop: 37,
    f: function() {
        return this.prop;
    }
};

console.log(o.f()); // 37
```
注意指向的是***调用它***的对象，为了强调这一点:   
   
```js
var o = {props: 37};

function independent() {
    return this.prop;
}

o.f = independent;

console.log(o.f()); // logs 37
```
    
### 对象原型链上的this
仍然指向调用它的那个对象。   
    
🌰：   
    
```js
var o = {f: function() { return this.a + this.b } };
var p = Object.create(o);

p.a = 1;
p.b = 4;

console.log(p.f()); //5
}
```

### 构造器中的this
当我们用new 构造函数()的形式调用构造函数的时候，里面的this指向一个以该构造函数为原型的新对象。   
🌰：    
   
```js
function MyClass() {
    this.a = 37;
}

var o = new MyClass();
console.log(o.a);

function C2() {
    this.a = 37;
    return {a:38};
}

o = new C2();
console.log(o.a); //38
```
   
注意当我们有return了一个对象的的时候，以return的那个对象为准。   
没有return一个对象的时候，默认this就是return的东西。    
   
### call/apply方法与this
call/apply方法可以用来修改this。    
   
```js
function add(c, d) {
    return this.a + this.b + c + d;
}

var o = {a:1, b:3};

add.call(o, 5, 7);

add.apply(o, [10, 20]);
```
注意call和apply的效果是一样的，第一个参数是this指向的对象，后面的参数就是函数正常的传参。    
不同的是call中就是按顺序把参数写进去，但是apply当中需要把参数以数组形式传入。    
    
### bind方法与this
bind方法是ES5才开始提供的。    
也是用来修改this的指向，然后，注意它只是修改指向，并不调用函数。    
🌰：    
   
```js
function f() {
    return this.a;
}

var g = f.bind({a:"test"});
console.log(g());
```
它和apply和call最明显的区别就是，调用的这个操作的主动权在我们手上，我们让它什么时候执行，它就什么时候执行。    
还有一点，它很“顽固”。就是当我们用bind绑定过了this之后，它就算被当作别的对象的方法调用,this也不会改变，除非你再次调用bind修改。   
🌰：    

```js
var o = {a:37, f:f, g:g};
console.log(o.f(), o.g()); //37, test
```
   
## 总结
啊，这篇文章我觉得最重要的还是后半部分的this啦～～
要熟悉啊要熟悉！    
下一篇开始写arguments～～～^_^

   
   


   


