# 一、基础类型

* 对于程序来说我们需要基本的数据单元,如：`numbers, strings, structures, boolean` 等数据结构。
* 在`TypeScript`中我们支持很多你所期望在JavaScript中所拥有的数据类型系统。
## 布尔类型
```typescript
let bool:boolean = true;
```

## 数值类型
```typescript
let num:number = 123;
```
## 字符串类型
```typescript
let str:string = 'abc';
```
## 数组类型
```typescript
//写法一：
// 定义一个数组，且数组中元素都是number类型
let arr:number[] = [1, 2, 3, 4];
// let arr: Array<number> = [1, 2, 3, 4]

//写法二、
// 数组中可以是number类型或者string类型
let arr:(number | string)[] = [1, '2'];
```
## 元组类型
```typescript
//元组的长度和元素类型必须遵守定义的长度和类型
let tuple:[string, number, boolean]；
tuple = ['1', 2, true];
```
## 枚举类型
```typescript
//索引号可以自己指定，也可以不手动指定，而使用默认的从零开始
enum Roles {
  SUPER_ADMIN = 1,
  ADMIN = 3,
  USER = 8
}
console.log(Roles.USER) // 8
console.log(Roles[1])  // SUPER_ADMIN
```
## any（任意）类型
```typescript
//可以给value指定任意类型
let value:any;
value = 1;
value = false;
value = '1';
value = [];
const arr:any[] = [1, '2', false];
```
## void类型
```typescript
//函数没有返回值
const consoleText = (text:string):void => {
  console.log(text);
}
//也可以给变量指定undefined或者null值（其他类型值不可以赋给v）
let v:void;
v = undefined;
v = null;
```
## undefined和null
```typescript
let u:undefined = undefined;
let n:null = null;
//undefined和null是任何类型的子类型，可以给任意类型赋值undefined和null
let value:number;
value = undefined;
value = null;
```

## never类型
``` typescript
//never类型是任何类型的子类型，可以赋值给任何类型；
//然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。
//即使 any也不可以赋值给never。
// 用法一
const ErrorFunc = (message:string):never => {
  throw new Error(message);
}
// 用法二
const infiniteFunc = (): never => {
  while(true) {}
}
```

## object类型
```typescript
//使用方法跟JavaScript一样
let obj = {
  name: 'lijaiwei'
};

function getObject (obj:object):void {
  console.log(obj);
}
```

## 类型断言
```typescript
//两种方法都可以指定target类型，
//但是在react的jsx语法中，只能使用target as string这种语法
const getLength = (target : string | number) : number => {
  if ((<string>target).length || (target as string).length === 0) {
    return (<string>target).length;
  } else {
    return target.toString().length;
  }
} 
```

# 二、接口

接口是一系列抽象方法的声明，是一些方法特征的集合，这些方法都应该是抽象的，需要由具体的类去实现，然后第三方就可以通过这组抽象方法调用，让具体的类执行具体的方法。


##  基本使用
```typescript
//  定义接口
interface NameInfo {
  firstName: string,
  lastName: string
}
// 使用接口约束传入值（对象）中属性值的类型
const getFullName = ({firstName, lastName}: NameInfo): string => {
  return `${firstName}&{lastName}`;
}
getFullName({
  firstName: 'li',
  lastName: 'jiawei'
})
```

## 可选属性
接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。 可选属性在应用“option bags”模式时很常用，即给函数传入的参数对象中只有部分属性赋值了。
```typescript
interface Vegetable {
  color?: string,  //  color为可选属性
  type: string
}
const getVegetables = ({color, type}: Vegetable): string => {
    return `A ${color ? (color + '') : ''}${type}`
}
getVegetables({type: 'tomato'})
```
带有可选属性的接口与普通的接口定义差不多，只是在可选属性名字定义的后面加一个?符号。

## 额外的属性检查
```typescript
interface Vegetable {
  color?: string,  //  color为可选属性
  type: string
}
const getVegetables = ({color, type}: Vegetable) => {
    return `A ${color ? (color + '') : ''}${type}`
}
getVegetables({
  type: 'tomato', 
  size: 'hello'
})

接口中未定义size属性，这个时候直接传入size属性会报错
```
绕开这些检查非常简单。

* 最简便的方法是使用类型断言：

```typescript
getVegetables({
  type: 'tomato', 
  size: 'hello'
} as Vegetable)
```
* 如果Vegetable带有上面定义的类型的color和type属性，并且还会带有任意数量的其它属性，那么我们可以这样定义它：

```typescript
interface Vegetable {
  color?: string,
  type: string,
  [prop: string]: any
}
```
我们稍后会讲到索引签名，但在这我们要表示的是Vegetable可以有任意数量的属性，并且只要它们不是color和type，那么就无所谓它们的类型是什么。
* 还可以利用类型兼容性

```typescript
interface Vegetable {
  color?: string,
  type: string
}
const getVegetables = ({color, type}: Vegetable) => {
    return `A ${color ? (color + '') : ''}${type}`
}
let obj = {
  type: 'tomato',
  size: 2
}
getVegetables(obj)
```
## 只读属性
```typescript
interface Vegetable {
  color?: string,
  readonly type: string  //type为只读属性
}
let vegetableObj: Vegetable = {
  color: 'orange',
  type: 'tomato'
}
vegetableObj.type = 'carrot'
// 当尝试修改vegetObj的type属性时，会报错
// Cannot assign to 'type' because it is a read-only property.
```
也可以定义数组的接口，规则也同样适用
```typescript
interface arrInter {
  0: string,
  readonly 1: number
}
let arr: arrInter = ['hello', 2]
```
## 函数类型
接口能够描述JavaScript中对象拥有的各种各样的外形。 除了描述带有属性的普通对象外，接口也可以描述函数类型。

为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。
```typescript
interface AddFunc {
  (num1: number, num2: number): number
}
// type AddFunc = (num1: number, num2: number) => number该方法与上面效果一样
const add: AddFunc = (n1, n2) => n1 + n2;
```

## 可索引的类型
与使用接口描述函数类型差不多，我们也可以描述那些能够“通过索引得到”的类型，比如`a[10]`或`ageMap["daniel"]`。
 可索引类型具有一个索引签名，它描述了对象索引的类型，还有相应的索引返回值类型。 让我们看一个例子：

```typescript
interface RoleDic {
  [id: string]: string
}
const role2: RoleDic = {
  a: 'super_admin',
  1: 'admin' // 会将number类型属性转换为string类型'1'
}
console.log(role2[0] === role2['a'])  // true
```
## 继承接口
和类一样，接口也可以相互继承。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里。
```typescript
interface Vegetable {
  color: string,
}
interface Tomato extends Vegetable {
  radius: number
}
const tomato: Tomato = {
  color: 'orange',
  radius: 1
}
```
## 混合类型

