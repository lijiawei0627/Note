# 一、`let`和`const`
## 暂时性死区：
只要块级作用域内存在`let`或`const`命令，它所声明的变量就绑定了这个区域，不再受外部影响。这个区块对这些命令声明的变量形成了封闭的作用域，总之在代码块内，使用`let`或`const`命令声明变量前，该变量都是不可用的。

>暂时性死区本质是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量

**示例：**该示例在给函数形参默认赋值时，函数形参会存在于全局环境和`foo`函数内部环境之间。当在函数形参中未声明a变量便使用a变量时，程序会报错；

```javascript
var a = 10;
function foo (b = a, a = a) {
  console.log(a) //ReferenceError: Cannot access 'a' before initialization at foo
}

foo();
```
改成：
```JavaScript
      var a = 10;
      function foo (b = a, c = b) {
        console.log(c) // 10
      }

      foo();
```
## 顶层对象属性

在全局环境中，用`let`、`const`和`class`声明的变量不属于顶层对象属性，而var和function声明的全局变量时顶层对象(window或global)的属性。

## 不允许重复声明

`let`不允许在相同作用域内，重复声明同一个变量。

```javascript
// 报错
function func() {
  let a = 10;
  var a = 1;
}

// 报错
function func() {
  let a = 10;
  let a = 1;
}
```

因此，不能在函数内部重新声明参数。

```javascript
function func(arg) {
  let arg;
}
func() // 报错

function func(arg) {
  {
    let arg;
  }
}
func() // 不报错
```

## 块级作用域 

`let`实际上为 JavaScript 新增了块级作用域。

```javascript
function f1() {
  let n = 5;
  if (true) {
    let n = 10;
  }
  console.log(n); // 5
}
```

上面的函数有两个代码块，都声明了变量`n`，运行后输出 5。这表示外层代码块不受内层代码块的影响。如果两次都使用`var`定义变量`n`，最后输出的值才是 10（var会进行变量提升）。

## `const`声明必须要有显示的初始化。

`const`常量不是对这个值本身的限制，而是对赋值的那个变量的限制。换句话说，这个值并没有因为`const`被锁定或者不可变，只是赋值本身不可变（**内存地址不得改动**）。比如对象或者数组，其内容仍可修改。

```javascript
    {
        const a = [1, 2, 3];
        a.push(4);
        console.log(a) //  [1, 2, 3, 4]
        
        a = 42;        //  TypeError
    }
```

## for循环
for循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。（也不属于全局作用域）。
* 同时当for循环头部使用let声明时，每次迭代都会重新声明并初始化。
>如果每 一轮循环的变量i都是重新声明的，那它怎么知道上一轮循环的值，从而计算出本轮循环的值？这是因为 JavaScript 引擎内部会记住上一轮循环的值，初 始化本轮的变量i时，就在上一轮循环的基础上进行计算。
```javascript
     for (let i = 0; i < 10; i++) {
       let i = 20;
       console.log(20); // 20
     }
     console.log(i)// ReferenceError: i is not defined
```

* for循环中使用`const`声明时，程序会报错。看一下第一次迭代就知道了。声明了一个 变量i， 赋值为0。 但这里使用`const`，也就意味着i在声明之后，就不能再改变了。好了，0 < 10,执行循环体，然后加1，报错了，i 不能变化了。一次迭代都没有走完，就报错了，说明，在使用常规for 循环时， `const`  不能用来声明变量。
```javascript
  for (const i = 0; i < 10; i++) {
      console.log(i) // 0
  }
  //  TypeError: Assignment to constant variable.
```
## let和`const`规则

let不允许在相同的作用域内重复声明同一个变量。

```javascript
function foo (str) {
  let str = 'hello, world'; //报错 "Identifier 'str' has already been declared"
}
```
---
# 二、数组的解构赋值
`ES6` 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（`Destructuring`）。

## 基本用法

* ##### 模式匹配
只要等号两边的模式相同，左边的变量就会被赋予对应的值。
```javascript
let [foo, [[bar], baz]] = [1, [[2], 3]];
 foo // 1
 bar // 2 
 baz // 3 
```
* ##### 不完全解构
另一种情况是不完全解构，即等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功。
```javascript
let [x, y] = [1, 2, 3]; 
x // 1 
y // 2 
 
let [a, [b], d] = [1, [2, 3], 4];
 a // 1 
 b // 2 
 d // 4 
```
## 默认值
**数组成员严格等于（===）undefined时，默认值才会生效**

```javascript
let [foo = true] = []; 
foo // true 
 
let [x, y = 'b'] = ['a'];
// x='a', y='b' 

let [x, y = 'b'] = ['a', undefined]; 
// x='a', y='b' 
```
---
# 三、对象的解构赋值
**先找到同名属性，再赋值给对应的变量**

>对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取 到正确的值。
## 赋值规则
**对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。**
```javascript
let { foo: baz } = { foo: "aaa", bar: "bbb" }; 
baz // "aaa" 
foo // error: foo is not defined 
```
上面代码中，`foo`是匹配的模式，`baz`才是变量。真正被赋值的是变量`baz`，而不是模式`foo`。
## 嵌套赋值
**与数组一样，解构也可以用于嵌套结构的对象。**
```javascript
let obj = {}; 
let arr = []; 
 
({ foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true }); 
 
obj // {prop:123} 
arr // [true] 
```
## 默认值
**对象的解构也可以指定默认值。默认值生效的条件是，对象的属性值严格等于undefined。**

