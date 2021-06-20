# 一、阻塞与非阻塞`IO`

## 共享状态并发

`Node`为`JavaScript`引入了一个复杂的概念，这在浏览器端从未有过：**共享状态的并发**。事实上，这种复杂度在像`Apache`与`mod_php`或者`Nginx`与`FastCGI`这样的Web应用开发模型下都从未有过。通俗讲，Node中，你需要对回调函数如何修改内存中的变量（状态）特别小心。

```javascript
var books = [
  'Metamorphosis',
  'Crime and punishment'
];
function serveBooks () {
  // 给客户端返回HTML代码
  var html = '<b>' + books.join('</b><br><b>') + '</b>';
  
  // 恶魔出现了，把状态修改了
  books = [];
  
  return html
}

// Node会将完整的图书列表返回给第一个请求，而第二个请求则返回一个空的图书列表
```

## 事件轮询

`php`

```php
print('Hello');

sleep(5);

print('World');
```

`node`

```javascript
console.log('Hello')
setTimeout(function () {
  console.log('World');
}, 5000);
```

上述两段代码不仅仅是语义上的区别（`Node.js`使用了回调函数），两者区别集中体现在阻塞和非阻塞的区别上。在第一个例子中，**`PHP` sleep()阻塞了线程的执行。当程序进人睡眠时，就什么事情都不做了。而`Node.js`使用了事件轮询，因此这里`setTimeout`是非阻塞的。**

采用了事件轮询意味着什么呢？从本质上来说，Node会先注册事件，随后不停地询问内核这些事件是否已经分发。当事件分发时，对应的回调函数就会被触发，然后继续执行下去。如果没有事件触发，则继续执行其他代码，直到有新事件时，再去执行对应的回调函数。

在`PHP`中，sleep—旦执行，执行会被阻塞一段指定的时间，并且在阻塞时间未达到设定时间前，不会有任何操作，也就是说这是同步的。和阻塞相反，`setTimeout`仅仅只是注册了一个事件，而程序继续执行，所以，这是异步的。

## 单线程的世界

有一点很重要，**Node是单线程的**。在没有第三方模块的帮助下是无法改变这一事实的。

既然执行时只有一个线程，也就是说，当一个函数执行时，同一时间不可能有第二个函数也在执行，那`Node.js`又是**如何做到高并发的呢？**

比如，一台普通的笔记本电脑，用Node书写的简单的服务器就能够处理每秒上千个请求。

Node并不提供真正的并行操作，因为那样需要引人更多的并行执行线程。关键在于，在调用堆栈执行非常快的情况下，同一时刻你无须处理多个请求。这也是为何说**`v8`搭配非阻塞`IO`是最好的组合**：**`v8`执行`JavaScript`速度非常快，而非阻塞`IO`确保了单线程执行时，不会因为有数据库访问或者硬盘访问等操作而导致被挂起。**

**比如说，在我们的应用中，常见的`IO`例子就是从数据库中获取数据。假设我们需要为某个请求响应数据库获取的数据。当请求到达时，调用堆栈中同步代码已经执行完成之后，只剩下数据库调用了。由于该调用是非阻塞的，在告诉Node “当你获取数据库响应时记得通知我"之后，Node就可以继续处理其他事情了。也就是说，Node 可以去处理更多的请求了！**

## 错误处理

首先，很重要的一点，正如本章之前介绍的，Node应用依托在一个拥有大量共享状态的大进程中。

举例来说，在一个HTTP请求中，如果某个回调函数发生了错误，整个进程都会遭殃。

**因为错误未被捕获，若访问`Web`服务器，进程就会崩溃。**

### `uncatchException`处理器

如果添加了`uncatchException`处理器，就不一样了。这个时候，进程不会退出，并且之后的事情都在你的掌控中。

```JavaScript
process.on('uncatchException', function (err) {
  console.error(err);
  process.exit(1); // 手动退出
})
```

### 分发`error`事件