先前我们提过，接口能够描述JavaScript里丰富的类型。 因为JavaScript其动态灵活的特点，有时你会希望一个对象可以同时具有上面提到的多种类型。
```typescript
interface Counter {
  (): void, // 函数类型，没有返回值
  count: number //  拥有count属性，为number类型
}
const getCounter = (): Counter => {
  const c = () => { c.count++ } // 函数执行体
  c.count = 0; // 初始化c的count属性为0
  return c;  // 返回一个Counter类型的函数
}
const counter: Counter = getCounter();
console.log(counter.count) //  1

// 该方法可以用来避免作用域内变量的命名冲突
```
## 类类型
明确一个类去符合一种接口
```typescript
interface FoodInterface {
  type: string
}
// 接口检测的是实例
// 如下面定义type属性为静态属性时，就会报错
class FoodClass implements FoodInterface {
  public type: string;
}
```
## 接口继承类
接口继承类时，会继承类成员，但不包含其实现。
即，接口声明了类中的成员，但并没有提供具体的实现。
接口同样会继承private（私有的），和protected（对于本包和子同样可见）
```typescript
class Control {
    private state: any; // 定义一个任何类型的私有属性
}

// 定义一个接口，该接口继承自Control
interface SelectableControl extends Control {
    select(): void;
}

// 定义一个子类，该类继承自Control,并使用接口SelectableControl，由于是子类，可以使用SelectableControl接口
class Button extends Control implements SelectableControl {
    select() { };
}

// 定义一个子类该类继承自Control
class TextBox extends Control {
    select(){}
}

// 定义一个Image类，该类不能使用SelectableControl接口
//class Image implements SelectableControl{

//}

// 和其余类没有任何继承关系的类
class Liaction {

}
```

# 三、类

## 基本用法
```typescript
class Point {
  public x: number;
  public y: number;
  constructor (x: number, y: number) {
    this.x = x;
    this.y = y;
  }
  public getPosition (): string {
    return `(${this.x}, ${this.y})`;
  }
  public getArea (): number {
    return this.x * this.y;
  }
}
const point = new Point(1, 2);
console.log(point);
```
## 继承
```typescript
class Person {
  public name: string;
  public age: number;
  constructor (name: string, age: number) {
    this.name = name;
    this.age = age
  }
  sayName ():void {
    console.log(`my name is ${this.name}`)
  }
}
class Man extends Person {
  public wife: string;
  constructor (name: string, age: number, wife: string) {
    super(name, age);
    this.wife = wife;
  }
}
```
## 修饰符
`TypeScript`类定义成员属性时提供了三种修饰符，分别是public、private、protected，属性如果不加修饰符则默认为public公有。

* public表示公有，在类里面、子类、类外部都可以访问。
* protected 受保护的，跟private很像，但是能在派生类里被使用，外部仍然无法访问，可以在当前类，和当前类的派生类内部被访问
* private 私有的，不能在声明他的类的外部访问,只有在当前类的内部被访问，所有的派生类和其他地方都无法访问
* `readonly` 只读修饰符
```typescript
class Person {
  public readonly name: string \\ public必须在readonly前面
}
```
## 存取器（get和set方法）
想使用get/set方法必须将编译器设置为`es5`或者更高。如果只有get的没有set的存取器会被自动推断为`readonly`
```typescript
let passcode = "secret passcode";

class Employee {
    private _fullName: string;          //设置私有变量

    get fullName(): string {
        return this._fullName;          //只能通过内部get和set方法进行访问和修改
    }

    set fullName(newName: string) {     //并且可以再操作之前进行逻辑处理
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            console.log("Error: Unauthorized update of employee!");
        }
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```
## 静态属性 `static`

> **只有类能访问到，实例不能访问**

```typescript
class Person {
  public static age: number = 18;
  public static getAge () {
    return Person.age
  }
  constructor () {}
}
const man = new Person()
console.log(Person.age); // 18
console.log(Person.getAge());  // 18
console.log(man.age) // 报错
```
## 可选属性
```typescript
class Info {
  public name: string;
  public age?: number; // 可选属性
  constructor (name: string, age?: number, public sex?: string) {
    this.name = name;
    this.age = age;
  }
}
const info1 = new Info('ljw');
const info3 = new Info('ljw', 20);
const info4 = new Info('ljw', 20, 'man');
console.log(info1); // Info {sex: undefined, name: "ljw", age: undefined}
console.log(info3); // Info {sex: undefined, name: "ljw", age: 20}
console.log(info4); // Info {sex: "man", name: "ljw", age: 20}
```
## 抽象类 `absract`
*   TS中的抽象类是提供其他类继承的基类，不能直接被实例化。
*   TS中使用`abstract`关键字定义抽象类，抽象方法，抽象属性和抽象存取器，抽象类中的抽象方法，抽象属性和抽象存取器不包含具体实现，而且必须在派生类中实现。
*   TS中使用`abstract`定义的抽象方法只能放在抽象方法中
*   抽象类和抽象方法主要是用来定义标准使用
```typescript
//抽象类
abstract class Base{
    public id:number;
   // 抽象属性
   public abstract _name: string;
   // 抽象存取器
   abstract get insideName(): string;
   abstract set insideName(value: string);
    //构造函数
    constructor(id:number) {
        this.id = id;
    }
    //非抽象方法
    run():any{};
    //抽象方法
    abstract start():any;
}
//抽象类派生类，必须使用抽象父类的抽象方法。
class User extends Base{
    constructor(id:number){
        super(id);
    }
    /*
    //抽象类的派生类可以不用实现抽象类中的非抽象方法
    run():void{
        console.log("user run");
    }
    */
    //抽象类的派生类必须实现父类抽象类中的抽象方法
    start():void{
        console.log(`id = ${this.id}`);
    }
}
let base = new Base();  // 报错  （无法创建抽象类的实例。）
let user = new User(1);
//user.run();//user run
user.start();//id = 1
```

# 四、函数

## 为函数定义类型
```typescript
// 方法一
function add(x: number, y:number):number {
    return x + y;
}

// 方法二
let myAdd = function(x:number, y:number):number {
    return x + y;
}

// 方法三
let add: (x: number, y: number) => number
add = (arg1: number, arg2: number) => arg1 + arg2
```
使用这三种方式，为函数定义参数类型和返回的类型
## 函数的可选参数
```typescript
let add = (num1: number, num2: number, num3?: number): number => {
  return num1 + num2 + num3 ? num3 : 0;
}
add(1, 2)
```
可选参数必须在必选参数后面，如上面`num3`形参的位置
## 默认参数
```typescript
// 给函数的设置默认参数时，该形参的类型即为默认值的类型（如下面num3=10与num3: number = 10等价）
let add = (num1: number, num2: number, num3 = 10): number => {
  return num1 + num2 + num3  // 13
}
add(1, 2)
add(1, 2, 'hello') // 报错
```
## 剩余参数
与`es6`用法类似
```typescript
const handleData = (arg1: number, ...args: number[]) => {
  console.log(args)  // [2, 3]
};
handleData(1, 2, 3)
```
## 函数的重载
在`JS`中会通过条件判断，判断参数的类型进行不同的逻辑处理，实现函数的重载