## 其他数据结构的解构赋值
* ##### 字符串的解构赋值
字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。（类数组）与使用字符串的length属性同理；（包装类）
```javascript
const [a, b, c, d, e] = 'hello'; 
a // "h" 
b // "e" 
c // "l" 
d // "l" 
e // "o"
```
类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。
```javascript
let {length : len} = 'hello'; len 
// 5 
```
* ##### 数值和布尔值的解构赋值
解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。
```javascript
let {toString: s} = 123; 
s === Number.prototype.toString // true 

let {toString: s} = true; 
s === Boolean.prototype.toString // true
```
上面代码中，数值和布尔值的包装对象都有`toString`属性，因此变量s都能取到值。
* ##### 函数参数的解构赋值
```javascript
function add([x, y]){   
  return x + y; 
} 
 
add([1, 2]); // 3 
```
## 用途
* 交换变量的值
* 从函数返回多个值
```javascript
function example() {   
  return {     
    foo: 1,     
    bar: 2   
  }; 
} 
let { foo, bar } = example(); 
```
* 函数参数的定义
* 提取 `JSON` 数据
* 函数参数的默认值
* 遍历 Map 结构
```javascript
for (let [key, value] of map) {   
  console.log(key + " is " + value); 
} 
```
* 输入模块的指定方法
```javascript
const { SourceMapConsumer, SourceNode } = require("source-map"); 
```
---
# 四、函数的扩展
## 函数参数的默认值
* ##### `ES6` 允许为函数的参数设置默认值，即直接写在参数定义的后面。
```javascript
function log(x, y = 'World') {   
  console.log(x, y); 
} 
```
* ##### 参数变量是默认声明的，所以不能用let或`const`再次声明。
```javascript
function foo(x = 5) {   
  let x = 1; // error   
  const x = 2; // error 
} 
```
* ##### 参数默认值是惰性求值的
```javascript
let x = 99; function foo(p = x + 1) {   
  console.log(p); 
} 
 
foo() // 100 
 
x = 100; 
foo() // 101 
```
上面代码中，参数p的默认值是x + 1。这时，每次调用函数`foo`，都会重新计算x + 1，而不是默认p等于 100。
* ##### 与解构赋值默认值结合使用
```javascript
function foo({x, y = 5}) {   
  console.log(x, y); 
}
foo({x: 1}) // 1 5 
```
## 函数的 length 属性
指定了默认值以后，函数的length属性，将返回没有指定默认值的参数个数。也就是说，指定了默认值后，length属性将失真。
```javascript
(function (a) {}).length // 1 
(function (a = 5) {}).length // 0 
(function (a, b, c = 5) {}).length // 2 
```
## 作用域
**一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域（context）。等到初始化结束，这个作用域就会消失。这种语法行为， 在不设置参数默认值时，是不会出现的。**

* 例1：
```javascript
var x = 1; 
 
function f(x, y = x) {   
  console.log(y); 
} 
 
f(2) // 2
```
上面代码中，参数y的默认值等于变量x。调用函数f时，参数形成一个单独的作用域。在这个作用域里面，默认值变量x指向第一个参数x，而不是全局 变量x，所以输出是2。
* 例2：
```javascript
var x = 1; 
 
function foo(x = x) {   // ... } 
 
foo() // ReferenceError: x is not defined 
```
上面代码中，参数x = x形成一个单独作用域。实际执行的是let x = x，由于暂时性死区的原因，这行代码会报错”x 未定义“。
## name 属性
* 如果将一个具名函数赋值给一个变量，则 `ES5` 和 `ES6` 的name属性都返回这个具名函数原本的名字。
```javascript
const bar = function baz() {}; 
 
// ES5 bar.name // "baz" 
 
// ES6 bar.name // "baz" 
```
## 尾调用优化 && 尾递归优化（只有在严格模式下才有用）
尾调用由于是函数的后一步操作，所以不需要保留外层函数的调用帧，因为调用位置、内部变量等信息都不会再用到了，只要直接用内层函数的调用 帧，取代外层函数的调用帧就可以了。

**目前除了safari浏览器以外，其他浏览器均不支持尾调用优化**

## 蹦床函数
>蹦床函数可以将递归执行转为循环执行
```javascript
function trampoline (f) {  
  while (f && f instanceof Function) {    
    f = f();   
  }   
  return f;
}
```
上面就是蹦床函数的一个实现，它接受一个函数f作为参数。只要f执行后返回一个函数，就继续执行。注意，这里是返回一个函数，然后执行该函数， 而不是函数里面调用函数，这样就避免了递归执行，从而就消除了调用栈过大的问题

---

# 五、箭头函数
## 返回对象

由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错。

```javascript
// 报错 let getTempItem = id => { id: id, name: "Temp" }; 
 
// 不报错 let getTempItem = id => ({ id: id, name: "Temp" }); 
```
## 箭头函数可以与变量解构结合使用。
## 使用注意点

* 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。

* 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。

* 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。
>rest参数：剩余参数语法允许我们将一个不定数量的参数表示为一个数组。
```javascript
function f(a, b, ...theArgs) {
  // ...
}
```

* 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。

## 箭头函数中的this对象

**箭头函数的 this 可以理解成词法作用域，而函数是有`this`的，对象并没有**

```javascript
var name = 'ymh'
function person() {
  this.name = 'jsl'
  let obj = {
    name: 'ljw',
    getName: () => {
      console.log(this)
    }
  }
  return obj
}
let one = new person()
one.getName() // {name: 'jsl'}
let two = person()
two.getName() // Window对象
```

从词法作用域来看，可以看到 `getName` 的父级是 `person` 函数返回的对象 `obj`，但是 **`obj` 对象不是一个函数作用域，没有 `this` 上下文，所以`getName`函数也无法指向 `obj` 对象的上下文**。按照规则，我们继续往上查找 `this`，于是找到了 `person` 函数！**因此`getName`中的this指向`person`函数中的`this`**

那么`person`函数中的`this`又是指向谁呢？

**`new`操作符本身会改变构造函数里面的`this`指向，执行构造函数如果没有`new`，里面的`this`是指向`window`。使用 `new之后`就会指向构造函数的实例**

