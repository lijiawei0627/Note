<script type="text/javascript" src="./jquery-3.4.1.js"></script>
<script type="text/javascript" src="./html.js"></script>
<link rel="stylesheet" type="text/css" href="./style.css">

# 一、元素渲染

 与浏览器的 DOM 元素不同，React 元素是创建开销极小的普通对象。React DOM 会负责更新 DOM 来与 React 元素保持一致。 

### 1、将一个元素渲染为DOM

 想要将一个 React 元素渲染到根 DOM 节点中，只需把它们一起传入 `ReactDOM.render()`：

```react
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
```

> 在实践中，大多数 React 应用只会调用一次 `ReactDOM.render()`。 

### 2、react只更新它需要的更新部分

 React DOM 会将元素和它的子元素与它们之前的状态进行比较，并只会进行必要的更新来使 DOM 达到预期的状态。 

### 3、插值表达式注意点

当{}中的值为**空字符串**、**布尔值**、**null**、**undefined**时，在react中元素依然会被展示，只是{}中的内容会被忽略掉

```react
<p>{ null }</p>
```

![image-20191108165211625](C:\Users\14724\AppData\Roaming\Typora\typora-user-images\image-20191108165211625.png)

# 二、组件 & Props

 组件，从概念上类似于 JavaScript 函数。它接受任意的入参（即 “props”），并返回用于描述页面展示内容的 React 元素。 

### 1、函数组件与 class **组件**

定义组件最简单的方式就是编写 JavaScript 函数：

```react
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

同时还可以使用 [ES6 的 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 来定义组件：

```react
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

上述两个组件在 React 里是等效的。

### 2、渲染函数

 React 元素可以是 DOM 标签： 

```react
const element = <div />;
```

也可以是用户自定义的组件：

```react
const element = <Welcome name="Sara" />;
```

 **当 React 元素为用户自定义组件时，它会将 JSX 所接收的属性（attributes）转换为单个对象传递给组件，这个对象被称之为 “props”。 **

```react
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

>**注意：** 组件名称必须以大写字母开头。

> React 会将以小写字母开头的组件视为原生 DOM 标签。

### 3、组合组件

 组件可以在其输出中引用其他组件。这就可以让我们用同一组件来抽象出任意层次的细节。 

### 4、Props 的只读性

 组件无论是使用[函数声明还是通过 class 声明](https://react.docschina.org/docs/components-and-props.html#function-and-class-components)，都决不能修改自身的 props。 

React 非常灵活，但它也有一个严格的规则：

> **所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。**

# 三、State & 生命周期

### 1、将函数组件转换成 class 组件

通过以下五步将 `Clock` 的函数组件转成 class 组件：

1. 创建一个同名的 [ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)，并且继承于 `React.Component`。
2. 添加一个空的 `render()` 方法。
3. 将函数体移动到 `render()` 方法之中。
4. 在 `render()` 方法中使用 `this.props` 替换 `props`。
5. 删除剩余的空函数声明。

```react
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

 使得我们可以使用如 **state** 或**生命周期方法**等很多其他特性。 

### 2、向 class 组件中添加局部的 state

```react
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

### 3、将生命周期方法添加到 Class 中

**生命周期方法：**

* `componentDidMount()` 方法会在组件已经被渲染到 DOM 中后运行 

*   组件从 DOM 中被移除时，React 就会调用 `componentWillUnmount()` 生命周期方法 

### 4、正确地使用 State

**关于 `setState()` 你应该了解三件事： **

* **不要直接修改 State**

  ```react
  // Wrong
  this.state.comment = 'Hello';
  
  应该使用 setState():
  // Correct
  this.setState({comment: 'Hello'});
  ```

  > 构造函数是唯一可以给 `this.state` 赋值的地方。

* **State的更新可能是异步的**

  出于性能考虑，React 可能会把多个 `setState()` 调用合并成一个调用。

  ```react
  this.setState({
      posts: response.posts
  })
  this.setState({
      comments: reponse.comments
  })
  
  // 被合并，一起批量处理。
  this.setState({
    posts: response.posts,
    comments: reponse.comments
  })
  ```

  因为 `this.props` 和 `this.state` 可能会异步更新，所以你不要依赖他们的值来更新下一个状态。

  ```react
  // Wrong
  this.setState({
    counter: this.state.counter + this.props.increment,
  });
  ```

  要解决这个问题，可以让 `setState()` 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数：

  ```react
  // Correct
  this.setState((state, props) => ({
    counter: state.counter + props.increment
  }));
  ```

  > `setState()` 的第二个参数为可选的回调函数，它将在 `setState` 完成合并并重新渲染组件后执行。通常，我们建议使用 `componentDidUpdate()` 来代替此方式。 

  ```javascript
  handleChange () {
    console.log(this.state.city) // 长沙
    this.setState({
      city: '北京'
    }, () => {
      console.log(this.state.city) // 北京
    })
  }
  ```

  

* State 的更新会被合并（批量处理state更新）

   当你调用 `setState()` 的时候，React 会把你提供的对象合并到当前的 state。 

  例如，你的 state 包含几个独立的变量：

  ```
    constructor(props) {
      super(props);
      this.state = {
        posts: [],
        comments: []
      };
    }
  ```

  然后你可以分别调用 `setState()` 来单独地更新它们：

  ```react
    componentDidMount() {
      fetchPosts().then(response => {
        this.setState({
          posts: response.posts
        });
      });
  
      fetchComments().then(response => {
        this.setState({
          comments: response.comments
        });
      });
    }
  ```

  这里的合并是浅合并，所以 `this.setState({comments})` 完整保留了 `this.state.posts`， 但是完全替换了 `this.state.comments`。

### 5、数据是向下流动的

 为什么称 state 为局部的或是封装的。因为除了拥有并设置了它的组件，其他组件都无法访问。 但是组件可以选择把它的 state 作为 props 向下传递到它的子组件中。

> 子组件不能直接修改props的内容。

# 四、事件处理

 React 元素的事件处理和 DOM 元素的很相似，但是有一点语法上的不同: 

- React 事件的命名采用小驼峰式（camelCase），而不是纯小写。
- 使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。

例如，传统的 HTML：

```react
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