由于TS是一种强类型的设计理念，所以在各种条件判断之前，必须多多种情况的函数条件进行多次声明
```typescript
// 函数重载只能使用function来定义，下面的只有前两个是函数重载，第三个是函数实体
function handleData(x: string): string[];
function handleData(x: number): number[];
function handleData(x: any): any {
  if (typeof x === 'string') {
    return x.split('')
  } else {
    return x.toString().split('').map(item => Number(item))
  }
}
console.log(handleData('abc')); // ["a", "b", "c"]
console.log(handleData(123));  // [1, 2, 3]
```
这样实现会在函数调用时进行严格的类型检查，同时通过判断，走不同的逻辑代码，其他类型调用该函数将会报错

# 五、泛型

**泛型是为了提高代码的重用性和代码的通用性**

## 使用泛型
泛型其实是将一种或者多种类型提升，并且用一个自定义的标识符用来表示，比如现在要定义一个函数，该参数类型可以是任意值，并且返回的是与其一样的类型的数组
```typescript
const getArray = <T>(value: T, times: number = 5): T[] => {
  return new Array(times).fill(value)
}
console.log(getArray<number>(123, 4).map(item => item.toFixed())  //  ["123", "123", "123", "123"]
```
## 泛型的应用(多个泛型变量)
```typescript
const getArray = <T, U>(param1: T, param2: U, times: number): Array<T, U> => {
  return new Array(times).fill([param1, param2]);
}
console.log(getArray<number, string>(1, 'a', 3))
```
##  泛型类

泛型既然也是一种类型，那也可以创建泛型类

```typescript

class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };

```
##  泛型继承接口，实现泛型的约束
1、例如现在要实现一个通用类型（有length方法的所有）的函数，打印其的length值并返回其本身，由于不知道其参数类型，可以指定其为泛型

```typescript
function returnLength <T> (arg: T): T{
  console.log(arg.length)
  return arg
}
//但是这样并不能保证所有的参数都有length这个方法，于是需要对泛型进行限制，增加接口

interface lengthRequire{
  length:number//表示其length属性必须为数值型
}
// 通过泛型继承接口来约束newReturnLength的arg参数必须有length属性，否则会报错
function newReturnLength<T extends lengthRequire>(arg: T): T{
  console.log(arg.length)
  return arg
}
newReturnLength([1, 2, 3])  // 3
newReturnLength('hello')  // 5
newReturnLength(1)  // undefined （编辑器会提醒类型“1”的参数不能赋给类型“lengthRequire”的参数）
```
####2、定义一个函数用来获取传入对象的属性值，并且传入的属性名`propName`必须为`object`中存在的属性，否则会报错

```typescript
// 给泛型变量K定义约束：通过K继承keyof T来约束传入的propName参数必须为object上的属性
//（通过keyof获取对象object上所有属性组成的一个数组）
const getProps = <T, K extends keyof T>(object: T, propName: K) => {
  return object[propName];
}
const person = {
  name: 'ljw',
  age: 20
}
getProps(person, 'name')  // 'ljw'
getProps(person, 'age') // 20
getProps(person, 'wife') // 报错
```

# 六、枚举

## 数字枚举类型
```typescript
enum Status {
  Uploading,
  Success = 2,
  Failed
}
console.log(Status.Uploading); // 0
console.log(Status[3]); // Failed
```
## 字符串枚举类型
```typescript
enum Message {
  Error = 'Sorry, error',
  Success = 'Hoho, success',
  Failed = Error
}
console.log(Message.Failed) // 'Sorry, error'
```
## 异构枚举类型
```typescript
enum Result {
  Failed = 0,
  Success = 'success'
}
console.log(Result[0]) // Failed
console.log(Result.Success) // success
```

# 七、类型推断

`TypeScript`里，在有些没有明确指出类型的地方，类型推论会帮助提供类型。如下面的例子

```typescript
let name = 'ljw';
name = 123; //  提示错误
```
变量name的类型被推断为字符串。 这种推断发生在初始化变量和成员，设置默认参数值和决定函数返回值时。
## 最佳通用类型
```typescript
let arr = [1, 'a']; //  推论为let arr: (number | string)[] = [1, 'a'];
arr = [2, 'b', false, 6] // false为布尔类型，会报错
// let str = 'hello, world';
```
## 上下文类型
`TypeScript`类型推论也可能按照相反的方向进行。 这被叫做“按上下文归类”。按上下文归类会发生在表达式的类型与所处的位置相关时。比如：

```typescript
window.onmousedown = function(mouseEvent) {
    console.log(mouseEvent.button);  //<- Error
};
```
这个例子会得到一个类型错误，`TypeScript`类型检查器使用`Window.onmousedown`函数的类型来推断右边函数表达式的类型。 因此，就能推断出 `mouseEvent`参数的类型了。 如果函数表达式不是在上下文类型的位置， `mouseEvent`参数的类型需要指定为any，这样也不会报错了。

# 八、类型兼容性

## 介绍
`TypeScript`中的类型兼容性基于结构子类型。结构类型是一种仅根据其成员关联类型的方式。

**`TypeScript`结构化类型系统的基本规则是，如果x要兼容y，那么y至少具有与x相同的属性。比如：**

```typescript
interface Info {
  name: string
}
let infos: Info
const infos1 = { name: 'ljw' };
const infos2 = { age: 20 };
const infos3 = { name: 'ljw', age: 20};
infos = infos1;
infos = infos2; // 报错
infos = infos3;
```
这里要检查`infos1、2、3是否能赋值给infos`，编译器检查`infos`中的每个属性，看是否能在`infos1、2、3`中也找到对应属性。 在这个例子中， `infos1、2、3`必须包含名字是`name的string`类型成员。`infos1、3满足`条件，因此赋值正确， `infos2`不满足，赋值失败。
## 递归检测（深层次检测）
```typescript
interface Info {
  name: string,
  info: { age: number }
}
let infos: Info
const infos1 = { name: 'ljw' }; 
const infos2  = { name: 'ljw', info: { age: 18 }}
infos = infos1; // 报错
infos = infos2;
```
## 函数兼容
### 参数个数

```typescript
// 参数个数
let x = (a: number) => 0
let y = (b: number, c: string) => 0
x = y; // 报错
y = x;
```
赋值函数的参数个数要小于被赋值函数的参数个数
实例：

```typescript
const arr = [1, 2, 3];
arr.forEach((item, index, array) => {
  console.log(item)
})
arr.forEach(item => {
  console.log(item)
});
// 虽然数组的forEach方法有三个参数，但是我们实际上可能只需要一个参数
```
### 参数类型

```typescript
let x = (a: number) => 0
let y = (b: string) => 0
x = y; // 报错
```
## 枚举兼容
```typescript
enum Status {
  On,
  Off
}
enum Animal {
  Dog,
  Cat
}
let s = Status.On
s = Animal.Dog // 报错
```
## 类的兼容
```typescript
class Animal {
  public static age: number
  constructor (public name: string) {}
}
class People {
  public static age: string
  constructor (public name: string) {}
}
class Food {
  constructor (public name: number) {}
}
let animal: Animal;
let people: People;
let food: Food;
// 不会检测类静态属性
animal = people;
animal = food; // 报错
```
**注：如果类中有私有属性或者受保护属性，那么也是不兼容的**

