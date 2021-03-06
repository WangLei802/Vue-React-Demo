# **Redux**

### 学习了之前的react基础语法糖及重要的生命周期后，自己对react的机制及思想更深了一点，作为码畜啊，就得不能把自己当人看，学不死往死学，接下来就继续探索react重要的用来管理数据状态和UI状态的JavaScript应用工具---- Redux

接下来的学习之路会用到antd UI 跟react 配合去做demo编码

先来安装antd
```
npm install antd --save
```

 我们先来写一个 TodoList demo

 编写index.js

```
import React from 'react';
import ReactDOM from 'react-dom'
import TodoList from './TodoList'

ReactDOM.render(<TodoList/>,document.getElementById('root'))
 ```

编写TodoList.js文件  先引入CSS样式  然后再将所需的todolist 组件导入

```
import React, { Component } from 'react';
import 'antd/dist/antd.css'
import { Input , Button , List } from 'antd'

const data=[
    '11111',
    '22222',
    '33333'
]

class TodoList extends Component {
    render() { 
        return ( 
            <div style={{margin:'10px'}}>
                <div>
                    <Input placeholder='write someting' style={{ width:'250px', marginRight:'10px'}}/>
                    <Button type="primary">Add</Button>
                </div>
                <div style={{margin:'10px',width:'300px'}}>
                    <List
                        bordered
                        dataSource={data}
                        renderItem={item=>(<List.Item>{item}</List.Item>)}
                    />    
                </div>
            </div>
         );
    }
}
export default TodoList;
```

todolist 组件上面已经完成  接下来我们就开始学习redux数据更新渲染以及各个文件的作用



### **创建Redux中的store及reducer文件**

**redux的工作流程有四个 react components --> action -->store <==> reducers
store --> react components**

其中最重要的便是 **store**，所有的数据都要放到**store**中进行管理，所以我们要优先对store文件进行编写

* 先安装redux
```
npm install --save redux
```
* 在src文件下建立store文件，在该文件下创建index文件进行编码
```
import { createStore } from 'redux'  // 引入createStore方法
const store = createStore()          // 创建数据存储仓库
export default store                 //暴露出去
```

* 现在的这个仓库很混乱   我们此时就需要用一个有管理能力的模块来管理仓库 这个模块就是redux，在store文件下建立redux.js文件

```
const defaultState = {}  //默认数据
export default (state = defaultState,action)=>{  //就是一个方法函数
    return state
}
```
* **state:** 是整个项目中需要管理的数据信息,这里我们没有什么数据，所以用空对象来表示。

* **reducer**文件建立好了，把reducer引入到store中,再创建store时，以参数的形式传递给store。

将index文件进行改造
```
import { createStore } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
const store = createStore(reducer) // 创建数据存储仓库
export default store   //暴露出去
```
### **在store中为todoList初始化数据**
仓库store和reducer都创建好了，可以初始化一下todoList中的数据了，在reducer.js文件的defaultState对象中，加入两个属性:inputValue和list

```
const defaultState = {
    inputValue : 'Nmae',
    list:[
        '11111',
        '22222'
    ]
}  
```

### **todolist组件获得store中的数据**

先引入  import store from './store/index'

引入后我们可以使用构造方法在控制台将引入的的store数据进行打印

```
constructor(props){
    super(props)
    console.log(store.getState())
}
```
接下来我们将store中的数据对todolist文件的数据进行赋值

```
class TodoList extends Component {
constructor(props){
    super(props)
    //关键代码-----------start
    this.state=store.getState();
    //关键代码-----------end
    console.log(this.state)
}
    render() { 
        return ( 
            <div style={{margin:'10px'}}>
                <div>
                    <Input placeholder={this.state.inputValue} style={{ width:'250px', marginRight:'10px'}}/>
                    <Button type="primary">Add</Button>
                </div>
                <div style={{margin:'10px',width:'300px'}}>
                    <List
                        bordered
                        //关键代码-----------start
                        dataSource={this.state.list}
                        //关键代码-----------end
                        renderItem={item=>(<List.Item>{item}</List.Item>)}
                    />    
                </div>
            </div>
         );
    }
}
export default TodoList;
```
以上就是创建store，reduce和如何使用store中的数据的一些基本知识点

接下来就来感受一下redux工作流程

