# 一、代码分割

 对你的应用进行代码分割能够帮助你“懒加载”当前用户所需要的内容，能够显著地提高你的应用性能。 

### 1、`import()`

 在你的应用中引入代码分割的最佳方式是通过动态 `import()` 语法。 

**使用之前：**

```javascript
import { add } from './math';

console.log(add(16, 26));
```

**使用之后：**

```JavaScript
import("./math").then(math => {
  console.log(math.add(16, 26));
});
```

> 动态 `import()` 语法目前只是一个 `ECMAScript (JavaScript)` [提案](https://github.com/tc39/proposal-dynamic-import)， 而不是正式的语法标准。预计在不远的将来就会被正式接受。 

### 2、`React.lazy`

> **注意：**`React.lazy` 和 Suspense 技术还不支持服务端渲染。 

 `React.lazy` 函数能让你像渲染常规组件一样处理动态引入（的组件）。 

**使用之前：**

```react
import OtherComponent from './OtherComponent';

function MyComponent() {
  return (
    <div>
      <OtherComponent />
    </div>
  );
}
```

**使用之后：**

```jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <OtherComponent />
    </div>
  );
}
```

`React.lazy` 接受一个函数，这个函数需要动态调用 `import()`。它必须返回一个 `Promise`，该 Promise 需要 resolve 一个 `defalut` export 的 React 组件。

### 3、Suspense（提供加载过渡动画的接口）

如果在 `MyComponent` 渲染完成后，包含 `OtherComponent` 的模块还没有被加载完成，我们可以使用加载指示器为此组件做优雅降级。（加载一个过渡动画）这里我们使用 `Suspense` 组件来解决。

```
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

 `fallback` 属性接受任何在组件加载过程中你想展示的 React 元素。你可以将 `Suspense` 组件置于懒加载组件之上的任何位置。你甚至可以用一个 `Suspense` 组件包裹多个懒加载组件。 

### 4、异常捕获边界

 如果模块加载失败（如网络问题），它会触发一个错误。你可以通过[异常捕获边界（Error boundaries）](https://react.docschina.org/docs/error-boundaries.html)技术来处理这些情况，以显示良好的用户体验并管理恢复事宜。 

```react
import MyErrorBoundary from './MyErrorBoundary';
const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

const MyComponent = () => (
  <div>
    <MyErrorBoundary>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </MyErrorBoundary>
  </div>
);
```

### 5、命名导出（Named Exports）

`React.lazy` 目前只支持默认导出（default exports）。如果你想被引入的模块使用命名导出（named exports），你可以创建一个中间模块，来重新导出为默认模块。

```JavaScript
// ManyComponents.js
export const MyComponent = /* ... */;
export const MyUnusedComponent = /* ... */;
```

```JavaScript
// MyComponent.js
export { MyComponent as default } from "./ManyComponents.js";
```

```jsx
// MyApp.js
import React, { lazy } from 'react';
const MyComponent = lazy(() => import("./MyComponent.js"));
```

# 二、Context

 Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。 

 使用 context, 我们可以避免通过中间元素传递 props。

```jsx
// Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
// 为当前的 theme 创建一个 context（“light”为默认值）。
const ThemeContext = React.createContext('light');

class App extends React.Component {
  render() {
    // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。
    // 无论多深，任何组件都能读取这个值。
    // 在这个例子中，我们将 “dark” 作为当前的值传递下去。
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间的组件再也不必指明往下传递 theme 了。
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 指定 contextType 读取当前的 theme context。
  // React 会往上找到最近的 theme Provider，然后使用它的值。
  // 在这个例子中，当前的 theme 值为 “dark”。
  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;
  }
}
```

### 1、使用 Context 之前的考虑

Context 主要应用场景在于*很多*不同层级的组件需要访问同样一些的数据。请谨慎使用，因为这会使得组件的复用性变差。

### 2、API

##### `React.createContext`

```jsx
const MyContext = React.createContext(defaultValue);  // defaultValue为传入的默认值，只有当组件所处的树中没有匹配到 Provider 时，其 defaultValue 参数才会生效。
```

##### `Context.Provider`

 Provider 接收一个 `value` 属性，传递给消费组件。  多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。 

> 当 Provider 的 `value` 值发生变化时，它内部的所有消费组件都会重新渲染。Provider 及其内部 consumer 组件都不受制于 `shouldComponentUpdate` 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。 

##### `Class.contextType`（类组件中使用）

```jsx
class MyClass extends React.Component {
  componentDidMount() {
    let value = this.context;
    /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
  }
  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }
  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }
  render() {
    let value = this.context;
    /* 基于 MyContext 组件的值进行渲染 */
  }
}
MyClass.contextType = MyContext;
```

 挂载在 class 上的 `contextType` 属性会被重赋值为一个由 [`React.createContext()`](https://react.docschina.org/docs/context.html#reactcreatecontext) 创建的 Context 对象。这能让你使用 `this.context` 来消费最近 Context 上的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。 

##### `Context.Consumer`（函数组件中使用）

```jsx
<MyContext.Consumer>
  {value => /* 基于 context 值进行渲染*/}
