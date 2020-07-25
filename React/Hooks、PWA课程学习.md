# 一、前言

`react` 精于渲染，可控性强，组件复用与可维护

`react hooks` 没有以下特性

```
constructor
this.setState
componentDidMount
render
```

# 二、预备知识

工具类：`node、npm、webpack、eslint、prettier`

语法类： `ES6、JSX、CSS flex`

概念类：`SPA/MPA、PWA`

![](https://img-blog.csdnimg.cn/20190818181101519.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190818181243900.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

效率类：`iconfont、snippets`（代码片段）

职责分离：提高复用，可维护性

# 三、项目搭建

创建项目：使用create-react-app构建工具

```bash
create-react-app train-ticket
```

编译脚本：`react-scripts` 作用与工作原理

```javascript
"scripts": {
  "start": "react-scripts start",
  "build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject"
}
```

解构编译脚本：`eject`的具体用法

```bash
yarn eject
```

# 四、React最新特性简介

## `Context`

![](https://img-blog.csdnimg.cn/20190819101248527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190819102222480.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

### 示例

#### 传递固定值

```react
import React,{ Component,createContext } from 'react';
import logo from './logo.svg';
import './App.css';
// 当Consumer找不到Provider时，就会使用传入的默认值
const BatteryContxt = createContext(90); //第一层级  Provider
 
//第二层级
class Middle extends Component{
  render(){  //入口文件中 ReactDOM.render 会把我们创建的react组件转换成真实的dom元素
     return <Leaf/>   //第三层级  Consumer
  }
}
 
class Leaf extends Component{
  render(){
    return (
      <BatteryContxt.Consumer> 
        {/* Consumer中不能直接调用组件，必须声明函数*/}
         
        {
          battery => <h1>Battery: {battery} </h1>
        }
     </BatteryContxt.Consumer>
    );
  }
} 
 
class App extends Component{
 render(){
   return (
     <BatteryContxt.Provider value={60}>  
       <Middle/>
     </BatteryContxt.Provider>
   );
 }
}
 
export default App;
```

#### 值变化，`Consumer`可以更新值

```react
import React,{ Component,createContext } from 'react';
import logo from './logo.svg';
import './App.css';
// 当Consumer找不到Provider时，就会使用传入的默认值
const BatteryContxt = createContext(90); //第一层级  Provider
 
//第二层级
class Middle extends Component{
  render(){  //入口文件中 ReactDOM.render 会把我们创建的react组件转换成真实的dom元素
     return <Leaf/>   //第三层级  Consumer
  }
}
 
class Leaf extends Component{
  render(){
    return (
      <BatteryContxt.Consumer> 
        {
          battery => <h1>Battery: {battery} </h1>
        }
     </BatteryContxt.Consumer>
    );
  }
} 
 
class App extends Component{
  state={
    battery:60
  }
 render(){
   const { battery }=this.state
   return (
     <BatteryContxt.Provider value={battery}>  
      <button
       type="button"
       onClick={()=>{this.setState({battery: battery-1 })}}
     >
       press
    </button>
       <Middle/>
     </BatteryContxt.Provider>
    
   );
 }
}
 
export default App;
```

#### 多个context可嵌套

```react
import React,{ Component,createContext } from 'react';
import logo from './logo.svg';
import './App.css';
const BatteryContext = createContext(); //第一层级  Provider   类比于全局变量，可传递默认值
const OnlineContext = createContext(); //多个createContext 可嵌套
 
//第二层级
class Middle extends Component{
  render(){  //入口文件中 ReactDOM.render 会把我们创建的react组件转换成真实的dom元素
     return <Leaf/>   //第三层级  Consumer
  }
}
 
class Leaf extends Component{
  render(){
    return (
      <BatteryContext.Consumer>  {/* Consumer中不能直接调用组件，必须声明函数*/}
        {
          battery => (   //箭头函数后面是对象 就要用()
            <OnlineContext.Consumer>
              {
                online => <h1>Battery: {battery} Online: {String(online)}</h1>
              }
            </OnlineContext.Consumer>
          )
        }
     </BatteryContext.Consumer>
    );
  }
} 
 
class App extends Component{
  state={
    battery:60,
    online:false
  }
 render(){
   const { battery, online }=this.state
   return (
     <BatteryContext.Provider value={ battery }>  
      <OnlineContext.Provider value={ online }>
        <button
          type="button"
          onClick={()=>{this.setState({battery: battery-1 })}}
        >
        press
        </button>
        
        <button
          type="button"
          onClick={()=>{this.setState({online: !online })}}
        >
        switch
        </button>
        <Middle/>
      </OnlineContext.Provider>
     </BatteryContext.Provider>
    
   );
 }
}
 
export default App;
```

### 总结

1. 模式为生产者和消费者
2. `Provider和Consumer`一一对应，`Consumer`中是返回值（函数）
3. `Provider`必须有值
4. 多个`Content`可嵌套使用
5. 尽量一个组件使用一个`Context`
6. `context`会影响组件的独立性

## `ContextType`

一个组件只有一个`Context`时，使用`ContentType`更简洁，可以不使用`Consumer`，但`Provider`必须要有值

```react
import React,{ Component,createContext } from 'react';
import './App.css';
// 生产者
const BatteryContext = createContext();
 
//消费者   主要区别
class Leaf extends Component {
  static contextType = BatteryContext
  render() {
    const battery = this.context;
    return (
       <h1> Battery:{ battery } </h1>      
    );
  } 
}
 
//中间组件 
class Middle extends Component{
  render(){
    return <Leaf/>
  }
}
 
class App extends Component{
  constructor(props){
    super(props);
    this.state={
      battery:60,
      online:true
      
    }
  }
  render(){
    const {
      battery
    }=this.state
    return (
      // Provider必须要有值
      <BatteryContext.Provider value = { battery }>
          <button onClick={ ()=>this.setState({battery: battery + 1 })}> Add </button>
          <Middle/>
      </BatteryContext.Provider> 
    );
  }
}
 
export default App;
```

## `lazy与Suspense` 实现延迟加载

- `lazy`异步导入组件
- `Suspense`填充加载状态
- `ErrorBoundary` 捕获组件加载错误组件

三者缺一不可

```react
import React,{ Component,lazy,Suspense } from 'react';
import './App.css';
const About=lazy(()=>import (/* webpackChunkName: "about" */ "./about"));
// ErrorBoundary 捕获后台组件 渲染错误的组件  利用react生命周期componemtDidCatch
//
 
 
class App extends Component{
  constructor(props){
    super(props);
    this.state={
      hasError:false
    }
  }
  
  // componemtDidCatch(){
  //   this.setState({
  //       hasError:true
  //   })
  // }
  // 当出现错误时，会执行该函数返回一个新的state数据，并合并到this.state中
  static getDerivedStateFromError(){
    return {
      hasError:true
    }
  }
  render(){
    // 当程序报错时，渲染error
    if(this.state.hasError){
      return <div> error </div>
    }
    return (
     <section>
       {/* fallback 必须传入组件的实例 加载状态*/}
        <Suspense fallback={<div>loading </div>}>
            <About/>
        </Suspense>
       </section>
    );
  }
}
 
export default App;
```

## Memo 实现指定组件进行渲染

**意义：**避免（函数）组件的无意义渲染（类似`shouldComponentUpdate`），提升组件性能

**问题：**

- 描述：父组件有俩部分内容：一个button,点击后count+1，另一个子组件Foo,输出字符串，当点击count时父组件重新渲染，子组件Foo也会重新渲染，降低性能
- 代码：

```react
import React,{ Component ,memo,Fragment} from "react";
 
//Foo 子组件
class Foo extends Component {
  render(){
    console.log("Foo render");
    return null;
  }
}
//父组件
class App extends Component {
  constructor(props) {
    super(props);
    this.state = { 
      count:1,
     };
  }
  render() {
    const count = this.state.count;
    return (
      <Fragment>
         <button
            onClick={()=>{ this.setState({ count:count +1 }) }}
         >
           ADD
         </button>
         <Foo name="alice"/>
      </Fragment>
    );
  }
}
export default App;
```

**三种改进方法：**

- 使用shouldComponentUpdate控制子组件的渲染
- 使用PureComponent
- 使用memo  参数必须是纯函数组件

> `**shouldComponentUpdate` 和 `React.PureComponent` 只会对数据进行浅层比较。当比较引用类型数据时，只会去判断该引用类型数据的指针是否改变。所以当我们改变引用类型的值时，尽量重新生成一个引用类型数据传递给state，以使组件能够正常渲染**

### **`shouldComponentUpdate`**

```react
import React,{ Component } from 'react';
import './App.css';

class Foo extends Component {
  shouldComponentUpdate (nextProps, nextState) {
    // 当父组件传入的name属性值不变时，返回false，避免重新Foo组件渲染
    if (nextProps.name === this.props.name) {
      return false
    }
    return true
  }
  render () {
    console.log('Foo render')
    return null;
  }
}

class App extends Component {
  state = {
    count: 1
  }
  render () {
    return (
      <div>
        <button onClick={() => {this.setState({ count: this.state.count + 1 })}}>
          Add
        </button>
        <Foo name="Mike" />
      </div>
    )
  }
}
 
export default App;
```

### `Memo`

 `React.memo` 为[高阶组件](https://react.docschina.org/docs/higher-order-components.html)。它与 [`React.PureComponent`](https://react.docschina.org/docs/react-api.html#reactpurecomponent) 非常相似，但它适用于函数组件，但不适用于 class 组件。 

```react
const Foo = memo(function Foo(props) {
  console.log('Foo render')
  return <div>{ props.name }</div>
})

class App extends Component {
  state = {
    count: 1
  }
  render () {
    return (
      <div>
        <button onClick={() => {this.setState({ count: this.state.count + 1 })}}>
          Add
        </button>
        <Foo name="Mike" />
      </div>
    )
  }
}
```

# 五、React Hooks

## React Hooks的概念与意义

### 类组件不足：

* 状态逻辑复用难

  缺少复用机制
  渲染属性和高阶组件导致层级冗余

* 趋向复杂难以维护

  生命周期函数混杂不相干逻辑
  相干逻辑分散在不同生命周期

* this指向困扰

### Hooks优势：

* 函数组件无this问题
* 自定义Hook方便复用逻辑状态
* 副作用关注点分离

## 使用State Hooks

```react
import React,{ useState } from "react";
function App (){
    const [count,setCount] = useState(1);
    //返回的是包含两个元素的数组：第一个元素，state 变量，setState 修改 state值的方法。
    return (
      <button 
         onClick={()=>{setCount(count + 1)}}
      >
        Click ({ count })
      </button>
    );
  }
 
export default App;
```

### React 怎么知道哪个 state 对应哪个 `useState` 

 `React` 靠的是 `Hook` 调用的顺序。 

```jsx
function Form() {
  // 1. Use the name state variable
  const [name, setName] = useState('Mary');

  // 2. Use an effect for persisting the form
  useEffect(function persistForm() {
    localStorage.setItem('formData', name);
  });

  // 3. Use the surname state variable
  const [surname, setSurname] = useState('Poppins');

  // 4. Use an effect for updating the title
  useEffect(function updateTitle() {
    document.title = name + ' ' + surname;
  });

  // ...
}
```

```jsx
// ------------
// 首次渲染
// ------------
useState('Mary')           // 1. 使用 'Mary' 初始化变量名为 name 的 state
useEffect(persistForm)     // 2. 添加 effect 以保存 form 操作
useState('Poppins')        // 3. 使用 'Poppins' 初始化变量名为 surname 的 state
useEffect(updateTitle)     // 4. 添加 effect 以更新标题

// -------------
// 二次渲染
// -------------
useState('Mary')           // 1. 读取变量名为 name 的 state（参数被忽略）
useEffect(persistForm)     // 2. 替换保存 form 的 effect
useState('Poppins')        // 3. 读取变量名为 surname 的 state（参数被忽略）
useEffect(updateTitle)     // 4. 替换更新标题的 effect

// ...
```

> `useState` 以稳定的顺序，稳定的次数执行  安装`npm i eslint-plugin-react-hooks -D`  检查在使用`usestate`时语法是否错误

安装完成之后，在`package.json的eslintConfig`中添加配置文件

```react
 "eslintConfig": {
    "extends": "react-app",
    "plugins": [
      "react-hooks"
    ],
    "rules":{
      "react-hooks/rules-of-hooks":"error"
    }
  },
```

### 延迟初始化

`useState` 可以传入一个函数，来执行延迟初始化，提高执行效率

```react
import React,{ useState } from "react";
import { tsPropertySignature } from "@babel/types";
function App (props){
    // 只会执行一次计算
    const [count,setCount] = useState(()=>{
      console.log("initial count");
      return props.defaultCount || 0;
    });
    return (
      <button 
         onClick={()=>{setCount(count + 1)}}
      >
        Click ({ count }))
      </button>
    );
  }
export default App;
```

## 使用Effect Hooks

- 副作用：绑定事件、网络请求、访问DOM
- 副作用时机：Mount之后、Update之后、Unmount之前

![](https://img-blog.csdnimg.cn/2020040410342430.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

### 示例

**实现页面输出document宽高动态改变值**

#### **1、类组件实现**

我们把副作用操作放到 `componentDidMount` 和 `componentDidUpdate` 函数中。

```react
import React, {Component} from "react";
class App extends Component {
  constructor(props) {
    super(props);
    this.state = { 
      count:0,
      size:{
        width:document.documentElement.clientWidth,
        height:document.documentElement.clientHeight
      }
     };
  }
  // 挂载后执行相应操作
  componentDidMount(){
      document.title = this.state.count
    	// 绑定事件
      window.addEventListener("resize",this.onSize,false);
  }
  componentWillUnmount(){
      // 解绑事件
      window.removeEventListener("resize",this.onSize,false);
  } 
  // 组件更新后执行相应操作
  componentDidUpdate(){
    document.title = this.state.count
  }
  
  onSize=()=>{
    this.setState({
      size:{
        width:document.documentElement.clientWidth,
        height:document.documentElement.clientHeight
      }
    })
  }
  render() {
    const {
      count,
      size
    }=this.state
    return (
      <div 
      onClick={()=>{this.setState({ count:count +1}) }}
      >
        Click ({count}) , Size ({size.width}*{size.height})
      </div>
    );
  }
}
 
export default App;
```

#### **2、hooks组件**

```react
import React, {useState,useEffect} from "react";
function App2 (){
  const [count,setCount] =useState(0);
  const [size,setSize] =useState({
        width:document.documentElement.clientWidth,
        height:document.documentElement.clientHeight
  });
    
  const onSize=()=>{
    setSize({
        width:document.documentElement.clientWidth,
        height:document.documentElement.clientHeight
      }
    )
  }
 
  useEffect(()=>{
    document.title=count;
  })
 	// 在第一次渲染之后和每次更新之后都会执行。
  useEffect(()=>{
    window.addEventListener("resize",onSize,false);
    // 返回一个清除函数,组件卸载的时候会执行
    return ()=>{
      window.removeEventListener("resize",onSize,false);
    }
  },[])
return (
    <button
      onClick={ ()=>{setCount(count +1)}}>
      Click ({ count }),
      Size ({size.width}*{size.height})
    </button>
  )
}
export default App2;
```

### 总结

**`useEffect` 做了什么？** 通过使用这个 Hook，你可以告诉 React 组件需要在渲染后执行某些操作。React 会保存你传递的函数（我们将它称之为 “effect”），并且在执行 DOM 更新之后调用它。

**为什么在组件内部调用 `useEffect`？** 将 `useEffect` 放在组件内部让我们可以在 `effect` 中直接访问 `count state` 变量（或其他 `props`）。我们不需要特殊的 `API` 来读取它 —— 它已经保存在函数作用域中。`Hook` 使用了 `JavaScript` 的闭包机制，而不用在 JavaScript 已经提供了解决方案的情况下，还引入特定的 `React API`。

**`useEffect` 会在每次渲染后都执行吗？** 是的，默认情况下，它在第一次渲染之后和每次更新之后都会执行。

**为什么要在 `effect` 中返回一个函数？** 这是 effect 可选的清除机制。每个 effect 都可以返回一个清除函数。如此可以将添加和移除订阅的逻辑放在一起。它们都属于 effect 的一部分。

**`useEffect` 的第二个参数是什么？**是一个可选的数组，只有数组中的每一项都没有改变时（**此处只会对数据作浅层比较，当比较引用类型数据时，只会去判断该引用类型数据的指针是否改变**。），才会阻止`useEffect`的执行。如果不传入第二个参数，那么每次渲染都会执行`useEffect`；如果传入空数组，那么`useEffect`只会执行一次

## 使用Context Hooks

`Consumer、ContextType、useContext分别对应Foo、Bar、Counter三个子组件`

```react
import React, { Component, useState, createContext, Fragment，useContext } from "react";
// 创建Context
const CountContext=createContext();

// Consumer
class Foo extends Component {
  render() {
    return (
        <CountContext.Consumer>
          {
            count=><div> { count } </div>
          }
        </CountContext.Consumer>
    );
  }
}

// ContextType
class Bar extends Component {
  static contextType = CountContext;
 
  render() {
    const count= this.context;
    return (
      <h1> {count} </h1>
    );
  }
}

// useContext
function Counter () {
  const count = useContext(CountContext)
  return (
  	<h1>{ count }</h1>
  )
}
 
function App(props){
  const [count,setCount] = useState(0);
  return (
    <Fragment>
      <button
        type="botton"
        onClick={()=>{setCount(count+1)}}
        >
        Click ({ count })
     </button>
     <CountContext.Provider value={count}>
       <Foo/>
       <Bar/>
       <Counter />
     </CountContext.Provider>
    </Fragment>
  )
}
 
export default App;
```

**接收一个 `context` 对象（`React.createContext` 的返回值）并返回该 `context` 的当前值。当前的 `context` 值由上层组件中距离当前组件最近的的 `value` `prop` 决定。**

## 使用Memo Hooks

**作用类似于`Vue`中的计算属性`computed`**

![](https://img-blog.csdnimg.cn/20190822110609555.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

**`useMemo` 判断一段逻辑是否重复执行，`memo`判断一个组件是否重复渲染**

**注意：二者只是用来性能优化，尽量不要改变原有的代码逻辑**

**`useMemo与useEffect` 语法一致，但是`useMemo` 是在渲染期间完成，`useEffect` 渲染完成之后调用**

### 用法

**用法与`useEffect`一致**

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

 把“创建”函数和依赖项数组作为参数传入 `useMemo`，它仅会在某个依赖项改变时才重新计算 `memoized` 值。这种优化有助于避免在每次渲染时都进行高开销的计算。 

 **请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 `useEffect` 的适用范畴，而不是 `useMemo`。** 

 如果没有提供依赖项数组，`useMemo` 在每次渲染时都会计算新的值。

### 示例

```react
import React,{ useState, useMemo } from 'react';
import './App.css';

function App () {
  const [count, setCount] = useState(0)
  const double = useMemo(() => {
    return count * 2;
  }, [count])

  return (
    <div>
      <button
        type="button"
        onClick={() => {setCount(count + 1)}}
      >
        Click { count }, double: { double }
      </button>
    </div>
  )
}
```

## 使用Callback Hooks

```react
  const Counter = memo(function (props) {
    console.log('counter render')
    return (
      <div onClick={props.onClick}>{props.count}</div>
    )
  })  

  const onClick = useCallback(() => {
      console.log('Click')
  }, [])
  
  // ... 
  
  <Counter count={double} onClick={onClick} />
```

有一个父组件，其中包含子组件，父组件传递一个函数给子组件；通常而言，如果父组件更新了（传递的函数也会重新创建一次），子组件也会执行更新；但是大多数场景下，这种更新是没有必要的，我们可以**借助useCallback来返回函数，当`useCallback`的依赖项不发生变化的时候，重新创建的函数不会返回，那么传递的函数会复用上一次的。**这样，子组件就能避免不必要的更新。

>`useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`。

## 使用Ref Hooks

![](https://img-blog.csdnimg.cn/2019082414313448.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

`useRef`

- 获取子组件或者DOM节点的句柄
- 渲染周期之间的共享数据存储

### 示例

#### 获取子组件

```react
import React, {useState,Fragment,useRef,PureComponent,useCallback} from "react";
 
// 不能为函数组件提供引用,所以此处使用类组件
class Counter extends PureComponent{
  speak(){
    console.log(`now counter is :${this.props.count}`)
  }
  render (){
    const { props } =this;
    return (
      <h1 onClick={ props.onClick }>{ props.count } </h1>
    )
  }
}
function App(props){
  // 创建ref
  const counterRef = useRef()
  const [count,setCount] = useState(0);
 
  const onClick = useCallback(()=>{   //此方法在子组件中触发
    console.log("Click");
    counterRef.current.speak();    //调用子组件方法
  },[])
  return (
    <Fragment>
      <button
        type="botton"
        onClick={()=>{setCount(count+1)}}
        >
          Click ({ count })
      </button>
      <Counter count={count} ref={ counterRef } onClick={ onClick }/>
    </Fragment>
  )
}
 
export default App; 
```

#### 共享数据存储

```react
import React,{ useState, useRef, useEffect } from 'react';

function App () {
  const [count, setCount] = useState(0)
  // 创建ref
  const it = useRef()

  // 设置一个定时器，每过一秒就使count加一
  useEffect(() => {
    it.current = setInterval(() => {
      setCount(count => count + 1)
    }, 1000);
  }, [])
  // 当count等于十时，清除定时器（此处用ref来保存定时器，如果直接声明一个变量来保存定时器的话，每次重新渲染都会定义一个新的变量，这样子无法使得定时器被清除）
  useEffect(() => {
    if (count >= 10) {
      clearInterval(it.current)
    }
  })

  return (
    <div>
      <button
        type="button"
        onClick={() => {setCount(count + 1)}}
      >
        Click { count }, double: { double }
      </button>
      <Counter ref={counterRef} count={double} onClick={onClick} />
    </div>
  )
}
 
export default App;
```

## 自定义Hooks

```react
import React,{ useState, useCallback, useRef, useEffect } from 'react';

// 定义useCounter Hook，提供显示视口尺寸功能
function useCounter (count) {
  const size = useSize()
  return (
    <h1>{ count }, { size.width }X{ size.height }</h1>
  )
}

// 定义useSize，提供实时计算视口尺寸功能
function useSize () {
  const [size, setSize] = useState({
    width: document.documentElement.clientWidth,
    height: document.documentElement.clientHeight
  })

  const onResize = useCallback(() => {
    setSize({
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight
    })
  }, [])

  useEffect(() => {
    window.addEventListener('resize', onResize, false);
    return () => {
      window.removeEventListener('resize', onResize, false)
    }
  }, [onResize])
  // 返回size
  return size;
}

// 定义useCount Hook，提供计时器功能
function useCount (defaultCount) {
  const [count, setCount] = useState(defaultCount)
  const it = useRef()
  useEffect(() => {
    it.current = setInterval(() => {
      setCount(count => count + 1)
    }, 1000);
  }, [])
  useEffect(() => {
    if (count >= 10) {
      clearInterval(it.current)
    }
  })
  // 返回count
  return [count, setCount]
}

function App () {
  // 直接使用定义的Hook
  const [count, setCount] = useCount(0)
  const Counter = useCounter(count)
  const size = useSize();

  return (
    <div>
      <button
        type="button"
        onClick={() => {setCount(count + 1)}}
      >
        Click { count }, { size.width }X{ size.height }
      </button>
      { Counter }
    </div>
  )
}
 
export default App;
```

## Hook规则

 `Hook` 本质就是 `JavaScript` 函数，但是在使用它时需要遵循两条规则。 

### 1、只在最顶层使用 Hook

 **不要在循环，条件或嵌套函数中调用 Hook，** 确保总是在你的 React 函数的最顶层调用他们。遵守这条规则，你就能确保 Hook 在每一次渲染中都按照同样的顺序被调用。 

 只要 Hook 的调用顺序在多次渲染之间保持一致，React 就能正确地将内部 state 和对应的 Hook 进行关联。 

如果我们想要有条件地执行一个 effect，可以将判断放到 Hook 的*内部*：



```jsx
  useEffect(function persistForm() {
    // 👍 将条件判断放置在 effect 中
    if (name !== '') {
      localStorage.setItem('formData', name);
    }
  });
```

### 2、只在 React 函数和自定义Hook中调用 Hook

## Hooks常见问题

* 生命周期函数如何映射到`Hooks`？

  `useEffect  =》 componentDidMount、componentDidUpdate、componentWillUnmount`

* 类实例成员变量如何映射到Hooks？

  ```react
  function App () {
    const it = useRef(0) // useRef可以传入参数，但是不支持函数参数
    return (
    	<div>{ it.current }</div>
    )
  }
  
  ```

* Hooks中如何获取历史props和state？

  **通过创建一个ref来保存上一次的数据**

  ```react
  function App () {
    const [count, setCount] = useState(0);
    const prevCountRef = useRef();
  
    useEffect(() => {
      // 保存当前的count数据
      prevCountRef.current = count
    })
    // 获取上一次的count数据
    const prevCount = prevCountRef.current;
    function click () {
      setCount(count + 1)
    }
    return (
      <div>
        <button onClick={click}>click</button>
        <h1>Now: {count}, before: {prevCount} </h1>
      </div>
    )
  }
  ```

  

* 如何强制更新一个Hooks组件？

  **更新一个不相关的数据，来达到强制更新Hooks组件的效果**

  ```react
  function App () {
    const [count, setCount] = useState(0);
    const [updater, setUpdater] = useState(0)
  
    function forceUpdate () {
      setUpdater(updater => updater + 1)
    }
  
    console.log('update')
    return (
      <div>
        <button onClick={forceUpdate}>click</button>
        <h1>{count}</h1>
      </div>
    )
  }
  ```


# 六、`Redux`的概念和意义

[redux参考文档](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html)

## `Redux`三大原则

* 单一数据源
* 状态不可变
* 纯函数修改状态

## 纯函数

**纯函数的概念：一个函数的返回结果只依赖其参数，并且执行过程中没有副作用。**

1. **返回结果只依赖其参数**

   返回值只和函数参数有关，与外部无关。无论外部发生什么样的变化，函数的返回值都不会改变。

2. **函数执行过程中没有副作用**

   函数执行的过程中对外部产生了可观察的变化，我们就说函数产生了副作用。
   例如修改外部的变量、调用`DOM API`修改页面，发送`Ajax`请求、调用`window.reload`刷新浏览器甚至是`console.log`打印数据，都是副作用。

## 异步`Action`

当需要使用异步`Action`时，我们需要先安装`redux-thunk或者redux-saga`

```react
export const getList = () => {
  // getState为函数
  return (dispatch, getState) => {
    let state = getState();
    axios.get('./api/headerList.json')
      .then((res) => {
        const data = res.data;
        if (data.success) {
          const action = InitListAction(data.data)
          dispatch(action)
        }
      })
      .catch((err) => {
        console.log(err)
      })
  }
}
```

**对于异步`Action`，我们需要返回一个函数，并且该函数的参数为`dispatch和getState`函数。**

**为什么异步`Action`返回的函数参数不直接传入`state`而是通过`getState`函数去获取？**因为在执行异步任务的过程中，由于外部的其他操作，有可能导致`store中的state`发生变化，那么如果当初在异步`Action`中直接传入`state`的话，异步`Action`中拿到的`state`就会是更新之前的`state`，而不是由于其他操作导致`state`发生变化之后最新的`state`。这样显然不是我们想要的结果。可以通过下面的简单程序例子更好的理解。

**反面案例：**

```javascript
 // 定义数据中心   
let state = {
  name: 'ljw'
}

// 定义dispatch函数
let dispatch = (action) => {
  if (typeof action === 'function') {
    action(dispatch, state)
    console.log('异步action')
    return
  }
  console.log('同步action')
}
// 创建异步action
function createAction () {
  return (dispatch, state) => {
    setTimeout(() => {
      // 由于外部同步操作修改了state，但是此处的state还是之前传入的旧state，可能会导致一些bug产生
      console.log(state)
      dispatch({
        type: 'add',
        data: 'hello, world'
      })
    }, 1000);
  }
}
// 执行dispatch
dispatch(createAction())

// 外部操作修改state
state = {
  name: 'ymh'
}
```

**正面案例：通过getState函数来获取最新的state**

```javascript
 // 定义数据中心   
let state = {
  name: 'ljw'
}
// 定义获取最新state函数
let getState = () => {
  return state
}
// 定义dispatch函数
let dispatch = (action) => {
  if (typeof action === 'function') {
    action(dispatch, getState)
    console.log('异步action')
    return
  }
  console.log('同步action')
}
// 创建异步action
function createAction () {
  return (dispatch, getState) => {
    setTimeout(() => {
      // 通过getState函数获取到最新的state
      let state = getState()
      console.log(state)
      dispatch({
        type: 'add',
        data: 'hello, world'
      })
    }, 1000);
  }
}
// 执行dispatch
dispatch(createAction())

// 外部操作修改state
state = {
  name: 'ymh'
}
```

# 七、渐进式`Web App`

## `PWA`组成技术

* `Service Worker`
* `Promise`
* `fetch`
* `cache API`
* `Notification API`

### `Service Worker`

服务工作线程

* 常驻内存运行
* 代理网络请求
* 依赖`HTTPS`

![](https://img-blog.csdnimg.cn/20200407184245725.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

### `Promise`

* 优化回调地狱
* `async/await`语法同步化
* `service worker的API`风格

### 网络请求`fetch`

* 比`XMLHttpRequest`更简洁
* `Promise`风格
* 依旧存在不足

### 支持资源的缓存系统

* 缓存资源`（CSS/Script/image）`
* 依赖`Service Worker`代理网络请求
* 支持离线程序运行

### `Notification API`消息推送

* 依赖用户授权
* 适合在`Service Worker`中推送

## `Service Worker`

[渐进式Web应用入门-ServiceWorker](https://blog.csdn.net/VpbzfFzYt/article/details/78130199)

[初探ServiceWorker](https://blog.csdn.net/xiaoxi_qianlan/article/details/50805896)

![](https://img-blog.csdnimg.cn/20200412165041119.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

**注册`service worker`**

![](https://img-blog.csdnimg.cn/20200410175830678.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200412172742351.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020041217291468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

注册成功后，就会进入安装(`Installation`)过程。(这里需要说明的是`activating`阶段是不会触发事件的，只有激活成功后才会触发`onactivate`事件)。`sw.js`定义了`ServiceWorker`的表现。

**service worker 脚本**

![](https://img-blog.csdnimg.cn/20200412175732168.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

`ServiceWorker`对第一次载入的页面是不起作用的，只有下次访问或者刷新页面后，`ServiceWorker`才能起作用。注意`ServiceWorker`的更新，每次访问`ServiceWorker`控制的页面，浏览器都会去比对当前`js`文件和注册路径对应的`js`文件，只要有一个字节不同，浏览器都会获取并安装新文件。但是，这不是立即生效的，原有的`ServiceWorker`还是会运行，只有当`ServiceWorker`控制的页面全部关闭后，新的`ServiceWorker`才会被激活。`(Shift+F5)`

## `Promise`

![](https://img-blog.csdnimg.cn/20200410175559362.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020041017562629.png)

**promise 加载100个图片，但同时只允许有10个promise运行，如何解决并保证效率？**

## 更优雅的请求`fetch`

**fetch是一个全局对象**

`Ajax请求json数据`

```javascript
 const xhr = new XMLHttpRequest();
 xhr.responseType = 'json'
 xhr.onreadystatechange = () => {
  if (xhr.readyState === 4) {
		if (xhr.status >= 200 && xhr.status < 300) {
			console.log(xhr.response)
		}
 	}
 }
 xhr.open('GET', '/userinfo.json', true)
 xhr.send(null)
```

使用`fetch`来请求数据

```javascript
fetch('/userinfo.json', {
  method: 'GET',
  // body: 
  headers: new Headers(),
  credentials: 'include'
}).then(response => response.json())
  .then(info => console.log(info))
```

## `cache API`

[cache API](https://segmentfault.com/a/1190000018629440)

![](https://img-blog.csdnimg.cn/20200412175352136.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dlX2ppYQ==,size_16,color_FFFFFF,t_70)

```javascript
// sw.js

const CACHE_NAME = 'cache-v1';  //命名空间，缓存存储的地方，每次修改内容都应该改变CACHE_NAME的值 
 
self.addEventListener('install',event => {
    console.log('install',event);
    event.waitUntil(caches.open(CACHE_NAME).then(cache => {
        cache.addAll(['/','./index.css']);  //写入资源的缓存
    }));  
});
 
self.addEventListener('activate',event => {
    console.log('activate',event);
  	// 清理缓存
    event.waitUntil(caches.keys().then(cacheNames => {
        return Promise.all(cacheNames.map(cacheName => {
            if(cacheName !== CACHE_NAME){
                return caches.delete(cacheName)
            }
        }))
    }));
})
 
self.addEventListener('fetch',event => {
    console.log('fetch',event);
    event.respondWith(caches.open(CACHE_NAME).then(cache => {
        return cache.match(event.request).then(response => {
            if (response){
                return response;
            }
 
            return fetch(event.response).then(response =>{
                cache.put(event.request,response.clone());
 
                return response;
            })
        })
    }))
})
```

## `Notification API`消息推送

[Notification](https://www.jianshu.com/p/69042b92cae1)

```javascript
// 代表当前对象获取到的用户授权，默认值为"default"，除了"default",还有granted（允许）、denied（禁止
Notification.permission 

// 请求获取用户授权
Notification.requestPermission().then(permission => console.log(permission))

// 创建Notification实例，相当于弹出一个通知
// 第一个参数是通知的title，第二个参数是一个对象，代表一系列的option
new Notification('Hello notification', {body: 'This is from console'})
```

## 如何在项目中开启`PWA`

在`src`文件目录下有一个`serviceWorker.js`文件，这个文件看起来很复杂，其实就是检查一下环境变量是否为`“production”`，且当前浏览器是否兼容`service worker`。 再来看一下我们的 `index.js` 文件，可以看到最后面有一句：

```javascript
serviceWorker.unregister();
```

如果我们想要使用`create-react-app`提供的`pwa`特性的话，我们需要将 `serviceWorker.unregister()` 改为 `serviceWorker.register()`，然后我们打包我们的项目，在打包得到的`build`文件夹下我们可以看到生成了一个`server-worker.js`文件 和 `preche-manifest.xxxxxxxxx.js` 和一个`manifest.json`。

其中的`server-worker.js`中规定了我们如何缓存我们的资源，`preche-manifest` 中列出了我们的静态资源。

为我们打包得到的build文件夹下的代码建一个服务并访问。 然后打开chrome控制台的Application查看，发现它帮助我们缓存了我们的静态文件。

我们把网断开，可以发现，我们的`app`依旧能正常打开。这就是目前的`create-react-app`给我们提供的默认的`pwa`特性。默认的为我们生成一个`service-worker.js`缓存我们的`app shell`，使得它可以在离线时正常的打开。

# 八、业务架构

## 项目工程初始化

### 项目改造

由于该项目为多页面应用，因此我们需要对项目进行一定的配置

* 首先我们需要在public目录下新增`order.html、query.html、ticket.html`三个文件，并且将其`title`改成相应的页面名称

* 再对`config`目录下的`paths.js`中导出的对象进行扩充

  ```javascript
  module.exports = {
    appHtml: resolveApp('public/index.html'),
    appQueryHtml: resolveApp('public/query.html'),
    appOrderHtml: resolveApp('public/order.html'),
    appTicketHtml: resolveApp('public/ticket.html'),
    appIndexJs: resolveModule(resolveApp, 'src/index/index'),
    appQueryJs: resolveModule(resolveApp, 'src/query/index'),
    appTicketJs: resolveModule(resolveApp, 'src/ticket/index'),
    appOrderJs: resolveModule(resolveApp, 'src/order/index')
  }
  ```

* 再对`config目录下的webpack.config.js`文件进行配置，将其中`entry`由数组

  ```javascript
  entry: [
           isEnvDevelopment && require.resolve('react-dev-utils/webpackHotDevClient'),
        	 paths.appIndexJs,
      	 ].filter(Boolean),
  ```

  改为对象

  ```javascript
  entry: {
    index: [
      paths.appIndexJs, 
      isEnvDevelopment && 
      require.resolve('react-dev-utils/webpackHotDevClient')].filter(Boolean),
    query: [
      paths.appQueryJs, 
      isEnvDevelopment && 
      require.resolve('react-dev-utils/webpackHotDevClient')].filter(Boolean),
    ticket: [
      paths.appTicketJs, 
      isEnvDevelopment && 
      require.resolve('react-dev-utils/webpackHotDevClient')].filter(Boolean),
    order: [
      paths.appOrderJs, 
      isEnvDevelopment && 
      require.resolve('react-dev-utils/webpackHotDevClient')].filter(Boolean)
  },
  ```

* 再新增其中`plugins`中的`HtmlWebpackPlugin`实例

  ```javascript
  new HtmlWebpackPlugin(
    Object.assign(
      {},
      {
        inject: true,
        template: paths.appHtml,
        filename: 'index.html',
        cchunks: ['index']
      },
      isEnvProduction
      ? {
        minify: {
          removeComments: true,
          collapseWhitespace: true,
          removeRedundantAttributes: true,
          useShortDoctype: true,
          removeEmptyAttributes: true,
          removeStyleLinkTypeAttributes: true,
          keepClosingSlash: true,
          minifyJS: true,
          minifyCSS: true,
          minifyURLs: true,
        },
      }
      : undefined
    )
  ),
  new HtmlWebpackPlugin(
    Object.assign(
      {},
      {
        inject: true,
        template: paths.appQueryHtml,
        filename: 'query.html',
        cchunks: ['query']
      },
      isEnvProduction
      ? {
        minify: {
          removeComments: true,
          collapseWhitespace: true,
          removeRedundantAttributes: true,
          useShortDoctype: true,
          removeEmptyAttributes: true,
          removeStyleLinkTypeAttributes: true,
          keepClosingSlash: true,
          minifyJS: true,
          minifyCSS: true,
          minifyURLs: true,
        },
      }
      : undefined
    )
  ),
  new HtmlWebpackPlugin(
    Object.assign(
      {},
      {
        inject: true,
        template: paths.appOrderHtml,
        filename: 'order.html',
        cchunks: ['order']
      },
      isEnvProduction
      ? {
        minify: {
          removeComments: true,
          collapseWhitespace: true,
          removeRedundantAttributes: true,
          useShortDoctype: true,
          removeEmptyAttributes: true,
          removeStyleLinkTypeAttributes: true,
          keepClosingSlash: true,
          minifyJS: true,
          minifyCSS: true,
          minifyURLs: true,
        },
      }
      : undefined
    )
  ),
  new HtmlWebpackPlugin(
    Object.assign(
      {},
      {
        inject: true,
        template: paths.appTicketHtml,
        filename: 'ticket.html',
        cchunks: ['ticket']
      },
      isEnvProduction
      ? {
        minify: {
          removeComments: true,
          collapseWhitespace: true,
          removeRedundantAttributes: true,
          useShortDoctype: true,
          removeEmptyAttributes: true,
          removeStyleLinkTypeAttributes: true,
          keepClosingSlash: true,
          minifyJS: true,
          minifyCSS: true,
          minifyURLs: true,
        },
      }
      : undefined
    )
  ),
  ```

  

# 九、项目开发

## `prop-types`的使用

在多人开发时，当被人使用自己定义的组件时，有可能出现类型传错的情况，而在自己的组件上加上`prop-types`，他可以对父组件传来的props进行检查，加入父组件中想传递的是字符串类型‘3’，而传递了一个数字类型3，如果没有类型检查系统不会给与提示，但是有了类型检查以后，再控制台会给你一个类型传递错误的提示。这样在工作中可以快速找到错误。

```jsx
//安装
npm install prop-types --save
//引入
import PropTypes from 'prop-types';

function CitySelector (props) {
  
}
CitySelector.propTypes = {
  show: PropTypes.bool.isRequired,
  cityData: PropTypes.object,
  isLoading: PropTypes.bool.isRequired,
  onBack: PropTypes.func.isRequired
};
```

## `Redux中bindActionCreator`的理解

```react
import React, { useCallback, useMemo } from 'react';
import { bindActionCreator } from 'redux'
import { connect } from 'react-redux';
import { exchangeFromTo, showCitySelector } from './actions';
import Journey from './Journey.jsx';

function App (props) {
  const { dispatch } = props;
  // 通过useMemo来做性能优化
  const cbs = useMemo(() => {
    return bindActionCreators(
      {
        exchangeFromTo,
        showCitySelector
      },
      dispatch
    );
  }, []);
  
  return (
  	<Journey {...cbs} />
  )
}

export default connect(
  function mapStateToProps(state) {
    return state;
  },
  function mapDispatchToProps(dispatch) {
    return { dispatch };
  }
)(App);
```

该函数接受两个参数,其中第一个参数表示`actionCreateor`函数，第二个参数为`dispatch`。因此`bindActionCreators可以直接dispacth`第一个参数（对象）的返回值，并**最终返回一个对象。**

**`bindActionCreators`的作用是将一个或多个`action和dispatch`组合起来生成`mapDispatchToProps`需要生成的内容。**

```react
const mapDispatchToProps = (dispatch) => {
  return {
    doExchangeFromTo: () => dispatch(exchangeFromTo),
    doShowCitySelector: () => dispatch(showCitySelector)
  }
}
等价于
const mapDispatchToProps = (dispatch) => {
  return bindActionCreators({
    exchangeFromTo,
    showCitySelector
  }, dispatch)
}
```

## `classnames库`

简单用法

```react
import classnames from 'classnames'

function App (props) {
  const { show } = props
  return (
  	<div className={classnames('city-selector', {hidden: !show})}></div>
  )
}
```

```javascript
classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); 
// => 'foo bar baz quux'
```

## `fetch`的使用

```react
export function fetchCityData() {
  return (dispatch, getState) => {
    // 判断缓存是否过期
    const cache = JSON.parse(
      localStorage.getItem('city_data_cache') || '{}'
    );
    if (Date.now() < cache.expires) {
      dispatch(setCityData(cache.data));
      return;
    }
    fetch('/rest/cities?_' + Date.now())
    	// 返回的响应是JSON格式的，所以调用res.json方法来转换数据。
      // 再返回另一个Promise，所以可以调用.then方法处理我们转换后的数据。
      .then(res => res.json())
      .then(cityData => {
      dispatch(setCityData(cityData));
			
      // 将数据写入缓存，并且加上时效
      localStorage.setItem(
        'city_data_cache',
        JSON.stringify({
          expires: Date.now() + 60 * 1000,
          data: cityData,
        })
      );

      dispatch(setIsLoadingCityData(false));
    })
      .catch(() => {
      dispatch(setIsLoadingCityData(false));
    });
  };
}
```

## 通过字母索引来进行定位

```jsx
// 通过给每一个title项加上data-cate自定义属性来提供定位依据
<li className="city-li" key="title" data-cate={title}>
  {title}
</li>


// 通过toAlpha方法来进行定位
const toAlpha = useCallback(alpha => {
  document.querySelector(`[data-cate='${alpha}']`).scrollIntoView();
}, [])
```

## 美团菜单页面联动

1. `DOM`更新后，获取整个`content`的高度（包裹所有item的盒子的高度）;
2. 获取每个`category`在`content`上的高度，从而获取跳转`y`值区间数组`jumpArr`；
3. 在`content`上监听滑动事件，判断当前y值在`jumpArr`中的位置，更新左侧category样式。

## `dayjs`的使用

```react
npm i dayjs --save

import dayjs from 'dayjs'
```

### 创建

```
dayjs() // 当前时间
dayjs('1995-12-25') // 1995-12-25
dayjs(Date.now() - 24 * 60 * 60 * 1000) // 昨天
```

### 格式化

```
dayjs().format('YYYY年MM月DD日 HH:mm:ss') // 2018年08月08日 00:00:00
dayjs().format('[YYYY]') // "[2018]"。[] 里的会原样输出。
```

### 操作

```
dayjs().add(7, 'days') // 之后的第7天
dayjs().subtract(1, 'months') // 上个月
dayjs().startOf('months') // 获取一月初 
dayjs().endOf('year') // 获取一年年末
```

### 查询

```
dayjs('2010-10-20').isBefore('2010-10-21') // 早于
dayjs('2010-10-20').isAfter('2010-10-19') // 晚于
dayjs().isLeapYear() // 闰年
```

