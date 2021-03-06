### redux 简单demo 
#### 安装第三方模块

 npm install --save redux <br/>
 npm install --save react-redux<br/>
 npm install --save react-router<br/>

#### 1.0 Store 
 
 Store 保存数据 相当于一个容器（整个项目里只能有一个Store包含所有数据）
 
* Redux 中利用createStore这个函数创建Store <br/> 
* 维持应用的 state；<br/>
* 提供 getState() 方法获取 state；<br/>
* 提供 dispatch(action) 方法更新 state；<br/>
* 通过 subscribe(listener) 注册监听器;<br/>
* 通过 subscribe(listener) 返回的函数注销监听器。<br/>
 ```
 import {createStore} from 'redux';
 const store = createStore(fn);
 ```
#### 1.1 State 

Store对象包含所有数据。如果想得到某个时点的数据，就要对 Store 生成快照。这种时点的数据集合，就叫做 State。

```
  import { createStore } from 'redux';
  const store = createStore(fn);
  const state = store.getState();
  
```
一个State 对应一个View，只要State相同View就相同。

#### 1.2 Action

State 的变化，会导致 View 的变化。但是，用户接触不到 State，只能接触到 View。所以，State 的变化必须是 View 导致的。Action 就是 View 发出的通知，表示 State 应该要发生变化了。

```
 const changeTextAction = {
  type:'CHANGE_TEXT'
}
const buttonClickAction = {
  type:'BUTTON_CLICK'
}
const onButtonLgAction ={
  type:'BUTTON_LGCLICK'
}

```
上面代码 三个Action 分别为 `CHANGE_TEXT` ` BUTTON_CLICK ` `BUTTON_LGCLICK`，
Action 描述当前发生的事情。改变 State 的唯一办法，就是使用 Action。它会运送数据到 Store。


#### 1.3  Reducer

  Store 收到 Action 以后，必须给出一个新的 State，这样 View 才会发生变化。这种 State 的计算过程就叫做 Reducer。
  
```
  const initialState = { 
    text:0
  }
  const reducer = (state = initialState,action) =>{
    switch(action.type){
      case "CHANGE_TEXT":
        return{
            text:state.text == 'Hello' ? 'world' : 'HELLO'   
        }
      case 'BUTTON_CLICK':
          return{
            text:state.text + 1
          }

      case 'BUTTON_LGCLICK':
          return{
            text:state.text + 10
          }    
      default:
          return initialState;
    }
  }

  ```
  
  上面代码 reducer 接收到 `CHANGE_TEXT` `BUTTON_LGCLICK` `BUTTON_LGCLICK` 任意一个Action 以后返回相对应的state

##### 1.3.1 combineReducers
   
   随着应用变得复杂，需要对 reducer 函数 进行拆分，拆分后的每一块独立负责管理 state 的一部分。
   combineReducers 辅助函数的作用是，把一个由多个不同 reducer 函数作为 value 的 object，合并成一个最终的 reducer 函数，然后就可以对这个 reducer   调用 createStore。
  
  ###### 实例
  
   reducers/todos.js
   
   ```
   export default function todos(state = [], action) {
      switch (action.type) {
      case 'ADD_TODO':
        return state.concat([action.text])
      default:
        return state
      }
    }
  ```
  
  reducers/counter.js
  
 ```
    export default function counter(state = 0, action) {
       switch (action.type) {
       case 'INCREMENT':
         return state + 1
       case 'DECREMENT':
         return state - 1
       default:
         return state
       }
     }
```
 
 reducers/index.js

```
   import { combineReducers } from 'redux'
   import todos from './todos'
   import counter from './counter'

   export default combineReducers({
     todos,
     counter
   })
   
```   
APP.jsx

```
   import { createStore } from 'redux'
   import reducer from './reducers/index'

   let store = createStore(reducer)

   store.dispatch({
     type: 'ADD_TODO',
     text: 'Use Redux'
   })
   console.log(store.getState())
   
```

#### 1.4 connect() 
  
  React-Redux 提供connect方法，用于从 UI 组件生成容器组件。connect的意思，就是将这两种组件连起来。
  
  ```
    import { connect } from 'react-redux'
    App = connect(mapStateToProps, mapDispatchToProps)(App)  
    
  ```
  上面代码接受两个参数：`mapStateToProps` `mapDispatchToProps` 前者负责输入逻辑,后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action
  
  #### 1.5 mapStateToProps()
  
  mapStateToProps是一个函数,建立一个从（外部的）state对象到（UI 组件的）props对象的映射关系.
  
  ```
  function mapStateToProps(state){
     return {text:state.text}
  }
  
  ```
  #### 1.6 mapDispatchToProps()
  
  mapDispatchToProps是connect函数的第二个参数，用来建立 UI 组件的参数到store.dispatch方法的映射。也就是说，它定义了哪些用户的操作应该当作   Action，传给 Store。它可以是一个函数，也可以是一个对象
  
  ```
  
  function mapDispatchToProps(dispatch){
    return{
      onButtonClick:() =>dispatch(buttonClickAction),
      onChangeText:() =>dispatch(changeTextAction),
      onButtonLgClick:() =>dispatch(onButtonLgAction),
    }
  }
  
  ```
  
  #### 1.7 Provider组件
  
  connect方法生成容器组件以后，需要让容器组件拿到state对象，才能生成 UI 组件的参数。
  Provider组件，可以让容器组件拿到state。

```
  ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>
    ,document.getElementById('root')
  )

```

#### 实例

```
import React, { Component } from 'react';  
import ReactDOM from 'react-dom';  
import { createStore } from 'redux';  
import { Provider, connect } from 'react-redux';  

class App extends Component{
  render(){
    const {text,onChangeText,onButtonClick,onButtonLgClick} = this.props;
    return(
        <div>
            <h1 onClick={onChangeText}>{text}</h1>
            <button onClick={onButtonClick}>clicke me</button>
            <button onClick={onButtonLgClick}>clicke me + 10</button>
        </div>
    )
  }
}

//action
const changeTextAction = {
  type:'CHANGE_TEXT'
}
const buttonClickAction = {
  type:'BUTTON_CLICK'
}
const onButtonLgAction ={
  type:'BUTTON_LGCLICK'
}

//reducer  处理逻辑
const initialState = {
  text:0
}
const reducer = (state = initialState,action) =>{
  switch(action.type){
    case "CHANGE_TEXT":
      return{
          text:state.text == 'Hello' ? 'world' : 'HELLO'   
      }
    case 'BUTTON_CLICK':
        return{
          text:state.text + 1
        }
    
    case 'BUTTON_LGCLICK':
        return{
          text:state.text + 10
        }    
    default:
        return initialState;
  }
}

//创建store
let store = createStore(reducer);

//映射Redux state到组件的属性  
function mapStateToProps(state){
  return {text:state.text}
}

//映射Redux actions到组件的属性  
function mapDispatchToProps(dispatch){
  return{
    onButtonClick:() =>dispatch(buttonClickAction),
    onChangeText:() =>dispatch(changeTextAction),
    onButtonLgClick:() =>dispatch(onButtonLgAction),
  }
}

//连接组件  
App = connect(mapStateToProps, mapDispatchToProps)(App)  

//渲染组件  
ReactDOM.render(
  <Provider store={store}>
      <App />
  </Provider>
  ,document.getElementById('root')
)

```

参考：

* http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_three_react-redux.html
* http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_two_async_operations.html
