# 一、less中的注释

* 以`//`开头的注释，不会被编译到`css`文件中去
* 以`/**/`包裹的注释会被编译到`css`文件中去

# 二、变量

可以使用@来申明一个变量：`@color: pink;`

## 普通属性值

作为普通属性值来使用：可以直接使用`@color`

```less
@width: 10px;
@height: @width + 10px;

#header {
  width: @width;
  height: @height;
}
```

编译为

```css
#header {
  width: 10px;
  height: 20px;
}
```

## 选择器和属性名

作为选择器和属性名：`#@{selector}`的形式

```less
// Variables
@my-selector: banner;
@fw: font-weight;

// Usage
.@{my-selector} {
  @{fw}: bold;
  line-height: 40px;
  margin: 0 auto;
}
```

编译为

```css
.banner {
  font-weight: bold;
  line-height: 40px;
  margin: 0 auto;
}
```

## `URL`

作为URL：`@{url}`

```less
// Variables
@images: "../img";

// Usage
body {
  color: #444;
  background: url("@{images}/white-sand.png");
}
```

编译为

```css
body {
  color: #444;
  background: url("../img/white-sand.png");
}
```

## 变量的延迟加载

两次定义变量时，使用变量的最后定义，从当前作用域向上搜索。这类似于css本身，其中css定义中的最后一个属性用于确定值。

```less
@var: 0;
.class {
  @var: 1;
  .brass {
    @var: 2;
    three: @var;
    @var: 3;
  }
  one: @var;
}
```

编译为

```css
.class {
  one: 1;
}
.class .brass {
  three: 3;
}
```

## 属性作为变量

可以使用`$prop`语法轻松地将属性像变量一样对待。有时，这会使得代码更轻便。

> 请注意，像变量一样，Less将选择当前/父范围内的最后一个属性作为“最终”值。

```less
.block {
  color: red; 
  .inner {
    background-color: $color; 
  }
  color: blue;  
} 
```

编译为

```css
.block {
  color: red; 
  color: blue;  
} 
.block .inner {
  background-color: blue; 
}
```

# 三、嵌套规则

## 基本嵌套

Less 提供了使用嵌套（nesting）代替层叠或与层叠结合使用的能力。假设我们有以下 `CSS` 代码：

```css
#header {
  color: black;
}
#header .navigation {
  font-size: 12px;
}
#header .logo {
  width: 300px;
}
```

可以写为：

```less
#header {
  color: black;
  .navigation {
    font-size: 12px;
  }
  .logo {
    width: 300px;
  }
}
```

用 Less 书写的代码更加简洁，并且模仿了 HTML 的组织结构。

## 父选择器&

在使用嵌套规则时，需要特别注意 & 符号。内层选择器前面的 & 符号就表示对父选择器的引用。在一个内层选择器的前面，如果没有 & 符号，则它被解析为父选择器的后代；如果有 & 符号，它就被解析为父元素自身或父元素的伪类。

```less
a {
  color: blue;
  &:hover {
    color: green;
  }
}

.button {
  &-cancel {
    background-image: url("cancel.png");
  }
}
```

编译为

```css
a {
  color: blue;
}

a:hover {
  color: green;
}

.button-cancel {
  background-image: url("cancel.png");
}
```

# 四、混合`Mixins`

混合（`Mixin`）是一种将一组属性从一个规则集包含（或混入）到另一个规则集的方法。假设我们定义了一个类（class）如下：

```css
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
```

如果我们希望在其它规则集中使用这些属性呢？没问题，我们只需像下面这样输入所需属性的类（class）名称即可，如下所示：

```less
#menu a {
  color: #111;
  .bordered();
}

.post a {
  color: red;
  .bordered();
}
```

`.bordered` 类所包含的属性就将同时出现在 `#menu a` 和 `.post a` 中了。（注意，你也可以使用 `#ids` 作为 `mixin` 使用。）

## 不带输出的混合

如果要创建一个`mixin`，但又不希望该`mixin`出现在`CSS`输出中，请在`mixin`定义后加上括号。

```less
.my-mixin {
  color: black;
}
.my-other-mixin() {
  background: white;
}
.class {
  .my-mixin();
  .my-other-mixin();
}
```

输出

```css
.my-mixin {
  color: black;
}
.class {
  color: black;
  background: white;
}
```

## `Mixins`中的选择器

`Mixins`不仅可以包含属性，还可以包含选择器。

例如：

```less
.my-hover-mixin() {
  &:hover {
    border: 1px solid red;
  }
}
button {
  .my-hover-mixin();
}
```

产出

```css
button:hover {
  border: 1px solid red;
}
```

## 参数混合

`Mixins`还可以接受参数，这些参数是在混合时传递到选择器块的变量。（参数`mixin`也可以具有其参数的默认值）

例如：

```less
.border-radius(@radius: 5px) {
  -webkit-border-radius: @radius;
     -moz-border-radius: @radius;
          border-radius: @radius;
}
```

这是我们如何将其混合到各种规则集中：

```less
#header {
  .border-radius(4px);
}
.button {
  .border-radius(6px);
}
div {
  .border-radius();
}
```

## 命名参数

混合引用可以通过名称而不是位置来提供参数值。任何参数都可以通过名称来引用，并且不必采用任何特殊顺序：

