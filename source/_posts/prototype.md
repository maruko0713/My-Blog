---
title: 实现一个长长的正确的原型链
date: 2016-09-04
tags: ["javascript","原型链"]
---

## 前言
作为一只野路子前端狗，理论基础不扎实会给日常开发带来很多困扰。上个月入了《javascript权威指南》和《javascript高级程序设计》（电子版的排版总把我搞得云里雾里，咬咬牙还是买了实体书）回来后，一阵猛刷。我想接触过js的小伙伴们一定对作用域和原型这两个概念不陌生。对于入门时的我来说，这两个概念是整本书当中比较复杂的知识点，也是我反复阅读反复尝试反复琢磨最多的地方。不过，一旦真的理解了，其实也蛮简单的。所以啃基础的时候，一定要克服自己的畏难心理！（严肃脸）。    
好了，上面一段基本上是一段鸡汤，这篇文章主要是记录一下今天发现的一个奇妙的实现长长的原型链的办法。

## js的继承为什么搞得这么麻烦
学过java的小伙伴一定对java中类的继承特性不陌生，一个类继承另一个类，只需简单的一个extends即可。然而js的麻烦之处就在于它根本没有class的类型，没有class，语言的功能就会被削弱很多，那咋整。不怕，虽然我们没有class这种类型，但我们有class的概念啊！在js中，我们把一个构造函数看作是js的一个类，想要继承的属性和方法，我们写进构造函数的prototype属性上，用构造函数new出来的对象就可以引用prototype中的属性和方法。平时我们写代码，见过最多的肯定是一层的继承，那万一我想继承很多个呢?

## 多层继承实现遇到的困难
一起来看一个单层的原型继承怎么实现:  
```js  
function Student(props) {
    this.name = props.name || "" ;
}
Student.prototype.hello = function() {
    console.log("hello!" + this.name + "!");
}
```
此时我想增加一层GoodStudent,让它继承Student，我们当然可以这么做:    
```js
function GoodStudent(props) {
    Student.call(this, props);
    this.study = "good";
}
```
这样，如果我们去new一个GoodStudent的实例出来的话，它是不能引用Student.prototype对象的，为啥呢，因为我们在确定一个对象的\_\_proto\_\_属性（即它的原型）时，其实是找的它构造函数的prototype属性里的constructor属性，这里我们既然是用GoodStudent来new的，那么我们的实例就是GoodStudent的实例，GoodStudent的prototype是谁，这里我们没定义，那就是一个仅有constructor属性的Object对象.再往上找，Object对象的\_\_proto\_\_属性是谁，是Object构造函数的prototype属性，再往上找,Object的prototype属性的\_\_proto\_\_就是null了，看到没，这里根本就没有Student的戏份。    
于是我们知道，我们之所以只能实现一个从自定义构造函数向Object构造函数的上溯，是因为我们的自定义构造函数的GoodStudent的prototype的*默认值*就是一个只有constructor的Object的对象，它只能上溯去找Object！那么这就简单啦，我们把它的prototype改成别的对象不就行了么！    

## 道格拉斯的原型链加长法
道格拉斯，就是发明JSON的那个道格拉斯。废话不多说，我们来改良上面的代码:    
```js
// GoodStudent构造函数:
function GoodStudent(props) {
    Student.call(this, props);
    this.study = "good";
}

// 空函数F:
function F() {
}

/*
 *注意这里，当我们把Student原型赋给F的原型对象时，F.prototype.constructor返回的就是
 *Student了，于是F创建出来的实例都将被认为是Student的实例.
 */
F.prototype = Student.prototype;

// 把GoodStudent的原型指向一个新的F对象，F对象的原型正好指向Student.prototype:
GoodStudent.prototype = new F();

//注意我们把prototype重写了，它不再拥有默认的constructor属性，我们给他补上
GoodStudent.prototype.constructor = GoodStudent;

// 继续在GoodStudent原型（就是new F()对象）上定义方法：
GoodStudent.prototype.getStudy = function () {
    return this.study;
};

// 创建xiaoming:
var xiaoming = new GoodStudent({
    name: "小明"
});
console.log(xiaoming.name); // '小明'
console.log(xiaoming.study); // good

// 验证原型:
console.log(xiaoming.__proto__ === GoodStudent.prototype); // true
console.log(xiaoming.__proto__.__proto__ === Student.prototype); // true

// 验证继承关系:
console.log(xiaoming instanceof GoodStudent); // true
console.log(xiaoming instanceof Student); // true
```
成功了!

## 总结
所有的自定义构造函数在创建之初，它都有一个默认的prototype属性，这个属性指向一个Object对象，如果我们想要延长原型链，在当前构造函数和Object构造函数之间增加一个类的继承，我们就需要去改造当前构造函数的prototype属性，使它指向一个可以指向“别处”（即它的“父类”）的对象，以此来实现一个加长版的原型链^_^_