## 泛型兼容
```typescript
interface Data<T> {
  data: T
}
let data1: Data<number>
let data2: Data<string>
data1 = data2; // 报错
```

# 九、高级类型

## 交叉类型
```typescript
const mergeFunc = <T, U>(arg1: T, arg2: U): T & U => {
  let res = {} as T & U;
  res = Object.assign(arg1, arg2);
  return res;
}
console.log(mergeFunc({ a: 'a'}, { b: 'b'}))
```
## 联合类型 

* 可以使类型具有一定的不确定性，可以增加代码的灵活性
* 变量联合类型
```typescript
const getLengthFunc = (content: string | number): number => {
  if (typeof content === 'string') {
    return content.length;
  } else {
    return content.toString().length;
  }
}
```
## 类型保护
* 通过类型断言（不方便，需要多次使用as）
  TS 提供了类型 `kotlin` 中经过检查的`smartcast` 的功能。
  只要在类型判断函数中返回 `parameterName as Type` 这样的断言。即可

```typescript
const valueList = [123, 'abc']
const getRandomValue = () => {
  const number = Math.random() * 10;
  if (number < 5) {
    return valueList[0];
  } else {
    return valueList[1];
  }
}
const item = getRandomValue();
// 使用类型断言进行保护
if ((item as string).length) {
  console.log((item as string).length);
} else {
  console.log((item as number).toFixed())
}
```
*  用户自定义的类型防御
  只要在类型判断函数中返回 `parameterName is Type` 这样的断言。即可

```typescript
const valueList = [123, 'abc']
const getRandomValue = () => {
  const number = Math.random() * 10;
  if (number < 5) {
    return valueList[0];
  } else {
    return valueList[1];
  }
}
function isString (value: number | string): value is string {
  return typeof value === 'string'
}
const item = getRandomValue();
if (isString(item)) {
  console.log(item.length);
} else {
  console.log(item.toFixed());
}
```
## 使用 `typeof` 类型防御
```typescript
const valueList = [123, 'abc']
const getRandomValue = () => {
  const number = Math.random() * 10;
  if (number < 5) {
    return valueList[0];
  } else {
    return valueList[1];
  }
}
const item = getRandomValue();
// 使用typeof类型保护时，只能使用===或者！==来判断
// 只能识别string/number/boolean/symbok中的一种
if (typeof item === 'string') {
  console.log(item.length);
} else {
  console.log(item.toFixed());
}
```
## 使用instance类型保护

```typescript
class CreateByClass1 {
  public age = 18;
  constructor () {}
}
class CreateByClass2 {
  public name = 'ljw';
  constructor () {}
}
function getRandomItem () {
  return Math.random() < 0.5 ? new CreateByClass1() : new CreateByClass2();
}
const item = getRandomItem();
if (item instanceof CreateByClass1) {
  console.log(item.age)
} else {
  console.log(item.name)
}
```
## null和undefined
```typescript
let value = '123'; // 等价于let value: string | undefined = '123' (null同理)
value = undefined;
value = null;
```
> 未完，等待后续更新。。。。。

# 十、`Symbols`

## 介绍

自`ECMAScript 2015`起，`symbol`成为了一种新的原生类型，就像`number`和`string`一样。

`symbol`类型的值是通过`Symbol`构造函数创建的。

```ts
let sym1 = Symbol();

let sym2 = Symbol("key"); // 可选的字符串key  Symbol(key)

let sym3 = Symbol({name: 'ljw'}) // Symbol([object Object])
```

Symbols是不可改变且唯一的。

```ts
let sym2 = Symbol("key");
let sym3 = Symbol("key");

sym2 === sym3; // false, symbols是唯一的

let s4 = Symbol.for("key")
let s5 = Symbol.for("key")
s4 === s5 // true
```

像字符串一样，symbols也可以被用做对象属性的键。

```ts
let sym = Symbol();

let obj = {
    [sym]: "value"
};

console.log(obj[sym]); // "value"
```

`Symbols`也可以与计算出的属性名声明相结合来声明对象的属性和类成员。

```ts
const getClassNameSymbol = Symbol();

class C {
    [getClassNameSymbol](){
       return "C";
    }
}

let c = new C();
let className = c[getClassNameSymbol](); // "C"
```

## 注意项

```typescript
const s = Symbol('name')
const info = {
  [s]: 'ljw',
  age: 18,
  sex: 'man'
}
console.log(info) // {age: 18, sex: "man", Symbol(name): "ljw"}
info[s] = 'haha'
console.log(info) // {age: 18, sex: "man", Symbol(name): "haha"}
for (const key in info) {
  console.log(key) // age, sex
}
// 获取对象属性名
console.log(Object.keys(info)) // [age, sex]
console.log(Object.getOwnPropertyNames(info)) // [age, sex]
console.log(JSON.stringify(info)) // {"age":18,"sex":"man"}
console.log(Object.getOwnPropertySymbols(info)) // [Symbol(name)]
console.log((Reflect.ownKeys(info))) // ["age", "sex", Symbol(name)]
```

## 众所周知的Symbols

除了用户定义的symbols，还有一些已经众所周知的内置symbols。 内置symbols用来表示语言内部的行为。

以下为这些symbols的列表：

### `Symbol.hasInstance`

`Symbol.hasInstance`方法，会被`instanceof`运算符调用。构造器对象用来识别一个对象是否是其实例。

```typescript
const obj = {
  [Symbol.hasInstance](otherObj) {
    console.log(otherObj)
  }
}
console.log({ a: 'a' } instanceof <any>obj) // {a: 'a'}  false
```

### `Symbol.isConcatSpreadable`

`Symbol.isConcatSpreadable`布尔值，表示当在一个对象上调用`Array.prototype.concat`时，这个对象的数组元素是否可展开。

```typescript
let arr = [1, 2]
arr[Symbol.isConcatSpreadable] = false
console.log([].concat(arr, [3, 4])) // [[1, 2], 3, 4]
```

### `Symbol.iterator`

`Symbol.iterator`方法，被`for-of`语句调用。返回对象的默认迭代器。

### `Symbol.match`

方法，被`String.prototype.match`调用。正则表达式用来匹配字符串。

### `Symbol.replace`

方法，被`String.prototype.replace`调用。正则表达式用来替换字符串中匹配的子串。

### `Symbol.search`

方法，被`String.prototype.search`调用。正则表达式返回被匹配部分在字符串中的索引。

### `Symbol.species`

函数值，为一个构造函数。用来创建派生对象。

### `Symbol.split`

方法，被`String.prototype.split`调用。正则表达式来用分割字符串。

### `Symbol.toPrimitive`

方法，被`ToPrimitive`抽象操作调用。把对象转换为相应的原始值。

### `Symbol.toStringTag`

方法，被内置方法`Object.prototype.toString`调用。返回创建对象时默认的字符串描述。

### `Symbol.unscopables`

