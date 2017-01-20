---
title: Javascript系统复习：对象标签和对象序列化
date: 2016-08-19
tags: ["javascript"]
---
## 前言:
前面我们说属性是有标签的，于是***对象也是有标签的***。    
    
## 对象标签
对象标签主要有三种:    
- [[proto]]
- [[class]]
- [[extensible]]
   
### 原型和class

第一个标签对应的是原型，我们可以通过prototype属性来访问。    
第二个标签没有一个指定的方式去查看或者修改它，我们可以间接地通过Object.prototype.toString方法来获取它。   
举个例子：    
   
```js
var toString = Object.prototype.toString;
function getType(o) {
    return toString.call(o).slice(8, -1);
}    

toString.call(null); // "[object Null]"
getType(null); // "Null"
getType(undefined); // "Undefined"
getType(1); // "Number"
getType(new Number(1));// "object"
typeof new Number(1); // "object"
getType(true); // "Boolean"
getType(new Boolean(true)); // "Boolean"
```

注意，在调用object.toString方法的时候，它会去把参数转换为对象，然后再作处理。    
因此slice方法就截去了object那几个字母，只返回我们想看到的对象。    
    
***挖坑***：需要搞清楚Object和Function对象上的toString到底什么区别。    

### extensible标签
extensible标签，表示对象是否可扩展。   
言外之意，就是你对象上的属性是否可添加。   
我们用Object.isExtensible这个方法来判断属性是否可添加。    
如果我们不想让一个属性扩展，可以使用preventExtensions方法。   
🌰：    
   
```js
var obj = {x: 1, y: 2};
Object.isExtensible(obj); //true
Object.preventExtensions(obj); 
Object.isExtensible(obj); // false
obj.z  = 1;
obj.z; // undefined
```

另外，还有一个Object.seal方法，它会在Object.preventExtensions的基础上把所有的属性都变成不可配置的。   
   
```js
Object.seal(obj);
Object.getOwnPropertyDescriptor(obj, "x");
// Object {value: 1, writable: true, enumerable: true, configurable: false}
object.isSealed(obj); //true
```
   
另外 Object.freeze  方法，会在seal方法的基础上把writable也变成false～～    
  
***注意***：我的所有操作，只针对对象的自有属性，不影响原型链。     
    
## 序列化 其它对象方法
啥是对象序列化？    
有时候我们需要把对象变成字符串方便传送给后端，这时候我们就要对对象进行序列化。    
   
```js
var obj = {x: 1, y: true, z: [1,2,3], a:undefined, b: NaN, c: Infinity};
JSON.stringify(obj); // "["x":1, "y":true,"z":[1,2,3],"b":"null","c":"null"]"
```

如果你序列化的值是undefined的话，那么它就不会出现在序列化的结果中。    
如果你序列化的值是NaN或者Infinity, 它就会被序列化成null。    
如果你序列化的值是时间对象，它会输出UTC格式的时间。    
   
后端如何解析js序列化字符串？   
   
```js
obj = JSON.parse('{"x": 1}');
obj.x;
```
就是这个JSON.parse方法。   
   
### 其它重要的对象方法补充    
- toString方法前面用过很多次了，就是返回对象的class标签 
- valueOf 尝试把对象转换为基本类型的时候会调用的一个方法 一般返回数字（和toString同时存在时候，类型转换优先调用valueOf）

   
## 总结
至此，对象这一块的知识点终于梳理得差不多了。   
中间页挖了一些坑，接下来的文章会写更深入的一些话题，加油！^_^



   