[对阮一峰《ES6 入门》中箭头函数 this 描述的质疑和探究](https://juejin.im/post/5e9dc9f0e51d45471a1cda81)

```javascript
function outer(){    
  var inner = function(){        
    var obj = {};        
    obj.getVal=()=>{             
      console.log("*******");              
      console.log(this);            
      console.log("*******");        
    }        
    return obj;    
  };    
  return inner; 
}
outer().bind(outer)().getVal(); 
// 通过改变箭头函数上层对象的this指向，来达到修改箭头函数this指向的目的
//输出如下
*******
  ƒ outer(){    
    var inner = function(){        
      var obj = {};        
      obj.getVal=()=>{            
        console.log("*******");            
        console.log(this);            
        console.log("*******");        
      }  
…*******
```

注意this对象的指向是可变的，但是在箭头函数中，它是固定的。**this指向的固定并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外部代码块的this。**正是因为它没有this，所以不能用作构造函数。

```javascript
function foo() {  
  return () => {    
    return () => {      
     return () => {    
       console.log('id:', this.id);     
     };   
   };   
  }; 
} 
 
var f = foo.call({id: 1}); 
 
var t1 = f.call({id: 2})()(); // id: 1 
var t2 = f().call({id: 3})(); // id: 1
var t3 = f()().call({id: 4}); // id: 1
```
上面代码之中，只有一个this，就是函数`foo`的this，所以`t1`、`t2`、`t3`都输出同样的结果。因为所有的内层函数都是箭头函数，都没有自己的 this，它们的this其实都是外层`foo`函数的this。

---
# 六、数组的扩展
## 扩展运算符
扩展运算符（spread）是三个点（...）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。
## 替代数组的 apply 方法
由于扩展运算符可以展开数组，所以不再需要apply方法，将数组转为函数的参数了。
```javascript
// ES5 的写法 
function f(x, y, z) {   // ... } 
var args = [0, 1, 2]; 
f.apply(null, args); 
 
// ES6的写法 
function f(x, y, z) {   // ... } 
let args = [0, 1, 2]; 
f(...args);
```
## 扩展运算符的应用
* 复制数组
```javascript
const a2 = [...a1]; 
```
* 合并数组
```javascript
[...arr1, ...arr2, ...arr3] 
```
* 与解构赋值结合
```JavaScript
[a, ...rest] = list 

// 提取剩余形参到rest中
function foo ({name, ...rest}) {
    console.log(name) // ljw
    console.log(rest) //  {age: 20, gender: 'man'}
}
let obj = {
    name: 'ljw',
    age: 20,
    gender: 'man'
}
foo(obj)
```
* 字符串
```javascript
[...'hello'] 
// [ "h", "e", "l", "l", "o" ] 
```
* 实现了 Iterator 接口的对象
```javascript
let nodeList = document.querySelectorAll('div'); 
let array = [...nodeList]; 
```
## `Array.from()` 
`Array.from`方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 `ES6` 新增的数据结构 Set 和 Map）。

```javascript
let arrayLike = {     
  '0': 'a',     
  '1': 'b',     
  '2': 'c',     
  length: 3 
};
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c'] 
```
## 数组的空位
数组的空位指，数组的某一个位置没有任何值。比如，Array构造函数返回的数组都是空位。
```javascript
Array(3) // [, , ,] 
```
>注意，空位不是undefined，一个位置的值等于undefined，依然是有值的。空位是没有任何值
**`ES5` 对空位的处理，已经很不一致了，大多数情况下会忽略空位。**

* `forEach`(), filter(), every() 和some()都会跳过空位。 
* map()会跳过空位，但会保留这个值 
* join()和`toString`()会将空位视为undefined，而undefined和null会被处理成空字符串。

---

# 七、对象的扩展

> 对象的属性名都是字符串类型

## 属性的简洁表示法
`ES6` 允许直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁。

## 属性名表达式 
方法一是直接用标识符作为属性名，方法二是用表达式作为属性名，这时要将表达式放在方括号之内。
## `Object.is`() 
`ES6` 提出“Same-value equality”（同值相等）算法，用来解决这个问题。`Object.is`就是部署这个算法的新方法。它用来比较两个值是否严格相等，**与 严格比较运算符（===）的行为基本一致。**
**不同之处只有两个**：一是+0不等于-0，二是NaN等于自身。

## `Object.assign`()
`Object.assign`方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。

```javascript
const target = { a: 1 }; 
 
const source1 = { b: 2 }; 
const source2 = { c: 3, b:4 }; 
 
Object.assign(target, source1, source2); 
target // {a:1, b:4, c:3} 
```
`Object.assign`方法的第一个参数是目标对象，后面的参数都是源对象。

>`Object.assign`方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。
## 属性的遍历
* ##### for...in
for...in循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。
* ##### `Object.keys`(obj)
`Object.keys`返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。
* ##### `Object.getOwnPropertyNames`(obj)
`Object.getOwnPropertyNames`返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。
* ##### `Object.getOwnPropertySymbols`(obj)
`Object.getOwnPropertySymbols`返回一个数组，包含对象自身的所有 Symbol 属性的键名。
* ##### `Reflect.ownKeys`(obj)
`Reflect.ownKeys`返回一个数组，包含对象自身的所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。
##  `__proto__`属性，`Object.setPrototypeOf`()，`Object.getPrototypeOf`()
>最好不要使用`__proto__`属性，而是使用下面的`Object.setPrototypeOf`()（写操作）、 `Object.getPrototypeOf`()（读操作）、`Object.create`()（生成操作）代替。
## `Object.setPrototypeOf`()
`Object.setPrototypeOf`方法的作用与`__proto__`相同，用来设置一个对象的prototype对象，返回参数对象本身。它是 `ES6` 正式推荐的设置原型对象 的方法。

```javascript
// 格式 
Object.setPrototypeOf(object, prototype) 
```
## `Object.getPrototypeOf`()
该方法与`Object.setPrototypeOf`方法配套，用于读取一个对象的原型对象。
```javascript
function Rectangle() {   // ... } 
 
const rec = new Rectangle(); 
 
Object.getPrototypeOf(rec) === Rectangle.prototype // true 
```
## 对象的扩展运算符 
```javascript
let z = { a: 3, b: 4 }; 
let n = { ...z }; 
n // { a: 3, b: 4 }
```
---
# 八、Symbol
Symbol 值通过Symbol函数生成。类似于字符串的数据类型，独一无二的。
>在Symbol出现之前，对象的键值只能是字符串，如果试图使用非字符串值作为对象的键，那么该值将被强制转换为字符串。

>注意，Symbol函数前不能使用new命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。也就是说，由于 Symbol 值不是对 象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。

>注意，Symbol函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的Symbol函数的返回值是不相等的。
## 作为属性名
由于每一个 Symbol 值都是不相等的，这意味着 Symbol 值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。这对于一个对象由多个 模块构成的情况非常有用，能防止某一个键被不小心改写或覆盖。
>注意，Symbol 值作为对象属性名时，不能用点运算符。
```javascript
const mySymbol = Symbol(); 
const a = {}; 
 
a.mySymbol = 'Hello!'; 
a[mySymbol] // undefined 
a['mySymbol'] // "Hello!" 
```
上面代码中，因为点运算符后面总是字符串，所以不会读取`mySymbol`作为标识名所指代的那个值，导致a的属性名实际上是一个字符串，而不是一个 Symbol 值。
>Symbol作为属性名。该属性不会出现在for...in ，for...of循环中，也不会被`Object.keys`()和`Object.getOwnPropertyNames`()返回。但是它不是私有属性，有一个方法`Object.getOwnPropertySymbols`()可以获取指定对象的所有Symbol属性名。（也可以通过`Reflect.ownKeys`()方法来返回对象所有的属
## Symbol函数的参数

如果Symbol的参数是一个对象，就会调用该对象的`toString`方法，将其转为字符串，然后才生成一个Symbol值。

## `Symbol.for`()和`Symbol.keyFor`()
```javascript
Symbol.for('lijiawei') === Symbol.for('lijiawei')  // true


let symbol1 = Symbol('lijaiwei');
let symbol2 = Symbol.for('lijiawei');
Symbol.keyFor(symbol1);  // undefined
Symbol.keyFor(symbol2);  // lijiawei
```

实例：消除魔术字符串

```javascript
const shapeType = {   triangle: 'Triangle' }; 

function getArea (shape, options) {  
  let area = 0; 
  switch (shape) {  
    case shapeType.triangle:      
      area = .5 * options.width *options.height; break;  
  }  
  return area; 
} 

getArea(shapeType.triangle, { width: 100, height: 100 }); 

```

**魔术字符串指的是，在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值。风格良好的代码，应该尽量消除魔术字符串，改由含义清 晰的变量代替。**
# 九、Set和Map数据结构
## Set

### 基本用法
`ES6` 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。
Set 本身是一个构造函数，用来生成 Set 数据结构。

```javascript
const s = new Set(); 
[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x)); 
 
for (let i of s) {   
  console.log(i); // 2 3 5 4
} 
```
上面代码通过add方法向 Set 结构加入成员，结果表明 Set 结构不会添加重复的值。
* Set 函数可以接受一个数组（或者具有 `iterable` 接口的其他数据结构）作为参数，用来初始化。
```javascript
const set = new Set([1, 2, 3, 4, 4]); 
```
## `WeakSet`（成员只能是对象，null除外）
`**WeakSet` 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别：**

* 首先，`WeakSet` 的成员只能是对象，而不能是其他类型的值。
* `WeakSet` 中的对象都是弱引用，即垃圾回收机制不考虑 `WeakSet` 对该对象的引用。
## Map
类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以 当作键。
>Map的键实际上是和内存地址绑定的，只要内存地址不一样，就视为两个键。
## `WeakMap`
`**WeakMap`与Map的区别有两点：**

* 首先，`WeakMap`只接受对象作为键名（null除外），不接受其他类型的值作为键名。
* 其次，`WeakMap`的键名所指向的对象，不计入垃圾回收机制。
>注意，`WeakMap` 弱引用的只是键名，而不是键值。键值依然是正常引用。
# 十、Proxy
`ES6` 原生提供 Proxy 构造函数，用来生成 Proxy 实例。

```javascript
var proxy = new Proxy(target, handler); 
```
>注意，要使得Proxy起作用，必须针对Proxy实例（上例是proxy对象）进行操作，而不是针对目标对象（上例是空对象）进行操作。如果handler没有设置任何拦截，那就等同于直接通向原对象。
## 拦截器
同一个拦截器函数，可以设置拦截多个操作。
### Proxy 支持的拦截操作：
* get(target, `propKey`, receiver)：拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。 
* set(target, `propKey`, value, receiver)：拦截对象属性的设置，比如`proxy.foo` = v或p`roxy['foo']` = v，返回一个布尔值。 
* has(target, `propKey`)：拦截`propKey` in proxy的操作，返回一个布尔值。 
>值得注意的是，has方法拦截的是`HasProperty`操作，而不是`HasOwnProperty`操作，即has方法不判断一个属性是对象自身的属性，还是继承的属性。
* `deleteProperty`(target, `propKey`)：拦截delete proxy[`propKey`]的操作，返回一个布尔值。 
* `ownKeys`(target)：拦截`Object.getOwnPropertyNames`(proxy)、`Object.getOwnPropertySymbols`(proxy)、`Object.keys`(proxy)，返回一 个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys`()的返回结果仅包括目标对象自身的可遍历属性。 
* `getOwnPropertyDescriptor`(target, `propKey`)：拦截`Object.getOwnPropertyDescriptor`(proxy, `propKey`)，返回属性的描述对象。 
* `defineProperty`(target, `propKey`, `propDesc`)：拦截`Object.defineProperty`(proxy, `propKey`, `propDesc`） 、 `Object.defineProperties`(proxy, `propDescs`)，返回一个布尔值。 
* `preventExtensions`(target)：拦截`Object.preventExtensions`(proxy)，返回一个布尔值。 
* `getPrototypeOf`(target)：拦截`Object.getPrototypeOf`(proxy)，返回一个对象。 
* `isExtensible`(target)：拦截`Object.isExtensible`(proxy)，返回一个布尔值。 
* `setPrototypeOf`(target, `proto`)：拦截`Object.setPrototypeOf`(proxy, `proto`)，返回一个布尔值。如果目标对象是函数，那么还有两种额 外操作可以拦截。 
* apply(target, object, `args`)：拦截 Proxy 实例作为函数调用的操作，比如proxy(...`args`)、`proxy.call`(object, ...`args`)、 `proxy.apply`(...)。 
* `construct(target, args)`：拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...`args`)。
## this问题
虽然 Proxy 可以代理针对目标对象的访问，但它不是目标对象的透明代理，即不做任何拦截的情况下，也无法保证与目标对象的行为一致。主要原因就是 在 Proxy 代理的情况下，目标对象内部的this关键字会指向 Proxy 代理。
```javascript
const target = {   
  m: function () {     
    console.log(this === proxy);   
  } 
}; 
const handler = {}; 
 
const proxy = new Proxy(target, handler); 
 
target.m() // false 
proxy.m()  // true
```
上面代码中，一旦proxy代理`target.m`，后者内部的this就是
# 十一、Reflect（等待后续更新内容）
Reflect对象与Proxy对象一样，也是 `ES6` 为了操作对象而提供的新 `API`。
# 十二、Promise对象
## 特点：
* 对象的状态不受外界影响。Promise对象代表一个异步操作，有三种状态：pending（进行中）、fulfilled（已成功）和rejected（已失败）。 
* Promise对象的状态改变，只有两种可能：从pending变为fulfilled和从pending 变为rejected。只要这两种情况发生，状态就凝固了，不会再变了
>Promise也有一些缺点。首先，无法取消Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise内部抛出的错误， 不会反应到外部。第三，当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。
##2、基本用法
Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。

Promise实例生成以后，可以用then方法分别指定resolved状态和rejected状态的回调函数。
```javascript
const promise = new Promise(function(resolve, reject) {   
  // ... some code 
 
  if (/* 异步操作成功 */){     
    resolve(value);   
  } else {     
    reject(error);   
  } 
}); 
promise.then(function(value) {   
  // success 
}, function(error) {   
  // failure 
});
```
>then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行。
##  `Promise.prototype.then`() 
Promise 实例具有then方法，也就是说，then方法是定义在原型对象`Promise.prototype`上的。它的作用是为 Promise 实例添加状态改变时的回调函数。
## `Promise.prototype.catch`() 
`Promise.prototype.catch`方法是.then(null, rejection)的别名，用于指定发生错误时的回调函数。

>一般总是建议，Promise 对象后面要跟catch方法，这样可以处理 Promise 内部发生的错误。catch方法返回的还是一个 Promise 对象，因此后面还可 以接着调用then方法。

**建议 总是使用catch方法，而不使用then方法的第二个参数。**
## `Promise.all`()
`Promise.all`方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

```javascript
const p = Promise.all([p1, p2, p3]); 
```
p的状态由`p1`、`p2`、`p3`决定，分成两种情况。
* 只有`p1`、`p2`、`p3`的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
* 只要`p1`、`p2`、`p3`之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。
>注意，如果作为参数的 Promise 实例，自己定义了catch方法，那么它一旦被rejected，并不会触发`Promise.all`()的catch方法。
##  `Promise.race`() 
`Promise.race`方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。

```javascript
const p = Promise.all([p1, p2, p3]); 
```
**只要`p1`、`p2`、`p3`之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。**

# 十三、Iterator 和 for...of 循环

**原生JavaScript对象没有遍历接口**

## Iterator（遍历器）的概念 
它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成 遍历操作（即依次处理该数据结构的所有成员）。

**实现Iterator功能：**
```javascript
   function makeIterator (array) {
     var nextIndex = 0;   
     return {     
       next: function() {       
         return nextIndex < array.length ? 
           {value: array[nextIndex++], done:false} :  
           {value: undefined, done: true};     
       }   
     }; 
   }
   
   var it = makeIterator(['a', 'b']); 
  
   it.next() // { value: "a", done: false }

   it.next() // { value: "b", done: false }

   it.next() // { value: undefined, done: true } 
```
## 作用
**Iterator 的作用有三个：**
* 一是为各种数据结构，提供一个统一的、简便的访问接口；
* 二是使得数据结构的成员能够按某种次序排列；
* 三是 `ES6` 创造了一种 新的遍历命令for...of循环，Iterator 接口主要供for...of消费
## 默认 Iterator 接口
默认的Iterator接口部署在数据结构的`Symbol.iterator`属性。或者说一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”。

**例如：**
```javascript
  let arr = ['a', 'b', 'c'];
  let iter = arr[Symbol.iterator]();
  
  iter.next() // {value: 'a', done: false}
  iter.next() // {value: 'b', done: false}
  iter.next() // {value: 'c', done: false}
  iter.next() // {value: undefined, done: true}
```
## 原生具备 Iterator 接口的数据结构如下：
* Array 
* Map 
* Set 
* String 
* `TypedArray` 
* 函数的 arguments 对象 
* `NodeList` 对象
## for...of 循环 
一个数据结构只要部署了`Symbol.iterator`属性，就被视为具有 iterator 接口，就可以用for...of循环遍历它的成员。也就是说，for...of循环内部调 用的是数据结构的`Symbol.iterator`方法。
### 适用范围：
* 数组 
* Set  
* Map 
* 某些类似数组的对象（比如arguments对象、DOM `NodeList` 对象）
* Generator 对象
* 字符串
### 优点：
* 有着同for...in一样的简洁语法，但是没有for...in那些缺点。 
* 不同于`forEach`方法，它可以与break、continue和return配合使用。 
* 提供了遍历所有数据结构的统一操作接口。

>for...in循环有几个缺点:
>* 数组的键名是数字，但是for...in循环是以字符串作为键名“0”、“1”、“2”等等。 
>* for...in循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。 
>* 某些情况下，for...in循环会以任意顺序遍历键名。
# 十四、Generator

`function*`这种声明方式（`function`关键字后面跟一个星号）会定义一个生成器函数（`Generator function`），调用`Generator`返回一个遍历器对象（`Iterator Object`），代表`Generator`函数的内部指针。

## 形式上

Generator函数是一个普通函数，但是有两个特征。一是，`function`关键字与函数名之间有一个星号；二是，函数体内部使用yield表达式， 定义不同的内部状态（yield在英语里的意思就是“产出”）。

```JavaScript
function* helloWorldGenerator() {   
  yield 'hello';   
  yield 'world';   
  return 'ending'; 
} 
 
var hw = helloWorldGenerator(); 

// 该函数有三个状态：hello，world 和 return 语句（结束执行）。
```

```javascript
hw.next() // { value: 'hello', done: false } 
 
hw.next() // { value: 'world', done: false } 
 
hw.next() // { value: 'ending', done: true } 
 
hw.next() // { value: undefined, done: true }
```

## yield表达式

由于 `Generator` 函数返回的遍历器对象，只有调用next方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。yield表达式就是暂停标志。

遍历器对象的next方法的运行逻辑如下。
（1）遇到yield表达式，就暂停执行后面的操作，并将紧跟在yield后面的那个表达式的值，作为返回的对象的value属性值。
（2）下一次调用next方法时，再继续往下执行，直到遇到下一个yield表达式。
（3）如果没有再遇到新的yield表达式，就一直运行到函数结束，直到return语句为止，并将return语句后面的表达式的值，作为返回的对象的value 属性值。
（4）如果该函数没有return语句，则返回的对象的value属性值为undefined。

> `yield`表达式与`return`语句既有相似之处，也有区别。相似之处在于，都能返回紧跟在语句后面的那个表达式的值。区别在于每次遇到`yield`，函数暂停 执行，下一次再从该位置继续向后执行，而`return`语句不具备位置记忆的功能。一个函数里面，只能**执行一次**（或者说一个）`return`语句，但是可以**执行多次**（或者说多个）yield表达式。正常函数只能返回一个值，因为**只能执行一次return**；Generator 函数可以返回一系列的值，因为**可以有任意多 个yield。**

## next方法的参数

**yield表达式本身没有返回值，或者说总是返回undefined。next方法可以带一个参数，该参数就会被当作上一个yield表达式的返回值。** 

```JavaScript
function* Foo () {
  const result = yield 1 + 2;
  console.log(result); // 我是被传进来的值1(如果不在next中传递参数进来，那么这里将会是undefined)
  return 'ending';
}

let foo = Foo();
console.log(foo.next()); // {value: 3, done: false}
console.log(foo.next("我是被传进来的值1"));// {value: "ending", done: true}
```

> 注意，由于next方法的参数表示上一个yield表达式的返回值，所以在第一次使用next方法时，传递参数是无效的。`V8` 引擎直接忽略第一次使用next 方法时的参数，只有从第二次使用next方法开始，参数才是有效的。从语义上讲，第一个next方法用来启动遍历器对象，所以不用带有参数。

这个功能有很重要的语法意义。`Generator` 函数从暂停状态到恢复运行，它的上下文状态（`context`）是不变的。通过`next`方法的参数，就有办法在 `Generator` 函数开始运行之后，继续向函数体内部注入值。也就是说，可以在 `Generator` 函数运行的不同阶段，从外部向内部注入不同的值，从而调整函数行为。

## for ... of 循环

**for...of循环可以自动遍历 Generator 函数时生成的Iterator对象，且此时不再需要调用next方法。**

```javascript
function* foo () {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for (let v of foo()) {
  console.log(v);
}
// 1, 2, 3, 4, 5
```

> 一旦next方法的返回对象的done属性为true，`for...of`循环就会终止，且不包含该返回对象。所以上面的return语句返回的6也不包括在for...of循环中

## yield* 表达式

如果在`Generator`函数内部调用另外一个`Generator`函数，默认情况下是没有效果的。可以使用yield* 来调用

```JavaScript
function* foo () {
  yield 'a';
  yield 'b';
}

function* bar () {
  yield 'x';
  yield* foo();
  yield 'y';
}

// 等同于
function* bar () {
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}
```

**如果被代理的Generator函数有return语句，那么便可以向代理它的Generator函数返回数据。**

```javascript
function* foo () {
  yield 2;
  yield 3;
  return "foo";
}

function* bar () {
  yield 1;
  var v = yield* foo();
  console.log(`v: ${v}`);
}

var it = bar();

console.log(it.next()); // {value: 1, done: false}
console.log(it.next()); // {value: 2, done: false}
console.log(it.next()); // {value: 3, done: false}
console.log(it.next()); 
// "v: foo"
// {value: undefined, done: true}
```

**从语法角度看，如果yield表达式后面跟的是一个遍历器对象，需要在yield表达式后面加上星号，表明它返回的是一个遍历器对象。这被称为yield*表 达式。**

```javascript
let delegatedIterator = (function* () { 
  yield 'Hello!'; 
  yield 'Bye!'; 
}());

let delegatingIterator = (function* () { 
  yield 'Greetings!'; 
  yield* delegatedIterator; 
  yield 'Ok, bye.'; 
}());

for (let value of delegatingIterator) { 
  console.log(value); 
} 
// "Greetings! // "Hello!" // "Bye!" // "Ok, bye." 
```

**实际上任何数据结构只要有`Iterator`接口，就可以被`yield*`遍历**

```javascript
let read = (function* () {
  yield 'hello';
  yield* 'hello';
})();

read.next().value; // "hello"
read.next().value; // "h"
```

## `Generator` 函数的this

`Generator` 函数总是返回一个遍历器，`ES6` 规定这个遍历器是 `Generator` 函数的实例，也继承了 `Generator` 函数的`prototype`对象上的方法。

```javascript
function* g () {}

g.prototype.hello = function () {
  return 'hi'
}
let obj = g();
obj instanceof g  // true
obj.hello(); // 'hi'
```

上面代码表明，`Generator` 函数`g`返回的遍历器`obj`，是`g`的实例，而且继承了`g.prototype`。但是，**如果把`g`当作普通的构造函数，并不会生效，因为 `g`返回的总是遍历器对象，而不是`this`对象。**

```javascript
function* g () {
  this.a = 11;
}

let obj = g();
obj.a // undefined
```

**Generator 函数g在this对象上面添加了一个属性a，但是obj对象拿不到这个属性。**

**`Generator`函数也不能跟`new`命令一起使用，否则会报错。因为`Generator`函数不能作为构造函数**

## `Generator`与状态机

```javascript
var ticking = true;
var clock = function () {
  if (ticking) {
    console.log('Tick');
  } else {
    console.log('Tock');
  }
  ticking = !ticking;
}
```

使用`Generator`实现

```javascript
var clock = function* () {
  while (true) {
    console.log('Tick');
    yield;
    console.log('Tock');
    yield;
  }
}
```

## 应用

### 异步操作的同步化

`Generator` 函数的暂停执行的效果，意味着可以把异步操作写在`yield`表达式里面，等到调用`next`方法时再往后执行。这实际上等同于不需要写回调函 数了，因为异步操作的后续操作可以放在yield表达式下面，反正要等到调用`next`方法时再执行。所以，`Generator` 函数的一个重要实际意义就是用来 处理异步操作，改写回调函数。

```javascript
function* loadUI () {
  showLoadingScreen();
  yield loadUIDataAsynchronously();
  hideLoadingScreen();
}
var loader = loaderUI();
// 加载UI
loader.next()

// 卸载UI
loader.next();
```

**Ajax 是典型的异步操作，通过 Generator 函数部署 Ajax 操作，可以用同步的方式表达。**

```javascript
function request(url) {
    // this is where we're hiding the asynchronicity,
    // away from the main code of our generator
    // it.next() 是generators的迭代器
    makeAjaxCall(url, function(response) {
        it.next(response);
    });
}

function *main() {
    var result1 = yield request('http://some.url.1');
    var data = JSON.parse(result1);
    
    var result2 = yield request('http://some.url.2/?id='+data.id);
    var resp = JSON.parse(result2);
    console.log('The value you asked for: '+ resp.value);
}

var it = main();
it.next(); //启动所有请求

```

### 控制流管理

### 部署Iterator接口

## Generator函数的异步应用

## 基于Promise对象的自动执行

```javascript
var fs = require('fs'); 
 
var readFile = function (fileName){   
  return new Promise(function (resolve, reject){     
    fs.readFile(fileName, function(error, data){       
      if (error) return reject(error);       
      resolve(data);     
    });   
  }); 
}; 
 
var gen = function* (){   
  var f1 = yield readFile('/etc/fstab');   
  var f2 = yield readFile('/etc/shells');   
  console.log(f1.toString());
  console.log(f2.toString()); 
}


var g = gen(); 
 
g.next().value.then(function(data){   
  g.next(data).value.then(function(data){     
    g.next(data);   
  }); 
}); 
手动执行其实就是用then方法，层层添加回调函数。理解了这一点，就可以写出一个自动执行器。

```

## co模块

co 模块是著名程序员 `TJ Holowaychuk` 于 2013 年 6 月发布的一个小工具，用于 Generator 函数的自动执行。

### 用法

下面是一个 Generator 函数，用于依次读取两个文件。

```javascript
var gen = function* () {   
  var f1 = yield readFile('/etc/fstab');   
  var f2 = yield readFile('/etc/shells');   
  console.log(f1.toString());   
  console.log(f2.toString()); 
}; 
```

co 模块可以让你不用编写 Generator 函数的执行器。

```JavaScript
var co = require('co'); 
co(gen); 
```

上面代码中，Generator 函数只要传入co函数，就会自动执行。

### 简单原理

将异步操作包装成 Promise 对象，用then方法交回执行权。

```JavaScript
function next (data) {
  let { value, done } = it.next(data); // 启动
  if (!done) {
    value.then(num => {
      next(num);
    })
  }
}
```

上面代码中，Generator 函数只要传入co函数，就会自动执行。

# 十五、`async`函数

 ## 语法糖

**`async`函数是Generator函数的语法糖，它使得异步操作变得更加方便。**

## `async`函数对Generator函数的改进

1)、内置执行器

2)、更好的语义

**`async`表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果**

3)、更广的适用性

**await命令后面可以是Promise对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。**

4)、返回值是Promise

**`async`函数的返回值是Promise对象，这比Generator函数的返回值是Iterator对象方便多了。你可以用then方法指定下一步的操作。**

**进一步说，`async`函数完全可以看作多个异步操作，包装成的一个Promise对象，而await命令就是内部then命令的语法糖。**

## 多种使用形式：
```javascript
// 函数声明
async function foo() {}
// 函数表达式
const foo = async function () {};
// 对象的方法
let obj = { async foo() {} };
obj.foo().then(...)
// Class 的方法
class Storage {
  constructor() {
    this.cachePromise = caches.open('avatars');
  }
  async getAvatar(name) {
    const cache = await this.cachePromise;
    return cache.match(`/avatars/${name}.jpg`);
  }
}
const storage = new Storage();
storage.getAvatar('jake').then(…);
// 箭头函数
const foo = async () => {};
```

## 语法

### (1)、返回Promise对象
`async`函数返回一个Promise对象。

`async`函数内部return语句返回的值，会成为then方法回调函数的参数。

```javascript
async function f() {
  return 'hello world';
}
f().then(v => console.log(v)) // "hello world"
```
### (2)、Promise对象的状态变化

`async`函数返回的Promise对象，必须等到内部所有await命令后面的Promise对象执行完，才会发生状态改变，除非遇到return语句或者抛出错误。也就是说，**只有`async`函数内部的异步操作执行完，才会执行then方法指定的回调函数。**

### (3)、await命令
正常情况下，await命令后面是一个Promise对象。如果不是，会被转成一个立即resolve的Promise对象。
```javascript
async function f() {
  return await 123;
}
f().then(v => console.log(v))
// 123

async function async1() {
console.log('async1 start');
await async2();
console.log('async1 end');
}

// 等价于

async function async1() {
  console.log('async1 start');
  Promise.resolve(async2()).then(() => {
    console.log('async1 end');
  })
}
```
**只要一个`await`语句后面的 `Promise` 变为`reject`，那么整个`async`函数都会中断执行。**

```javascript
async function f () {
  await Promise.reject('error');
  await Promise.resolve('hello, world'); // 不会执行
}
```

上面代码中，第二个await语句是不会执行的，因为第一个await语句状态变成了reject。

有时，我们希望即使前一个异步操作失败，也不要中断后面的异步操作。**这时可以将第一个await放在try...catch结构里面，这样不管这个异步操作是 否成功，第二个await都会执行。**

```javascript
async function f () {
  try {
    await Promise.reject('出错了');
  } catch (e) {
    
  }
  return await Promise.resolve('hello, world')
}
```

**另一种方法是await后面的 Promise 对象再跟一个catch方法，处理前面可能出现的错误。**

```javascript
async function f () {  
  await Promise.reject('error')
  	.catch(e => console.log(e))
  await Promise.resolve('hello, world'); // 不会执行
}
```

### (4)、错误处理

如果await后面的异步操作出错，那么等同于`async`函数返回的Promise对象被reject

**如果有多个await命令，可以统一放在try...catch结构中。**

```javascript
async function main() {
  try {
    const val1 = await firstStep();
    const val2 = await secondStep(val1);
    const val3 = await thirdStep(val1, val2);
    console.log('Final: ', val3);
  }
  catch (err) {
    console.error(err);
  }
}
```
### (5)、使用注意点

**第一点，await命令后面的Promise对象，运行结果可能是rejected，所以最好把await命令放在try...catch代码块中。**

---

**第二点，多个await命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。**

```javascript
let foo = await getFoo();
let bar = await getBar();
```
上面代码中，`getFoo`和`getBar`是两个独立的异步操作（即互不依赖），被写成继发关系。这样比较耗时，因为只有`getFoo`完成以后，才会执行`getBar`，完全可以让它们同时触发。
```javascript
// 写法一
let [foo, bar] = await Promise.all([getFoo(), getBar()]);
// 写法二
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
```
上面两种写法，`getFoo`和`getBar`都是同时触发，这样就会缩短程序的执行时间。

---

**第三点，await命令只能用在`async`函数之中，如果用在普通函数，就会报错。**

# 十六、Class的基本语法（语法糖）

**`ES6` 的类，完全可以看作构造函数的另一种写法。**

构造函数的`prototype`属性，在 `ES6` 的“类”上面继续存在。事实上，类的所有方法都定义在类的`prototype`属性上面。

```javascript
class Point {
  constructor() {
    // ...
  }

  toString() {
    // ...
  }

  toValue() {
    // ...
  }
}

// 等同于

Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};
```

```javascript
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}