</MyContext.Consumer>
```

 这里，React 组件也可以订阅到 context 变更。这能让你在[函数式组件](https://react.docschina.org/docs/components-and-props.html#function-and-class-components)中完成订阅 context。 

### 3、消费多个 Context

 为了确保 context 快速进行重渲染，React 需要使每一个 consumers 组件的 context 在组件树中成为一个单独的节点。 

```jsx
// 提供初始 context 值的 App 组件
    return (
      <ThemeContext.Provider value={theme}>
        <UserContext.Provider value={signedInUser}>
          <Layout />
        </UserContext.Provider>
      </ThemeContext.Provider>
    );
    
    
// 一个组件可能会消费多个 context
function Content() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <UserContext.Consumer>
          {user => (
            <ProfilePage user={user} theme={theme} />
          )}
        </UserContext.Consumer>
      )}
    </ThemeContext.Consumer>
  );
}
```

### 4、注意

因为 context 会使用参考标识（reference identity）来决定何时进行渲染，这里可能会有一些陷阱，当 provider 的父组件进行重渲染时，可能会在 consumers 组件中触发意外的渲染。举个例子，当每一次 Provider 重渲染时，以下的代码会重渲染所有下面的 consumers 组件，因为 `value` 属性总是被赋值为新的对象：



```jsx
class App extends React.Component {
  render() {
    return (
      <Provider value={{something: 'something'}}>
        <Toolbar />
      </Provider>
    );
  }
}
```



为了防止这种情况，将 value 状态提升到父节点的 state 里：



```jsx
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: {something: 'something'},
    };
  }

  render() {
    return (
      <Provider value={this.state.value}>
        <Toolbar />
      </Provider>
    );
  }
}
```



#  三、错误边界

 这种组件**可以捕获并打印发生在其子组件树任何位置的 JavaScript 错误，并且，它会渲染出备用 UI**，而不是渲染那些崩溃了的子组件树。 

### 1、注意

**错误边界**无法**捕获以下场景中产生的错误：**

- 事件处理（[了解更多](https://react.docschina.org/docs/error-boundaries.html#how-about-event-handlers)）
- 异步代码（例如 `setTimeout` 或 `requestAnimationFrame` 回调函数）
- 服务端渲染
- 它自身抛出来的错误（并非它的子组件）

### 2、使用方法

 如果一个 class 组件中定义了 [`static getDerivedStateFromError()`](https://react.docschina.org/docs/react-component.html#static-getderivedstatefromerror) 或 [`componentDidCatch()`](https://react.docschina.org/docs/react-component.html#componentdidcatch) 这两个生命周期方法中的任意一个（或两个）时，那么它就变成一个错误边界。当抛出错误后，请使用 `static getDerivedStateFromError()` 渲染备用 UI ，使用 `componentDidCatch()` 打印错误信息。 

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染能够显示降级后的 UI
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // 你同样可以将错误日志上报给服务器
    logErrorToMyService(error, info);
  }

  render() {
    if (this.state.hasError) {
      // 你可以自定义降级后的 UI 并渲染
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

然后你可以将它作为一个常规组件去使用：

```jsx
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

### 3、边界应该放置在哪？

错误边界的粒度由你来决定，可以将其包装在最顶层的路由组件并为用户展示一个 “Something went wrong” 的错误信息，就像服务端框架经常处理崩溃一样。你也可以将单独的部件包装在错误边界以保护应用其他部分不崩溃。