```javascript
var net = require('net');

net.createServer(function (connection) {
  connection.on('error', function () {
    // err是一个错误对象
  })
}).listen(400);
```

Node中，许多像`http、net`这样的原生模块都会分发`error`事件。如果该事件未处理，就会抛出未捕获的异常。

### 回调中的错误对象

除了`uncaughtException和error`事件外，**绝大部分`Node`异步`API`接收的回调函数，第一个参数都是错误对象**或者是`null`：

```javascript
var fs = require(fs);

fs.readFile('/etc/passwd', function (err, data) {
  if (err) return console.error(err);
  console.log(data);
})
```

错误处理中，每一步都很重要，因为它能让你书写更安全的程序，并且不丢失触发错误的上下文信息。

## 堆栈追踪

**在JavaScript中，当错误发生时，在错误信息中可以看到一系列的函数调用，这称为堆栈追踪。**

```javascript
function c () {
  b();
}

function b () {
  a();
}

function a () {
  throw new Error('here')
}

c();
```

运行之后可以看到堆栈追踪信息：

![image-20200318113606283](D:\Note\image\image-20200318113606283.png)

针对上述代码，显示的堆栈追踪信息在上图中，你能清晰地看到导致错误发生的函数调用路径。下面，我们来看一下引人事件轮询后会怎么样：

```javascript
function c () {
  b();
}

function b () {
  a();
}

function a () {
  setTimeout(() => {
    throw new Error('here')
  }, 10);
}

c();
```

![image-20200318113931770](D:\Note\image\image-20200318113931770.png)

执行上面代码时，有用的堆栈信息就全都丢失了。我们都清楚，要捕获一个未来才会执行的函数所抛出的错误是不可能的，这就是为什么在`Node.js`中，每步都要正确进行错误处理的原因了。一旦遗漏，你就会发现发生了错误后很难追踪，因为上下文信息都丢失了。

## 总结

至此，已经明白了**事件轮询、非阻塞`IO`以及`V8`是如何互相配合为开发者提供书写高性能网络应用的能力。**

还学到了`Node`通过单线程的执行环境，提供了极大的简便，不过，也正因如此，当我们书写网络应用时，**要尽可能地避免使用同步`IO`**。除此之外，**该线程中所有的状态都是维护在一个内存空间中的**，因此，我们写程序的时候要格外小心。

也清楚地明白了，**非阻塞`IO`和回调引人了新的调试和错误处理的方式，这种方式与写阻塞式`IO`的程序时是截然不同的。**

# 二、`Node`中的`JavaScript`

## `global`对象

**在浏览器中，全局对象指的就是`window`对象。**在`window`对象上定义的任何内容都可以被全局访问到。比如，`setTimeout`其实就是`window.setTimeout`, `document`其实就是 `window. document`。

**Node中有两个类似但却各自代表着不同含义的对象**，如下所示：

* **`global`：**和`window`—样，任何global对象上的属性都可以被全局访问到。
* **`process`：**所有全局执行上下文中的内容都在`process`对象中。在浏览器中，只有一个`window`对象，在`Node`中，也只有一个`process`对象。举例来说，在浏览器中窗口的名字是`window.name`,类似的，在Node中进程的名字是`process.title`。

## 实用的全局对象

浏览器中有些函数和工具虽然并非语言标准的一部分，但却非常实用，如今，它们已经被人们看作是`JavaScript`的一部分了。它们都是以全局的形式暴露出来的。

举例来说，`setTimeout`并非`ECMAScript`的一部分，但浏览器却仍将其视作重要的特性来实现。事实上，该函数是无法通过纯JavaScript重写的。

另外有些`API`，人们还在讨论是否要加人到语言规范中（处在建议阶段），不过，**`Node. js`为了让编写Node应用效率更高就把它们加进来了。`setImmediate API`就是一个例子，在`Node`中，它的作用和`process .nextTick`相当**

### `process.nextTick`

`process.nextTick`函数可以将一个函数的执行时间规划到下一个事件循环中：