// 等同于

function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};

var p = new Point(1, 2);
```

## 静态方法

**静态方法不会被实例继承，而是直接通过类调用，而父类静态方法可以为子类继承**

## 类的prototype属性

**类所有的方法都被定义在类的prototype属性上,且不可枚举。因此我们可以通过在类的prototype上定义新的方法来达到给类添加方法的效果**

## Point === `Point.prototype.constructor`
```javascript
Point === Point.prototype.constructor // true 
```

## 方法内部this默认指向

**类的方法内部this默认指向类的实例**

```javascript
class Logger { 
  printName(name = 'there') {
    this.print(`Hello ${name}`); 
  } 
  print(text) { 
    console.log(text); 
  }
} 
 
const logger = new Logger(); 
const { printName } = logger; 
printName(); // TypeError: Cannot read property 'print' of undefined 
```
`printName`方法中的this，默认指向Logger类的实例。但是，如果将这个方法提取出来单独使用，this会指向该方法运行时所在的环境，因为找不到print方法而导致报错。

一个比较简单的解决方法是，在构造方法中绑定this，这样就不会找不到print方法了。
```javascript
class Logger {  
  constructor() {  
    this.printName = this.printName.bind(this);
  } 
 
  // ...
}
```

## `constructor`方法

**`onstructor`方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。**

```javascript
class Point { } 
 
