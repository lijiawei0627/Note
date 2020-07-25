# 一、`Redux`

```bash
npm install redux
```

## `createStore`和`reducer`

 reducer是纯函数，纯函数指的是，给固定的输入，就一定会有固定的输出，而且不会有任何的副作用 

```react
// store.js
import { createStore } from 'redux'

// state为之前的数据，action为当前用户需要操作的数据
const counterReducer = (state = 0, action) => {
  switch (action.type) {
    case 'add':
      return state + 1;
    case 'minus':
      return state - 1;
    default:
      // 每次都必须返回一个全新的state
      return state;
  }
}

const store = createStore(counterReducer);

export default store;
```

## `getState`

## `dispatch`

## `subscribe`

```react
// App.js
import React from 'react'
// 导入store
import store from './store.js'

class App extends Component {
  constructor (props) {
  	super(props);
      
  	// 获取store中的数据，并赋给this.state
  	this.state = store.getState();
      
  	// store发生改变时，将会执行handleStateChange
  	store.subscribe(this.handleStateChange)
  }
  add = () => {
  	// 创建一个action
  	const action = {
    	type: 'add'
  	}
    
  	// 将action传给store
  	store.dispatch(action);
  }

  handleStateChange () {
    // 六、更新组件中数据
    this.setState(store.getState())
  }
  render () {
      return (
        <div>
        {/* .... */}
        </div>
      )
  }
}

```

## `combineReducers`（合并各组件reducer）

可以从`redux`或者`redux-immutable`【推荐】中获取`combineReducers`函数

```react
// /src/store/reducer.js


// import {combineReducers} from 'redux'
// 引用redux-immutable中的combineReducers可以生成immutable对象
import {combineReducers} from 'redux-immutable'
import {reducer as headerReducer} from '../common/header/store'
import homeReducer from '../pages/home/store/reducer'
import detailReducer from '../pages/detail/store/reducer'
import {reducer as loginReducer} from '../pages/login/store'

// 使用combineReducers整合各组件的reducer.js
const reducer = combineReducers ({
  header: headerReducer,
  home: homeReducer,
  detail: detailReducer,
  login: loginReducer
})

export default reducer
```

> 注意：当使用`combineReducers`进行整合reducer时，在各组件中进行`mapStateToProps`映射时，要在state后面跟上整合对象中相对应的键值

```react
const mapStateToProps = (state) => {
  return {
    // 因为引用redux-immutable中的combineReducers来整合各组件的reducer，可以生成immutable对象
    // 所以必须使用immutable对象提供的get方法获取header中的focused数据
    // 也可以使用state.getIn(['header', 'focused'])来获取header和focused数据
    focused: state.get('header').get('focused')
  }
}
```

## `applyMiddleware`（应用中间件）

```react
// src/store/index.js

// 调用applyMiddleware来应用中间件
import {createStore, compose, applyMiddleware} from 'redux'
// 引入combineReducers整合各组件的reducer.js之后的reducer.js
import reducer from './reducer'
import thunk from 'redux-thunk'

// 应用（Chrome）Redux插件
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;
const store = createStore(reducer, composeEnhancers(
  // 使用redux-thunk，在action中异步获取数据
  applyMiddleware(thunk)
))

export default store;
```

# 二、`react-redux`

```bash
npm install react-redux
```

## Provider（类似于Context中的Provider）

内部源码实现是用的`Context`

```react
// src/App.js

import ReduxTest from './ReduxTest.js'
import {Provider} from 'react-redux'
import store from './store/index'

function App() {
  return (
    // 此时Provider内部的组件都有能力获取到store,Provider为store提供器
    <Provider store = {store}>
      {/* Probider中包裹需要使用store的各组件 */}
      <ReduxTest />
    </Provider>
  );
}
```

## `connect`

## `mapStateToProps`

## `mapDispatchToProps`