# 四、Refs 转发

 Ref 转发是一项将 [ref](https://react.docschina.org/docs/refs-and-the-dom.html) 自动地通过组件传递到其一子组件的技巧。 

### 1、`React.forwardRef`

 使用 `React.forwardRef` 来获取传递给它的 `ref`，然后转发到它渲染的 DOM `button`： 

```jsx
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 你可以直接获取 DOM button 的 ref：
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

 这样，使用 `FancyButton` 的组件可以获取底层 DOM 节点 `button` 的 ref ，并在必要时访问，就像其直接使用 DOM `button` 一样。 

### 2、注意

第二个参数 `ref` 只在使用 `React.forwardRef` 定义组件时存在。常规函数和 class 组件不接收 `ref` 参数，且 props 中也不存在 `ref`。

Ref 转发不仅限于 DOM 组件，你也可以转发 refs 到 class 组件实例中。

### 2、在 DevTools 中显示自定义名称

`React.forwardRef` 接受一个渲染函数。React DevTools 使用该函数来决定为 ref 转发组件显示的内容。

例如，以下组件将在 DevTools 中显示为 “*ForwardRef*”：



```jsx
const WrappedComponent = React.forwardRef((props, ref) => {
  return <LogProps {...props} forwardedRef={ref} />;
});
```



如果你命名了渲染函数，DevTools 也将包含其名称（例如 “*ForwardRef(myFunction)*”）：



```jsx
const WrappedComponent = React.forwardRef(
  function myFunction(props, ref) {
    return <LogProps {...props} forwardedRef={ref} />;
  }
);
```

# 五、Fragments

 React 中的一个常见模式是一个组件返回多个元素。Fragments 允许你将子列表分组，而无需向 DOM 添加额外节点。 

### 1、动机

一种常见模式是组件返回一个子元素列表。以此 React 代码片段为例：

```jsx
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}
```

`` 需要返回多个 ` 元素以使渲染的 HTML 有效。如果在 `` 的 `render()` 中使用了父 div，则生成的 HTML 将无效。

```jsx
class Columns extends React.Component {
  render() {
    return (
      <div>
        <td>Hello</td>
        <td>World</td>
      </div>
    );
  }
}
```

得到一个 `` 输出：

```jsx
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>
```

Fragments 解决了这个问题。

```jsx
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

这样可以正确的输出 ``：

```jsx
<table>
  <tr>
    <td>Hello</td>
    <td>World</td>
  </tr>
</table>
```

### 2、短语法

你可以使用一种新的，且更简短的语法来声明 Fragments。它看起来像空标签：

```jsx
class Columns extends React.Component {
  render() {
    return (
      <>
        <td>Hello</td>
        <td>World</td>
      </>
    );
  }
}
```

你可以像使用任何其他元素一样使用 `<> `，除了它**不支持 key 或属性。**

#  六、高阶组件

高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。

具体而言，**高阶组件是参数为组件，返回值为新组件的函数。**

```react
function withKaikeba (Comp) {
  const name = '高阶组件';
    
  //  返回一个新组件
  return class extends React.Component {
    componentDidMount () {
      console.log('do something');
    }
    render () {
      return (
        <Comp { ...this.props } name = { name }></Comp>
      )
    }
  }
}

//  原始组件
function Kaikeba (props) {
  return (
    <div>{ props.stage } —— { props.name }</div>
  )
}

//  通过高阶组件withKaikeba的修饰，可以得到一个扩展组件
const NewKaikeba = withKaikeba(Kaikeba);
```



### `antd`的form表单组件简单封装

```react
import React from 'react';