// 等同于 

class Point {   constructor() {} }
```

## 私有属性和私有方法

---
# 十七、Class的继承
## super方法

**子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类没有自己的this对象，而是继承父类的this对象，然后对其进行加工。如果不调用super方法，子类就得不到this对象**

```javascript
class Point { /* ... */ } 
 
class ColorPoint extends Point {  
  constructor() {   } 
} 
 
let cp = new ColorPoint();  // ReferenceError 
```

## `Object.getPrototypeOf`()
`Object.getPrototypeOf`方法可以用来从子类上获取父类。

```javascript
Object.getPrototypeOf(ColorPoint) === Point
// true
```
因此，可以使用这个方法判断，一个类是否继承了另一个类

**注：**
子类的`__proto__`指向父类

子类的prototype的`__proto__`指向父类的prototype

## super关键字
super这个关键字，既可以当作函数使用，也可以当作对象使用。在这两种情况下，它的用法完全不同。
第一种情况，super作为函数调用时，代表父类的构造函数。`ES6` 要求，子类的构造函数必须执行一次super函数。

```javascript
class A {}

class B extends A {
  constructor() {
    super();
  }
}
```
上面代码中，子类B的构造函数之中的super()，代表调用父类的构造函数。这是必须的，否则 JavaScript 引擎会报错。

注意，super虽然代表了父类A的构造函数，但是返回的是子类B的实例，即super内部的this指的是B，因此super()在这里相当于`A.prototype.constructor.call`(this)。

第二种情况，super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

```javascript
class A {
  p() {
    return 2;
  }
}