在 React 中略微不同：

```react
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

### 1、注意点：

* **在 React 中另一个不同点是你不能通过返回 `false` 的方式阻止默认行为。你必须显式的使用 `preventDefault` 。 **

*  **class 的方法默认不会[绑定](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) `this`。如果你忘记绑定 `this.handleClick` 并把它传入了 `onClick`，当你调用这个函数的时候 `this` 的值为 `undefined`。 这是因为class中的this指向与实例。可以通过下面两种方法解决**

```react
// 一、在构造函数中对其进行this绑定
constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 为了在回调中使用 `this`，这个绑定是必不可少的
    this.handleClick = this.handleClick.bind(this);
}
  

// 二使用实验性的 public class fields 语法（箭头函数），你可以使用 class fields 正确的绑定回调函数：
// 此语法确保 `handleClick` 内的 `this` 已被绑定。
// 注意: 这是 *实验性* 语法。
handleClick = () => {
  console.log('this is:', this);
}
```

### 2、向事件处理程序传递参数

 通常我们会为事件处理函数传递额外的参数。  以下两种方式都可以向事件处理函数传递参数： 

```react
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>

<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

 上述两种方式是等价的，分别通过[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)和 [`Function.prototype.bind`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind) 来实现。 

> 切不可使用

```react
<button onClick={this.deleteRow(id, e)}>Delete Row</button>
```

# 五、列表 & Key

在 React 中，把数组转化为[元素](https://react.docschina.org/docs/rendering-elements.html)列表的过程是相似的。

### 1、渲染多个组件

```react
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);
```

### 2、基础列表组件

```react
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

### 3、key

 key 帮助 React 识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个确定的标识。 

 一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串。通常，我们使用来自数据 id 来作为元素的 key： 

```react
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```

> 如果列表项目的顺序可能会变化，我们不建议使用索引来用作 key 值，因为这样做会导致性能变差，还可能引起组件状态的问题。 

### 4、用 key 提取组件

 元素的 key 只有放在就近的数组上下文中才有意义。 

```react
function ListItem(props) {
  // 正确！这里不需要指定 key：
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 正确！key 应该在数组的上下文中被指定
    <ListItem key={number.toString()}
              value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```

### 5、key 只是在兄弟节点之间必须唯一

 数组元素中使用的 key 在其兄弟节点之间应该是独一无二的。然而，它们不需要是全局唯一的。当我们生成两个不同的数组时，我们可以使用相同的 key 值。

# 六、表单

 在 React 里，HTML 表单元素的工作方式和其他的 DOM 元素有些不同，这是因为表单元素通常会保持一些内部的 state。 

```react
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('提交的名字: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          名字:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
```

由于在表单元素上设置了 `value` 属性，因此显示的值将始终为 `this.state.value`，这使得 React 的 state 成为唯一数据源。由于 `handlechange` 在每次按键时都会执行并更新 React 的 state，因此显示的值将随着用户输入而更新。 

# 七、组合 vs 继承

 React 有十分强大的组合模式。我们推荐使用组合而非继承来实现组件间的代码重用。 

### 1、包含关系（类似于vue中的插槽）

 有些组件无法提前知晓它们子组件的具体内容。  我们建议这些组件使用一个特殊的 `children` prop 来将他们的子组件传递到渲染结果中： 

```react
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
```

这使得别的组件可以通过 JSX 嵌套，将任意组件作为子组件传递给它们。

```react
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```

 少数情况下，你可能需要在一个组件中预留出几个“洞”。这种情况下，我们可以不使用 `children`，而是自行约定：将所需内容传入 props，并使用相应的 prop。 

```react
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```

### 2、props.children不仅仅只是jsx（可以是任意的js表达式）

 从本质上来讲， `props.children` 可以是任何的类型，比如数组、函数、对象等等。 

```react
class App extends React.Component {
  render () {
    return (
      <div>
        {
          <Fetcher name = 'getUser'>
            {
              ({name, age}) => (
                <p>{name} - {age}</p>
              )
            }
          </Fetcher>
        }
      </div>
    )
  }
}

const Api = {
  getUser () {
    return {
      name: 'jeery',
      age: 20
    }
  }
}

function Fetcher (props) {
  const user = Api[props.name]();
  return props.children(user)
}
```

### 3、React中实现过滤器功能 （`React.Children.map`）

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

