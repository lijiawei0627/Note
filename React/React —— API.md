# 一、React 顶层 API

### 创建 React 元素

 我们建议[使用 JSX](https://zh-hans.reactjs.org/docs/introducing-jsx.html) 来编写你的 UI 组件。每个 JSX 元素都是调用 [`React.createElement()`](https://zh-hans.reactjs.org/docs/react-api.html#createelement) 的语法糖。 

### 转换元素

`React` 提供了几个用于操作元素的 API：

- [`cloneElement()`](https://zh-hans.reactjs.org/docs/react-api.html#cloneelement)
- [`isValidElement()`](https://zh-hans.reactjs.org/docs/react-api.html#isvalidelement)
- [`React.Children`](https://zh-hans.reactjs.org/docs/react-api.html#reactchildren)

### Fragments

 提供了用于减少不必要嵌套的组件。 

### Refs

- [`React.createRef`](https://zh-hans.reactjs.org/docs/react-api.html#reactcreateref)
- [`React.forwardRef`](https://zh-hans.reactjs.org/docs/react-api.html#reactforwardref)

### Suspense

Suspense 使得组件可以“等待”某些操作结束后，再进行渲染。目前，Suspense 仅支持的使用场景是：[通过 `React.lazy` 动态加载组件](https://zh-hans.reactjs.org/docs/code-splitting.html#reactlazy)。它将在未来支持其它使用场景，如数据获取等。

- [`React.lazy`](https://zh-hans.reactjs.org/docs/react-api.html#reactlazy)
- [`React.Suspense`](https://zh-hans.reactjs.org/docs/react-api.html#reactsuspense)

### `React.Component`

 `React.Component` 是使用 [ES6 classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 方式定义 React 组件的基类： 

### `React.PureComponent`

 `React.PureComponent` 与 [`React.Component`](https://react.docschina.org/docs/react-api.html#reactcomponent) 很相似。两者的区别在于 [`React.Component`](https://react.docschina.org/docs/react-api.html#reactcomponent) 并未实现 [`shouldComponentUpdate()`](https://react.docschina.org/docs/react-component.html#shouldcomponentupdate)，而 `React.PureComponent` 中以浅层对比 prop 和 state 的方式来实现了该函数。 

### `React.memo`

 `React.memo` 为[高阶组件](https://react.docschina.org/docs/higher-order-components.html)。它与 [`React.PureComponent`](https://react.docschina.org/docs/react-api.html#reactpurecomponent) 非常相似，但它适用于函数组件，但不适用于 class 组件。 

### `createElement()`

 创建并返回指定类型的新 [React 元素](https://react.docschina.org/docs/rendering-elements.html)。 

 使用 [JSX](https://react.docschina.org/docs/introducing-jsx.html) 编写的代码将会被转换成使用 `React.createElement()` 的形式。如果使用了 JSX 方式，那么一般来说就不需要直接调用 `React.createElement()`。 

### `cloneElement()`

```react
React.cloneElement(
  element,
  [props],
  [...children]
)
```

 以 `element` 元素为样板克隆并返回新的 React 元素。返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果。新的子元素将取代现有的子元素，而来自原始元素的 `key` 和 `ref` 将被保留。 

**用例**：

```react
// 修改children
function RadioGroup (props) {
  return (
    <div>
      { React.Children.map(props.children, child => {
        // child实际上是一个vdom，但是vdom是不可以更改的，需要克隆一个新的去改才行
        // error示例
        // child.props.name = props.name
              
        // 通过cloneElement来解决这个问题      
        // 向每一个child中传递一个name属性
        return React.cloneElement(child, { name: props.name})
      })}
    </div>
  )
}
```

### `React.Children`

`React.Children` 提供了用于处理 `this.props.children` 不透明数据结构的实用方法。

### `React.Children.map`

`React.Children.map`的实现考虑到*props.children*可能是一个数组或一个对象。 

`React.Children.map`可以传递两个参数，第一个参数为props.children，第二个参数为遍历函数。

```react
function Filter (props) {
  return (
    <div>
      {
        //  将props.children传递进去，其中child为props.children的遍历项
        React.Children.map(props.children, child => {
          if (child.type !== props.type) {
            return;
          }
          return child;
        })
      }
    </div>
  )
}

export default function () {
  return (
    <div>
      <Filter type = 'p'>
        <h1>React</h1>
        <p>react</p>
        <h1>vue</h1>
        <p>vue</p>
      </Filter>
    </div>
  )
}
```

### `React.Children.forEach`

```react
React.Children.forEach(children, function[(thisArg)])
```

与 [`React.Children.map()`](https://react.docschina.org/docs/react-api.html#reactchildrenmap) 类似，但它不会返回一个数组。

### `React.createRef`

`React.createRef` 创建一个能够通过 ref 属性附加到 React 元素的 [ref](https://react.docschina.org/docs/refs-and-the-dom.html)。

### `React.forwardRef`

`React.forwardRef` 会创建一个React组件，这个组件能够将其接受的 [ref](https://react.docschina.org/docs/refs-and-the-dom.html) 属性转发到其组件树下的另一个组件中。这种技术并不常见，但在以下两种场景中特别有用：

- [转发 refs 到 DOM 组件](https://react.docschina.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)
- [在高阶组件中转发 refs](https://react.docschina.org/docs/forwarding-refs.html#forwarding-refs-in-higher-order-components)

`React.forwardRef` 接受渲染函数作为参数。React 将使用 `props` 和 `ref` 作为参数来调用此函数。此函数应返回 React 节点。

```react
const FancyButton = React.forwardRef((props, ref) => (  
  <button ref={ref} className="FancyButton">    
    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

### `React.lazy`

`React.lazy()` 允许你定义一个动态加载的组件。这有助于缩减 bundle 的体积，并延迟加载在初次渲染时未用到的组件。

# 二、React.Component

### 组件的生命周期

#### 挂载

当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：

- [**`constructor()`**](https://react.docschina.org/docs/react-component.html#constructor)
- [`static getDerivedStateFromProps()`](https://react.docschina.org/docs/react-component.html#static-getderivedstatefromprops)
- [**`render()`**](https://react.docschina.org/docs/react-component.html#render)
- [**`componentDidMount()`**](https://react.docschina.org/docs/react-component.html#componentdidmount)

> [`componentWillMount()`](https://react.docschina.org/docs/react-component.html#unsafe_componentwillmount) 将被抛弃使用， 在新代码中应该[避免使用它们](https://react.docschina.org/blog/2018/03/27/update-on-async-rendering.html) 

#### 更新

当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：

- [`static getDerivedStateFromProps()`](https://react.docschina.org/docs/react-component.html#static-getderivedstatefromprops)
- [`shouldComponentUpdate()`](https://react.docschina.org/docs/react-component.html#shouldcomponentupdate)
- [**`render()`**](https://react.docschina.org/docs/react-component.html#render)
- [`getSnapshotBeforeUpdate()`](https://react.docschina.org/docs/react-component.html#getsnapshotbeforeupdate)
- [**`componentDidUpdate()`**](https://react.docschina.org/docs/react-component.html#componentdidupdate)

>  [`componentWillUpdate()`](https://react.docschina.org/docs/react-component.html#unsafe_componentwillupdate) 和 [`componentWillReceiveProps()`](https://react.docschina.org/docs/react-component.html#unsafe_componentwillreceiveprops)  将被抛弃使用， 在新代码中应该[避免使用它们](https://react.docschina.org/blog/2018/03/27/update-on-async-rendering.html) 

#### 卸载

当组件从 DOM 中移除时会调用如下方法：

- [**`componentWillUnmount()`**](https://react.docschina.org/docs/react-component.html#componentwillunmount)

#### 错误处理

当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：

- [`static getDerivedStateFromError()`](https://react.docschina.org/docs/react-component.html#static-getderivedstatefromerror)
- [`componentDidCatch()`](https://react.docschina.org/docs/react-component.html#componentdidcatch)

### 不常用的生命周期方法

#### `shouldComponentUpdate()`

```react
shouldComponentUpdate(nextProps, nextState)
```

根据 `shouldComponentUpdate()` 的返回值，判断 React 组件的输出是否受当前 state 或 props 更改的影响。默认行为是 state 每次发生变化组件都会重新渲染。

> **考虑使用内置的 [`PureComponent`](https://react.docschina.org/docs/react-api.html#reactpurecomponent) 组件**，而不是手动编写 `shouldComponentUpdate()`。  不建议在 `shouldComponentUpdate()` 中进行深层比较或使用 `JSON.stringify()`。这样非常影响效率，且会损害性能。 

#### `static getDerivedStateFromProps()`

```
static getDerivedStateFromProps(props, state)
```

`getDerivedStateFromProps` 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。

#### `getSnapshotBeforeUpdate()`

```
getSnapshotBeforeUpdate(prevProps, prevState)
```

`getSnapshotBeforeUpdate()` 在最近一次渲染输出（提交到 DOM 节点）之前调用。

#### `static getDerivedStateFromError()`

```
static getDerivedStateFromError(error)
```

此生命周期会在后代组件抛出错误后被调用。 它将抛出的错误作为参数，并返回一个值以更新 state

#### `componentDidCatch()`

```
componentDidCatch(error, info)
```

此生命周期在后代组件抛出错误后被调用。 它接收两个参数：

1. `error` —— 抛出的错误。
2. `info` —— 带有 `componentStack` key 的对象，其中包含[有关组件引发错误的栈信息](https://react.docschina.org/docs/error-boundaries.html#component-stack-traces)。

### 过时的生命周期方法

* #### `componentWillMount`

* #### `componentWillReceiveProps`

* #### `componentWillUpdate`

# 三、DOM 元素

 在 React 中，所有的 DOM 特性和属性（包括事件处理）都应该是小驼峰命名的方式。 

### checked

 当 `` 组件的 type 类型为 `checkbox` 或 `radio` 时，组件支持 `checked` 属性。 

### className

`className` 属性用于指定 CSS 的 class

### dangerouslySetInnerHTML（类似于vue中的v-html）

`dangerouslySetInnerHTML` 是 React 为浏览器 DOM 提供 `innerHTML` 的替换方案。 当你想设置 `dangerouslySetInnerHTML` 时，需要向其传递包含 key 为 `__html` 的对象，以此来警示你。 

```react
function createMarkup() {
  return {__html: 'First &middot; Second'};
}

function MyComponent() {
  return <div dangerouslySetInnerHTML={createMarkup()} />;
}

<div dangerouslySetInnerHTML={{ __html: '<div>123</div>' }} /> 
```

### onChange

`onChange` 事件与预期行为一致：每当表单字段变化时，该事件都会被触发。

### selected

`` 组件支持 `selected` 属性。

### style

 在文档中，部分例子为了方便，直接使用了 `style`，但是**通常不推荐将 `style` 属性作为设置元素样式的主要方式**。在多数情况下，应使用 [`className`](https://react.docschina.org/docs/dom-elements.html#classname) 属性来引用外部 CSS 样式表中定义的 class。 

```jsx
// Result style: '10px'
<div style={{ height: 10 }}>
  Hello World!
</div>

// Result style: '10%'
<div style={{ height: '10%' }}>
  Hello World!
</div>
```

# 四、支持的事件

### 剪贴板事件

```
onCopy onCut onPaste
```

### 复合事件

```
onCompositionEnd onCompositionStart onCompositionUpdate
```

### 键盘事件

```
onKeyDown onKeyPress onKeyUp
```

### 焦点事件

```
onFocus onBlur
```

### 表单事件

```
onClick onContextMenu onDoubleClick onDrag onDragEnd onDragEnter onDragExit
onDragLeave onDragOver onDragStart onDrop onMouseDown onMouseEnter onMouseLeave
onMouseMove onMouseOut onMouseOver onMouseUp
```

### 其他事件

[具体参考](https://react.docschina.org/docs/events.html)

