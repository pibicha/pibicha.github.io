---
title: React 学习
date: 2018-05-18 10:30:09
categories:
- 前端
tags:
- React
description:
image: https://picsum.photos/id/100/2000/1200
image-sm: https://picsum.photos/id/100/500/300
---  

最近要写前端，以前对前端五花八门的库、框架都不知道怎么入手，就会写JavaScript，听说了react的all in js ，于是尝试一下。  
网上很多教程是通过React.createClass方式，和通过extends方式有很多差异，这里记录一下粗鄙的学习与理解过程……  

[demo地址](https://github.com/pibicha/react_demo)
### react demo

react是SPA（single page application）的一个实现，其JSX实JavaScript的扩展，语法同JavaScript，熟悉js的话，上手react是比较快速的。

这里将react与java作比较，让熟悉java的人也能看懂react的使用。

### 类即组件
```react
import React, {Component} from 'react';

class HelloWorld extends Component {

    render() {
        return(
            <h1>Hello World!</h1>
        )
    }
}
export default HelloWorld;
```
类就是一个组件，上面的例子中声明了一个HelloWorld组件，render方法是组件的入口，用来渲染页面；其实现就是返回Html的标签，可以夹杂js代码来实现一些逻辑。
使用该组件，就像使用普通html的标签，通过`<HelloWorld>`即可~~~
### 导入包
上述helloworld的例子中，首先导入了需要的模块（也就是类），然后声明了一个HelloWorld类；到此为止和java的声明式编程是一样的。
(附带说明 export default HelloWorld; 是将写的模块导出，其他地方才能导入以便复用)
### 程序入口——render()
在这个例子中，最主要的是render方法，它类似于java的main方法，是程序的入口；只不过render方法是渲染界面的入口；需要根据不同场景去实现，这里简单的返回了一个div。


---

### JSX使用
上一个例子简单介绍了react与java的相近之处；从render方法上看到，在react的类（组件）中，可以直接使用html的标签，这就是react的All in js的思想体现；然而普通的返回标签是满足不了稍微复杂页面的需求，通常需要js代码来处理这些标签如何展示，在使用到js的地方，通过'{' 和' }'包含js代码即可

再看一个例子：
```react
import React, {Component} from 'react';

class Button extends Component {
    render() {
        var users = ['小明', '小红', '小芳'];
        return (
            <div>
                {
                    users.map(function (user) {
                        return <li>{user}</li>
                    })
                }
            </div>);
    }
}
export default Button
```

这个例子除了render方法，其他地方大致和helloworld一致；
render方法中，使用了js原生的map方法，将数组users做了一些处理，从语法上来说：在render方法中，遇到html标签使用'<xx>'即可，遇到js代码就使用'{}'来包含住js代码即可。

### 组件的props
react的props和java无法做比较，因为它不是字面上的“属性”，props是用来在使用组件时传递消息的，和`input`标签的value属性功能一样；
使用例子：
```react
import React, {Component} from 'react';
class User extends Component {
    render() {
        return (
            <h1>Hello {this.props.name},welcome {this.props.addr}!</h1>
        )
    }
}
export default User;
```
上面例子中，react在render方法中使用了{this.props.name}来接收消息；
在使用组件时，只需将组件的属性传入进去即可：
`<User name='james' addr={'New York'}/>`

关于props的children可以惨老demo5，实际上差不多

### state属性
react的state属性，就和java的属性差不多了；同样的可以在类中声明，并且可以在声明时赋值、或在构造器中赋值。
```react
import React, {Component} from 'react'

class State extends Component {

    // constructor(props) {
    //     super(props);
    //     this.state = {liked: false};
    // }

    // 和java语言一样 赋初值的地方可以在声明变量时就赋值，或者在构造器中赋值
    state = {liked: false};

    handleClick() {
        this.setState({
            liked: !this.state.liked
        });
    };
    render() {
        var text = this.state.liked ? "Love" : "Hate";
        return (
            <h1 onClick={this.handleClick.bind(this)}>I {text} u </h1>
        );
    }
}
export default State
```
上面的例子中，state可以直接在类中声明，并赋初始值；在render方法中每次点击文本，触发handleClick事件都会更新state的值~

再看下props与state的功能和区别：  
props是用来做标签之间的交互的，类似于`input`标签的value属性，可以从用户那获取输入值；  
state是组件本身的属性，可以写到组件之中，和java的成员属性一样，可以写到类里面一样；state可以用来控制组件的行为；  

- props就是java中的方法入参，用来传递消息
- state类似java中的成员属性，用来控制组件的行为

---
根据上面的基础，之后创建表单组件和ajax组件，都是由上述方式组合而成的。可以自由发挥