* 添加input响应事件 onChange 事件  在todolist.js文件中
```
<Input 
    placeholder={this.state.inputValue} 
    style={{ width:'250px', marginRight:'10px'}}
    //---------关键代码----start
    onChange={this.changeInputValue.bind(this)}
    //---------关键代码----end
/>

changeInputValue(e){
    console.log(e.target.value)
}
```
基于前面的学习  现在input 的事件我们可以拿到当前的value值 那么接下来我们就要去改变store里面的值

根据前面的redux工作流程，接下来我们首先要去创造**Action**

```
changeInputValue(e){
    const action ={
        type:'change_input_value',
        value:e.target.value
    }
}
```
* **Action**里面有两个属性，第一个则是对**action**的描述，第二个则是要改变的值

* 到这里还不能说可以改变到**store**里面的值，需要注意的是创建完**action**我们必须去通过**dispatch()** 方法传递给 **store** 从而建立联系
```
changeInputValue(e){
    const action ={
        type:'changeInput',
        value:e.target.value
    }
    store.dispatch(action)
}
```

## **store的自动推送策略**
 通过react 的官网流程图我们可以看到，**store**只是一个仓库，他没有管理能力，然而他会把接收到的**action**传送给**reducer**，我们在reducer控制打印台打印结果看下

 ```
 export default (state = defaultState,action)=>{
    console.log(state,action)
    return state
}
 ```
* **state:** 指的是原始仓库里的状态。
* **action:** 指的是action新传递的状态。

现在通过浏览器控制台打印我们可以清楚的看到 **Reducer** 已经成功地拿到传递过来的新数据，接下来就要去改变 **store** 里面的值。

* 首先我们要去判断 **action** 中的 **type** 类型是否正确，如若正确，则需要声明一个新的变量 **newState** 
* 然后进行赋值操作并将新声明的 **newState** return出去
* **需要注意的是：** **Reducer**里只能接收 **state** ，不能改变 **state**

```
export default (state = defaultState,action)=>{
    <!-- 此处就是action提交过来的类型值 去做判断 -->
    if(action.type === 'changeInput'){
        let newState = JSON.parse(JSON.stringify(state))     //深度拷贝state
        newState.inputValue = action.value
        return newState
    }
    return state
}
```

此时 **store** 中的数据已完成更新，但是我们在组件中可以看到数据并没有更新，我们需要在todolist.js中 **constructor** 添加下面代码

```
constructor(props){
    super(props)
    this.state=store.getState();
    this.changeInputValue= this.changeInputValue.bind(this)
    //----------关键代码-----------start
    this.storeChange = this.storeChange.bind(this)  //转变this指向
    store.subscribe(this.storeChange) //订阅Redux的状态
    //----------关键代码-----------end
}

```
我们还需在下面写 **storeChange** 的方法 ，并且重新setState一次就可以实现组件也是变化的。在代码的最下方，编写storeChange方法。
```
storeChange(){
     this.setState(store.getState())
 }
```
现在在浏览器中就可以组件跟rudux同步进行了改变

### **完善TodoList组件**

給按钮添加事件

```
<Button type="primary" onClick={this.click.bind(this)}>Primary</Button>
click(){
    console.log('click')
}
```

## 接下来创建 **Action** 并使用 **dispath()** 传递给 **store**

```
click(){
    const action = { type:'addItem'}
    store.dispatch(action)
}
```
此时已经把 **action** 传递到了 **store** ，然后到 **reducer** 进行业务逻辑编写

```
export default (state = defaultState,action)=>{
    if(action.type === 'changeInput'){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.inputValue = action.value
        return newState
    }
    //关键代码------------------start----------
    //state值只能传递，不能使用
    if(action.type === 'addItem' ){ //根据type值，编写业务逻辑
        let newState = JSON.parse(JSON.stringify(state)) 
        newState.list.push(newState.inputValue)  //push新的内容到列表中去
        newState.inputValue = ''
        return newState
    }
     //关键代码------------------end----------
    return state
}
```

### **Redux实现ToDoList的删除功能**

```
<div style={{margin:'10px',width:'300px'}}>
    <List
        bordered
        dataSource={this.state.list}
        renderItem={(item,index)=>(<List.Item onClick={this.deleteItem.bind(this,index)}>{item}</List.Item>)}
    />    
</div>
deleteItem(index){
    const action = {
        type:'deleteItem',
        index
    }
    store.dispatch(action)
}
```
在reducer中业务逻辑实现

```
if(action.type === 'deleteItem' ){ 
    let newState = JSON.parse(JSON.stringify(state)) 
    newState.list.splice(action.index,1)  //删除数组中对应的值
    return newState
}
```

