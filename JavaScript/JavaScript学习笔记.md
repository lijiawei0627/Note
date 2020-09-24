# 一、var和let定义变量的for循环执行顺序分别为
```JavaScript
  for (let i = 0; i < 10; i++) {
      console.log(i);
  }
```
**var定义i变量时**：先定义变量i；再判断条件表达式，满足条件时，执行循环体内容，当返回true时，执行i++。然后再判断条件表达式，重复上面操作。

**let定义i变量时**：当前的i只在本轮循环有效，所以每一次循环的i其实都是一个新的变量。如果每 一轮循环的变量i都是重新声明的，那它怎么知道上一轮循环的值，从而计算出本轮循环的值？这是因为 JavaScript 引擎内部会记住上一轮循环的值，初始化本轮的变量i时，就在上一轮循环的基础上进行计算。

---
# 二、函数参数按值传递

## 按值传递

**所有函数的参数都是按值传递的**

> 注：很多人错误的认为：在局部作用域中修改的对象会在全局作用域中反映出来，就说明参数是按引用传递的。
>
> **实际上引用值：**对象变量它里面的值是这个对象在堆内存中的内存地址。因此如果按引用传递，它传递的值也就是这个内存地址。那么`var obj = new Object();`会重新给obj分配一个地址，比如是`0x321`了，那么它就不在指向有`name = 'ljw';`属性的内存单元了。相当于把实参obj和形参obj的地址都改了，那么最终就是输出`ymh`。

```javascript
  function setName (obj) {
      obj.name = 'ljw';
      obj = new Object();
      obj.name = 'ymh';
  }
  let person = new Object();
  setName(person);
  console.log(person.name);   //  'ljw'
```
将person对象按值传递到`setName`函数中，并给obj添加一个name属性，然后又将一个新对象赋给变量obj，并设置了新的name属性，但该新对象为局部对象，在函数执行完毕之后被销毁

## 函数的形参

**函数的形参都是以let的方法声明的**

```JavaScript
var b = 3;
function foo (a = 42, b = a + b + 5) {
    
}
//b = a + b + 5在参数b的TDZ中访问b，所以会报错。
// 而访问a却没问题，因为此时刚好跨出了参数a的TDZ
```
---
# 三、管理内存
**一旦数据不再有用最好通过将其值设置为null来释放内存，此做法叫做解除引用，让其值脱离执行环境，以便垃圾收集器下次运行时将其回收**

> **注**：**解除一个值的引用并不意味着自动回收该值所占用的内存。解除引用的真正作用是让值脱离执行环境，以便下次垃圾收集器运行时回收内存**

---
# 四、变量、作用域、内存小结
## 对象引用

在操作对象时，当复制保存着**对象引用**的某个变量时，操作的实际上是对象的引用。但在为对象添加或者修改属性之类的，操作的是**对象本身**

## 伪块作用域

**伪块作用域**：在for、while、switch、if语句中的使用var声明变量时会将变量添加到当前执行环境中，而let声明变量则不会

```JavaScript
for (var i = 0; i < 10; i++) {}
console.log(i);  // 10



// 在for循环中使用let声明变量时，
//该变量存在于for循环块内部和外部中间的那一块作
//用域中，且每次循环都会重新声明一个i变量
for (let i = 0; i < 10; i++) {
    let i = 0;
    console.log(i); // 0
}
console.log(i)  // ReferenceError: i is not defined

```

## 作用域

**作用域：负责收集并维护所有的标识符（变量）**

> **注：作用域分为词法作用域和动态作用域。JavaScript中作用域是词法作用域（事实上大部分语言都是基于词法作用域的）。**

两者区别：动态作用域并不关心函数和作用域是如何声明以及在何处声明的,只关心它们从何调用。换句话说,作用域链是基于调用栈的,而不是代码中的作用域嵌套因此,如果`Javascript`具有动态作用域,理论,下面代码中的`foo()`在执行时将会输出3。而事实上因为JavaScript是词法作用域，所下面代码会输出2
```JavaScript
     function foo () {
       let i = 3;
       test();
     }
     function test () {
       console.log(i);  // 2
     }
     let i = 2
     foo();
```

4、全局作用域是最简单的作用域，但也是最差的。

尽管使用全局变量很容易，但是它们会被所有加载到页面的脚本所共享。容易导致命名冲突。全局命名空间的污染会导致很多问题，很容易导致不同文件中定义的变量和函数被重写。另外在性能上面也会有损耗。（因为JavaScript的名称解析顺序，是在最近的作用域开始查找变量，并逐层向外扩展）

---
# 五、引用类型小结
1、当通过变量来访为对象属性时，必须使用方括号法

2、数组调用`valueOf`方法会返回数组本身，而调用数组的`toString`方法会返回由数组每个值的字符串形式拼接而成的一个以逗号为分割的字符串

3、JavaScript中函数是一等公民，函数声明的优先级最高

---
# 六、函数

## 函数内部对象

**在函数内部，有两个特殊的对象：`arguments 和 this`**

### arguments

`ECMAScript`中的**参数在内部是用一个数组来表示的**。函数接收到的始终都是这个数组，而不关心数组中包含哪些参数（如果有参数的话）。实际上，在函数体内可以**通过 `arguments` 对象来访问这个参数数组**，从而获取传递给函数的每一个参数。 

**arguments 对象只是与数组类似（它并不是 Array 的实例）**

关于 `arguments` 的行为，还有一点比较有意思。那就是**它的值永远与对应命名参数的值保持同步**。 

```javascript
function doAdd(num1, num2) {     
  arguments[1] = 10;        
  alert(arguments[0] + num2); 
} 
```

执行`doAdd`如果传入两个参数。因为 `arguments` 对象中的值会自动反映到对应的命名参数，所以修改 `arguments[1]`，也就修改了 `num2`，结果它们的 值都会变成 10。不过，这**并不是说读取这两个值会访问相同的内存空间；它们的内存空间是独立的**，但 它们的**值会同步**。另外还要记住，**如果只传入了一个参数，那么为 arguments[1]设置的值不会反应到 命名参数中。**这是因为 **`arguments` 对象的长度是由传入的参数个数决定的，不是由定义函数时的命名 参数的个数决定的。** 

## 函数属性

每个函数都包含两个属性：`length 和 prototype`。

### length

`length` 属性表示函数希望接收的命名参数的个数

```javascript
function sayName(name){     
  alert(name); 
}       
 
function sum(num1, num2){     
  return num1 + num2; 
} 
 
function sayHi(){     
  alert("hi"); 
} 
 
alert(sayName.length);      //1 
alert(sum.length);          //2 
alert(sayHi.length);        //0 
 
```

### prototype

在 `ECMAScript` 核心所定义的全部属性中，耐人寻味的就要数 `prototype` 属性了。对于 `ECMAScript` 中的引用类型而言，**`prototype` 是保存它们所有实例方法的真正所在**。换句话说，诸如 `toString()和 valueOf()`等方法实际上都保存在 `prototype` 名下，只不过是通过各自对象的实例访问罢了。另外在 `ECMAScript 5`中，`prototype` 属性是不可枚举的，因此**使用 `for-in` 无法发现**