对象，它自己拥有的属性会被`with`作用域排除在外。

# 十一、迭代器和生成器

## 可迭代性

当一个对象实现了`Symbol.iterator`属性时，我们认为它是可迭代的。 一些内置的类型如 `Array`，`Map`，`Set`，`String`，`Int32Array`，`Uint32Array`等都已经实现了各自的`Symbol.iterator`。 对象上的 `Symbol.iterator`函数负责返回供迭代的值。

## `for..of` 语句

`for..of`会遍历可迭代的对象，调用对象上的`Symbol.iterator`方法。 下面是在数组上使用 `for..of`的简单例子：

```ts
let someArray = [1, "string", false];

for (let entry of someArray) {
    console.log(entry); // 1, "string", false
}
```

### `for..of` vs. `for..in` 语句

`for..of`和`for..in`均可迭代一个列表；但是用于迭代的值却不同，`for..in`迭代的是对象的 *键* 的列表，而`for..of`则迭代对象的键对应的值。

下面的例子展示了两者之间的区别：

```ts
let list = [4, 5, 6];

for (let i in list) {
    console.log(i); // "0", "1", "2",
}

for (let i of list) {
    console.log(i); // "4", "5", "6"
}
```

另一个区别是`for..in`可以操作任何对象；它提供了查看对象属性的一种方法。 但是 `for..of`关注于迭代对象的值。内置对象`Map`和`Set`已经实现了`Symbol.iterator`方法，让我们可以访问它们保存的值。

```ts
let pets = new Set(["Cat", "Dog", "Hamster"]);
pets["species"] = "mammals";

for (let pet in pets) {
    console.log(pet); // "species"
}

for (let pet of pets) {
    console.log(pet); // "Cat", "Dog", "Hamster"
}
```

## 代码生成

### 目标为 `ES5 和 ES3`

当生成目标为ES5或ES3，迭代器只允许在`Array`类型上使用。 在非数组值上使用 `for..of`语句会得到一个错误，就算这些非数组值已经实现了`Symbol.iterator`属性。

编译器会生成一个简单的`for`循环做为`for..of`循环，比如：

```ts
let numbers = [1, 2, 3];
for (let num of numbers) {
    console.log(num);
}
```

生成的代码为：

```js
var numbers = [1, 2, 3];
for (var _i = 0; _i < numbers.length; _i++) {
    var num = numbers[_i];
    console.log(num);
}
```

### 目标为 `ECMAScript 2015` 或更高

当目标为兼容`ECMAScipt` 2015的引擎时，编译器会生成相应引擎的`for..of`内置迭代器实现方式。

# 十二、模块

> **关于术语的一点说明:** 请务必注意一点，`TypeScript 1.5`里术语名已经发生了变化。 “内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”，这是为了与 [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/)里的术语保持一致，(也就是说 `module X {` 相当于现在推荐的写法 `namespace X {`)。

## 介绍

从`ECMAScript 2015`开始，`JavaScript`引入了模块的概念。`TypeScript`也沿用这个概念。

模块在其自身的作用域里执行，而不是在全局作用域里；这意味着定义在一个模块里的变量，函数，类等等在模块外部是不可见的，除非你明确地使用`export`形式之一导出它们。 相反，如果想使用其它模块导出的变量，函数，类，接口等的时候，你必须要导入它们，可以使用 `import`形式之一。

## 导出

### 导出声明

任何声明（比如变量，函数，类，类型别名或接口）都能够通过添加`export`关键字来导出。

```ts
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```



```ts
export const numberRegexp = /^[0-9]+$/;

export class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
```

### 导出语句

导出语句很便利，因为我们可能需要对导出的部分重命名，所以上面的例子可以这样改写：

```ts
class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export { ZipCodeValidator };
export { ZipCodeValidator as mainValidator };
```

### 重新导出

我们经常会去扩展其它模块，并且只导出那个模块的部分内容。 重新导出功能并不会在当前模块导入那个模块或定义一个新的局部变量。

```ts
export class ParseIntBasedZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && parseInt(s).toString() === s;
    }
}

// 导出原先的验证器但做了重命名
export {ZipCodeValidator as RegExpBasedZipCodeValidator} from "./ZipCodeValidator";
```

或者一个模块可以包裹多个模块，并把他们导出的内容联合在一起通过语法：`export * from "module"`。

```ts
export * from "./StringValidator"; // exports interface StringValidator
export * from "./LettersOnlyValidator"; // exports class LettersOnlyValidator
export * from "./ZipCodeValidator";  // exports class ZipCodeValidator
```

### 默认导出

每个模块都可以有一个`default`导出。 默认导出使用 `default`关键字标记；并且一个模块只能够有一个`default`导出。 需要使用一种特殊的导入形式来导入 `default`导出。

`default`导出十分便利。 比如，像`JQuery`这样的类库可能有一个默认导出 `jQuery`或`$`，并且我们基本上也会使用同样的名字`jQuery`或`$`导出`JQuery`。

```ts
declare let $: JQuery;
export default $;
```



```ts
import $ from "JQuery";

$("button.continue").html( "Next Step..." );
```

类和函数声明可以直接被标记为默认导出。 标记为默认导出的类和函数的名字是可以省略的。

```ts
export default class ZipCodeValidator {
    static numberRegexp = /^[0-9]+$/;
    isAcceptable(s: string) {
        return s.length === 5 && ZipCodeValidator.numberRegexp.test(s);
    }
}
```



```ts
import validator from "./ZipCodeValidator";

let myValidator = new validator();
```

或者

```ts
const numberRegexp = /^[0-9]+$/;

export default function (s: string) {
    return s.length === 5 && numberRegexp.test(s);
}
```



```ts
import validate from "./StaticZipCodeValidator";

let strings = ["Hello", "98052", "101"];

// Use function validate
strings.forEach(s => {
  console.log(`"${s}" ${validate(s) ? " matches" : " does not match"}`);
});
```

`default`导出也可以是一个值

```ts
export default "123";
```

```ts
import num from "./OneTwoThree";

console.log(num); // "123"
```

## 导入

模块的导入操作与导出一样简单。 可以使用以下 `import`形式之一来导入其它模块中的导出内容。

* 导入一个模块中的某个导出内容

```ts
import { ZipCodeValidator } from "./ZipCodeValidator";

let myValidator = new ZipCodeValidator();
```

可以对导入内容重命名

```ts
import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
let myValidator = new ZCV();
```

* 将整个模块导入到一个变量，并通过它来访问模块的导出部分

```ts
import * as validator from "./ZipCodeValidator";
let myValidator = new validator.ZipCodeValidator();
```

* 具有副作用的导入模块

尽管不推荐这么做，一些模块会设置一些全局状态供其它模块使用。 这些模块可能没有任何的导出或用户根本就不关注它的导出。 使用下面的方法来导入这类模块：

```ts
import "./my-module.js";
```

# 十三、命名空间

> **关于术语的一点说明:** 请务必注意一点，`TypeScript 1.5`里术语名已经发生了变化。 “内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”，这是为了与 [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/)里的术语保持一致，(也就是说 `module X {` 相当于现在推荐的写法 `namespace X {`)。