// 创建一个高阶组件：扩展现有表单，事件处理、数据收集、校验
function kFromCreate(Comp) {
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.state = {}

      // 输入项的配置集合
      this.options = {}
    }

    handleChange = (e) => {
      // 从组件中解构出name和value属性
      const { name, value } = e.target;

      this.setState({ [name]: value }, () => {
        // State的更新可能是异步的
        // setState() 的第二个参数为可选的回调函数，
        // 它将在 setState 完成合并并重新渲染组件后执行。
        // 确保值发生变化之后再进行校验
        this.validateField(name);
      })
    }

    // 校验单个字段函数
    validateField = (field) => {
      // 获取校验规则
      const rules = this.options[field].rules;

      // 任意一项失败则返回false
      const ret = !rules.some(rule => {
        if (rule.required) {
          if (!this.state[field]) {
            // 校验失败
            this.setState({
              [field + 'Message']: rule.message
            })
            return true;
          }
        }
      })
      // 如果校验成功，则清空校验错误信息
      if (ret) {
        this.setState({
          [field + 'Message']: ''
        })
      }
      return ret;
    }

    // 校验所有字段
    validate = cb => {
      // 获取所有字段的校验结果
      const rets = Object.keys(this.options).map(field => 
        this.validateField(field)
      )
      
      const ret = rets.every(item => item == true);
      // 通过回调函数返回结果和this.state数据
      cb(ret, this.state)
    }

    // 创建input包装器
    // field为组件的name，option为校验规则
    getFielDec = (field, option) => {
      // 保存当前输入项的配置option
      this.options[field] = option;

      return InputComp => (
        <div>
          {/* InputComp实际上是一个vdom，但是vdom是不可以更改的*/}
          {/* 需要克隆一个新的去改才行,并在上面添加属性和方法 */}
          {React.cloneElement(InputComp, {
            name: field,
            value: this.state[field] || "",
            onChange: this.handleChange
          })}

          {/* 校验的错误信息 */}
          { 
            this.state[field + 'Message'] ? 
            <p style = { {color: 'red', margin: 0} }>
              { this.state[field + 'Message'] }
            </p> : ''
          }
        </div>
      )
    }
    render() {
      // 以属性的形式将getFielDec和validate方法传递给Comp
      return <Comp getFielDec={ this.getFielDec } validate = { this.validate }></Comp>
    }
  }
}

class kFrom extends React.Component {
  onSubmit = () => {
    // 校验所有字段
    this.props.validate((isValid, data) => {
      if (isValid) {
        console.log('校验通过')
        console.log(`登录:${data}`)
      } else {
        alert('校验失败')
      }
    })
  }
  render() {
    const { getFielDec } = this.props
    return (
      <div>
        {getFielDec("uname", {
          rules: [{ required: true, message: '用户名必填' }]
        })(<input />)}

        {getFielDec("password", {
          rules: [{ required: true, message: '密码必填' }]
        })(<input type = 'password'/>)}

        <button onClick = { this.onSubmit }>登录</button>
      </div>
    )
  }
}

const WrappedNormalLoginForm = kFromCreate(kFrom)

export default WrappedNormalLoginForm;
```



# 七、深入`JSX`

实际上，`JSX` 仅仅只是 `React.createElement(component, props, ...children)` 函数的语法糖。如下 `JSX` 代码：

```jsx
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>
```

会编译为：

```React
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)
```

### 1、React 必须在作用域内

由于 JSX 会编译为 `React.createElement` 调用形式，所以 `React` 库也必须包含在 JSX 代码作用域内。

在如下代码中，虽然 `React` 和 `CustomButton` 并没有被直接使用，但还是需要导入：

```jsx
import React from 'react';
import CustomButton from './CustomButton';

function WarningButton() {
  // return React.createElement(CustomButton, {color: 'red'}, null);
  return <CustomButton color="red" />;
}
```

### 2、用户定义的组件必须以大写字母开头

### 3、`JSX` 中的 Props

有多种方式可以在 `JSX` 中指定 props。

* JavaScript 表达式作为 Props

  ```jsx
  <MyComponent foo={1 + 2 + 3 + 4} />
  ```

* 字符串字面量

  ```jsx
  <MyComponent message="hello world" />
  
  <MyComponent message={'hello world'} />
  ```

* Props 默认值为 “True”

  ```jsx
  <MyTextBox autocomplete />
  
  <MyTextBox autocomplete={true} />
  ```

* 属性展开

  ```jsx
  function App1() {
    return <Greeting firstName="Ben" lastName="Hector" />;
  }
  
  function App2() {
    const props = {firstName: 'Ben', lastName: 'Hector'};
    return <Greeting {...props} />;
  }
  ```

# 八、性能优化

### 1、`shouldComponentUpdate` 和`React.PureComponent`的作用

 如果你的组件只有当 `props.color` 或者 `state.count` 的值改变才需要更新时，你可以使用 `shouldComponentUpdate` 来进行检查：

```jsx
class CounterButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

  shouldComponentUpdate(nextProps, nextState) {
    if (this.props.color !== nextProps.color) {
      return true;
    }
    if (this.state.count !== nextState.count) {
      return true;
    }
    return false;
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    );
  }
}
```

 可以使用 `React.PureComponent` 来代替手写 `shouldComponentUpdate`。 

> `shouldComponentUpdate` 和 `React.PureComponent` 只会对数据进行浅层比较。当比较引用类型数据时，只会去判断该引用类型数据的指针是否改变。所以当我们改变引用类型的值时，尽量重新生成一个引用类型数据传递给state，以使组件能够正常渲染

```react
class App extends React.Component {
  constructor (props) {
    super(props);
    this.state = {
      body: ['ljw', 'ojx']
    }
  }
  add = () => {
    this.setState((state) => {
      return {
        // 重新生成一个引用类型数据传递给state,以使Son组件能够正常渲染
        body: [...state.body, 'ymh'] 
      }
    })
  }
  render () {
    return (
      <div>
        <Son body = { this.state.body }></Son>
        <button onClick = { this.add }>add</button>
      </div>
    )
  }
}