```JavaScript
console.log(1);
process.nextTick(function () {
  console.log(3);
});
console.log(2);
```

把它想象成是`setTimeout(fn, 1)`或者“通过异步的方法在最近的将来调用该函数" ，你就很容易能理解为什么上述例子的输出结果是1，2，3了。

## 模块系统

`JavaScrip`源生态是一个全局的世界。所有如`setTimeout、document`等这样在浏览器端使用的`API`，都是全局定义的。

在`ES6`之前，`JavaScript`语言标准中并未为模块依赖以及模块独立定义专门的`API`。就导致了引入的多个模块可能会出现对全局命名空间的污染及命名冲突的问题。

`Node`摒弃了采用定义一堆全局变量（或者跑很多可能根本就不会用到的代码）的方式，转而引人了一个简单但却强大无比的模块系统，该**模块系统有三个核心的全局对象：`require、module和exports`**

## 绝对模块和相对模块

* 绝对模块是指Node通过在其内部`node_modules`查找到的模块，或者Node内置的如 `fs`这样的模块。

  ```javascript
  let fs = require('fs')
  ```

* 相对模块是指将`require`指向一个相对工作目录中的JavaScript文件。

  ```javascript
  let a = require('./module_a');
  ```

## 暴露`API`

要让模块暴露一个`API`成为`require`调用的返回值，就要依靠`module`和`exports`这两个全局变量。

**在默认情况下，每个模块都会暴露出一个空对象`（module.exports）`。**如果你想要在该对象上添加一些属性和方法，那么简单地使用`exports`即可：

```javascript
//  module_a.js
exports.name = 'john';
exports.data = 'this is some data';

var privateVariable = 5;

exports.getPrivate = function () {
  return privateVariable;
}
```

```javascript
// index.js
var a = require('./module_a');
console.log(a.name);
console.log(a.data);
console.log(a.getPrivate());
```

执行结果为：

![image-20200318124901998](D:\Note\image\image-20200318124901998.png)

**`exports`其实就是对`module .exports`的引用，其在默认情况下是一个对象。要是在该对象上逐个添加属性无法满足你的需求，你还可以彻底重写`module.exports`。**

## 事件

**`Node.js`中的基础`API`之一就是`EventEmitter`。在`Node`中，可以随处进行事件的监听和分发。**

**所有能触发事件的对象都是 `EventEmitter` 类的实例。**

### 示例一

例如，一个简单的 `EventEmitter` 实例，绑定了一个监听器。 `eventEmitter.on()` 用于注册监听器， `eventEmitter.emit()` 用于触发事件。

```JavaScript
const EventEmitter = require('events');
// const a = new EventEmitter();

// 通过继承的方式将API添加到自己的类中
class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();
myEmitter.on('event', () => {
  console.log('event called');
});
myEmitter.emit('event');
```

![image-20200318131038363](D:\Note\image\image-20200318131038363.png)

**事件是`Node`非阻塞设计的重要体现。Node通常不会直接返回数据（因为这样可能会在等待某个资源的时候发生线程阻塞），而是采用分发事件来传递数据的方式。**

### 示例二

以HTTP服务器为例。当请求到达时，Node会调用一个回调函数，这个时候数据可能不会一下子都到达。

```javascript
http.Server(function (req, res) {
  var buf = '';
  req.on('data', () => {
    buf += data;
  });
  req.on('end',() => {
    console.log('数据接收完毕！')
  });
})
```

这是`Node.js`中很常见的例子：**将请求数据内容进行缓冲（`data`事件），等到所有数据都接收完毕后（`end`事件）再对数据进行处理。**

不管是否“所有的数据"都已到达，`Node`为了让你能够尽快知道请求已经到达服务器，都需要分发事件出来。在Node中，事件机制就是一个很好的机制，能够通知你尚未发生但即将要发生的事情。

## buffer

**除了模块之外，`Node`还弥补了语言另外一个不足之处，那就是对二进制数据的处理。**