## 介绍

**命名空间：内部模块，主要用于组织代码，避免命名冲突**

**在代码量较大的情况下，为了避免各种变量命名相冲突，可将相似功能的函数、类、接口等放置到命名空间内，同Java的包、.Net的命名空间一样，`TypeScript`的命名空间可以将代码包裹起来，只对外暴露需要在外部访问的对象。命名空间内的对象通过export对外暴露**

## 用法

```typescript
namespace A {
  interface Animal {
    name: string;
    eat(): void
  }
  export class Dog implements Animal {
    name: string;
    constructor(theName: string) {
      this.name = theName
    }
    eat() {
      console.log(`${this.name}吃狗粮`)
    }
  }
  export class Cat implements Animal {
    name: string
    constructor(theName: string) {
      this.name = theName
    }
    eat() {
      console.log(`${this.name}吃猫粮`)
    }
  }
}

var d = new A.Dog('狗狗')
d.eat()
```



# 十四、声明合并

**“声明合并”是指编译器将针对同一个名字的两个独立声明合并为单一声明。 合并后的声明同时拥有原先两个声明的特性。 任何数量的声明都可被合并；不局限于两个声明。**

## 合并接口

最简单也最常见的声明合并类型是接口合并。 从根本上说，合并的机制是把双方的成员放到一个同名的接口里。

```ts
interface Box {
    height: number;
    width: number;
}

interface Box {
    scale: number;
}

let box: Box = {height: 5, width: 6, scale: 10};
```

接口的非函数的成员应该是唯一的。如果它们不是唯一的，那么它们必须是相同的类型。如果两个接口中同时声明了同名的非函数成员且它们的类型不同，则编译器会报错。

对于函数成员，每个同名函数声明都会被当成这个函数的一个重载。 同时需要注意，当接口 `A`与后来的接口 `A`合并时，后面的接口具有更高的优先级。

如下例所示：

```ts
interface Cloner {
    clone(animal: Animal): Animal;
}

interface Cloner {
    clone(animal: Sheep): Sheep;
}

interface Cloner {
    clone(animal: Dog): Dog;
    clone(animal: Cat): Cat;
}
```

这三个接口合并成一个声明：

```ts
interface Cloner {
    clone(animal: Dog): Dog;
    clone(animal: Cat): Cat;
    clone(animal: Sheep): Sheep;
    clone(animal: Animal): Animal;
}
```

> 注意每组接口里的声明顺序保持不变，但各组接口之间的顺序是后来的接口重载出现在靠前位置。

## 合并命名空间

与接口相似，同名的命名空间也会合并其成员。 

`Animals`声明合并示例：

```ts
namespace Animals {
    export class Zebra { }
}

namespace Animals {
    export interface Legged { numberOfLegs: number; }
    export class Dog { }
}
```

等同于：

```ts
namespace Animals {
    export interface Legged { numberOfLegs: number; }

    export class Zebra { }
    export class Dog { }
}
```

## 不同类型合并

命名空间可以与其它类型的声明进行合并。 

### 命名空间和类

这让我们可以表示内部类。

```ts
class Album {
  label: Album.AlbumLabel;
}
namespace Album {
  export class AlbumLabel { }
}
let a = new Album()
a.label
```

合并规则与上面 `合并命名空间`小节里讲的规则一致，我们必须导出 `AlbumLabel`类，好让合并的类能访问。 合并结果是一个类并带有一个内部类。 你也可以使用命名空间为类增加一些静态属性。

### 命名空间和函数

除了内部类的模式，你在JavaScript里，创建一个函数稍后扩展它增加一些属性也是很常见的。 TypeScript使用声明合并来达到这个目的并保证类型安全。

```ts
function buildLabel(name: string): string {
    return buildLabel.prefix + name + buildLabel.suffix;
}

namespace buildLabel {
    export let suffix = "";
    export let prefix = "Hello, ";
}

console.log(buildLabel("Sam Smith"));
```

### 命名空间和枚举

```ts
enum Color {
    red = 1,
    green = 2,
    blue = 4
}

namespace Color {
    export function mixColor(colorName: string) {
        if (colorName == "yellow") {
            return Color.red + Color.green;
        }
        else if (colorName == "white") {
            return Color.red + Color.green + Color.blue;
        }
        else if (colorName == "magenta") {
            return Color.red + Color.blue;
        }
        else if (colorName == "cyan") {
            return Color.green + Color.blue;
        }
    }
}
```

# 十五、装饰器

**装饰器是一种特殊的声明，它能够被附加到类声明、方法、属性或参数上，可以修改类的行为**

**通俗的装饰器就是一个方法，可以注入到类、方法、属性或参数上来扩展类、属性、方法、参数的功能。**

**常见的装饰器有：类装饰器、属性装饰器、方法装饰器、参数装饰器**

**装饰器的写法：普通装饰器（无法传参）、装饰器工厂（可传参）**

## 类装饰器

类装饰器在类声明之前被声明（紧靠着类声明）。类装饰器应用于类构造函数，可以用来监视、修改或替换类定义

### 普通装饰器

```typescript
// 类装饰器
function logClass(params: any) {
  // params就是当前类
  console.log(params);
  params.prototype.apiUrl = '动态扩展的属性'
  params.prototype.run = function() {
    console.log('我是一个run方法')
  }
}

@logClass
class HttpClient{
  constructor() {

  }
  getData() {

  }
}
var http:any = new HttpClient()
console.log(http.apiUrl) // 动态扩展的属性
http.run() // 我是一个run方法
```

### 装饰器工厂（可传参）

```typescript
function logClass(params: string) {
  return function(target: any) {
    console.log(target)
    console.log(params)
    target.prototype.apiUrl = params
  }
}

@logClass('http://www.itying.com/api')
class HttpClient {
  constructor() {}
  getData() {}
}
var http = new HttpClient()
console.log(http.apiUrl)
```

## 属性装饰器

**属性装饰器声明在一个属性声明之前（紧靠着属性声明）。属性装饰器表达式会在运行时当作函数被调用，传入下列两个参数**

* 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
* 属性的名字

```typescript
// 属性装饰器
function logProperty(params: any) {
  return function(target: any, attr: any) {
    console.log(params) // http://itying.com
    console.log(target) // {getData: ƒ, constructor: ƒ}
    console.log(attr) // url
    target[attr] = params // http://itying.com
  }
}
class HttpClient {
  @logProperty('http://itying.com')
  public url: any
  constructor() {}
  getData() {}
}
let http = new HttpClient()
console.log(http.url) // http://itying.com
```

## 方法装饰器

**方法装饰器声明在一个方法的声明之前（紧靠着方法声明）。 它会被应用到方法的 属性描述符上，可以用来监视，修改或者替换方法定义。 方法装饰器表达式会在运行时当作函数被调用，传入下列3个参数：**

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
2. 方法的名字。
3. 成员的属性描述符。