class Son extends React.PureComponent {
  render () {
    return(
      <div>{this.props.body.map((item, index) => {
        console.log('render');
        return <div key = { index }>{ item }</div>
      })}</div>
    )
  }
}
```



### 2、不可变数据的力量

 避免该问题最简单的方式是避免更改你正用于 props 或 state 的值。 

```jsx
handleClick() {
  this.setState(state => ({
    words: [...state.words, 'marklar'], //给words赋值一个新数组。
  }));
};
```

 你可以用类似的方式改写代码来避免可变对象的产生。 

### 3、使用不可变数据结构

 [Immutable.js](https://github.com/facebook/immutable-js) 是另一种解决方案。它通过结构共享提供了不可变、持久化集合： 

- *不可变*：一旦创建，一个集合便不能再被修改。
- *持久化*：对集合进行修改，会创建一个新的集合。之前的集合仍然有效。
- *结构共享*：新的集合会尽可能复用之前集合的结构，以最小化拷贝操作来提高性能。

 不可变数据使得追踪变更非常容易。每次变更都会生成一个新的对象使得我们只需要检查对象的引用是否改变。 

[具体使用参考](https://www.jianshu.com/p/0fa8c7456c15)

### 4、React 16.6 之 `React.memo()`（memo也是高阶组件，可以给它传递函数组件）

 `React.memo()` 和 `PureComponent` 很相似，它帮助我们控制何时重新渲染组件。 

`PureComponent` 要依靠 class 才能使用。而 `React.memo()` 可以和 functional component 一起使用。

```jsx
import React from 'react';

const MySnowyComponent = React.memo(function MyComponent(props) {
  // only renders if props have changed!
});

// can also be an es6 arrow function
const OtherSnowy = React.memo(props => {
  return <div>my memoized component</div>;
});

// and even shorter with implicit return
const ImplicitSnowy = React.memo(props => (
  <div>implicit memoized component</div>
));
```

### 5、为什么它被称作 memo？

在维基百科上是这么说的：

> 在计算机领域，记忆化是一种主要用来提升计算机程序速度的优化技术方案。它将开销较大的函数调用的返回结果存储起来，当同样的输入再次发生时，则返回缓存好的数据，以此提升运算效率。

# 九、Refs & DOM

 Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。 

### 1、何时使用 Refs

下面是几个适合使用 refs 的情况：

- 管理焦点，文本选择或媒体播放。
- 触发强制动画。
- 集成第三方 DOM 库。

避免使用 refs 来做任何可以通过声明式实现来完成的事情。

### 2、勿过度使用 Refs

### 3、创建 Refs

 Refs 是使用 `React.createRef()` 创建的，并通过 `ref` 属性附加到 React 元素。 

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

### 4、访问 Refs

当 ref 被传递给 `render` 中的元素时，对该节点的引用可以在 ref 的 `current` 属性中被访问。

```jsx
const node = this.myRef.current;
```

# 十、Render Props（后续更新内容）

 术语 [“render prop”](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce) 是指一种在 React 组件之间使用一个值为函数的 prop 共享代码的简单技术 

```jsx
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)}/>
```

# 十一、使用 `PropTypes` 进行类型检查

 自 React v15.5 起，`React.PropTypes` 已移入另一个包中。请使用 [`prop-types` 库](https://www.npmjs.com/package/prop-types) 代替。 

```jsx
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: PropTypes.string
};
```

[具体用法](https://react.docschina.org/docs/typechecking-with-proptypes.html)

