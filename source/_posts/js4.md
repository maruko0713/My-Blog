---
title: Javascript系统复习：Javascript对象和原型链
date: 2016-08-16
tags: ["javascript"]
---
## 前言:
对象是js中的一个非常重要的概念。    
因为在js中，除了基本类型，就是对象。    
对象中包含一系列属性，这些属性是无序的。    
每个属性都有一个字符串key和对应的value。    
    
## 对象的key
key的类型，你在赋值的时候，你可以给key起任何的名字。   
但是随便起名字归随便起名字，名字起完之后，js解释器还是会自动把你起的名字想方设法地转换为字符串，所以最后***key的类型还是字符串***。    

<!-- more --> 
   
## 对象中的属性的标签
每一个属性，它自身都有很多标签，比如:    
- writable
- enumerable
- configurable
- value
- get/set
这些东西用于为每一个属性提供它的权限控制，比如是否可写啥的。   
有点杂，决定单独写篇文章写吧。。。。    
   
## 对象的标签
除了每个属性都有标签和get／set方法之外，每个对象都有一个原型。  
   
### 对象的prototype标签  
每个对象都有一个prototype属性，设置原型用的。        
🌰：  
   
```js
function foo(){
};
foo.prototype.z = 3;
var obj = new foo();
```
此时我们如果访问obj.z,是可以返回3的，因为顺着它的原型链，它找到了z。    
    
### 对象的class标签和extensible标签
class标签表示对象属于哪个种类。    
extensible标签表示这个对象是否还允许增加新的属性。   
    
## 创建对象
创建对象有两种方法:    
   
### 对象字面量
```js
var obj1 = {x: 1, y:2};
```

### new 构造函数
```js
function foo() {

}
foo.prototype.z = 3;

var obj = new foo();
obj.y = 2;
obj.x = 1;
obj.x;
obj.y;
obj.z;
```

### Object.create()方法
看一段代码：   

```js
var obj = Object.create({x: 1});
obj.x; // 1
typeof obj.toString // "function"
obj.hasOwnProperty("x");

var obj = Object.create(null);
obj.toString // undefined
```

往create方法里面传入对象，也可以创建一个新对象。   
注意这个新对象的原型，就是传入的这个参数。     
如果我们传入的是一个字面量，那么这个字面量创建的对象原型就是Object，自然可以引出它的Object.toString方法。   
如果我们传入的是null,我们的对象就没有原型，自然也就调用不出toString方法了。    

这里我们引出了原型。  
我们访问x和y的时候，都正常输出，但你访问z也可以正常输出，这是为什么。    
这就是因为我们沿着原型链向上查找了，所以x其实是foo.prototype对象里面的属性。    
    
## 原型链
原型链就是每一个构造函数的prototype，一个挨一个向上查找，所组成的链条。    
每一个对象的原型链溯源尽头都是Object,Object.prototype为null。    

还是上面那段代码，我们这样做:    
   
```js
"z" in obj; //true
obj.hasOwnProperty("z"); //false
```

就是说虽然你在obj中可以遍历出z这个属性，但是这个属性并不是这个obj对象上的，而是对象的原型链上的。    
   
如果我们这时候这么干：   
   
```js
obj.z = 10;
```

这个操作，是为obj对象增加一个z属性，并不是修改prototype的z属性，现在obj就有了一个自己的z属性了。    
只要你给z赋值，那么obj就拥有了一个自由的属性z，而不再使用原型链里的z。    
   
## 对象的属性操作
对象的属性我们分这几个方面来看:   
- 读写对象属性
- 属性异常
- 删除属性
- 检测属性
- 枚举属性
   
### 属性的读写
很常见，就是这样:   

```js
var obj = {x: 1, y: 2};
obj.x;
obj["y"];

obj["x"];
obj.y = 4;
}
```
   
注意我们在遍历属性的时候经常用for in，for in 的一个缺陷就是它会把原型链上的属性也全部都遍历出来。   
  
### 属性读写的异常
🌰：

```js
var obj = {x: 1};
obj.y;
var yz = obj.y.z;
obj.y.z = 2;
```

常见的异常就是像这样，尝试获取undefined上面的一个属性，就会报错。     
   
### 属性的删除
🌰：    
   
```js
var person = {age: 28, title: "fe"};
delete person.age; // true
delete person["title"]; // true
person.age; // undefined
delete person.age; // true
```

注意我们重复删除已经不存在的属性,它仍然返回true。   
只要这个属性能达到一个不存在的状态，那么都是true。   

有一些属性是不允许被删除的：     
   
```js
delete Object.prototype; //false
```
就会false了～～   
   
### 属性的标签
对于每一个属性，都会有一系列标签，来控制它的权限。    
简单尝试一下:    
   
```js
var descriptor = Object.getOwnPropertyDescriptor(Object, "prototype");
descriptor.configurable; // false
```
descriptor承接下了prototype的所有标签，比如我们试图输出它是否可配置，答案就是false。    
    
### var 定义的变量不能被删除
🌰：   

```js
var globalVal = 1;
delete globalVal;
```
delete是删除不了这样的变量的。   
我们还是用它删除属性吧。    
  
delete删除一个变量，首先就是要看它的声明方式。   
比如同样是全局变量，我这样:    
   
```js
var a = 1;
delete a; // false
window.a = 1;
delete a; //true
```
delete只认变量形式的声明。    
   
### 属性检测
🌰：   
   
```js
var cat = new Object;
cat.legs = 4;
cat.name = "kitty";

"legs" in cat;// true
"abc" in cat;// false
"toString" in cat;// true 继承得到的    
    
cat.hasOwnProperty("legs");// true
cat.hasOwnProperty("toString")// false

cat.propertyIsEnumerable("legs");// true
cat.propertyIsEnumerable("toString");// false
```
 
首先我们用in，我们会发现它自己的属性和方法和原型链中的属性和方法都会被检测出来。 
检测对象自身的属性我们使用hasOwnProperty。
propertyIsEnumerable是用来查看属性是否可以被枚举的。   
啥叫可枚举？当我们console.log的时候，只会显示对象上的直接属性，这是因为原型链上的大部分属性的Enumerable属性都是false，因此不会显示。    
   
### 定义属性标签
如何设置属性的标签?  
用defineProperty()方法。    

举个例子:    
   
```js
Object.defineProperty(cat, "price", {enumerable:false, value:100});
```

第一个参数传入对象名，第二个参数传入属性名，最后一个参数传入属性标签的设定。     
要注意，***当我们自己用new Object或者对象字面量创建对象的时候，它默认是属性标签都为true***。而我们用defineObject()方法创建一个属性的时候，它的标签如果你不特殊写的话，默认都是false~~~,所以楼上的enumerable有点多此一举啦～～    
    
## 小结
没想到写了这么长，只能暂时打住了，不然下次再看会花眼。   
这篇文章中篇幅比较大的还是属性方面的知识点。   
下一次会继续复习属性，嘿嘿～～





   


   





    