class B extends A {
  constructor() {
    super();
    console.log(super.p()); // 2
  }
}

let b = new B();
```
这里需要注意，由于super指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过super调用的。
```javascript
class A {
  constructor() {
    this.p = 2;
  }
}

class B extends A {
  get m() {
    return super.p;
  }
}

let b = new B();
b.m // undefined
```
上面代码中，p是父类A实例的属性，`super.p`就引用不到它。

如果属性定义在父类的原型对象上，super就可以取到。
```javascript
class A {}
A.prototype.x = 2;

class B extends A {
  constructor() {
    super();
    console.log(super.x) // 2
  }
}

let b = new B();
```
如果super作为对象，用在静态方法之中，这时super将指向父类，而不是父类的原型对象。
```javascript
class Parent {
  static myMethod(msg) {
    console.log('static', msg);
  }

  myMethod(msg) {
    console.log('instance', msg);
  }
}

class Child extends Parent {
  static myMethod(msg) {
    super.myMethod(msg);
  }

  myMethod(msg) {
    super.myMethod(msg);
  }
}

Child.myMethod(1); // static 1

var child = new Child();
child.myMethod(2); // instance 2
```

# 十八、`CommonJS`和`AMD/CMD`

## `CommonJS`（同步加载）

 `CommonJS`就是为`JS`的表现来制定规范，`NodeJS`是这种规范的实现，`webpack` 也是以`CommonJS`的形式来书写。因为`js`没有模块的功能所以`CommonJS`应运而生，它希望`js`可以在任何地方运行，不只是浏览器中。它的终极目标是提供一个类似Python，Ruby和Java标准库。

`CommonJS`定义的模块分为:{模块引用(`require`)} {模块定义(`exports`)} {模块标识(`module`)}

`require()`用来引入外部模块；`exports`对象用于导出当前模块的方法或变量，唯一的导出口；`module`对象就代表模块本身。

为了方便，Node为每个模块提供一个exports变量，指向`module.exports`。这等同在每个模块头部，有一行这样的命令。

```JavaScript
var exports = module.exports;
```

于是我们可以直接在 exports 对象上添加方法，表示对外输出的接口，如同在`module.exports`上添加一样。注意，不能直接将exports变量指向一个值，因为这样等于切断了`exports`与`module.exports`的联系。

## `AMD`（异步加载）

基于`commonJS`规范的`nodeJS`出来以后，服务端的模块概念已经形成，很自然地，大家就想要客户端模块。而且最好两者能够兼容，一个模块不用修改，在服务器和浏览器都可以运行。但是，由于一个重大的局限，使得`CommonJS`规范不适用于浏览器环境。还是上面的代码，如果在浏览器中运行，会有一个很大的问题，你能看出来吗？

```javascript
var math = require('math');
math.add(2, 3);
```

第二行`math.add(2, 3)`，在第一行`require('math')`之后运行，因此必须等`math.js`加载完成。也就是说，如果加载时间很长，整个应用就会停在那里等。**注意 `require` 是同步的。**

这对服务器端不是一个问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间。但是，对于浏览器，这却是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于"假死"状态。因此，浏览器端的模块，不能采用"同步加载"（synchronous），只能采用"异步加载"（asynchronous）。这就是AMD规范诞生的背景。

[详情参考](https://www.cnblogs.com/moxiaowohuwei/p/8692359.html)

## `CMD`

大名远扬的玉伯写了`seajs`，就是遵循他提出的`CMD`规范，与`AMD`蛮相近的，不过用起来感觉更加方便些，最重要的是中文版，应有尽有：[seajs官方doc](http://seajs.org/docs/#docs)

```javascript
1 define(function(require,exports,module){...});
```

前面说`AMD`，说`RequireJS`实现了`AMD`，`CMD`看起来与`AMD`好像呀，那`RequireJS`与`SeaJS`像不像呢？

虽然`CMD`与`AMD`蛮像的，但区别还是挺明显的，官方非官方都有阐述和理解，我觉得吧，说的都挺好：