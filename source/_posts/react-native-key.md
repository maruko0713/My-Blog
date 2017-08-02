
---
title: 一图开发手记：React中的key属性踩坑
date: 2017-07-20
tag: "React Native"
---
## 前言:
最近在重构一图的过程中，拾起了初次开发时的一个历史遗留问题。这个问题出现在一图app的书单内容页面，点开一个书单的书目列表，在我将第一本书向左滑动使其显示删除按钮后，它大概长下面这样:    
![书目列表1](http://7xl4oh.com1.z0.glb.clouddn.com/key1.jpeg)  
     

当我点击删除按钮的时候，我的意思是，希望本书消失，然后下面的书顶上来，然而它呈现了这个效果给我:

![书目列表2](http://7xl4oh.com1.z0.glb.clouddn.com/key2.jpeg)
    
确实顶上来了，但是为什么我根本没有滑动哲学大辞典，它还是自己向左移动并把删除按钮露出来了？？？    
准确地说，为啥它似乎看起来像是继承了它楼上那本书(陈景润传)的样式一样？？？？？     
这个就是因为，作为开发者，我没有用好React中的key属性。    

<!-- more -->     
    
## React中的key属性
react中的key属性，它是一个特殊的属性。   
它是出现不是给开发者用的（例如你为一个组件设置key之后不能获取组件的这个key props），而是给react自己看的:

>Key is not really about performance, it’s more about identity (which in turn leads to better performance). Randomly assigned and changing values do not form an identity   

所以react利用key来识别组件，它是一种身份标识标识，就像我们的身份证用来辨识一个人一样。每个key对应一个组件，相同的key react认为是同一个组件，这样后续相同的key对应组件都不会被创建。    
有了key属性后，就可以与组件建立了一种对应关系，react根据key来决定是销毁重新创建组件还是更新组件。    
### 然后下面划重点！！！！！！！
<u>
- key相同，若组件属性有所变化，则react只更新组件对应的属性；没有变化则不更新。
- key值不同，则react先销毁该组件(有状态组件的componentWillUnmount会执行)，然后重新创建该组件（有状态组件的constructor和componentWillUnmount都会执行）
</u>   
    
## 我错在哪里了 我怎么改的
我的图书列表，是这么写的:    
   
```jsx
<ScrollView style={styles.item_container}>
  {this.state.book_list.map((item, i) => {
    return (
      <BookItem2
        key={i}
        item={item}
        user={this.props.user}
        onDelete={(item, i) => {
          this.onDelete(item)
        }}
      />
    )
  })}
</ScrollView>      
```

可以说是非常蠢了，我竟然用当前图书列表中该书所占的序号作为它的key。   
    
可想而知，苦逼的哲学大辞典，在陈景润传作为1号选手被删除之后，key自动由2变1，承接了1号选手的角色。    

React看了一眼，发现1号选手是一个存在过的，并且已经具备style的组件。机智地选择了不卸载重装，而只做属性更新（即书名和图片之类）的省事做法。    
于是2号选手华丽变身1号选手，保留了1号选手的所有的属性之外的特性————包括被左移并显现出删除按钮这个样式。    

此时哲学大辞典的内心毫无波动，甚至还有点想笑=。=     
     
## 后记
铁一般的事实告诉我们。   
果然实践才是检验真理的唯一标准（跪）    
咩咩丸眉头一皱，发现React并不简单QAQ（逃。。。。。。）    



    
