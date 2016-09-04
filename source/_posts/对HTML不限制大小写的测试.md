
---
title: 小细节:css中的的大小写区分
date: 2016-08-28
tag: "css"
---
## 前言:
  
这篇文章看起来可能没有什么技术含量，是对一个小细节的验证。起因是读《javascript权威指南》这本书时，里面提到说js是大小写敏感的语言，而html不是，因此html中设定属性时属性名属性值不限制大小写，标签名也不限制。由此想到，css中的属性名和属性值以及选择器是否限制大小写呢，今天就是为了验证一下这个问题。     
    
## 对HTML不限制大小写的测试    
js大小写敏感还是比较明显比较常见的，先用一个小例子来看一下HTML对大小写不敏感的是什么样子的:    
来个花样作死的写法:    
```html
<body>
    <Input TYPE="TEXT">
    <input TYPE="BUTTON">
</body>
```
得到的效果和全小写时一样:
![html测试图](http://7xl4oh.com1.z0.glb.clouddn.com/blog4%E6%B5%8B%E8%AF%951.jpg);    
    
当然，为了规范，我们平时还是要坚持全小写为妙。
    
## 对css是否支持不区分大小写的测试    
直觉上应该是不支持的。。。但我们还是用代码来看一下:        
首先我们从id下手:    
css部分:    
```css 
#Test {
    height:100px;
    width:100px;
    background:red;
    color:#fff;
    font-size:33px;
}
```
结构部分:    
```html
    <div id="test></div>
```
大小写不同，我们看到火狐君给我们的效果是:    
![大小写测试css1](http://7xl4oh.com1.z0.glb.clouddn.com/blog22.jpg)    
一片空白，服了，id要区分大小写.    
    
虽然感觉已经知道结果了，但还是来一发class:    
css部分:    
```css
.Test {
    height:100px;
    width:100px;
    background:red;
    color:#fff;
    font-size:33px;
}
```
结构部分:    
```html
    <div class="test></div>
    <div class="Test></div>
```
得到的结果是:    
![大小写测试css2](http://7xl4oh.com1.z0.glb.clouddn.com/blog4%E6%B5%8B%E8%AF%952.jpg)    
    
好啦，心服口服,class和id是不能模糊大小写的，所以更要坚持全小写啦。
    
那么选择器在书写的时候，是否完全区分大小写呢？经测试，id和class选择器是必然要区分的，而标签选择器就比较随意啦，不区分大小写,写"H2"照样可以选中二号标题标签^_^    
    
对于属性值和属性名,它们也是不区分大小写滴～～～～比如:    
```css
<style>
    .test {
        Height:100px;
        width:100px;
        Background:red;
        color:#fff;
        font-size:33PX;
    }
</style>
```
html部分:
```html
    <div class="test">哈</div>
```
我们看到这个height，width属性名和font-size的属性值都模糊了大小写。    
然而我们仍然可以得到:    
![css属性大小写测试](http://7xl4oh.com1.z0.glb.clouddn.com/css%E5%A4%A7%E5%B0%8F%E5%86%99%E5%B1%9E%E6%80%A7%E6%B5%8B%E8%AF%95.jpg)    
    
于是，我们得出结论：在css中:    
- id名和class名是区分大小写的    
- 选择器中，只有标签选择器不区分大小写    
- 属性名和属性值是不区分大小写的    
    
这样一来,css大小写敏感的问题就解决啦，但是为了规范，我们平时还是要坚持小写到底啊～～～哈哈