```typescript
function get(params: any) {
  return function(target: any, methodName: any, desc: any) {
    console.log(params) // http://www.itying.com
    console.log(target) // {getData: ƒ, constructor: ƒ}
    console.log(methodName) // getData
    console.log(desc) // {writable: true, enumerable: true, configurable: true, value: ƒ}
    // 重写getData方法
    desc.value = function () {
      console.log('hello, world')
    }
  }
}
class HttpClient {
  public url: any | undefined
  constructor() {}
  @get('http://www.itying.com')
  getData() {
    console.log(this.url)
  }
}
let http = new HttpClient()
http.getData() // hello, world
```

## 参数装饰器

参数装饰器表达式会在运行时当作函数被调用，传入下列3个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
2. 方法的名字。
3. 参数在函数参数列表中的索引。

> 注意  参数装饰器只能用来监视一个方法的参数是否被传入。

```typescript
function logParams(params: any) {
  return function(target: any, methodName: any, paramsIndex: any) {
    console.log(params) // xxxx
    console.log(target) // {getData: ƒ, constructor: ƒ}
    console.log(methodName) // getData
    console.log(paramsIndex) // 0
  }
}
class HttpClient {
  public url: any | undefined
  constructor() {}
  getData(@logParams('xxxx') uuid: any) {
    console.log('我是getData里面的方法')
  }
}
let http = new HttpClient()
http.getData(123) // 我是getData里面的方法
```

# 十六、`Mixins`

**除了传统的面向对象继承方式，还流行一种通过可重用组件创建类的方式，就是联合另一个简单类的代码。**

```typescript
class ClassAa {
  public isA: boolean
  public funcA() {}
}
class ClassBb {
  public isB: boolean
  public funcB() {}
}
class ClassAB implements ClassAa, ClassBb {
  public isA: boolean = false
  public isB: boolean = false
  public funcA: () => void
  public funcB: () => void
}
function mixins(base: any, from: any[]) {
  from.forEach(fromItem => {
    Object.getOwnPropertyNames(fromItem.prototype).forEach(key => {
      console.log(key)
      base.prototype[key] = fromItem.prototype[key];
    });
  });
}
mixins(ClassAB, [ClassAa, ClassBb])
const ab = new ClassAB()
console.log(ab)
```

# 十七、`tsconfig.json`配置