```react
// src/ReduxTest.js

import {connect} from 'react-redux'

function ReduxTest (props) {
  const { inputValue, list, handleInputChange } = props;
  return (
    <div>
      {/* .... */}
    </div>
  )
}

// 将store里面的数据映射到组件props中,mapStateToProps为映射关系,state中即为store中数据
const mapStateToProps = (state) => {
  return {
    inputValue: state.inputValue,
    list: state.list
  }
}

// 将store.dispatch映射到props上，通过this.props调用函数
const mapDispatchToProps = (dispatch) => {
  return {
    handleInputChange (e) {
      const action = getInputChangeAction(e.target.value)
      // 将action传递给store
      dispatch(action)
    }
  }
}

// connect函数执行后会返回一个高阶组件。mapStateToProps, mapDispatchToProps为配置项（状态映射）
export default connect(mapStateToProps, mapDispatchToProps)(ReduxTest);
```

## 异步

react默认只支持同步，实现异步任务，比如延迟，网络请求，需要中间件的支持，比如我们使用最简单的`redux-thunk`和`redux-logger`

### `redux-thunk`

#### 安装

```bash
npm install redux-thunk
```

#### 应用中间件

```react
// store.js

// 调用applyMiddleware来应用中间件
import {createStore, compose, applyMiddleware} from 'redux'
import reducer from './reducer'
import thunk from 'redux-thunk'

// 应用（Chrome）Redux插件
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;
const store = createStore(reducer, composeEnhancers(
  // 使用redux-thunk，在action中异步获取数据
  applyMiddleware(thunk)
))

export default store;
```

#### 使用`redux-thunk`

 `redux-thunk`中间件的使用，使得action可以为一个函数 。将原本应该写在组件中的异步请求数据逻辑拆分到`actionCreators.js`中，以便于管理和代码复用

```react
// store/actionCreators.js

// 使用了redux-thunk之后，actionCreators中可以定义函数使用。在函数中进行异步请求，请求完成之后再进行dispatch。
export const getTodoList = () => {
  // 函数中可以传递两个参数：dispatch和getState方法
  return (dispatch) => {
    axios.get('http://127.0.0.2:5000/test')
      .then((res) => {
        const data = res.data
        const action = {
            type: 'INIT_LIST_date',
            data: data
        }
        // 异步请求数据之后再dispatch
        dispatch(action)
      })
      .catch((err) => {
        console.log(err);
      })
  }
}
```

### `redux-saga`

 `redux-saga` 是一个用于管理应用程序 Side Effect（副作用，例如异步获取数据，访问浏览器缓存等）的 library，它的目标是让副作用管理更容易，执行更高效，测试更简单，在处理故障时更容易。 

#### 安装

```bash
$ npm install --save redux-saga
```

#### 创建、注册、运行

```react
// store/saga.js


import {takeEvery, put} from 'redux-saga/effects'
import {GET_INIT_LIST} from './actionTypes'
import axios from 'axios'
import {initListAction} from './actionCreators'

// 可以将组件中dispatch派发的action作为参数传递进去
function * getInitList (action) {
  try {
    yield console.log('abc')
    const res = yield axios.get('http://127.0.0.2:5000/test');
    const action = initListAction(res.data)
    // redux-saga提供的类似于store.dispatch方法
    yield put(action)
  } catch (error) {
    console.log('网络请求失败')
  }
}
function* mySaga() {
  // 当action.type为GET_INIT_LIST时，执行getInitList函数
  yield takeEvery(GET_INIT_LIST, getInitList)
  // 可以监听多个action
  // yield takeEvery(GET_INIT_LIST2, getInitList2)
}

export default mySaga
```



```react
// store/index.js

import { createStore, applyMiddleware, compose } from 'redux'
import reducer from './reducer'
import createSagaMiddleware from 'redux-saga'
import todoSagas from './sagas'

// 创建saga中间件
const sagaMiddleware = createSagaMiddleware()

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;

const store = createStore(reducer, composeEnhancers(
  // 注册中间件
  applyMiddleware(sagaMiddleware)
))

// 中间件运行saga，applyMiddleware.run()
sagaMiddleware.run(todoSagas);

export default store;
```



# 三、`react-router-dom`

```bash
npm install react-router-dom
```

## 起步

