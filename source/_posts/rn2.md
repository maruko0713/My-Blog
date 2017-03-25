---
title: RN|很RN的部分|基础组件篇
date: 2017-03-23
tags: ["React Native"]
---
## 前言
RN中有一些提前为我们写好的最基本的组件。它们是:    
- 视图组件 View
- 文本组件 Text
- 图像组件 Image
- 列表视图组件 ListView
   
## View
View组件是创建用户界面的一个最基本的组件，类似于我们html里面div标签。    
我们可以给它添加一些样式，添加事件等等。    
它当然必须是可以嵌套的:    
    
```xml
<View>
    <View></View>
</View>
```

当我们写出上述jsx代码的时候，它底层自动帮我们对应了ios的UIView组件，或者安卓的android.view组件。     
     
## Text
顾名思义是写文本用的，注意它的用法规则是必须要嵌套在一个View组件里面～～    
同样可以嵌套:    
   
```xml
<View>
    <Text style={{fontWeight:"400"}}>
        我是小丸子    
        <Text style={{fontWeight:"600"}}>你呢</Text>
    </Text>
</View>
```
    
<!-- more --> 

### 自定义一个文本组件
是的，有时候我们需要全局保持一个文本样式，但是又总不能个挨个给每一个组件增加一个同样的style，这时候我们可以自定义一个组件，然后在接下来的开发中都使用这一个组件:     
比如:    
    
```xml
class HeaderText extends React.Component {
    render (
        <Text style={{color:"#fff"}}>
            {this.props.children}
        </Text>
    )    
}
```

    
### Image
图像组件     
Image咋用??我们把图片链接放在source属性里:    
    
```xml
<View>
    <Image
        style={这里写样式} 
        source={require("这里写地址")} />
</Image>
</View>
```

Image只能自关闭吗？    
当然不啦！    
因为RN中没有单独设置背景图像的属性，所以这时候我们要使用有开始有结束的标签了:    
    
```xml
<View>
    <Image
        style={{styles.backgroundImage}}
        source={require("这里写地址")}
        >
        <View>
            <Text>
                我是写在Image上的文字哦～～～    
            </Text/>
        </View>
    </Image>
</View>
```

## ListView
ListView组件用于显示一个垂直的滚动列表，其中的元素之间结构近似而仅数据不同。   
因此，数据是关键。    
ListView组件必须的两个属性是dataSource和renderRow。dataSource是列表的数据源，而renderRow则逐个解析数据源中的数据，然后返回一个设定好格式的组件来渲染。     
dataSource这个属性需要我们预先初始化：     
    
```xml
class ListViewBasics extends Component {
    constructor(props) {
        super(props);
        let movies = [{
          {title:"你的名字"},
          {title:"大鱼海棠"},
          {title:"模仿游戏"}
        }];

        let dataSource = new ListView.DataSource({
            // 必须手动写的一个方法，判断每行的数据是否更改了
            rowHasChanged:(row1,row2) => row1!==row2
        });
        this.state = {
            // 注入要处理的数据
            movies: dataSource.cloneWithRows(movies);
        };
    }

    render() {
        return {
            <View>
                <ListView
                    data={this.state.movies}
                    // 用renderRow属性迭代movies的每一个子元素进行渲染
                    renderRow={
                        movie => <Text>{movie.title}</Text>
                    }
                    />
            </View>
        }
    }
}
```

## 总结
既然有最基础的组件，那么就有其它的组件哈哈哈哈～～
下一篇会写一写其它的组件啦～～～