# 七、函数、对象、原型

**1、无论什么时候，只要创建一个函数，就会为该函数创建一个prototype属性，在默认情况下，所有原型对象都会自动获得constructor和`__proto__`属性。constructor属性是指向prototype属性所在函数的指针。原型对象默认只会取得constructor属性和`__proto__`属性，其他属性都是从Object继承而来，其call，apply方法均来自`Function.prototype`**

> 对象的`__proto__`和构造函数的`prototype`属性只是指向于同一个对象(引用)。当改变两者任意一个的指针指向，对另外一个变量无影响。

```JavaScript
// 定义普通对象
var obj = {};
obj.__proto__ === Object.prototype // true
obj.constructor === Object
```

![img](http://img.lijiawei0627.xyz/img/239adedfdbece29391d2d7c37b01a7e9.jpg)

**2、每个函数都包含两个属性：length 和 prototype。另外具名函数还拥有name属性。其中，length属性表示函数希望接收的命名参数的个数，name属性返回函数名，且不可修改**

```JavaScript
    let a = function b () { 
      console.log(a.name)
    }
    a(); // 'b'
    a.name = 'hello'
    a(); // 'b'
```
**注：在上面代码中，b的使用只限于函数内部使用，在函数外部使用会报错**

## 属性设置和屏蔽

例1：
```JavaScript
myObject.foo = "bar";
```
如果属性名`foo`既出现在`myObject`中也出现`myObject`的[[Prototype]]链上层么就会发生屏蔽。`myobject`中包含的`foo`属性会屏蔽原型链上层的所有`foo`属性,因为`myObject.foo`总是会选择原型链中最底层的`foo`属性

**例2：**

```JavaScript
   let anotherObject = {
       a: 2
   }
   let myObject = Object.create(anotherObject);
   anotherObject.a;  // 2
   myObject.a; // 2
   myObject.hasOwnProperty("a"); // false
   myObject.a++; //隐式屏蔽！myObject.a = myObject.a+1
   anotherObject.a; // 2
   myObject.a; // 3
   myObject.hasOwnProperty("a"); // true
```
尽管`myObject.a`看起来应该(通过委托)查找并增加`anotherObject.a`属性,但是别忘了+操作相当于`myObject.a=myObject.a+1`,因此操作首先会通过[[prototype]]查找属性a并从`anotherObject.a`获取当前属性值2,然后给这个值加1,接着用[[Put]]将值3赋给`myObject`中新建的屏蔽属性a

## 构造函数

通常使用首字母大写来规范构造函数

### constructor

```JavaScript
   function Foo () {}
   Foo.prototype = {};
   let foo = new Foo();
   foo.constructor === Foo; // false
   foo.constructor === Object; // true
```
Object(...)并没有“构造”`foo`,对吧?看起应该是`Foo()`构造了它,大部分开发者都认为是`Foo()`执行了构造工作,但是问题在于,如果你认为constructor表示“由...构造的话`,foo.constructor`应该是`Foo`,但是它并不是`Foo`到底怎么回事?`foo`并没有,constructor属性,所以它会委托[[Prototype]]链上的prototype,但是这个对象也没有constructor属性(不过默认的`Foo.prototype`对象有这个属性),所以它会继续委托,这次会委托给委托链顶端的`Object.prototype`。这个对象有constructor属性,指向内置的Object(...)函数。(当然你可以在给`Foo.prototype`重新赋值时，添加一个constructor属性，并使该属性值为`Foo`。)

### 对象关联

[[Prototype]]机制就是存在于对象中的一个内部链接，它会引用其他对象。

**7、除了通过`Object.create(null)`创建的对象以外，其他对象都有`__proto__`属性**

```JavaScript
   let obj = Object.create(null);
   console.log(obj.__proto__) // undefined
```

---
# 八、匿名函数
匿名函数的执行环境具有全局性，因此其this对象通常执行window
```JavaScript
(function () {
    console.log(this) //  window
})()
```

---
# 九、cookie
1、`document.cookie`为string类型

2、服务器将set-cookie， `Http`头作为响应的一部分，发送给客户端，客户端在请求中添加cookie发送给服务器，且只有名称和值才会被作为cookie信息的一部分被发送给服务器

---
# 十、call,apply
1、改变函数执行时的this指向，目前所有关于它们的运用，都是基于这一点来进行的。

2、**注：调用call/apply/bind的必须是个函数
call、apply和bind是挂在`Function.prototype`上的三个方法,只有函数才有这些方法。**

```javascript
     function Person (name, age, sex) {
         this.name = name;
         this.age = age;
         this.sex = sex;
     }
     function Student (name, age, sex, id) {
         Person.call(this, name, age, sex);
         this.id = id;
     }
     let student = new Student('ljw', 20, '男', 17690)
     console.log(student)  // {name: "ljw", age: 20, sex: "男", id: 17690}

```

3、如果第一个参数是null，则使用全局对象代替；

---
# 十一、编译原理
## 编译器

负责语法分析和代码生成。

## 引擎

负责整个过程中JavaScript的编译及执行过程。浏览器不同，其引擎也不同，比如Chrome采用的是`v8`，Safari采用的是`SquirrelFish Extreme`。

## 作用域

负责收集并维护所有的标识符（变量）

### 类别

**注：作用域分为词法作用域和动态作用域。JavaScript中作用域是词法作用域（事实上大部分语言都是基于词法作用域的）。**

**两者区别：动态作用域并不关心函数和作用域是如何声明以及在何处声明的,只关心它们从何调用。换句话说,作用域链是基于调用栈的,而不是代码中的作用域嵌套因此,如果`Javascript`具有动态作用域,理论,下面代码中的`foo()`在执行时将会输出3。而事实上因为JavaScript是词法作用域，所下面代码会输出2**

```javascript
     function foo () {
       let i = 3;
       test();
     }
     function test () {
       console.log(i);  // 2
     }
     let i = 2
     foo();
```

## `LHS`查询和`RHS`查询

当变量出现在赋值操作的左侧时进行`LHS`查询，出现在右侧时进行`RHS`查询。讲得更准确一点,`RHS`查询与简单地查找个变量的值别无二致,而`LHS`查询则是试图找到变量的容器本身,从而可以对其赋值。从这个角度说,`RHS`并不是真正意义上的“赋值操作的右侧”,更准确地说**非左侧**

**注：我们习惯将var a = 2；看作一个声明，而实际上JavaScript引擎并不这么认为。它将var a 和a = 2当作两个单独的声明，第一个是编译阶段的任务，而第二个则是执行阶段的任务。**

---
# 十二、this全面解析

**切记函数是有`this`的，而对象并没有**

## this机制

* **this机制跟动态作用域相似。this的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式**

## new操作

**`new`操作符本身会改变构造函数里面的`this`指向，执行构造函数如果没有`new`，里面的`this`是指向`window`。使用 `new之后`就会指向构造函数的实例**

* **使用new来调用函数，或者说发生构造函数调用时，会自动执行下面的操作：**

**①、创建(或者说构造)一个全新的对象**

**②.这个新对象会被执行[Prototype]连接到构造函数的原型**

**③.将构造函数的作用域赋给新对 象（因此 构造函数中的this 就指向了这个新对象）；**

**④.执行构造函数中的代码（为这个新对象添加属性）。如果函数没有显式返回其他对象,那么new表达式中的函数调用会自动返回这个新对象。**

 ```javascript
    function Foo () {
      this.name = 'ymh';
      this.age = 20;
      let person = {
        name: 'lijiawei'
      }
      return person
    }
    let obj = new Foo();
    console.log(obj);  // {name: 'lijiawei'}
 ```
 **注：当函数显示返回一个object类型的对象时，则新对象为返回值。 而且该新对象的`__proto__`默认会指向`Object.prototype`。 **

## 函数调用

* `JS（ES5）`里面有三种函数调用形式：

```JavaScript
func(p1, p2) 
obj.child.method(p1, p2)
func.call(context, p1, p2) // 先不讲 apply
```

* 事实上， 第三种调用形式，才是正常调用形式：

```JavaScript
func.call(context, p1, p2)
```

* 其他两种都是语法糖，可以等价地变为 call 形式： 

```JavaScript
func(p1, p2) 等价于
func.call(undefined, p1, p2)

obj.child.method(p1, p2) 等价于
obj.child.method.call(obj.child, p1, p2)
```

* this，就是上面代码中的 context。就这么简单。

* this 是你 call 一个函数时传的 context，由于你从来不用 call 形式的函数调用，所以你一直不知道。

当你写下面代码时

```JavaScript
function func(){
  console.log(this)
}

func()
```

等价于

```JavaScript
function func(){
  console.log(this)
}

func.call(undefined) // 可以简写为 func.call()
```

按理说打印出来的 this 应该就是 undefined 了吧，但是浏览器里有一条规则：

> 如果你传的 context 就 null 或者 undefined，那么 window 对象就是默认的 context（严格模式下默认 context 是 undefined）

* 例题：

```JavaScript
var obj = {
  foo: function(){
    console.log(this)
  }
}

var bar = obj.foo
obj.foo() // 转换为 obj.foo.call(obj)，this 就是 obj
bar() 
// 转换为 bar.call()
// 由于没有传 context
// 所以 this 就是 undefined
// 最后浏览器给你一个默认的 this —— window 对象
```

[文章链接](https://zhuanlan.zhihu.com/p/23804247)

## 严格模式

* **严格模式下，不能将全局对象用于默认绑定，此时this会绑定到undefined**

---

# 十三、正则表达式

---

# 十四、instance
**一、`instanceof` 操作符的原理：**

左侧对象：A，搜寻他自身的隐式原型。`__proto__`属性

右侧对象：B，则是返回他的prototype属性

**经典例题：**

```javascript
    var tmp = {};
    var A = function() {};
    A.prototype = tmp;
    
    var a = new A();
    // 在用A new出了一个对象a后，
    //改写了A的原型对象prototype
    //会导致a无法再在A的原型对象上进行查找 
    A.prototype = {}; 
    
    // 这是一个创建对象的方法，
    //b的隐式原型（__proto__）指向tmp 
    var b = Object.create(tmp);
    
    // 将A的构造函数（Function）赋给b，
    //我理解其实这句话没什么影响，
    //构造函数和原型函数本质上是有很大区别的。。。
    b.constructor = A.constructor;
    
    // 因为改写过A的原型对象，
    //因此a不再是A的实例，a.__proto__ != A.prototype 
    console.log(a instanceof A);   // false
    
    // 只是具有相同的构造函数，
    //并不符合instanceof 判断为true的条件
    console.log(b instanceof A);    // false
```

---

# 十五、`forEach`
## 参数

`forEach`可以传递两个参数，其中第一个参数为必需，第二个参数可选。传递给函数的值一般用 "this" 值。如果这个参数为空，"undefined" 会传递给 "this" 值。

```javascript
   let arr = [1，2，3];
   arr.forEach() //Uncaught TypeError: undefined is not a function at Array.forEach
```

## 空数组使用`forEach`方法

当一个空数组使用`forEach`方法时，传递的函数不会执行。

例:
```javascript
   let arr = new Array(10000);
   arr.forEach((item) => {
       console.log(item); //无输出
   })
   //  undefined
```

## 空位

`forEach`，`filter`，`every`，`some`会跳过空位，map会跳过空位，但是会保留这个值。

例如：
```javascript
    let array = [,1,,2,,3];
    array = array.map((i) => ++i)
    console.log(array) //[,2,,3,,4]
```
---

# 十六、属性类型 

## 属性类别

`ECMAScript`中有两种属性：数据属性和访问器属性。 

##  数据属性

**数据属性包含一个数据值的位置。在这个位置可以读取和写入值。数据属性有4个描述其行为的 特性。** 

## 修改属性默认特性

修改属性默认的特性，必须使用`ECMAScript5`的 `Object.defineProperty()`方法。这个方法接收三个参数：属性所在的对象、属性的名字和一个描述符对象。其中，描述符（descriptor）对象的属性必须是：configurable、enumerable、writable和value。设置其中的一或多个值，可以修改对应的特性值。

## 访问器属性（使用`es6`中的Proxy替代）

访问器属性不包含数据值；它们包含一对儿 getter和 setter函数（不过，这两个函数都不是必需的）。 在读取访问器属性时，会调用getter函数，这个函数负责返回有效的值；在写入访问器属性时，会调用setter函数并传入新值，这个函数负责决定如何处理数据。

**注：`Object.freeze()`可以通过将该属性设置为false来阻止对象状态的改变，让我们冻结对象，但是它不能被用于冻结嵌套对象属性**

---

# 十七、基本概念

## 1、表达式和语句：

1. 表达式可以嵌套在别的表达式中，而语句不行。语句只能独立出现。
2. 语句没有返回值，但是它有结果值。

---

# 十八、隐式类型转换

## 转换规则：
* 引用值转换为布尔值都为true

* 除了空字符串，其他字符串转换为布尔值都是true

```javascript
Boolean('') //  false
Boolean(' ')  // true
```

* 在转换不同的数据类型时，相等和不相等操作符遵循下列基本规则：
  * 如果有一个操作数是布尔值，则在比较相等性之前先将其转换为数值——false 转换为 0，而 true 转换为 1；
  * 如果一个操作数是字符串，另一个操作数是数值，在比较相等性之前先将字符串转换为数值； 
  *  如果一个操作数是对象，另一个操作数不是，则调用对象的 `valueOf()`方法，用得到的基本类 型值按照前面的规则进行比较；
  * 这两个操作符在进行比较时则要遵循下列规则。 

## 比较规则

在进行比较时则要遵循下列规则

* null 和 undefined 是相等的。
* 要比较相等性之前，不能将 null 和 undefined 转换成其他任何值。 
* 如果有一个操作数是 `NaN`，则相等操作符返回 false，而不相等操作符返回 true。重要提示： 即使两个操作数都是 `NaN`，相等操作符也返回 false；因为按照规则，`NaN` 不等于 `NaN`。 
* 如果两个操作数都是对象，则比较它们是不是同一个对象。如果两个操作数都指向同一个对象， 则相等操作符返回 true；否则，返回 false。 

## 实例

```JavaScript
[]+{}   结果是 "[object Object]"

{}+[]   结果是 0

// false => 0  [].toString() => "" => 0
false == []; 

// 要比较相等性之前，不能将 null 和 undefined 转换成其他任何值。
false == null;  // false

//[] => '' => 0 == ![] => false =》0
[] = ！[]  // true

//a => 42   b => '42' => 42
var a = [42];
var b = ['42'];
a < b;

//a => '42'  b => '043'
//比较双方都为字符串，则按字母顺序来比较
var a = ['42'];
var b= ['043'];
a < b; false;

// alert中的参数为对象时，会直接调用该对象的toString方法
let obj = {
  valueOf () {
    return 'hello'
  },
  toString () {
    return 'world'
  }
}
alert(obj) // world
```
**解析：**

1. []+{}会分别隐式转换为空字符串和"[object Object]"。因此结果为"[object Object]"
2. 在 {} + [] 中，{} 被解析为空的 block，随后的 + 被解析为正号运算符。即实际上成了：
   { // empty block }
   +[]

# 十九、原型链

大部分对象自身都会带有一个`__proto__`和`constructor`属性(除了create(null)创建的空对象和`Object.prototype`以外)，其中constructor继承自该对象构造函数。两者只是指向于同一个对象(引用)。当改变两者任意一个的指针指向，对另外一个变量无影响。

![img](http://img.lijiawei0627.xyz/img/239adedfdbece29391d2d7c37b01a7e9-1577265170741.jpg)

## `instanceof`

**`instanceof` 操作符的原理：**

左侧对象：A，搜寻他自身的隐式原型`__proto__`属性

右侧对象：B，则是返回他的prototype属性

只有用来判断引用类型时，才能有效，当用来判断基本类型时，只会返回`false`

```javascript
let str = 'hello';
str instanceof String // false

function foo () {}
foo instanceof Function // true
```

> 注意`true.__proto__ = Boolean.prototype为true`
> `true、'str'、1这些基本数据类型之所以有__proto__属性`，完全是因为会生成包装类。
>
> 例如：当我们将`true.__proto__设置为{}`时，`true.__proto__ = Boolean.prototype仍然为true`。另外记住`true instanceof Boolean 为false`
> 其他的基本数据类型同理

## `__proto__`

注意：

* 函数的`__proto__`属性指向`Function.prototype`（其他类型的`__proto__`属性也是一样）

  ```javascript
  function foo () {}
  foo.__proto__ === Function.prototype // true
  
  let Foo = new Function()
  typeof Foo // 'function'
  Foo.__proto__ === Function.prototype // true
  
  let str = 'hello'
  str.__proto__ === String.prototype // true
  ```

* 大部分对象自身都会带有`__proto__`属性，该属性指向于该对象的构造函数的原型

## `constructor`

创建一 个函数，就会同时创建它的 prototype 对象，这个对象也会自动获得 constructor 属性

重写构造函数默认的 prototype 对象，因此 constructor 属性也就变成了新 对象的 constructor 属性（指向 Object 构造函数），不再指向 原来的构造函数，而是指向Object。此时，尽管 instanceof 操作符还能返回正确的结果，但通过 constructor 已经无法确定对象的类型了

```JavaScript
function Foo () {}
Foo.constructor === Function // true

let foo = new Foo()
foo.constructor === Foo // true

Object.constructor === Function // true
typeof Object === 'function' // true
```

# 二十、`toString`（）

每个JavaScript数据类型的原型上都会带有一个`toString`方法。当进行字符串转换时，会进行调用。其中需要注意`Array.prototype.toString`和`Object.prototype.toString`方法

## `Array.prototype.toString`

该方法会将数组拆开，然后转化为字符串类型。类似于`arr.join()`效果

```javascript
[1,2].toString()  // '1,2'
```

## `Object.prototype.toString`

可以使用该方法来判断生成对象的基本数据类型

```javascript
let arr = [1, 3,2]
let obj = {}
console.log(Object.prototype.toString.call(arr)) // [object Array]
console.log(Object.prototype.toString.call(obj)) // [object Object]
```

> 注意`toString`方法的指向。默认情况下指向于`Object.prototype`，该对象调用`toString`方法会转化为[object Object]

# 二十一、数组方法

> JavaScript数组中会修改原数组的方法：`splice、reverse、fill、copyWithin、sort、push、pop、unshift、shift`

## 修改数组

* splice()

splice() 方法可删除从 index 处开始的零个或多个元素，并且用参数列表中声明的一个或多个值来替换那些被删除的元素。如果从 `arrayObject` 中删除了元素，则返回的是含有被删除的元素的数组。splice() 方法会直接对数组进行修改。

```javascript
var a = [5,6,7,8];
console.log(a.splice(1,0,9)); //[]
console.log(a);  // [5, 9, 6, 7, 8]
var b = [5,6,7,8];
console.log(b.splice(1,2,3));  //[6, 7]
console.log(b); //[5, 3, 8]
```

* reverse()

reverse() 方法用于颠倒数组中元素的顺序。返回的是颠倒后的数组，会改变原数组。

```javascript
var arr = [2,3,4];
console.log(arr.reverse()); //[4, 3, 2]
console.log(arr);  //[4, 3, 2]
```

* `fill()`

  用新元素替换掉数组内的元素，可以指定替换下标范围。

  ```javascript
  arr.fill(value, start, end)
  ```


## 不修改数组

* slice()

返回一个新的数组，包含从 start 到 end （不包括该元素）的 `arrayObject` 中的元素。返回选定的元素，该方法不会修改原数组。

```javascript
var arr = [2,3,4,5];
console.log(arr.slice(1,3));  //[3,4]
console.log(arr);  //[2,3,4,5]
```

* `from`

  将类似数组的对象（array-like object）和可遍历（`iterable`）的对象转为真正的数组

  ```javascript
  const bar = ["a", "b", "c"];
  Array.from(bar);
  // ["a", "b", "c"]
  
  Array.from('foo');
  // ["f", "o", "o"]
  ```


## 数组迭代

* **`indexOf` 和 `lastIndexOf`**

都接受两个参数：查找的值、查找起始位置
不存在，返回 -1 ；存在，返回位置。`indexOf` 是从前往后查找， `lastIndexOf` 是从后往前查找。
**`indexOf`**

```javascript
var a = [2, 9, 9];
a.indexOf(2); // 0
a.indexOf(7); // -1

if (a.indexOf(7) === -1) {
  // element doesn't exist in array
}
```

* **`lastIndexOf`**

```javascript
var numbers = [2, 5, 9, 2];
numbers.lastIndexOf(2);     // 3
numbers.lastIndexOf(7);     // -1
numbers.lastIndexOf(2, 3);  // 3
numbers.lastIndexOf(2, 2);  // 0
numbers.lastIndexOf(2, -2); // 0
numbers.lastIndexOf(2, -1); // 3
```

* `every`

对数组的每一项都运行给定的函数，每一项都返回 `ture`,则返回 true

```javascript
function isBigEnough(element, index, array) {
  return element < 10;
}    
[2, 5, 8, 3, 4].every(isBigEnough);   // true
```

* `some`

对数组的每一项都运行给定的函数，任意一项都返回 `ture`,则返回 `true`

```javascript
function compare(element, index, array) {
  return element > 10;
}    
[2, 5, 8, 1, 4].some(compare);  // false
[12, 5, 8, 1, 4].some(compare); // true 
```

* `filter`

对数组的每一项都运行给定的函数，返回 结果为 `ture` 的项组成的数组

```javascript
var words = ["spray", "limit", "elite", "exuberant", "destruction", "present", "happy"];

var longWords = words.filter(function(word){
  return word.length > 6;
});
// Filtered array longWords is ["exuberant", "destruction", "present"]
```

* `map`

对数组的每一项都运行给定的函数，返回每次函数调用的结果组成一个新数组

```javascript
var numbers = [1, 5, 10, 15];
var doubles = numbers.map(function(x) {
   return x * 2;
});
// doubles is now [2, 10, 20, 30]
// numbers is still [1, 5, 10, 15]
```

* `find()`

传入一个回调函数，找到数组中符合当前搜索规则的第一个元素，返回它，并且终止搜索。

```javascript
const arr = [1, "2", 3, 3, "2"]
console.log(arr.find(n => typeof n === "number")) // 1
```

* `findIndex`()

传入一个回调函数，找到数组中符合当前搜索规则的第一个元素，返回它的下标，终止搜索。

```javascript
const arr = [1, "2", 3, 3, "2"]
console.log(arr.findIndex(n => typeof n === "number")) // 0
```



* of

用于将一组值，转换为数组。这个方法的主要目的，是弥补数组构造函数 Array() 的不足。因为参数个数的不同，会导致 Array() 的行为有差异。

```javascript
Array() // []
Array(3) // [, , ,]
Array(3, 11, 8) // [3, 11, 8]
Array.of(7);       // [7]
Array.of(1, 2, 3); // [1, 2, 3]

Array(7);          // [ , , , , , , ]
Array(1, 2, 3);    // [1, 2, 3] 
```

* `entries() 返回迭代器：返回键值对`

```JavaScript
//数组
const arr = ['a', 'b', 'c'];
for(let v of arr.entries()) {
  console.log(v)
}
// [0, 'a'] [1, 'b'] [2, 'c']

//Set
const arr = new Set(['a', 'b', 'c']);
for(let v of arr.entries()) {
  console.log(v)
}
// ['a', 'a'] ['b', 'b'] ['c', 'c']

//Map
const arr = new Map();
arr.set('a', 'a');
arr.set('b', 'b');
for(let v of arr.entries()) {
  console.log(v)
}
// ['a', 'a'] ['b', 'b']
```

* `values() 返回迭代器：返回键值对的value`

```javascript
//数组
const arr = ['a', 'b', 'c'];
for(let v of arr.values()) {
  console.log(v)
}
//'a' 'b' 'c'

//Set
const arr = new Set(['a', 'b', 'c']);
for(let v of arr.values()) {
  console.log(v)
}
// 'a' 'b' 'c'

//Map
const arr = new Map();
arr.set('a', 'a');
arr.set('b', 'b');
for(let v of arr.values()) {
  console.log(v)
}
```

 

* `keys() 返回迭代器：返回键值对的key`

```javascript
//数组
const arr = ['a', 'b', 'c'];
for(let v of arr.keys()) {
  console.log(v)
}
// 0 1 2

//Set
const arr = new Set(['a', 'b', 'c']);
for(let v of arr.keys()) {
  console.log(v)
}
// 'a' 'b' 'c'

//Map
const arr = new Map();
arr.set('a', 'a');
arr.set('b', 'b');
for(let v of arr.keys()) {
  console.log(v)
}
// 'a' 'b'
```

* `includes`

判断数组中是否存在该元素，参数：查找的值、起始位置，可以替换 `ES5` 时代的 `indexOf` 判断方式。`indexOf` 判断元素是否为 `NaN`，会判断错误。

```javascript
var a = [1, 2, 3];
a.includes(2); // true
a.includes(4); // false
```

# 二十二、正则表达式

匹配特殊字符或有特殊搭配原则的字符的最佳选择

## 创建方法

```javascript
// 一、直接量
let reg = /abce/m;

// 二、new RegExp
let reg = new RegExp("abc", "m");

// 三、
let reg = /abce/m;
// 通过new操作会返回一个新的正则表达式对象
let reg1 = new RegExp(reg);
// 不使用new，会返回参数reg的一个引用
let reg2 = RegExp(reg);
reg2 === reg // true
```

## 修饰符

* `i`

  忽略大小写

* `g`

  全局匹配

* `m`

  多行匹配

```javascript
let reg = /^a/g; // 全局匹配以a开头
let str = "abcde\na";
console.log(str.match(reg)) // ["a"];

let reg1 = /^a/gm; // 全局匹配多行以a开头
console.log(str.match(reg)) // ["a", "a"];
```

## 表达式

* `[]`

```javascript
// 一个方括号代表一位,方括号里面表示取值范围。全局匹配有连续三个数字的情况
let reg = /[0123456789][0123456789][0123456789]/g 
let reg = /[0-9][0-9][0-9]/g
let str = "12309u98723zudiajwdak";
console.log(str.match(reg)) //["123", "987"]
```

* `[^abc]`查找任何不在方括号之间的字符

```javascript
let reg = /[^5-9][^5-9]/g
let str = '125934';
console.log(str.match(reg)); // ["12", "34"]
```

* `（red|blue|green）`匹配其中任意一项

```javascript
let reg = /(abc|bcd)[0-9]/g;
let str = "bcd2";
console.log(str.match(str)); // ["bcd2"]
```

## 元字符

* ^  匹配输入字符串的开始位置。 

  ```javascript
  let reg = /^\wb/g;
  let str = "abcdeb";
  console.log(str.match(reg));  // ["ab"]
  ```

* $  匹配输入字符串的结束位置。

  ```javascript
  let reg = /\wb$/g;
  let str = "abcdeb";
  console.log(str.match(reg)); // ["eb"]
  ```

* \1、\2

* \w

  ```javascript
  // 两者等价
  let reg = /\w/g; 
  let reg = /[0-9A-z]/g;
  ```

* \d

  ```javascript
  // 两者等价
  let reg = /\d/g;
  let reg = /[0-9]/g;
  ```

* \s

  匹配任何空白字符串，包括空格、制表符、换页符等等。等价于[\f\n\r\t\v]。

*  \b 

  匹配一个单词边界，也就是指单词和空格间的位置。例如， `'er\b'` 可以匹配"never" 中的 `'er'`，但不能匹配 "verb" 中的 `'er'`。

  ```javascript
  let reg = /\bc/g;
  let str = 'abc cde fgh'
  console.log(str.match(reg)) // ["c"]
  ```

> 注意： ①子表达式中也可以写元字符。②大写的元字符为小写元字符的取反

## 量词

```javascript
// n+ === {n，}
// n* === {n, }
// ? === {0, 1}
let reg = /\w+/g;
let reg1 = /\w*/g;
let reg2 = /\d*/g;
let reg3 = /a{3}/g;

let str = "abc";
let str2 = "aaaaaaaa"

console.log(str.match(reg)); // ["abc"]
console.log(str.match(reg1)); // ["abc", ""]
console.log(str.match(reg2)); // ["", "", "", ""]
console.log(str2.match(reg3)); // ["aaa", "aaa"]
```

> 匹配时遵循贪婪原则。最多能匹配几个就匹配几个

## `RegExp`对象常用属性

### `lastIndex`

 匹配时的游标位置，该属性只有设置标志 g 才能使用。在exec方法和test方法中均会用到

### `exec`

检索字符串中指定的值。返回找到的值，并确定其位置

```javascript
let reg = /ab/g;
let str = "abababab";
console.log(reg.exec(str))   // ["ab", index: 0, input: "abababab", groups: undefined]
console.log(reg.exec(str))   // ["ab", index: 2, input: "abababab", groups: undefined]
console.log(reg.exec(str))	 // ["ab", index: 4, input: "abababab", groups: undefined]
console.log(reg.exec(str))	 // ["ab", index: 6, input: "abababab", groups: undefined]
console.log(reg.exec(str)) 	 // null
console.log(reg.exec(str))	 // ["ab", index: 0, input: "abababab", groups: undefined]
```

### `test`

检索字符串中指定的值。返回`true`或`false`

```javascript
let reg = /\w/g;
let str = 'abcd';
console.log(reg.test(str)) // true
```

## 支持正则表达式String对象的方法

### `match`

```javascript
let reg = /\w/g;
let str = 'ab';
console.log(str.match(reg)) // ['a', 'b']

let reg1 = /\w/;
let str = 'ab';
console.log(str.match(reg)); // ["a", index: 0, input: "ab", groups: undefined]

// 注意全局匹配和非全局匹配的区别
```

### `search`

检索与正则表达式相匹配的值

```javascript
let reg = /a/g;
let str = 'bacsasdsad';
console.log(str.search(reg))
console.log(str.search(reg))
```

### `split`

按传入正则表达式把字符串分割为字符串数组

### `replace`（常用）

替换与正则表达式匹配的字符串

```javascript
let reg = /a/g;
let str = "aa";
console.log(str.replace(reg, "b")); // "bb"


// $操作
let reg1 = /(\w)\1(\w)\2/g;
let str1 = "aabb";
// $1代表正则表达式中的第一个子表达式，$2代表第二个子表达式
console.log(str1.replace(reg1, "$2$2$1$1")) // "bbaa"

//$转义字符
let reg3 = /(\w)\1(\w)\2/g;
let str3 = "aabb";
console.log(str3.replace(reg3, "$$$$")) // $$

// 第二个参数传入一个函数
let reg2 = /(\w)\1(\w)\2/g;
let str2 = "aabb";
// $代表匹配结果，$1代表第一个子表达式，$2代表第二个子表达式
console.log(str2.replace(reg2, function ($, $1, $2) {
  return $2 + $2 + $1 + $1;
}))   // "bbaa"
```

#### 例题一

```JavaScript
// the-first-name
// theFirstName

let reg = /-(\w)/g;
let str = "the-first-name";
console.log(str.replace(reg, function ($, $1) {
  return $1.toUpperCase();
})) // theFirstName
```

#### 例题二

```javascript
//"1000000000"
// 1.000.000.000

let str = "1000000000";
// 匹配非单词边界，且后面跟着一到多个由三个数字组成的字符结尾的空格
let reg = /(?=(\B)(\d{3})+$)/g;
console.log(str.replace(reg, "."))  // 1.000.000.000
```



## 补充

### 正向预查 (?=pattern)

 不参与查找，只做修饰

```javascript
let str = "abaaaaa";
let reg = /a(?=b)/g; // 匹配后面跟着b的a
console.log(str.match(reg)); ["a"]

let reg1 = /a(?!b)/g; // 匹配后面不跟着b的a
console.log(str.match(reg1)); // ["a", "a", "a", "a", "a"]
```

### 贪婪匹配和非贪婪匹配

```javascript
// 默认为贪婪匹配
let str = 'aaa';
let reg = /a+/g;
console.log(str.match(reg)); // ["aaa"];
// 非贪婪匹配
let reg1 = /a+?/g
console.log(str.match(reg1)); // ["a", "a", "a"]
```

# 二十三、DOM

**Document Object Model（文档对象模型）**

**DOM定义了表示和修改文档所需的方法**。DOM对象即为宿主对象，由浏览器厂商定义，用来操作html和xml功能的一类对象的集合。也有人称DOM是对HTML以及XML的标准编程接口

## DOM选择器

* `document`代表整个文档
* `document.getElementById()`  // 元素id在`IE8`以下的浏览器，不区分id大小写
* `getElementByTagName()`     // 标签名
* `getElementsByName()`   // 需注意，只有部分标签name可生效
* `getElementsByClassName()`  // 类名 —>`IE8`及以下版本没有
* `querySelector()`  // `CSS`选择器，可以通过`CSS`选择器的规则选出`dom`元素
* `querySelectorAll()` // `CSS`选择器，可以通过`CSS`选择器的规则选出`dom`元素，得到的是一个类数组

> `querySelector`和`querySelectorAll()`等`CSS`规则选择器方法选出的DOM类数组是静态的，在后续是不可改变的。而其他的DOM选择器是实时的。

```javascript
<div></div>
<div></div>
<div></div>

// 1
let div = document.getElementsByTagName('div');
console.log(div.length); // 3
div[0].remove();
// 注意，当类数组中的某个元素不再满足选择器的条件时，就会从已经获得的dom类数组中剔除
console.log(div.length); // 2

// 2
let div = document.querySelectorAll('div');
console.log(div.length); // 3
div[0].remove();
console.log(div.length)  // 3
```

## 遍历节点树

* `parentNode` 父节点（最顶端的`parentNode`为`#document`）
* `childNodes` 查找直系子节点（不包括孙节点），除了元素节点，还会加上元素节点之间的文本节点、属性节点、注释节点等等。
* `firstChild` 第一个子节点
* `lastChild`  最后一个子节点
* `nextSibling` 后一个兄弟节点
* `previousSibling` 前一个兄弟节点

## 基于元素节点的遍历

* `parentElement`  返回当前元素的父元素节点（最顶端是html）
* `children`  只返回当前元素的元素子节点
* `firstElementChild`   返回的是第一个元素节点
* `lastElementChild`    返回的是最后一个元素节点
* `nextElementSibling` 后一个兄弟元素节点
* `previousElementSibling` 前一个兄弟元素节点

## 节点的属性

* `nodeName`
  * 元素的标签名，以大写形式表示，只读
* `nodeValue`
  * `Text`节点或`Comment`（注释）节点的文本内容，可读写
* `nodeType`
  * 该节点的类型，只读
  * 元素节点 —— 1
  * 属性节点 —— 2
  * 文本节点 —— 3
  * 注释节点 —— 8
  * document —— 9
  * `DocumentFragment` —— 11
* `attributes`
  * `Element`节点的属性集合，比如`id、name、class`等等属性

> 得到的DOM对象都是类数组

## 元素节点属性

* `innerHtml`   获取元素内的所有内容
* `innerText`   获取元素内的文本内容。使用`innerText`赋值时，会覆盖元素内原有的所有的内容

> `innerHTML` 设置或获取位于对象起始和结束标签内的HTML。`outerHTML`设置或获取对象及其内容的HTML形式

## `DOM继承树`

document ——> `HTMLDocument.prototype` ——> `Document.prototype`

![image-20200620104049609](http://img.lijiawei0627.xyz/img/image-20200620104049609.png)

## DOM基本操作

* 增
  * `document.createElement();`   创建元素节点
  * `document.createTextNode();`   创建文本节点
  * `document.createComment();`    创建注释节点
  * `document.createDocumentFragment();`   创建文档碎片

* 插
  * `PARENTNODE.appendChild()`   可以理解为`push`  。 `appendChild`为剪切操作，当把一个页面中已有的`dom`插入到其他地方，那么原来位置的`dom`会消失
  * `PARENTNODE.insertBefore(a, b)` 将`a`节点插入到`b`节点前面，也是剪切操作

* 删
  * `parent.removeChild();`   父元素删除子元素，剪切操作
  * `child.remove();`       销毁自身

* 替换
  
  * `parentNode.replaceChild（new, origin）`  new元素替换origin元素
  
* Element节点的一些属性

  * innerHTML
  * innerText（火狐不兼容）

* Element节点的一些方法

  * ele.setAttribute()

    ```javascript
    div.setAttribute('class', 'demo')
    ```

  * ele.getAttribute()

    ```javascript
    div.getAttribute('class')
    ```

# 二十四、命令式、声明式编程

* 命令式编程：命令“机器”如何去做事情(how)，这样不管你想要的是什么(what)，它都会按照你的命令实现。

* 声明式编程：告诉“机器”你想要的是什么(what)，让机器想出如何去做(how)。

用命令式编程风格实现，像下面这样：

```JavaScript
var numbers = [1,2,3,4,5]
var doubled = []
for(var i = 0; i < numbers.length; i++) {
  var newNumber = numbers[i] * 2
  doubled.push (newNumber)
}
console.log (doubled) //=> [2,4,6,8,10]
```

而使用声明式编程方法，我们可以用 Array.map 函数，像下面这样：

```javascript
var numbers = [1,2,3,4,5]
var doubled = numbers.map (function (n) {
	return n * 2
})
console.log (doubled) //=> [2,4,6,8,10]
```

**三大主流框架都是声明式操作DOM**

# 二十五、`Nodejs`单线程

`Nodejs`是一个平台，构建在chrome的`V8上`（`js`语言解释器），采用事件驱动、非阻塞模型（ c++库：`libuv`）。

## 高并发

为什么单线程却能够支持高并发？

1. 前提：I/O密集型任务
2. 单线程的解释：主线程一个，底层工作线程多个。
3. 事件机制的底层依赖库：`libuv、libeio、libev`

- `libuv`：开发node过程中需要跨平台，首选为Linux使用`libev`（底层为`epoll`），备选`Windows`使用`IOCP`实现，用于**抽象`libev`和`IOCP`的高性能网络库**。

- - 非阻塞TCP、非阻塞命名管道、`UDP`、异步`DNS`
  - 异步文件系统、路径查找、ANSI转义、文件系统事件
  - 子进程生成、线程池调度、进程间IPC与套接字共享
  - 高分辨率时间、定时器

- epoll和IOCP的区别：

- - epoll用于Linux系统，IOCP用于Windows系统；
  - `epoll`是**同步非阻塞模型**：当事件资源满足时发出可处理通知消息（**主线程需要自己去处理**）；`IOCP`是**异步非阻塞模型**，当事件完成时发出通知消息（**工作线程帮主线程处理完了**）。

- ![img](https://images2018.cnblogs.com/blog/1136599/201805/1136599-20180524145348812-64839149.png)

1. `nodejs`运行机制：

![img](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014100803.png)

* `V8`引擎解析JavaScript脚本
* 解析后的代码，调用`Node API`
* [`libuv库`](https://github.com/joyent/libuv)负责`Node API`的执行。它将不同的任务分配给不同的线程，形成一个`Event Loop`（事件循环），以异步的方式将任务的执行结果返回给`V8`引擎
* `V8`引擎再将结果返回给用户

## 优势

1. 多线程占用内存高
2. 多线程间切换使得CPU开销大
3. 多线程由内存同步开销
4. 编写单线程程序简单
5. 线程安全

## 劣势

1. CPU密集型任务占用CPU时间长
2. 无法利用CPU的多核
3. 单线程抛出异常使得程序停止

# 二十六、事件循环

`JavaScript`是一门单线程且非阻塞的脚本语言，这意味着`JavaScript`代码在执行的任何时候都只有一个主线程来处理所有任务。而非阻塞是指当代码需要处理异步任务时，主线程会挂起（`pending`）这个任务，当异步任务处理完毕之后，主线程再根据一定规则去执行相应的回调。

事实上，当任务处理完毕后，`JavaScript`会将这个事件加入一个队列中，我们称这个队列为**事件队列**。被放入事件队列中的事件不会立刻执行其回调，而是等待当前执行栈中的所有任务执行完毕之后，主线程会去查找事件队列中是否有任务。

异步任务有两种类型：微任务（`microtask`）和宏任务（`macrotask`）。不同类型的任务会被分配到不同的任务队列中。

当执行栈中的所有任务都执行完毕后，会去检查微任务队列中是否有事件存在,如果存在，则会依次执行微任务队列中事件对应的回调，直到为空。然后去宏任务队列中取出一个事件，把对应的回调加入当前执行栈，当执行栈中的所有任务都执行完毕后，检查微任务队列中是否有事件存在。无限重复此过程，就形成了一个无限循环，这个循环就叫作事件循环。

属于微任务的事件包括但不限于以下几种：

* `Promise.then`
* `MutationObserver`
* `Object.observe`
* `process.nextTick`

属于宏任务的事件包括但不限于以下几种：

* `setTimeout`
* `setInterval`
* `setImmediate`
* `MessageChannel`
* `requestAnimationFrame`
* `I/O`
* `UI`交互事件

例题：

![img](http://img.lijiawei0627.xyz/img/fa7e0f1ed077430c8bf765ce121bbc14.png)

这个程序的打印顺序为1、9、5、0、6、2、7、4、8、3

# 二十七、执行栈

​		当我们执行一个方法时，JavaScript会生成一个与这个方法对应的执行环境（`context`），又叫执行上下文。这个执行环境中有这个方法的私有作用域、上层作用域的指向、方法的参数、私用作用域中定义的变量以及this对象。这个执行环境会被添加到一个栈中,这个栈就是执行栈。

​		如果在这个方法的代码中执行到了一行函数调用语句,那么`JavaScript`会生成这个函数的行环境并将其添加到执行栈中,然后进入这个执行环境继续执行其中的代码。执行完毕并返回结果后，JavaScript会退出执行环境并把这个执行环境从栈中销毁，回到上一个方法的执行环境。这个过程反复进行,直到执行栈中的代码全部执行完毕。这个执行环境的栈就是执行栈。

# 二十八、事件绑定

传统的 HTML：

```html
<button onclick="activateLasers(this, 'ljw', 21)">
  Activate Lasers,可以通过传入this，来获取当前dom对象
</button>
```

在 React 中略微不同：

```jsx
<button onClick={this.activateLasers}>  Activate Lasers
</button>

<button onClick={(e) => this.activateLasers(e, 'ljw', '21')}>  Activate Lasers
</button>
```

**在`Vue`中，同时兼容传统HTML和react中的写法**

## 原生事件绑定方式

* **行内绑定**

  ```html
  <button onclick="handle(this) // 通过参数传入当前dom对象">html标签事件绑定</button>
  
  <script>
  	function handle(target) {
      console.log(this) // this指向window
      console.log(target) // target指向dom对象
      console.log(window.event) // 获取event原始dom事件
    }
  </script>
  ```

* **`js`事件绑定**

  ```javascript
  <button id="btn2">js事件绑定</button>
  
  function show (e) {
    console.log(this); // this指向dom
    console.log(e); // e指向event原始dom事件
  }
  document.getElementById("btn2").onclick = show;
  //只能触发print方法，如果需要同时触发两个方法，只能使用事件监听
  ```

* **事件监听**

  ```javascript
  function show (e) {
    console.log(this); // this指向dom
    console.log(e); // e指向event原始dom事件
  }
  // 第三个参数设置为true就在捕获过程中执行，反之就在冒泡过程中执行处理函数。
  document.getElementById("btn3").addEventListener("click",show, false); 
  ```

## `Vue`中的事件绑定

### 事件处理方法

```vue
<button v-on:click="greet">Greet</button>

greet: function (event) {
  // `this` 在方法里指向当前 Vue 实例
  alert('Hello ' + this.name + '!')
  // `event` 是原生 DOM 事件
	console.log(event)
}
```

### 内联处理器的方法

```vue
<button v-on:click="say('hi')">Say hi</button>
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

say: function (message) {
  alert(message)  // hi
}

warn: function (message, event) {
	console.log(message) // Form cannot be submitted yet.
  // 现在我们可以访问原生事件对象
  console.log(event)
}
```

## `React`中的事件绑定

### 通过`bind`绑定`this`

```react
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 为了在回调中使用 `this`，这个绑定是必不可少的
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}
```

你必须谨慎对待 `JSX` 回调函数中的 `this`，在 JavaScript 中，class 的方法默认不会[绑定](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) `this`。如果你忘记绑定 `this.handleClick` 并把它传入了 `onClick`，当你调用这个函数的时候 `this` 的值为 `undefined`。

### `class fields`

如果觉得使用 `bind` 很麻烦，这里有两种方式可以解决。如果你正在使用实验性的 [public class fields 语法](https://babeljs.io/docs/plugins/transform-class-properties/)，你可以使用 class fields 正确的绑定回调函数：

```react
class LoggingButton extends React.Component {
  // 此语法确保 `handleClick` 内的 `this` 已被绑定。
  // 注意: 这是 *实验性* 语法。
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

### 箭头函数

如果你没有使用 class fields 语法，你可以在回调中使用[箭头函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)：

```react
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // 此语法确保 `handleClick` 内的 `this` 已被绑定。
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```

### 向事件处理程序传递参数

在循环中，通常我们会为事件处理函数传递额外的参数。例如，若 `id` 是你要删除那一行的 ID，以下两种方式都可以向事件处理函数传递参数：

```react
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

# 二十九、Web存储机制

##  `Storage` 类型  

`Storage` 类型提供大的存储空间（因浏览器而异）来存储名值对儿。`Storage` 的实例与其他对象类似，有如下方法。

* `clear()`： 删除所有值；Firefox中没有实现 。
* `getItem(name)`：根据指定的名字 `name` 获取对应的值。
*  `key(index)`：获得 index位置处的值的名字。 
*  `removeItem(name)`：删除由 name 指定的名值对儿。 
* `setItem(name, value)`：为指定的 name 设置一个对应的值 

## `sessionStorage`对象  

```javascript
//使用方法读取数据 
var name = sessionStorage.getItem("name"); 
 
//使用属性读取数据 
var book = sessionStorage.book; 
```

**`sessionStorage` 对象存储特定于某个会话的数据，也就是该数据只保持到浏览器关闭。这个对象就像会话 `cookie`，也会在浏览器关闭后消失**

`sessionStorage` 对象应该主要用于仅针对会话的小段数据的存储。如果需要跨越会话存储数据， 那么 `globalStorage` 或者 `localStorage` 更为合适

##  `globalStorage` 对象 

作为初的 `Web Storage`规范的一部分，这个对象的目的是跨越会话存储数据，但**有特定的访问限制**。要使用 `globalStorage`，首先要指定哪些域可以访问该数据。可以通过方括号标记使用属性来实现.

```javascript
//保存数据 
globalStorage["wrox.com"].name = "Nicholas"; 
 
//获取数据 
var name = globalStorage["wrox.com"].name; 
```

**上面代码中，访问的是针对域名 `wrox.com` 的存储空间。`globalStorage` 对象不是 `Storage` 的实例， 而具体的 `globalStorage["wrox.com"]`才是。这个存储空间对于 `wrox.com` 及其所有子域都是可以访问的**

```javascript
globalStorage["www.wrox.com"].name = "Nicholas"; 
 
//获取数据 
var name = globalStorage["www.wrox.com"].name; 
```

这里所指定的存储空间只能由来自 `www.wrox.com` 的页面访问，其他子域名都不行。 

**如果不使用 `removeItem()或者 delete` 删除，或者用户未清除浏览器缓存，存储在 `globalStorage` 属性中的数据会一直保留在磁盘上。** 

## `localStorage 对象`  

`localStorage` 对象在修订过的 `HTML 5` 规范中作为持久保存客户端数据的方案取代了 `globalStorage`。与 `globalStorage` 不同，不能给 `localStorage` 指定任何访问规则；规则事先就设定好了。**要访问同一个`localStorage` 对象，页面必须来自同一个域名（子域名无效），使用同一种 协议，在同一个端口上**。这相当于 `globalStorage[location.host]`。 

`localStorage 是 Storage 的实例，所以可以像使用 sessionStorage` 一样来使用它

```javascript
//使用方法存储数据 
localStorage.setItem("name", "Nicholas"); 
 
//使用属性存储数据 
localStorage.book = "Professional JavaScript"; 
 
//使用方法读取数据 
var name = localStorage.getItem("name"); 
 
//使用属性读取数据 
var book = localStorage.book; 
```

**存储在 `localStorage` 中的数据和存储在 `globalStorage` 中的数据一样，都遵循相同的规则： 数据保留到通过 `JavaScript` 删除或者是用户清除浏览器缓存。** 