```react
// src/App.js


import {BrowserRouter, Route, Link} from 'react-router-dom'

function App() {
  return (
    <Provider store = {store}>
      <div>
        <GrobalStyle />          
        // 通过Link标签进行路由跳转
        <Link to = '/'>首页</Link>
        <Link to = '/about'>关于</Link>
        <BrowserRouter>
          <Header />
          //  添加exact，以使路由匹配时不进行包容匹配
          <Route path='/' exact component = {Home}></Route>
          <Route path='/about' exact component = {About}></Route>
        </BrowserRouter>
      </div>
    </Provider>
  );
}

export default App;
```

> 包容匹配：例如/login 会同时匹配到`/`和`/login`

## 动态路由

```react
import {BrowserRouter, Route, Link} from 'react-router-dom'

// 在props中会传递进来一个路由器对象match（获取参数信息）
function Detail (props) {
    return (
      <div>
        // 拿到路由后面的参数id
        当前课程为：{ props.match.params.id }
      </div>
    )
}

function App() {
  return (
    <Provider store = {store}>
      <div>
        // 在路由后面传递参数
        <Link to = '/detail/pythone'></Link>
        <Link to = '/detail/web'></Link>
        <BrowserRouter>
           <Route path= '/detail/:id' exact component = {Detail}></Route>
        </BrowserRouter>
      </div>
    </Provider>
  );
}

export default App;
```

## 404页面匹配路由

```react
import {BrowserRouter, Route, Link} from 'react-router-dom'

function NoMatch (props) {
    return (
      <div>
        404, {props.location.pathname}不存在
      </div>
    )
}

function App() {
  return (
    <Provider store = {store}>
      <div>
        <Link to = '/detail/pythone'></Link>
        <Link to = '/detail/web'></Link>
        <BrowserRouter>
           <Route path='/' exact component = {Home}></Route>
           <Route path='/about' exact component = {About}></Route>
           <Route path= '/detail/:id' exact component = {Detail}></Route>
            {/* 404,匹配不到path时，渲染404路由对应组件NoMatch */}
           <Route component = {NoMatch}/>
        </BrowserRouter>
      </div>
    </Provider>
  );
}

export default App;
```



## 嵌套路由

```react
import {BrowserRouter, Route, Link} from 'react-router-dom'

function About () {
  return (
    <div>
      <div>
        <Link to = '/about/me'>个人信息</Link>
        <Link to = '/about/order'>订单信息</Link>
      </div>
      <Switch>
        //  在About中再嵌套路由
        <Route path='/about/me' component = {() => (<div>me</div>)}></Route>
        <Route path='/about/order' component = {() => (<div>order</div>)}></Route>
        // 进入页面时，默认进入/about/me组件
        <Redirect to = '/about/me'></Redirect>          
      </Switch>
    </div>
  )
}

function App() {
  return (
    <Provider store = {store}>
      <div>
        <BrowserRouter>
          <Route path='/' exact component = {Home}></Route>
          <Route path='/about' exact component = {About}></Route>
        </BrowserRouter>
      </div>
    </Provider>
  );
}

export default App;
```



## Switch组件

类似于添加exact的功能，以使路由匹配时不进行包容匹配

```react
import {BrowserRouter, Route, Link, Switch} from 'react-router-dom'

function App() {
  return (
    <Provider store = {store}>
      <div>
        <BrowserRouter>
          <Switch>
            <Route path='/' component = {Home}></Route>
            <Route path='/about' component = {About}></Route>
          </Switch>
        </BrowserRouter>
      </div>
    </Provider>
  );
}

export default App;
```

## 重定向

```
<Redirect to = '/about/me'></Redirect>
```

## 路由守卫

# 四、 [UmiJS](https://umijs.org/zh/) 

 `umi`，中文可发音为乌米，是一个可插拔的企业级 react 应用框架。 

## 项目骨架

```bash
npm init
npm install umi -D
```

## 新建index页

```bash
umi g page index
umi g page users
```

## 起服务看效果

```bash
umi dev
```



# 五、`redux`解决方案 -- `dva`

