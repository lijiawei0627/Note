# 一、使用 State Hook

 *Hook* 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。 

```jsx
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 "count" 的 state 变量
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

### 1、Hook 和函数组件

你之前可能把它们叫做“无状态组件”。但现在我们为它们引入了使用 React state 的能力，所以我们更喜欢叫它”函数组件”。

Hook 在 class 内部是**不**起作用的。但你可以使用它们来取代 class 。

### 2、声明 State 变量

### 3、读取 State

 在函数中，我们可以直接用 `count`: 

### 4、更新 State

在 class 中，我们需要调用 `this.setState()` 来更新 `count` 值：

```jsx
  <button onClick={() => this.setState({ count: this.state.count + 1 })}>
    Click me
  </button>
```

在函数中，我们已经有了 `setCount` 和 `count` 变量，所以我们不需要 `this`:

```jsx
  <button onClick={() => setCount(count + 1)}>
    Click me
  </button>
```

# 二、使用 Effect Hook

 *Effect Hook* 可以让你在函数组件中执行副作用操作 

```jsx
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

> 如果你熟悉 React class 的生命周期函数，你可以把 `useEffect` Hook 看做 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合。 它相较于生命周期函数来说，可以编写更加易读易理解的代码。因为我们可以将不同的逻辑拆分到不同的useEffect中。

**在 React 组件中有两种常见副作用操作：需要清除的和不需要清除的。**

### 1、无需清除的 effect

 有时候，我们只想**在 React 更新 DOM 之后运行一些额外的代码。**比如发送网络请求，手动变更 DOM，记录日志，这些都是常见的无需清除的操作。 

- #####  使用 class 的示例

在 React 的 class 组件中，`render` 函数是不应该有任何副作用的。 这就是为什么在 React class 中，我们把副作用操作放到 `componentDidMount` 和 `componentDidUpdate` 函数中。 

- ##### 使用 Hook 的示例

 与 `componentDidMount` 或 `componentDidUpdate` 不同，使用 `useEffect` 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。大多数情况下，effect 不需要同步地执行。 

### 2、需要清除的 effect

 我们研究了如何使用不需要清除的副作用，还有一些副作用是需要清除的。例如**订阅外部数据源**。 

* ##### 使用 Class 的示例

  在 React class 中，你通常会在 `componentDidMount` 中设置订阅，并在 `componentWillUnmount` 中清除它。

* ##### 使用 Hook 的示例

   `useEffect` 的设计是在同一个地方执行。如果你的 effect 返回一个函数，React 将会在执行清除操作时调用它： 

> **为什么要在 effect 中返回一个函数？** 这是 effect 可选的清除机制。每个 effect 都可以返回一个清除函数。如此可以将添加和移除订阅的逻辑放在一起。它们都属于 effect 的一部分。 

> **React 何时清除 effect？** React 会在组件卸载的时候执行清除操作。 

### 3、通过跳过 Effect 进行性能优化

 在某些情况下，每次渲染后都执行清理或者执行 effect 可能会导致性能问题。在 class 组件中，我们可以通过在 `componentDidUpdate` 中添加对 `prevProps` 或 `prevState` 的比较逻辑解决： 

```jsx
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    document.title = `You clicked ${this.state.count} times`;
  }
}
```

这是很常见的需求，所以它被内置到了 `useEffect` 的 Hook API 中。

```react
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新
```

> 未来版本，可能会在构建时自动添加第二个参数。 

> 如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（`[]`）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循依赖数组的工作方式。 

# 三、Hook规则

 Hook 本质就是 JavaScript 函数，但是在使用它时需要遵循两条规则。 

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

### 3、  React 怎么知道哪个 state 对应哪个 `useState` 

 React 靠的是 Hook 调用的顺序。 

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

# 四、自定义Hook（复用组件）

 通过自定义 Hook，可以将组件逻辑提取到可重用的函数中。 （自定义Hook也是一个函数）

示例：该组件用于显示好友的在线状态：

```jsx
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

现在我们假设聊天应用中有一个联系人列表，当用户在线时需要把名字设置为绿色。我们可以把上面类似的逻辑复制并粘贴到 `FriendListItem` 组件中来，但这并不是理想的解决方案：

```jsx
import React, { useState, useEffect } from 'react';