此刻就可以点击删除列表的每一项


### **工作中写Redux的小技巧**

写Redux Action的时候，我们写了很多Action的派发，产生了很多Action Types，如果需要Action的地方我们就自己命名一个Type,会出现两个基本问题：

* 这些Types如果不统一管理，不利于大型项目的服用，设置会长生冗余代码。
* 因为Action里的Type，一定要和Reducer里的type一一对应在，所以这部分代码或字母写错后，浏览器里并没有明确的报错，这给调试带来了极大的困难。


所以避免以上问题发生，可以单独拆分出一个文件.在 **src/store** 文件下面，建立一个acyionTypes.js文件，然后把所有的 **type** 类型集中放到该文件进行统一管理

```
export const  CHANGE_INPUT = 'changeInput'
export const  ADD_ITEM = 'addItem'
export const  DEL = 'del'
```

完了在todolist文件中引入

**修改Action方法**
```
import { CHANGE_INPUT , ADD_ITEM , DEL } from './store/actionTypes'

<!-- 现在就可以用这些常量来替代原来的Type值 -->
changeInputValue(e){
    // Action就是一个对象
    const action ={
        type:CHANGE_INPUT,                  //对action的描述
        value:e.target.value                        //要改变的值
    }
    //要通过dispatch()方法传递给store
    store.dispatch(action)
}
click(){
    const action = {
        type:ADD_ITEM
    }
    store.dispatch(action)
}
del(index){
    const action = {
        type:DEL,
        index
    }
    store.dispatch(action)
}
```

接下来我们在修改 **reducer** 文件中的代码

```
export default (state = defaultState,action)=>{  //就是一个方法函数
    console.log(state,action)
    let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
    if(action){
        switch(action.type){
            case CHANGE_INPUT:
                newState.inputValue = action.value
                return newState   
                break
            case ADD_ITEM:
                // let newState = JSON.parse(JSON.stringify(state)) 
                console.log(newState)
                newState.list.push(newState.inputValue)  //push新的内容到列表中去
                newState.inputValue = ''
                return newState
                break
            case DEL:
                // let newState = JSON.parse(JSON.stringify(state)) 
                newState.list.splice(action.index,1)  //push新的内容到列表中去
                return newState
                break
        }
    }
    return state
}
```

## **上面我们对Action中的Type进行了改造，那么接下来我们继续对Redux Action进行代码打磨**

**在src/store下建立actionCreators.js文件，在引入上面文件actionTypes.js文件,并对其进行修改如下**
```
import  {CHANGE_INPUT,ADD_ITEM,DEL}  from './actionTypes'

export const changeInputAction = (value)=>({
    type:CHANGE_INPUT,
    value
})
export const addItemAction  = ()=>({
    type:ADD_ITEM
})
export const deleteItemAction  = (index)=>({
    type:DEL,
    index
})
```

**接下来将其文件引入到todolist.js文件中,并将其方法进行修改如下**
```
import {changeInputAction,addItemAction,deleteItemAction} from './store/actionCreators'
changeInputValue(e){
    // Action就是一个对象
    const action = changeInputAction(e.target.value)
    //要通过dispatch()方法传递给store
    store.dispatch(action)
}
click(){
    const action = addItemAction()
    store.dispatch(action)
}
del(index){
    const action = deleteItemAction(index)
    store.dispatch(action)
}
```
**上面这些就实现了Redux Action 和我们业务逻辑的分离，这样操作可使我们在工作中更好的去维护我们的程序。**

### **Redux的几个注意点：**
* **store必须是唯一的，多个store是坚决不允许，有且只能有一个store空间**

* **只有store能改变自己的内容，Reducer不能改变**

* 认为把业务逻辑写在了Reducer中，那改变state值的一定是Reducer，其实不然，在Reducer中我们只是作了一个返回，返回到了store中，并没有作任何改变。Reducer只是返回了更改的数据，但并没有更改store中的数据，store文件拿到reducer的数据后，自己对自己进行了更新 **(就像是你看着手机里美好的画面 （reducer），然后你的大脑接收到这个画面后 (store)，作为单身狗的你自己安慰了自己一波(自己调用自己并进行更新) ))**


* **Reducer必须是纯函数**

## **UI 组件与业务逻辑的拆分**
上面就是redux的一些基础知识及我们工作中的一些常用写法，但我们在正常工作过中往往不止于此，在我们多人合作中，我们往往会将逻辑部分跟UI部分拆分。接下来就讲我门上述的todolist这个demo进行逻辑层与UI层的拆分