```less
.mixin(@color: black; @margin: 10px; @padding: 20px) {
  color: @color;
  margin: @margin;
  padding: @padding;
}
.class1 {
  .mixin(@margin: 20px; @color: #33acfe);
}
.class2 {
  .mixin(#efca44; @padding: 40px);
}
```

编译成：

```css
.class1 {
  color: #33acfe;
  margin: 20px;
  padding: 20px;
}
.class2 {
  color: #efca44;
  margin: 10px;
  padding: 40px;
}
```

## `@arguments`

`@arguments`在`mixin`内部有特殊含义，它包含了在调用`mixin`时传递的所有参数。如果您不想处理单个参数，这将很有用：

```less
.box-shadow(@x: 0; @y: 0; @blur: 1px; @color: #000) {
  -webkit-box-shadow: @arguments;
     -moz-box-shadow: @arguments;
          box-shadow: @arguments;
}
.big-block {
  .box-shadow(2px; 5px);
}
```

结果是：

```css
.big-block {
  -webkit-box-shadow: 2px 5px 1px #000;
     -moz-box-shadow: 2px 5px 1px #000;
          box-shadow: 2px 5px 1px #000;
}
```

## 高级参数和`@rest`变量

`...`如果希望`mixin`接受可变数量的参数，则可以使用。在变量名之后使用此命令会将这些参数分配给变量。

```less
.mixin(...) {        // matches 0-N arguments
.mixin() {           // matches exactly 0 arguments
.mixin(@a: 1) {      // matches 0-1 arguments
.mixin(@a: 1; ...) { // matches 0-N arguments
.mixin(@a; ...) {    // matches 1-N arguments
```

此外：

```less
.mixin(@a; @rest...) {
   // @rest与@a后面的参数绑定
   // @arguments绑定所有参数
}
```

# 五、运算

算术运算符 `+`、`-`、`*`、`/` 可以对任何数字、颜色或变量进行运算。如果可能的话，算术运算符在加、减或比较之前会进行单位换算。计算的结果以**最左侧操作数的单位类型为准**。如果单位换算无效或失去意义，则忽略单位。无效的单位换算例如：`px` 到 `cm` 或 `rad` 到 `%` 的转换。

```less
// 所有操作数被转换成相同的单位
@conversion-1: 5cm + 10mm; // 结果是 6cm

// conversion is impossible
@incompatible-units: 2 + 5px - 3cm; // 结果是 4px

// example with variables
@base: 5%;
@filler: @base * 2; // 结果是 10%
@other: @base + @filler; // 结果是 15%

@base: 2cm * 3mm; // 结果是 6cm
```

还可以对颜色进行算术运算：

```less
@color: #224488 / 2; //结果是 #112244
background-color: #112244 + #111; // 结果是 #223355
```

## calc() 特例

为了与 CSS 保持兼容，`calc()` 并不对数学表达式进行计算，但是在嵌套函数中会计算变量和数学公式的值。

```less
@var: 50vh/2;
width: calc(50% + (@var - 20px));  // 结果是 calc(50% + (25vh - 20px))
```

# 六、导入（Importing）

“导入”的工作方式和你预期的一样。你可以导入一个 `.less` 文件，此文件中的所有变量就可以全部使用了。如果导入的文件是 `.less` 扩展名，则可以将扩展名省略掉：

```css
@import "library"; // library.less
@import "typo.css";
```

# 七、映射（Maps）

从 Less 3.5 版本开始，你还可以将混合（mixins）和规则集（rulesets）作为一组值的映射（map）使用。

```less
#colors() {
  primary: blue;
  secondary: green;
}

.button {
  color: #colors[primary];
  border: 1px solid #colors[secondary];
}
```

输出符合预期：

```css
.button {
  color: blue;
  border: 1px solid green;
}
```

# 八、继承

**性能比混合高，灵活度比混合低。**

```less
.inherit {
  display: inline-block;
}
.inherit:hover{
  background-color:yellow;
}
 
.test {
  width: 400px;
  height: 400px;
  border: 1px solid red;
 
  .test1:extend(.inherit) {
    width: 100px;
    height: 100px;
    border: 1px solid blue;
  }
 
  .test2:extend(.inherit all) { /* 加all继承所有的状态 */
    width: 50px;
    height: 50px;
    border: 1px solid yellow;
  }
}
```

编译为

```css
.inherit,
.test .test1,
.test .test2 {
  display: inline-block;
}
.inherit:hover,
.test .test2:hover {
  background-color: yellow;
}
.test {
  width: 400px;
  height: 400px;
  border: 1px solid red;
}
.test .test1 {
  width: 100px;
  height: 100px;
  border: 1px solid blue;
}
.test .test2 {
  width: 50px;
  height: 50px;
  border: 1px solid yellow;
}
```

# 九、转义

转义（Escaping）允许你使用任意字符串作为属性或变量值。任何 `~"anything"` 或 `~'anything'` 形式的内容都将按原样输出，除非 [interpolation](https://less.bootcss.com/features/#variables-feature-variable-interpolation)。

```less
div {
  padding: ~"calc(100px + 100)";
}
```

编译为

```css
div {
  padding: calc(100px + 100);
}
```