function FriendListItem(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

相反，我们希望在 `FriendStatus` 和 `FriendListItem` 之间共享逻辑。

### 1、提取自定义 Hook

```jsx
import React, { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

### 2、使用自定义 Hook

```jsx
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

```jsx
function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

> 注意：①、**自定义 Hook 必须以 “`use`” 开头** ；②、 **在两个组件中使用相同的 Hook不 会共享 state**  ；③、 每次*调用* 自定义的Hook，它都会获取独立的 state。 

# 五、Hook API 索引

### 1、基础 Hook

#### `useState`

```jsx
const [state, setState] = useState(initialState);
```

返回一个 state，以及更新 state 的函数。

```jsx
setState(newState);

//函数式更新
<button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
```

#### useEffect

```jsx
useEffect(didUpdate);
```

该 Hook 接收一个包含命令式、且可能有副作用代码的函数。

 使用 `useEffect` 完成副作用操作。赋值给 `useEffect` 的函数会在组件渲染到屏幕之后执行。 

默认情况下，effect 将在每轮渲染结束后执行，但你可以选择让它 [在只有某些值改变的时候](https://react.docschina.org/docs/hooks-reference.html#conditionally-firing-an-effect) 才执行。

####  `useContext`

```jsx
const MyContext = createContext({
  name: 'lijiawei'
});
function App () {
  const [obj] = useState({name: 'oujiaxin'})
  return (
      <MyContext.Provider value = { obj }>
        <Son></Son>
      </MyContext.Provider>
  )
}

function Son () {
  const value = useContext(MyContext)
  return (
    <div>{ value.name }</div>
  )
}
```

 接收一个 context 对象（`React.createContext` 的返回值）并返回该 context 的当前值。当前的 context 值由上层组件中距离当前组件最近的  ` <MyContext.Provider> ` 的 `value` prop 决定。 

 当组件上层最近的 ` <MyContext.Provider> ` 更新时，该 Hook 会触发重渲染，并使用最新传递给 `MyContext` provider 的 context `value` 值。 

 调用了 `useContext` 的组件总会在 context 值变化时重新渲染。 

### 2、额外的Hook

#### useReducer

 [`useState`](https://react.docschina.org/docs/hooks-reference.html#usestate) 的替代方案。它接收一个形如 `(state, action) => newState` 的 reducer，并返回当前的 state 以及与其配套的 `dispatch` 方法。 

```jsx
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
    </>
  );
}
```

> React 会确保 `dispatch` 函数的标识是稳定的，并且不会在组件重新渲染时改变。这就是为什么可以安全地从 `useEffect` 或 `useCallback` 的依赖列表中省略 `dispatch`。 

#### `useCallback`

```jsx
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

 把内联回调函数及依赖项数组作为参数传入 `useCallback`，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。 

> `useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`。 

#### `useMemo`（类似于vue中的计算属性）

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

 把“创建”函数和依赖项数组作为参数传入 `useMemo`，它仅会在某个依赖项改变时才重新计算 memoized 值。这种优化有助于避免在每次渲染时都进行高开销的计算。 

 请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 `useEffect` 的适用范畴，而不是 `useMemo`。 

 如果没有提供依赖项数组，`useMemo` 在每次渲染时都会计算新的值。 

[参考文档](https://zhuanlan.zhihu.com/p/66166173)

#### `useRef`

```jsx
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

#### `useImperativeHandle`

 `useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值。 

```jsx
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
FancyInput = forwardRef(FancyInput);
```

#### `useLayoutEffect`

 其函数签名与 `useEffect` 相同，但它会在所有的 DOM 变更之后同步调用 effect。可以使用它来读取 DOM 布局并同步触发重渲染。  在浏览器执行绘制之前，`useLayoutEffect` 内部的更新计划将被同步刷新。 

 尽可能使用标准的 `useEffect` 以避免阻塞视觉更新。 

#### `useDebugValue`

 `useDebugValue` 可用于在 React 开发者工具中显示自定义 hook 的标签。 

```jsx
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  // ...

  // 在开发者工具中的这个 Hook 旁边显示标签
  // e.g. "FriendStatus: Online"
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}
```