`tsconfig.json` 所包含的属性并不多，只有 7 个，ms 官方也给出了它的[定义文件](https://link.jianshu.com/?t=http://json.schemastore.org/tsconfig)。但看起来并不怎么舒服，这里就翻译整理一下。（若有误，还请指出）

- `files`: 数组类型，用于表示由 ts 管理的文件的具体文件路径

- `exclude`: 数组类型，用于表示 ts 排除的文件（2.0 以上支持 Glob）

- `include`: 数组类型，用于表示 ts 管理的文件（2.0 以上）

- `compileOnSave`: 布尔类型，用于 `IDE` 保存时是否生成编译后的文件

- `extends`: 字符串类型，用于继承 ts 配置，2.1 版本后支持

- `compilerOptions`: 对象类型，设置编译的选项，不设置则使用默认配置，配置项比较多，后面再列

- `typeAcquisition`: 

  对象类型，设置自动引入库类型定义文件(`.d.ts`)相关，该对象下面有 3 个子属性分别是：

  - `enable`: 布尔类型，是否开启自动引入库类型定义文件(`.d.ts`)，默认为 `false`
  - `include`: 数组类型，允许自动引入的库名，如：`["jquery", "lodash"]`
  - `exculde`: 数组类型，排除的库名

如不设定 `files` 和 `include`，ts 默认是 `exclude` 以外的所有的以 `.ts` 和 `.tsx` 结尾的文件。如果，同时设置 `files` 的优先级最高，`exclude` 次之，`include` 最低。

上面都是文件相关的，编译相关的都是靠 `compilerOptions` 设置的，接着就来看一看。

| 属性名                             | 值类型      | 默认值                  | 描述                                                         |                        |
| :--------------------------------- | :---------- | :---------------------- | :----------------------------------------------------------- | ---------------------- |
| `allowJs`                          | `boolean`   | `false`                 | `编译时，允许有 js 文件`                                     |                        |
| `allowSyntheticDefaultImports`     | `boolean`   | `module === "system"`   | `允许引入没有默认导出的模块`                                 |                        |
| `allowUnreachableCode`             | `boolean`   | `false`                 | `允许覆盖不到的代码`                                         |                        |
| `allowUnusedLabels`                | `boolean`   | `false`                 | `允许未使用的标签`                                           |                        |
| `alwaysStrict`                     | `boolean`   | `false`                 | `严格模式，为每个文件添加 "use strict"`                      |                        |
| `baseUrl`                          | `string`    | ``                      | `与 path 一同定义模块查找的路径，详细参考[这里](https://link.jianshu.com/?t=http://www.typescriptlang.org/docs/handbook/module-resolution.html#base-url)` |                        |
| `charset`                          | `string`    | `"utf8"`                | `输入文件的编码类型`                                         |                        |
| `checkJs`                          | `boolean`   | `false`                 | `验证 js 文件，与 allowJs 一同使用`                          |                        |
| `declaration`                      | `boolean`   | `false`                 | `生成 .d.ts 定义文件`                                        |                        |
| `declarationDir`                   | `string`    | ``                      | `生成定义文件的存放文件夹（2.0 以上）`                       |                        |
| `diagnostics`                      | `boolean`   | `false`                 | `是否显示诊断信息`                                           |                        |
| `downlevelIteration`               | `boolean`   | `false`                 | `当 target 为 ES5 或 ES3 时，提供对 for..of，解构等的支持`   |                        |
| `emitBOM`                          | `boolean`   | `false`                 | `在输出文件头添加 utf-8 (BOM)字节标记`                       |                        |
| `emitDecoratorMetadata`            | `boolean`   | `false`                 | `详见 [issue](https://link.jianshu.com/?t=https://github.com/Microsoft/TypeScript/issues/2577)` |                        |
| `experimentalDecorators`           | `boolean`   | `false`                 | `允许注解语法`                                               |                        |
| `forceConsistentCasingInFileNames` | `boolean`   | `false`                 | `不允许不同变量来代表同一文件`                               |                        |
| `importHelpers`                    | ``          | `boolean`               | `false`                                                      | `引入帮助（2.1 以上）` |
| `inlineSourceMap`                  | `boolean`   | `false`                 | `将 source map 一同生成到输出文件中`                         |                        |
| `inlineSources`                    | `boolean`   | `false`                 | `将 ts 源码生成到 source map 中，需要同时设置 inlineSourceMap 或 sourceMap` |                        |
| `isolatedModules`                  | `boolean`   | `false`                 | `将每个文件作为单独的模块`                                   |                        |
| `jsx`                              | `string`    | `"preserve"`            | `jsx 的[编译方式](https://link.jianshu.com/?t=http://www.typescriptlang.org/docs/handbook/jsx.html)` |                        |
| `jsxFactory`                       | `string`    | `"React.createElement"` | `定义 jsx 工厂方法，React.createElement 还是 h（2.1 以上）`  |                        |
| `lib`                              | `string[]`  | ``                      | `引入库定义文件，可以是["es5", "es6", "es2015", "es7", "es2016", "es2017", "esnext", "dom", "dom.iterable", "webworker", "scripthost", "es2015.core", "es2015.collection", "es2015.generator", "es2015.iterable", "es2015.promise", "es2015.proxy", "es2015.reflect", "es2015.symbol", "es2015.symbol.wellknown", "es2016.array.include", "es2017.object", "es2017.sharedmemory", "esnext.asynciterable"]（2.0 以上）` |                        |
| `listEmittedFiles`                 | `boolean`   | `false`                 | `显示输入文件名`                                             |                        |
| `listFiles`                        | `boolean`   | `false`                 | `显示编译输出文件名`                                         |                        |
| `locale`                           | `string`    | `随系统`                | `错误信息的语言`                                             |                        |
| `mapRoot`                          | `string`    | ``                      | `定义 source map 的存放位置`                                 |                        |
| `maxNodeModuleJsDepth`             | `number`    | `0`                     | `检查引入 js 模块的深度，需同 allowJs 一同使用`              |                        |
| `module`                           | `string`    | ``                      | `指定模块生成方式，["commonjs", "amd", "umd", "system", "es6", "es2015", "esnext", "none"]` |                        |
| `moduleResolution`                 | `string`    | ``                      | `指定模块解析方式，["classic" : "node"]`                     |                        |
| `newLine`                          | `string`    | `随系统`                | `行位换行符，"crlf" (windows) 或 "lf" (unix)`                |                        |
| `noEmit`                           | `boolean`   | `false`                 | `不显示输出`                                                 |                        |
| `noEmitHelpers`                    | `boolean`   | `false`                 | `不在输出文件中生成帮助`                                     |                        |
| `noEmitOnError`                    | `boolean`   | `false`                 | `出错后，不输出文件`                                         |                        |
| `noFallthroughCasesInSwitch`       | `boolean`   | `false`                 | `switch 语句中，每个 case 都要有 break`                      |                        |
| `noImplicitAny`                    | `boolean`   | `false`                 | `不允许隐式 any，如果true，函数的形参必须带类型，如果叫不出class名的js对象，那就得any，比如（d:any）=>{}如果false，函数的样子更像js （d）=>{}` |                        |
| `noImplicitReturns`                | `boolean`   | `false`                 | `函数所有路径都必须有显示 return`                            |                        |
| `noImplicitThis`                   | `boolean`   | `false`                 | `不允许 this 为隐式 any`                                     |                        |
| `noImplicitUseStrict`              | `boolean`   | `false`                 | `输出中不添加 "use strict"`                                  |                        |
| `noLib`                            | `boolean`   | `false`                 | `不引入默认库文件`                                           |                        |
| `noResolve`                        | `boolean`   | `false`                 | `不编译三斜杠或模块引入的文件`                               |                        |
| `noUnusedLocals`                   | `boolean`   | `false`                 | `未使用的本地变量将报错（2.0 以上）`                         |                        |
| `noUnusedParameters`               | `boolean`   | `false`                 | `未使用的参数将报错（2.0 以上）`                             |                        |
| `outDir`                           | `string`    | ``                      | `定义输出文件的文件夹`                                       |                        |
| `outFile`                          | `string`    | ``                      | `合并输出到一个文件`                                         |                        |
| `paths`                            | `object`    | ``                      | `与 baseUrl 一同定义模块查找的路径，详细参考[这里](https://link.jianshu.com/?t=http://www.typescriptlang.org/docs/handbook/module-resolution.html#base-url)` |                        |
| `preserveConstEnums`               | `boolean`   | `false`                 | `不去除枚举声明`                                             |                        |
| `pretty`                           | `boolean`   | `false`                 | `美化错误信息`                                               |                        |
| `reactNamespace`                   | `string`    | `"React"`               | `废弃。改用jsxFactory`                                       |                        |
| `removeComments`                   | `boolean`   | `false`                 | `去除注释`                                                   |                        |
| `rootDir`                          | `string`    | `当前目录`              | `定义输入文件根目录`                                         |                        |
| `rootDirs`                         | `string []` | ``                      | `定义输入文件根目录`                                         |                        |
| `skipDefaultLibCheck`              | `boolean`   | `false`                 | `废弃。改用 skipLibCheck`                                    |                        |
| `skipLibCheck`                     | `boolean`   | `false`                 | `对库定义文件跳过类型检查（2.0 以上）`                       |                        |
| `sourceMap`                        | `boolean`   | `false`                 | `生成对应的 map 文件`                                        |                        |
| `sourceRoot`                       | `string`    | ``                      | `调试时源码位置`                                             |                        |
| `strict`                           | `boolean`   | `false`                 | `同时开启 alwaysStrict, noImplicitAny, noImplicitThis 和 strictNullChecks (2.3 以上)` |                        |
| `strictNullChecks`                 | `boolean`   | `false`                 | `null 检查（2.0 以上）`                                      |                        |
| `stripInternal`                    | `boolean`   | `false`                 | `不输出 JSDoc 注解`                                          |                        |
| `suppressExcessPropertyErrors`     | `boolean`   | `false`                 | `不提示对象外属性错误`                                       |                        |
| `suppressImplicitAnyIndexErrors`   | `boolean`   | `false`                 | `不提示对象索引隐式 any 的错误`                              |                        |
| `target`                           | `string`    | `"es3"`                 | `输出代码 ES 版本，可以是 ["es3", "es5", "es2015", "es2016", "es2017", "esnext"]` |                        |
| `traceResolution`                  | `boolean`   | `false`                 | `跟踪模块查找信息`                                           |                        |
| `typeRoots`                        | `string []` | ``                      | `定义文件的文件夹位置（2.0 以上）`                           |                        |
| `types`                            | `string []` | ``                      | `设置引入的定义文件（2.0 以上）`                             |                        |
| `watch`                            | `boolean`   | `false`                 | `监听文件变更`                                               |                        |

一般情况下，`tsconfig.json` 文件只需配置 `compilerOptions` 部分。

```json
{
  "compilerOptions": {
    "allowSyntheticDefaultImports": true, // 允许引入没有默认导出的模块
    "module": "es2015",
    "removeComments": true,
    "preserveConstEnums": true,
    "sourceMap": true,
    "strict": true,
    "target": "es5",
    "lib": [
      "dom",
      "es5",
      "es2015"
    ]
  }
}
```

其中，`allowSyntheticDefaultImports` 是使用 `vue` 必须的，而设置 `module` 则是让模块交由 `webpack` 处理，从而可以使用 `webpack2` 的摇树。另外，加上`allowJs`，这样就可以一点点将现有的 `js` 代码转换为 ts 代码了。

如果，你在 `webpack` 中设置过 `resolve` -> `alias`，那么，在 `ts config` 中也需要通过 `baseUrl` + `path` 的方式来定义模块查找的方式。

```
<a name="tslint"></a>
```