我们所有的UI和逻辑都在todolist这个js文件中编写，他两个完全耦合在一块

* 在src文件下建立todolistUI.js文件，再将todolist文件中JSX部分代码粘贴过来
* antd库引入，再将用到的css及用到的组件copy过来
```
import React, { Component,Fragment } from 'react';
import './Todolist.css'
import 'antd/dist/antd.css'
import { Input, Button, List} from 'antd'
class TodoListUi  extends Component {
    constructor(props) {
        super(props);
        this.state = {  }
    }
    render() { 
        return ( 
            <Fragment>
                <div className='margin'>
                    <h1>Antd-UI框架</h1>
                    <Input value={this.state.inputValue} style={{ width:'250px'}} onChange={this.changeInputValue.bind(this)}/>
                    <Button type="primary" onClick={this.click.bind(this)}>Primary</Button>
                    <div style={{margin: '20px auto',width:'600px',}}>
                        <List 
                        bordered
                        dataSource={this.state.list}
                        renderItem={(item,index)=>(<List.Item onClick={this.del.bind(this,index)}>{item}</List.Item>)}
                        /> 
                    </div>
                           
                </div>    
            </Fragment>
         );
    }
}
 
export default TodoListUi ;
```

**但是这并没有TodoListUI.js组件所需要的state(状态信息)，接下来需要改造父组件进行传递值(用到了我们之前学到的传递值)**

* 在todolist.js文件中引入我们的todolistUI组件
```
import TodoListUI from './TodoListUI';
<!-- render函数中如下写法 -->
render() { 
    return ( 
        <TodoListUI />
    );
}
```

以上完成了UI与业务层的分离，接下来我们需要改变todolistUi文件中的属性，将两个组件进行整合

**两个组件的整合用大白话来说就是通过属性传值，把子组件所需要的值传递给子组件，子组件进行相应的绑定就行了

在todolist中render函数如下进行属性值传递
```
render() { 
    return ( 
        <TodoListUI
            inputValue={this.state.inputValue}
            list={this.state.list}
            changeInputValue={this.changeInputValue}
            click={this.click}
            del={this.del}
        />
    );
}
```

在todolistUI中接受并进行绑定改造，代码如下
```
 render() { 
    return ( 
        <Fragment>
            <div className='margin'>
                <h1>Antd-UI框架</h1>
                <Input value={this.props.inputValue} style={{ width:'250px'}} onChange={this.props.changeInputValue.bind(this)}/>
                <Button type="primary" onClick={this.props.click.bind(this)}>Primary</Button>
                <div style={{margin: '20px auto',width:'600px',}}>
                    <List 
                    bordered
                    dataSource={this.props.list}
                    renderItem={(item,index)=>(<List.Item onClick={this.props.del.bind(this,index)}>{item}</List.Item>)}
                    /> 
                </div>
                        
            </div>    
        </Fragment>
    );
}
```
以上就是我们组建于业务逻辑拆分的简单学习，在工作中我们要去思考每个组件的耦合性，从而进行合理的组件与业务逻辑的合理拆分


## **Redux-thunk的使用方法**

我们可以将向后台请求数据的程序放在我们的中间件中执行，这样就形成了一套完整的Redux流程

**安装Redux-thunk**
```
npm install --save redux-thunk
```
我们需要在store/index.js中改写文件，一般情况下我们只需要将以下代码作为工作代码、固定代码就好了

```
import { createStore , applyMiddleware ,compose } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
import thunk from 'redux-thunk'

const composeEnhancers =   window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}):compose

const enhancer = composeEnhancers(applyMiddleware(thunk))

const store = createStore( reducer, enhancer) // 创建数据存储仓库
export default store   //暴露出去
```

接下来我们只需在 **actionCreators.js** 文件里编写业务逻辑

```
// 中间件  (可将后台请求数据这步操作放在中间件)
export const getTodoList = () =>{
    // 此函数可直接传递进dispatch，我们只需dispatch(action)直接调用就好
    return (dispatch)=>{
        const data = [1,3]
        const action = getList(data)
        dispatch(action)
    }
}
```

我们编写好组件后，我们只需在我们所需要的组件中引入方法
```
componentDidMount(){
    // let data = [1,2]
    const action = getTodoList()
    store.dispatch(action)
}
```
