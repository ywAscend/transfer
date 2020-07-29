---
title: react事件对象隐形传递
date: 2020-07-29 15:52:47
tags: React
---

### React事件回顾
React 事件的命名采用小驼峰式（camelCase），而不是纯小写。使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。

##### 事件绑定方式
```
1.在constructor中绑定,这种方式官方推荐的，性能较好，但是稍显繁琐

class EventCom extends React.Component {
    constructor(props){
        super(props)

        //constructor中注册
        this.handleClick = this.handleClick.bind(this)
    }
    handleClick(){
        alert('it's ok')
    }
    render(){
        return(
            <div>
                <button onClick={this.handleClick()}>click me </button>
            </div>
        )
    }
}

2.直接在标签上绑定（bind或箭头函数）
此语法问题在于每次渲染组件时都会创建不同的回调函数。在大多数情况下，这没什么问题，
但如果该回调函数作为 prop 传入子组件时，这些组件可能会进行额外的重新渲染。
class EventCom extends React.Component {
    constructor(props){
        super(props)
    }
    handleClick(){
        alert('it's ok')
    }
    render(){
        return(
            <div>
                <button onClick={this.handleClick.bind(this)}>click me </button>
                <button onClick={()=>this.handleClick}>click me </button>
            </div>
        )
    }
}

3. 使用class fields 语法(推荐)
class EventCom extends React.Component {
    constructor(props){
        super(props)
    }
    handleClick= ()=>{
        alert('it's ok')
    }
    render(){
        return(
            <div>
                <button onClick={this.handleClick}>click me </button>
            </div>
        )
    }
}

```

##### 事件传参
1.标签上使用bind传参或箭头函数，两者是等价的
```
class EvantParamCom extends React.Component {
    constructor(props){
        super(props){
            this.state = {
                list:[{..},{...}]
            }
        }
    }
    handClick(index){
        alert(index)
    }
    render(){
        let {list} = this.state
        return(
            <div>
                list.map((item,index) => {
                    return(
                        <!-- <div key={index} onClick={this.handleClick.bind(this,index)}>{item.id}</div> -->
                        <div key={index} onClick={()=>this.handleClick(index)}>{item.id}</div>
                    )
                })
                
            </div>
        )
    }
}

React 的事件对象 e 会被作为第二个参数传递。如果通过箭头函数的方式，事件对象必须显式的进行传递，而通过 bind 的方式，事件对象以及更多的参数将会被隐式的进行传递。

<div key={index} onClick={this.handleClick.bind(this,index)>{item.id}</div>
<div key={index} onClick={(e)=>this.handleClick(index,e)}>{item.id}</div>

//获取事件对象
handleClick(index,event){
    console.log(index,event)
}

3. class fileds语法,获取隐形事件对象

class EventCom extends React.Component {
    
    handleClick = data => event => {
        //data:数据，event：事件对象
        console.log(data,event) // 0000, class...
    }

    render(){
        return(
            <div>
                <button onClick={this.handleClick('0000')}>click</button>
            </div>
        )
    }
}



```


