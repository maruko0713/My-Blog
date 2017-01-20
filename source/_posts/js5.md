---
title: Javascript系统复习：get和set方法／属性标签深入
date: 2016-08-17
tags: ["javascript"]
---
## 前言:
上一篇太长了，我给它截成两部分来讲。   
这一篇会开辟get和set的新世界，然后就上一篇当中的属性标签再往下深入一下。   
    
## getter和setter方法
看一段代码:   
   
```js
var kid = {
    name: "wanzifa",
    weibo: "miemie",
    get age() {
        return new Date().getFullYear() - 1996;
    },
    set age(val) {
        console.log("不能修改成" + val);
    }
}
console.log(kid.age);
kid.age = 100;// 输出 不能修改成100
console.log(kid.age);// 还是27
```

get,set方法很特别，它们和一般的方法不一样。   
一般我们定义一个方法，是方法名:方法体。    
这两个方法是以get或者set开头，然后直接跟属性名，后面跟方法体。    
这里面我们定义的get方法按照我们的想法去输出我们想要获取的属性值，
set方法按照我们定义的方式去对我们的属性设置操作作出反应。   
   
再举个例子： 
   
```js
var kid = {
    name: "wanzifa",
    $age: null,
    get age() {
        if(this.$age == undefined) {
            return new Date().getFullYear() - 1996;
        } else {
            return this.$age;
        }
    },
    set age(val) {
        val += val;
        if(isNaN(val) && val > 0 && val < 150) {
            this.$age = +val;
        } else {
            throw new Error("Incorrect val=" + val)''
        }
    }
}
console.log(kid.age); // 20
kid.age = 100; 
console.log(kid.age); // 100
kid.age = "xixi"; // 抛出 error:Incorrect val = NaN
```

我们对代码进行进一步的处理，让我们的get和set方法分别具备判断功能。    
get和set方法就像对属性操作进行一个动态的把关。    
    
## get/set与原型链 
先看一段代码:    
    
```js
function foo() {
};
Object.defineProperty(foo.prototype, "z", {get: function(){
    return 1;
}});
var obj = new foo();
obj.z; //1
obj.z = 10;
obj.z; //仍然是1
```
诡异！！   
赋值无法覆盖原型链上的值了！！   
是的，如果原型链上有某属性对应的get/set方法，那么我们即便给现有对象添加了该属性的设置，
在通过当前对象访问属性的时候，仍然优先调用原型链上的get方法返回给你，也就是说你赋值也白赋值。    
咋办？？？    
这时候请出强劲的defineProperty方法:   
   
```js
Object.defineProperty(obj,"z",
{value: 100, configurable: true});
obj.z; //100
delete obj.z; //true
obj.z // 回到1了
```
    
## 属性标签
如何查看一个属性的标签。   
用这个方法:    
   
```js
Object.getOwnPropertyDescriptor(对象，属性名);
```
返回一个对象，对象中的键值对就是它的属性标签。   
比如:    
   
```js
Object.getOwnPropertyDescriptor({pro: true}, "pro");
```
返回:    
   
```js
Object {value:true, writable: true, enumerable:true, configurable:true}
```
其中,value就是属性的值，writable表示属性是否可写，enumerbal表示属性是否可以被遍历（for in遍历，Object.keys方法），configurable表示前面的这些东西能不能被修改，或者该属性能否被删除。    
    
## 另一个定义属性标签的方法
前有defineProperty,后有defineProperties。    
🌰：    
   
```js
Object.defineProperties(person,{
  title: {value, "fe", enumerable:true},
  corp: {value: "Baba", enumerable: true, writable: true},
  salary: {value: 50000, enumerable: true, writable: true}, 
  luck: {
    get: function() {
        return Math.random > 0.5 ? "good" : "bad";
    }
  },
  promote: {
    set: function(level) {
        this.salary *= 1 + level*0.1;
    }
  }
});

Object.getOwnPropertyDescriptor(person, "salary");
// Object {value:50000, writable:true, enumerbale: true, configurable: false}
Object.getOwnPropertyDescriptor(person, "corp");
// Object {value: "BABA", writable:false, enumerable:true, configurable: false}
```
  
嘻嘻，实现了一口气定义一堆标签的梦想哈哈哈哈。    
注意上面这段代码，我们属性标签中的标签不仅可以是属性，还可以是方法，前提是set/get方法。    
   
## 属性标签小总结
先上图：    
![属性标签总结图](http://7xl4oh.com1.z0.glb.clouddn.com/js5.png)
从图上我们可以看出，当configurable和writable都为true,我们想干啥干啥。    
configurable控制的是属性标签和get/set方法的修改和属性的删除。   
wirtable专注于可写不可写问题。    
    
## 总结
终于把属性说完啦哈哈哈哈！！！ 
下一篇看一下对象标签和对象序列化！！^_^