在引入 [`TypedArray`](http://nodejs.cn/s/oh3CkV) 之前，JavaScript 语言没有用于读取或操作二进制数据流的机制。 `Buffer` 类是作为 `Node.js API` 的一部分引入的，用于在 TCP 流、文件系统操作、以及其他上下文中与八位字节流进行交互。

该功能一部分作用就是可以对数据进行编码转换。比如，你可以创建一幅用`base64`表示的图片，然后将其作为二进制`PNG`图片的形式写人到文件中：

```javascript
const mybuffer = Buffer.alloc(11, 'cGVsbG8gd29ybGQ=', 'base64');
console.log (mybuffer);
require('fs').writeFile('./404.gif', mybuffer, (err, data) => {
  if (err) return new Error(err);
});
```

[博客参考](https://blog.csdn.net/qq_34629352/article/details/88037778)

# 三、`FS API`

> `fs`模块是唯一一个同时提供同步和异步`API`的模块。

[FS API](http://nodejs.cn/api/fs.html#fs_fs_readdir_path_options_callback)

## 理解什么是流（`stream`）

我们已经知道，`console.log`会输出到控制台。事实上，`console.log`内部做了这样的事情：它**在指定的字符串后加上\ n（换行）字符，并将它写到`stdout`流中。**

process全局对象中包含了三个流对象，分别对应三个`UNIX`标准流：

* **`stdin`**：标准输入（可读流）

  `stdin`流默认的状态是暂停的（paused）。通常，执行一个程序，程序会做一些处理，然后退出。不过，有些时候，程序需要一直处在运行状态来接收用户输入的数据。

* **`stdout`**：标准输出（可写流）

* **`stderr`**：标准错误（可写流）

[node.js中的流](https://www.runoob.com/nodejs/nodejs-stream.html)

`steam`对象和`EventEmitter`很像（事实上，前者继承自后者。所有的 Stream 对象都是 `EventEmitter` 的实例）。在`Node`中，你会接触到各种类型流，如TCP套接字、HTTP请求等。简而言之，当涉及持续不断地对数据进行读写时，流就出现了。

其中`fs.createReadStream`方法允许为一个文件创建一个可读的`Stream`对象。

为了更好地理解`stream`的威力，我们来看如下两个例子。

```javascript
fs.readFile('my-file.txt', (err, contents) => {
  // 对文件进行处理
})
```

上述例子中，回调函数必须要等到整个文件读取完毕、载入到`RAM`、可用的情况下才会触发。

而下面这个例子，每次会读取可变大小的内容块，并且每次读取后会触发回调函数：

```javascript
var stream = fs.createReadStream('my-file.txt');
stream.on('data', (chunk) => {
  // 处理文件部分内容
})
stream.on('end', (chunk) => {
  // 文件读取完毕
})
```

为什么这种能力很重要呢？假设有个很大的视频文件需要上传到某个`Web`服务。这时，你无须在读取完整的视频内容后再开始上传，使用`Stream`就可以大大提速上传过程。

## 输入输出

```javascript
const fs = require('fs');

// process.cwd() 方法返回 Node.js 进程的当前工作目录。
fs.readdir(process.cwd(), (err, files) => {
  console.log('');

  if (!files.length) {
    return console.log('No files to show!');
  }
  console.log('Select which file or directory you want to see');
  function file(i) {
    var filename = files[i];
    // fs.stat() 检查文件是否存在
    fs.stat(__dirname + '/' + filename, (err, stat) => {
      // 如果 fs.Stats 对象描述文件系统目录，执行stat.isDirectory则返回 true。
      if (stat.isDirectory()) {
        console.log(filename);
      } else {
        console.log(filename);
      }
      i++;
      if (i === files.length) {
        console.log('');
        // process.stdout.write而不是console.log；这样就无须换行，让用户可以直接在提示语后进行输人
        process.stdout.write('Enter you choice:');
        // 等待用户输入
        process.stdin.resume();
      } else {
        file(i);
      }
    })
  }
  file(0);
})
```

## 用`fs`进行文件操作

