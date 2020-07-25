# 第 1 节 基于 JavaScript 的数据应用开发概述

这本小册的主要目的是让前端工程师和希望能快速实现动态数据应用的数据工作者，学习如何使用 JavaScript 和前端技能来开发具有可用性的数据应用。

## 1.1 你会学到什么？

基于前端技术来开发一个复杂的动态数据应用，需要用到最基本的 JavaScript 常量变量控制、JavaScript 基本数据处理方法、可视化工具、动态数据控制方法等。所以本小册会先从最基本的 JavaScript 数据类型、处理方法讲起，到较为复杂的数据结构，再到逐渐复杂的数据可视化，最后我们将重新定义数据处理，使用前端技能让你的可视化数据应用变得更加灵活。

总体来说，你将会从本小册中学到以下技能点。

*   JavaScript 对基本数据类型的操作
*   JavaScript 对复杂数据结构的操作
*   复杂数据结构的处理技巧
*   基于 ECharts 可视化工具库对简单数据和复杂数据进行图表绘制
*   结合 Vue.js 为数据流添加动态处理功能

对于有 JavaScript 基础的读者来说，可能前面数节你会觉得稍稍有些无趣，但笔者还是希望你能认真地跟着一起学习，因为这些内容可能会改变你对 JavaScript 数据处理的一些既有印象。

## 1.2 在一切开始之前

本小册的全部知识都建立在 JavaScript 语言之上，所以在学习本小册之前，你首先需要准备好 JavaScript 开发环境。虽然说在进行数据可视化和动态数据应用开发的时候，我们必须使用浏览器和网页作为 JavaScript 的运行载体，但是在这之前你也可以选择 Node.js 作为学习 JavaScript 开发的平台。

### 1.2.1 CodePen

当然你也可以选择一些基于网页的即时运行 JavaScript 结果工具来进行学习，这里笔者推荐使用 [CodePen](https://codepen.io/pen/?editors=0012)。你可以直接在这上面编辑 JavaScript 代码，并实时查看运行结果，也可以将其分享给你的小伙伴。

![CodePen](https://user-gold-cdn.xitu.io/2018/4/7/162a02129a549543?w=2120&h=1422&f=jpeg&s=145011)

在 CodePen 中可以添加一些第三方 JavaScript 库，点击右上角的 “Settings” 按钮，选择 “JavaScript” 标签页，在下方的 “Add External Scripts/Pens” 你就可以使用第三方 JavaScript 库的地址进行引入。CodePen 也提供了一些比较常用的库以供便捷地引入，比如在本小册中将会使用到的 Lodash 工具库。

![Xnip2018-03-73_08-48-19](https://user-gold-cdn.xitu.io/2018/4/7/162a0212995c3077?w=1436&h=1440&f=jpeg&s=229649)

当然 CodePen 并不只能运行 JavaScript，它也允许我们直接在上面进行 HTML 和 CSS 的开发，还能实时看到所生成网页的结果。这在我们学习如何进行数据可视化时同样可以派上用场。

### 1.2.2 单页应用

如果你觉得需要一步到位到进行页面应用开发，那么请跟着笔者一步一步准备你的开发环境。

首先请选择一个你最喜欢的编辑器或 IDE，创建一个空的文件夹并将其命名为 `js-learning`。在这个文件夹中创建一个 HTML 文件并将其命名为 `index.html`。将以下内容写入到 `index.html` 文件中。

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Build a data app</title>
</head>
<body>
  <div id="app"></div>
  <script src="main.js"></script>
</body>
</html>

```

然后在这个文件夹中再创建一个 `main.js` 文件并输入以下内容。

```
console.log('Hello World')

```

保存后，双击 `index.html` 并使用你最喜欢的浏览器打开（笔者推荐使用 Google Chrome），按下键盘上的 F12 功能键，在弹出的开发者工具中你就能看到刚才在 `main.js` 文件中输出的 `Hello World`。

此后在本小册的学习中，你就可以通过修改 `main.js` 中的代码来动手自己尝试了。

## 1.3 还是在一切开始之前

本小册的内容比较多且非常需要你一步一步跟着动手尝试，所以笔者建议你最好在空闲时间进行学习，并随时准备好开发环境，而不是单纯地阅读本小册。

**Learn by doing，你将会事半功倍。**

# 第 2 节 基本数据处理 · 字符串和数字

无论在任何的现代编程语言中，最基本的数据格式都会是字符串和数字。字符串用于表达人类文明的自然语言，数字用于表达量化世界的各种数值。

## 2.1 字符串

字符串在自然世界中充当着非常重要的角色，所有的自然语言（比如汉语、英语等）都由多个字符组成，比如经典的 “Hello World” 这句话则由 11 个字符组成：`H,e,l,l,o,<space>,W,o,r,l,d`。

而当我们在希望使用计算机程序来理解我们平常所说的自然语言时，我们则需要对包含了目标自然语言的字符串进行拆分，如分词算法；同时当我们需要使用计算机程序来自动化地完成一些自然语言表达时，如自动组成“今天你有 3 个待办事务”，则需要对多段字符串进行组合而变成一个字符串；当我们想要从寻找字符串中符合“XXX 你好，我是 XXX”句式的文字时，我们又应该如何进行检索呢？别着急，我们一步一步来。

### 2.1.1 创建字符串

在 JavaScript 中创建一个字符串跟其他语言并没有较大的区别，可以分别使用 `'`、`"` 和 `` ` `` 作为边界标识。

```
const str1 = 'string 1'
const str2 = "string 2"
const str3 = `string 3`

```

从 JavaScript 的语法定义上 `'` 和 `"` 并没有太大的差别，但是 `` ` `` 的用途较为特殊。虽然 `` ` `` 同样可以以与 `'` 和 `"` 一样的使用方式使用，但也可以有更高级的用法。

```
const target = 'World'
const word = `Hello ${target}` //=> Hello World

```

同时 `` ` `` 也可以用于创建一个“多行字符串”，即字符串内容中包含多行文本。

```
const str = `
Hello
World
`

//=>
// Hello
// World

```

### 2.1.2 分割字符串

当我们在学习任何一门编程语言时，书本或者老师教我们的第一行代码往往是“如何输出 Hello World”，如上一小节中。而在数据科学领域中，又应该以什么来充当这个 "Hello World" 的例子呢？

一般来说人们都会使用**词频统计**作为数据科学的 "Hello World"，简单来说就是对一段英语内容中的单词进行频次（出现次数）统计。

我们在这里选取 MIT 开源协议中的一部分内容：

> Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
>
> The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
>
> THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

我们在进行词频统计之前，我们首先需要对源内容进行预处理。首先我们要了解的是，我们可以看到这段内容中有小写字母、大写字母以及标点符号（包括逗号、句号、括号、双引号、斜杠和冒号）。

而从语言角度，`HELLO`、`hello` 和 `Hello` 都是一样的，所以我们需要先完成以下预处理任务：

1.  去除文本中的标点符号、数字
2.  将所有大写字母转换为小写字母

假设原文为 "Hey dude, how is it going?"，则需要首先转换为 "hey dude how is it going"。

**去除文本中的标点符号**

在做任何需求之前，都需要先对需求的上下文进行多方位理解，比如去除标点符号、数字这个需求在全英语的语境下可以说是约等于只保留英文字母。这样我们就可以从文本中直接筛选出英文字母和空格即可，这里可以使用 ASCII 码进行甄别。大写字母的 ASCII 码范围为 65 到 90，即 `A` 到 `Z`，而小写字母则为 97 到 122，空格的 ASCII 码为 32，换行符的 ASCII 码为 10。在 JavaScript 可以用 `string.charCodeAt()` 方法获取字符的 ASCII 码。

```
const originalText = 'Hey dude, how is it going?'

let wordOnlyText = ''

for (let i = 0; i < originalText.length; ++i) {
  const letter = originalText[i]
  const asciiCode = letter.charCodeAt()

  if ((asciiCode >= 65 && asciiCode <= 90) || (asciiCode >= 97 && asciiCode <= 122) || asciiCode === 32) {
    wordOnlyText += letter
  }
}

console.log(wordOnlyText) //=> "Hey dude how is it going"

```

> 数值变量 `i` 使用表达式 `++i` 的意义为将其数值加 1，并将其结果作为该表达式的值；而表达式 `i++` 则为将其数值加 1，但返回 `i` 的原值。

**将所有大写字母转换为小写字母**

上面我们用到了 `string.charCodeAt()` 方法来获取字符的 ASCII 码，那么自然也有对应的方法用于将 ASCII 码转换为对应字符 `String.fromCharCode(code)`。

而从字母的 ASCII 码范围可以看到，将大写字母的 ASCII 码数值加上 32 便是其对应小写字母的 ASCII 码数值。

```
let lowerCaseText = ''

for (let i = 0; i < wordOnlyText.length; ++i) {
  const letter = wordOnlyText[i]
  const asciiCode = letter.charCodeAt()
  
  if (asciiCode >= 65 && asciiCode <= 90) {
    lowerCaseText += String.fromCharCode(asciiCode + 32)
  } else {
    lowerCaseText += letter
  }
}

console.log(lowerCaseText) //=> "hey dude how is it going"

```

幸运的是，在 JavaScript 中早就已经内置了将文本中的大写字母转换为小写字母的 API 了 —— `string.toLowerCase()`。

```
const lowerCaseText = wordOnlyText.toLowerCase()

```

完成了文本的预处理之后，就可以将文本数据进行分割了。而最主要用到的方法便是 `string.split(separator)`，其中这个 `separator` 则是定义了用于分割字符串内容的“分割符”。比如在该需求中，我们已经将文本内容进行了预处理，单词与单词之间的分割符则是空格。

```
const originalText = `
Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
`

let wordOnlyText = ''

for (let i = 0; i < originalText.length; ++i) {
  const letter = originalText[i]
  const asciiCode = letter.charCodeAt()

  if ((asciiCode >= 65 && asciiCode <= 90) || (asciiCode >= 97 && asciiCode <= 122) || asciiCode === 32) {
    wordOnlyText += letter
  }
}

const lowerCaseText = wordOnlyText.toLowerCase()

/*---------------------------------------------------------------------*/

const words = lowerCaseText.split(' ')

console.log(words.length) //=> 163

```

而如何实现词频统计，我们先稍作休息，后面的章节继续来学习。

### 2.1.3 组装字符串

除了对字符串进行拆分以外，我们也常常需要将不同的信息通过各种方式拼装成一个完整的字符串信息。最常见的例子便是我们几乎每天都能遇见的，由程序自动发送的自动短信、自动邮件等。要学习如何在 JavaScript 中进行字符串组装，我们需要一步一步来。

字符串之间的连接可以直接使用 `+` 运算符完成。

```
const str1 = 'Hello'
const str2 = 'World'

console.log(str1 + ' ' + str2) //=> "Hello World"

```

这是最基本的字符串连接方式，这适用于数据量较小的字符串信息拼接。若当数据量比较大时，需要将多个数据量“嵌入”到一个文本模板中。一般情况下，开发工程师会选择一个较为顺手的模板引擎来实现这个功能，但在这里我们一切从简，使用 JavaScript 中的字符串模板特性 `` ` `` 来完成这个需求。

```
const name = 'Will Wen Gunn'
const level = 'Gold'

const message = `
Hello, ${name}.
Here is Turing Airline, you are the ${level} member of our Privilege Club.
`

console.log(message)
//=>
// Hello, Will Wen Gunn.
// Here is Turing Airline, you are the Gold member of our Privilege Club.

```

### 2.1.4 正则表达式

我们回过头来看看我们在进行字符串分割的时候，需要剔除文本中非英文字母的内容。而前面我们所使用的方法为判定每一个字符的 ASCII 码是否符合英文字母的范围。这种方法虽然容易理解，但很显然这并非最好的办法。而正则表达式的使用可以让这个需求的实现变得非常简单。

```
const originalText = 'Hey dude, how is it going?'

const words = originalText.toLowerCase().match(/\w+/g)

console.log(words.length) //=> 6

```

当然，因为正则表达式这一个知识点单独拿出来就可以写一本书了，所以这里我们不再详细阐述。

## 2.2 数字

除了自然语言以外，当我们需要准确地表达世界上任何事物时，我们还需要有数字的帮助。

> *   我今年 18 岁
> *   我有 3 个孩子

### 2.2.1 四则运算

四则运算是所有数学运算的基础，懂得如何对数字做四则运算是进行后续更高深运算的首要前提。

```
const a = 3
const b = 4

a + b //=> 7
b - a //=> 1
a * b //=> 12
a / b //=> 0.75

```

### 2.2.2 优先级

我们在计算数学式子的时候经常会碰到这样的情况：

![2 + 3 * 4 = 14 \quad (1)\\
(2 + 3) * 4 = 20 \quad (2)](https://juejin.im/equation?tex=2%20%2B%203%20*%204%20%3D%2014%20%5Cquad%20(1)%5C%5C%0A(2%20%2B%203)%20*%204%20%3D%2020%20%5Cquad%20(2))

由于在四则运算中乘法和除法拥有比加法和减法更高的优先级，若当我们要计算 ![2](https://juejin.im/equation?tex=2) 与 ![3](https://juejin.im/equation?tex=3) 的和再乘以 ![4](https://juejin.im/equation?tex=4) 的结果时就需要像 ![(2)](https://juejin.im/equation?tex=(2)) 式这样写，否则由于优先级的原因会导致错误的结果，如式子 ![(1)](https://juejin.im/equation?tex=(1))。

在 JavaScript 中也同样存在这样的问题，当然我们也是可以使用括号来解决。

```
console.log(2 + 3 * 4) //=> 14
console.log((2 + 3) * 4) //=> 20

```

### 2.2.3 幂运算

问：设有一个边长为 3 厘米的正方体，求该正方体的体积为多少？

答：![V = 3 \times 3 \times 3 = 27](https://juejin.im/equation?tex=V%20%3D%203%20%5Ctimes%203%20%5Ctimes%203%20%3D%2027)

问：设一个正方体的边长为 ![n](https://juejin.im/equation?tex=n)，求该正方体的体积为多少？

答：![V = n^3](https://juejin.im/equation?tex=V%20%3D%20n%5E3)

幂运算，又称指数运算，是数学中非常重要的一种运算方式，具体定义便是同一个数值的多次自乘结果。有了幂运算才有后面更为深入的对数运算、导数运算、方差运算等等。在 JavaScript 中，使用 `Math.pow(base, exponent)` 来进行幂运算，其中 `base` 为底数即上式中的 ![n](https://juejin.im/equation?tex=n)，而 `exponent` 则为指数即右上角的 ![3](https://juejin.im/equation?tex=3)。

```
const V1 = 3 * 3 * 3
console.log(V1) //=> 27

const V2 = Math.pow(3, 3)
console.log(V2) //=> 27

const calcCubeVolume = function(sideLength) {
  return Math.pow(sideLength, 3)
}
console.log(calcCubeVolume(3)) //=> 27

```

### 2.2.4 对数运算

当人类发明了幂运算之后，为了能够知道一个数究竟是由什么数进行了幂运算所得的，所以有幂运算的逆转运算——对数运算。

> *   27 是 3 的多少次方？
> *   1024 是 2 的多少次方？

在数学中这就涉及了**对数运算**，比如求“27 是 3 的多少次方”则为

![\log_3 27 = 3 \\
\log_2 1024 = 10](https://juejin.im/equation?tex=%5Clog_3%2027%20%3D%203%20%5C%5C%0A%5Clog_2%201024%20%3D%2010)

而在数学中有一个神奇的数值**自然对数** ![e](https://juejin.im/equation?tex=e)，关于这个数值的传奇故事你可以搜索到非常多的文章甚至视频。在 JavaScript 中的 `Math.log(x)` 函数只接受一个参数，而这个函数的直接作用便是计算以自然对数 ![e](https://juejin.im/equation?tex=e) 为底 ![x](https://juejin.im/equation?tex=x) 的对数。

![\log_e x](https://juejin.im/equation?tex=%5Clog_e%20x)

那么就很奇怪了，我们要如何在 JavaScript 中计算并非以 ![e](https://juejin.im/equation?tex=e) 为底的对数结果呢？这得先回到数学推导上来。对数中有一个经典公式为**换底公式**，定义如下：

![\log_\alpha x=\frac{\log_\beta x}{\log_\beta \alpha}](https://juejin.im/equation?tex=%5Clog_%5Calpha%20x%3D%5Cfrac%7B%5Clog_%5Cbeta%20x%7D%7B%5Clog_%5Cbeta%20%5Calpha%7D)

具体推导可以参考[维基百科页面 · 对数](https://zh.wikipedia.org/wiki/%E5%AF%B9%E6%95%B0)。那么代入我们前面公式中就可以用这样的方式解决了。

![\log_\alpha x=\frac{\log_e x}{\log_e \alpha}](https://juejin.im/equation?tex=%5Clog_%5Calpha%20x%3D%5Cfrac%7B%5Clog_e%20x%7D%7B%5Clog_e%20%5Calpha%7D)

换成使用 JavaScript 实现便是：

```
function getBaseLog(base, x) {
  return Math.log(x) / Math.log(base)
}

console.log(getBaseLog(2, 1024)) //=> 10

```

### 2.2.5 求和

当两个数值相加时用一个加号可以完成，当三个数值相加时用两个加号可以完成，但若需要求 1000 个数值甚至更多的数值相加的时候，数学表达式要怎么表达呢？

假设数列 ![L](https://juejin.im/equation?tex=L) 为有 ![n](https://juejin.im/equation?tex=n) 个元素的自然数数列，其中 ![L_i \; (1 \le i \le n)](https://juejin.im/equation?tex=L_i%20%5C%3B%20(1%20%5Cle%20i%20%5Cle%20n)) 为数列 ![L](https://juejin.im/equation?tex=L) 的第 ![i](https://juejin.im/equation?tex=i) 个元素。那么求数列 ![L](https://juejin.im/equation?tex=L) 的元素总和 ![S](https://juejin.im/equation?tex=S) 可以用以下公式表达：

![S=\sum_{i=1}{^n}L_{i}](https://juejin.im/equation?tex=S%3D%5Csum_%7Bi%3D1%7D%7B%5En%7DL_%7Bi%7D)

比较可惜的是，在 JavaScript 中并没有直接等同于这个 ![\sum{}](https://juejin.im/equation?tex=%5Csum%7B%7D) 的 API。但是我们可以手动使用**循环**的方式来实现这个需求。

```
let S = 0
const L = [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
const n = L.length

for (let i = 0; i < n; ++i) {
  S += L[i]
}

console.log(S) //=> 55

```

### 2.2.6 求余

求余可能是在数学中比较少见的一种运算，但是计算机领域中却会经常使用，比如进制之间的转换等等。

```
5 % 2 == 1
4 % 2 == 0

```

## 小结

在本节学习中，你学会了如何使用 JavaScript 创建一个字符串以及一个数字，并且学会了如何对它们进行计算、转换、拼接以及拆分。那么为了能够更好地理解和吸收本节的知识，动动手完成以下的习题吧。

### 习题

1.  尝试使用 JavaScript 将字符串 `"Hello World"` 中的小写字母全部转为大写字母。
2.  尝试筛选出章节 **2.1.2** 中 MIT 开源协议的文本中的大写英文字母。
3.  使用 JavaScript 完成以下式子的计算。

![1 + (2 + 3) * 4 \div 5 - (6 + 7)](https://juejin.im/equation?tex=1%20%2B%20(2%20%2B%203)%20*%204%20%5Cdiv%205%20-%20(6%20%2B%207))

4.  定义函数 ![F](https://juejin.im/equation?tex=F)，接受唯一参数长度为 ![i](https://juejin.im/equation?tex=i) 的数列 ![L](https://juejin.im/equation?tex=L)。使用 JavaScript 实现该函数。

![F(L) = L_1 * L_2 * \cdots * L_i](https://juejin.im/equation?tex=F(L)%20%3D%20L_1%20*%20L_2%20*%20%5Ccdots%20*%20L_i)

# 第 3 节 基本数据处理 · 对象字面量

> “都这么大了，还找不到对象吗？”  
> “不用找，我可以 new 一个。”

对象是 JavaScript 中的基础，它可以用于表达具象的事物，可以表达抽象的事物，也可以将具象事物抽象表达，反之亦然。

## 3.1 描述万物的对象

> 我是小问，多领域开发者，主要为 Web 开发与大数据、机器学习领域。

若要根据这段个人介绍，将笔者使用 JavaScript 进行抽象化表达，便可以使用对象字面量来实现。

```
const author = {
  name: '小问',
  title: '多领域开发者',
  domains: [ 'Web 开发', '大数据', '机器学习' ]
}

const someone = {
  name: 'Ben',
  age: 25,
  title: 'Web Developer',
  skills: [ 'JavaScript', 'TypeScript', 'HTML', 'CSS', 'React', 'MobX' ]
}

```

对象字面量可以将一个具象的事物在计算机程序中抽象化表达，但同时也可以将一个抽象的事物变得更为具象，就好比这一篇文章原本是一个抽象的事物，而在程序中却可以将其具象化表达。

```
const post = {
  title: '基本数据处理 · 对象字面量',
  serialNo: 2,
  parentBook: {
    title: '基于 JavaScript 开发灵活的数据应用',
    author: {
      name: '小问',
      title: '多领域开发者',
      domains: [ 'Web 开发', '大数据', '机器学习' ]
    }
  },
  
  content: '......'
}

```

当然对象的属性键（Key）也并非只能用这样的方式定义，如果说需要为一个数值定义一些数学特征值，包括底数为 2 的对数、底数为自然对数 ![e](https://juejin.im/equation?tex=e) 的对数以及底数为 10 的对数。

```
const x = 1024

function getBaseLog(base, x) {
  return Math.log(x) / Math.log(base)
}

const baseLog = {
  2: getBaseLog(2, x),
  e: getBaseLog(Math.E, x),
  10: getBaseLog(10, x)
}

console.log(baseLog) //=> {2: 10, 10: 3.0102999566398116, e: 6.931471805599453}

```

当需要描述的事物更加抽象时，可能连属性键都会是动态生成的，那么这时候就需要更高级的语法来实现这样的需求了。

```
const prefix = 'MK'
const sourceVersion = 1
const latestVersion = 47

const ironMan = {
  [prefix + sourceVersion]: '2008',
  [prefix + latestVersion]: '2017'
}

console.log(ironMan.MK47) //=> 2017

```

在 `{ [<expression>]: value }` 中的 `expression` 为一个表达式，即可以计算出结果的代码，如上面一段代码的 `prefix + sourceVersion`。

## 3.2 对象内容操作

对象被定义以后，自然就是对其的使用，而最直接的便是对对象内容的读取和写入。

### 3.2.1 对象内容读取

JavaScript 中对象内容读取十分的简单，如果属性键为字符串，且该字符串中只包含英文字母和下划线的话，可以直接用 `<obj>.<key>` 来读取属性值。

```
const post = {
  title: '基本数据处理 · 对象字面量',
  serialNo: 2,
  parentBook: {
    title: '基于 JavaScript 开发灵活的数据应用',
    author: {
      name: '小问',
      title: '多领域开发者',
      domains: [ 'Web 开发', '大数据', '机器学习' ]
    }
  },
  
  content: '......'
}

console.log(post.title) //=> 基本数据处理 · 对象字面量
console.log(post.parentBook.author.name) //=> 小问

```

而当对象中所需要读取的目标属性键为数字、包含英文字母和下划线以外的字符串甚至是 `Symbol` 对象的时候，就需要使用 `obj[key]` 的形式来读取属性值了。

```
const obj = {
  1: 2,
  'a b c': 'd e f',
  [Symbol.for('foo')]: 'bar'
}

console.log(obj[1]) //=> 2
console.log(obj['a b c']) //=> d e f
console.log(obj[Symbol.for('foo')]) //=> bar

```

### 3.2.2 修改对象内容

虽然使用 `const` 语句所定义的对象是不能直接被替换的，但是其中的内容依然能被修改。

> 关于 `const`、`let` 和 `var` 的故事，可以自行搜索，也可以参考笔者的[《实战 ES2015》](https://item.jd.com/11995765.html)，其中有很详细的讲解。

在 JavaScript 中存在着“引用”和“值”的概念区别，当然这同样不是本书的讨论范围。简单地解释，就是对对象内容进行修改跟进行读取类似，只是在读取语句后面加上 `= <new value>` 即可。

```
const obj = {
  foo: 'bar',
  1: 2,
  'a b c': 'd e f',
  [Symbol.for('foo')]: 'bar'
}

obj.foo = 'rab'
obj[1] = 3
console.log(obj.foo) //=> rab
console.log(obj[1]) //=> 3

```

当然，当你需要为一个对象添加新的属性时，也是通过同样的方式添加属性。

```
const obj = {}

obj.foo = 'bar'
obj[1] = 2

```

但要非常注意的是，在**一般情况**下，无论是对对象进行添加、读取还是修改属性，都遵循着嵌套链完整的原则，具体如下例所示。

```
const outer = {
  inner: {}
}

outer.inner.foo = 'bar' // OK
outer.something.bar = 1 // Error!

```

## 小结

对象可以说是在 JavaScript 编程开发中最最重要的概念，懂得如何在最基础的知识上学会灵活使用，在后面的学习和开发中你会变得事半功倍。

### 习题

请用对象字面量描述自己，尽可能多地丰富对象属性内容，并注意其中的层级嵌套关系。

# 第 4 节 基本数据处理 · 数组

让我们再次把目光放回我们在第 2 节中提出的大数据中的 “Hello World” 词频统计上。在前面的章节中，我们将 MIT 开源协议中的一部分文本进行了预处理，并将这个文本切割成以字符串为元素的数组。

那么我们就可以开始学习如何处理数组、更强的数组以及使用数组完成我们的案例。

## 4.1 数组

数组在数学中也可以称为“数列”，也就是以数字或其他类型内容为元素的有序集合。

```
// 整型数字数组
const intArray = [ 1, 2, 3, 4, 5 ]
// 浮点型数字数组
const floatArray = [ 1.1, 1.2, 1.3, 1.4, 1.5 ]
// 字符串数组
const strArray = [ 'a', 'b', 'c', 'd', 'e' ]

```

在第 2 节中我们完成的将文本预处理便是将一段较长的文本变成了这种字符串数组。在数据科学领域中，数组可以说是承载了绝大部分数据的表达任务，无论是规整的数据表，还是随时间排序的时间序列，或是复杂多变的非结构化数据，都可以使用数组或类数组的形式表达。

### 4.1.1 长度

我们前面讲到数组是一个**有序集合**，那么就意味着它包含了若干个元素。当然了，数组可空。因为它是一个包含了若干元素的集合，所以它就肯定天然地包含了一个属性，那便是**元素的数量**。

```
const array = [ 1, 2, 3, 4, 5 ]
console.log(array.length) //=> 5

```

### 4.1.2 修改内容

因为在计算机中的可用内存是有限的，所以大部分程序在创建数据（比如数组）的时候，都需要先设定好该数据的所占长度。但在 JavaScript 中这并不需要，因为实际在 JavaScript 中数组就是一个特殊的对象，但这并不在讨论范围内。

所以在 JavaScript 中，对数组内容的修改会比较方便。“增查改删”是数据库应用领域中最常见的操作，这在数组中也是一样的。

#### 增加内容

一般来说向数组增加内容是在数组的末端新增内容（Append），当然也可能存在将新内容添加到数组首端或是插入到中间的某一个部分的需求。

**添加到末端 Append**

Append 操作在 JavaScript 中使用 `array.push(element1[, ...[, elementN]])` 方法直接实现。

```
const array = []

array.push(1)
console.log(array) //=> [1]

array.push(2, 3)
console.log(array) //=> [1, 2, 3]
console.log(array.length) //=> 3

```

**添加到首端 Prepend**

添加到首端的操作在 JavaScript 中可以使用 `array.unshift(element1[, ...[, elementN]])` 方法。

```
const array = [ 4, 5 ]

array.unshift(3)
console.log(array) //=> [3, 4, 5]

array.unshift(1, 2)
console.log(array) //=> [1, 2, 3, 4, 5] 

```

**插入到中间某位置 Insert**

有的时候我们还需要往数组中的某一个位置添加元素。但需要注意的是，在 JavaScript 中数组元素的位置是从 `0` 开始的，也就是数组的第一个元素的下标为 `0`，第二个为 `1`。

假设我们需要在数组 `[ 1, 2, 4, 5 ]` 中的第三个位置，即下标为 `2` 的位置上添加元素 `3`。这需要用到 `array.splice(start, deleteCount, element1[, ...[, elementN]])` 方法。你可以注意到该方法第二个参数是 `deleteCount`，因为这个方法也可以用来删除数组中某一个位置开始的若干个元素，而当我们将这个参数设置为 `0` 的时候，该方法第三个以及后面的参数便会插入到下标为 `start` 的位置，后面的元素自动往后推导。

```
const array = [ 1, 2, 6, 7 ]

array.splice(2, 0, 3)
console.log(array) //=> [1, 2, 3, 6, 7]

array.splice(3, 0, 4, 5)
console.log(array) //=> [1, 2, 3, 4, 5, 6, 7]

```

#### 查找内容

因为我们说数组是一个有序集合，所以我们在对数组中的元素进行查找的时候也是一个有序进行的过程，而最常用的内容查找方法便是 `filter` 过滤器。

过滤器的逻辑便是定义一个过滤函数，该函数会有序地被传入数组中当前下标的元素，而它则需要返回该函数是否符合其过滤要求，即结果为 `true` 或 `false`。

假设我们需要在数组 `[1, 2, 3, 4, 5, 6, 7, 8]` 中找出偶数项，即对元素进行对 `2` 求余结果为 `0` 时即为偶数。

```
const array = [ 1, 2, 3, 4, 5, 6, 7, 8 ]
const evenNumbers = array.filter(function(x) {
  return x % 2 == 0
})

console.log(evenNumbers) //=> [2, 4, 6, 8]

```

#### 删除内容

删除内容在实际应用中有非常多的含义，有可能是删除不符合某一种条件的元素，那么使用过滤器即可实现；有可能是需要删除某一个位置上的元素，那么就需要使用上面提到的 `array.splice(start, deleteCount)` 方法。

比如我们要删除数组 `[1, 2, 3, 10, 4, 5]` 中下标为 `3` 的元素 `10`，就可以这样使用，删除从位置 `3` 开始的 `1` 个元素。

```
const array = [1, 2, 3, 10, 4, 5]

array.splice(3, 1)

console.log(array) //=> [1, 2, 3, 4, 5]

```

#### 更新内容

对数组中的某一个元素进行修改，这种操作与对象中的修改对象属性内容是一样的，因为数组就是一个特殊的对象（属性键为自增长自然数）。

```
const array = [ 1, 2, 3, 4, 5 ]

array[0] = 10
console.log(array) //=> [10, 2, 3, 4, 5]

```

#### “题外话”：封装数组操作工具

虽然绝大多数操作都可以直接使用 JavaScript 中自带的 API 来实现，但是如 `array.splice()` 这种方法看上去就很容易产生操作错误。那么为了避免开发中的失误，我们可以通过定义一个抽象对象来封装一个用于操作数组的工具库。

```
const arrayUtils = {
  // methods
}

```

**添加内容**

前面我们说道了为数组添加内容有三种模式：末端添加、首端添加和中间插入，那么我们就可以分别为它们封装好 `append`、`prepend` 和 `insert` 函数。

```
const arrayUtils = {

  // ...
  
  append(array, ...elements) {
    array.push(...elements)
    
    return array
  },
  
  prepend(array, ...elements) {
    array.unshift(...elements)
    
    return array
  },
  
  insert(array, index, ...elements) {
    array.splice(index, 0, ...elements)
    
    return array
  }
}

// 使用
const array = []
arrayUtils.append(array, 3)    // 末端添加元素 3
arrayUtils.prepend(array, 1)   // 首端添加元素 1
arrayUtils.insert(array, 1, 2) // 在位置 1 添加元素 2

console.log(array) //=> [1, 2, 3]

```

**删除内容**

因为要删除数组中的某一个元素同样需要用到 `array.splice()` 方法，为了避免歧义我们也可以将其封装到工具库中。

```
const arrayUtils = {

  // ...
  
  remove(array, index) {
    array.splice(index, 1)

    return array
  }
}

// 使用
const array = [ 1, 2, 3 ]
arrayUtils.remove(array, 1)

console.log(array) //=> [1, 3]

```

### 4.1.3 以数组为单位的基本处理方法

我们前面对数组的介绍，全部都是以元素为单位的操作。但是在大多数情况下，我们都需要以整个数组为单位进行运算，比如进行平均数计算等等。那么我们就需要有一些方法来对整个数组进行处理和计算。

一般来说对数组的总体进行处理可以归类为两个操作：转换和聚合。

#### 转换

转换便是将一个数组中的内容，以一定的方式规律地转换为另一个数组内容。为什么要进行数据转换？因为有时候并不是天生就是可计算的，比如视频、图像、声音和文本等等，而当我们讨论运算的时候，都是以数字为运算的基础。那么为了方便进行运算，就需要先将这些“不可计算”的数据转换为数字，就比如我们前面学习字符串处理的时候就使用过了将英文字母转换为 ASCII 码的过程。

在 JavaScript 中对数组进行“扫描”有不少方法，如前面提到过的 `filter`、只进行循环的 `forEach`、与 `filter` 类似的但只返回第一个匹配值的 `find`，以及接下来我们需要用到的用于进行数据转换的 `map` 和用于聚合数据的 `reduce`。

![数组转换](https://user-gold-cdn.xitu.io/2018/4/9/162a91fd92263e1a?w=346&h=521&f=png&s=24132)

假设我们需要将数组 `[ 1, 2, 3, 4, 5 ]` 中的每一个元素都转换为较其增 2 的数值，也就是说要给每一个元素做 `+ 2` 的操作，那么我们就可以使用 `array.map(callback)` 方法来实现。

```
const array = [ 1, 2, 3, 4, 5 ]

const addedArray = array.map(function(x) {
  return x + 2
})

console.log(addedArray) //=> [3,4,5,6,7]

```

当然我们也可以用来做不同数据类型之间的转换，比如由 ASCII 码组成的数组 `[ 72, 101, 108, 108, 111, 32, 87, 111, 114, 108, 100 ]`，我们需要把它转化为对应的字符串数组就可以这样做。

```
const asciiArray = [ 72, 101, 108, 108, 111, 87, 111, 114, 108, 100 ]

const charArray = asciiArray.map(function(ascii) {
  return String.fromCharCode(ascii)
})

console.log(charArray) //=> ["H", "e", "l", "l", "o", "W", "o", "r", "l", "d"]

```

#### 聚合

什么是聚合？或许我们刚开始听到这个词的时候会“一脸懵逼”，而看到 `reduce` 这个方法名的时候则更是头疼了。其实不用担心，这个方法在我们很多年前学习加法运算的时候就已经使用过了。不相信吗？我们来接着看。

我们来回忆一下当年我们是怎么一步一步做 `1 + 2 + 3 + 4` 这道加法运算题的。根据从左到右的运算法则，我们需要首先计算 `1 + 2` 等于 `3`；然后将这个和再与 `3` 相加得到 `6`，并且以此类推最终得到了这个式子的结果为 `10`。

![reduce](https://user-gold-cdn.xitu.io/2018/4/7/162a0219662c9951?w=521&h=261&f=png&s=12977)

而其实这个过程就是 `reduce` 方法的过程。我们换做使用 JavaScript 来实现便是这样的。

```
const array = [ 1, 2, 3, 4 ]

const sumResult = array.reduce(function(left, right) {
  return left + right
})

console.log(sumResult) //=> 10

```

为此我们就可以对这个聚合结果做一个小封装，比如求数组中数值相加的和与相乘的积。

```
const array = [ 1, 2, 3, 4 ]

function sum(array) {
  return array.reduce(function(left, right) {
    return left + right
  })
}

function multi(array) {
  return array.reduce(function(left, right) {
    return left * right
  })
}

console.log(sum(array))   //=> 10
console.log(multi(array)) //=> 24

```

甚至我们还可以将这个封装的程度再往抽象的方向进一步发展，这其中涉及了一些函数式编程的概念。

```
const array = [ 1, 2, 3, 4 ]

function reduceFn(callback) {
  return function(array) {
    return array.reduce(callback)
  }
}

const sum = reduceFn(function(left, right) {
  return left + right
})
const multi = reduceFn(function(left, right) {
  return left * right
})

console.log(sum(array))   //=> 10
console.log(multi(array)) //=> 24

```

#### 又一个“题外话”：Lodash 工具库

[Lodash](https://Lodash.com) 是一个包含了非常多实用工具函数的 JavaScript 工具库，其中也包括了非常多我们在对对象型、数组型数据进行处理时需要用到的函数。我们在实际开发中可以借助 Lodash 以大大提高我们的开发效率。

安装 Lodash 工具库的方法有很多，如果你目前正在浏览器环境中使用 JavaScript 进行开发，那么就可以在 HTML 文件的 `head` 部分中加入以下代码以加载 Lodash 工具库。

```
<script type="application/javascript" src="https://cdn.staticfile.org/Lodash.js/4.17.5/Lodash.js"></script>


```

**使用 Lodash 实现数组相加**

正好我们可以使用 Lodash 来实现我们前面所用到的数组相加。

```
const array = [ 1, 2, 3, 4 ]

const sumResult = _.sum(array)

console.log(sumResult) //=> 20


```

是的！Lodash 早就已经为我们提供了这个用于计算数值数组中所有元素相加的函数了。当然，Lodash 的实用性可不止如此，后面我们可以继续来学习。

### 4.1.4 “更强”的数组

我们前面接触到的数组基本都是只包含了像数值、字符串这样的简单元素。那么如果说数组所包含的元素是更为复杂的对象，甚至是数组呢？实际开发经验告诉我们，除了包含数值、字符串这样的简单数据外，我们还需要“更强”的数组以对付更复杂的需求。

比如我们需要使用一个数组来存储某个部门的人员数据，那么该数组中的元素就应该代表了该部门中的每一个人的抽象映射。而为了能够表达一个人的各种属性，我们需要用对象来完成这样的需求，也就是说我们需要让对象成为数组的元素内容。

```
const crew = [
  {
    name: 'Peter',
    gender: 'male',
    level: 'Product Manager',
    age: 32
  },
  {
    name: 'Ben',
    gender: 'male',
    level: 'Senior Developer',
    age: 28
  },
  {
    name: 'Jean',
    gender: 'female',
    level: 'Senior Developer',
    age: 26
  },
  {
    name: 'Chang',
    gender: 'male',
    level: 'Developer',
    age: 23
  },
  {
    name: 'Siva',
    gender: 'female',
    level: 'Quality Assurance',
    age: 25
  }
]


```

而当我们需要表达一个抽象的二维空间（比如数学中的直角坐标系）甚至更高维度空间中的许多点的集合时，每一个点都可以使用一个向量来表示其在对应空间中的位置，比如 `[ 3, 5 ]`。那么自然地，用于表达这些点的集合的数组就是一个以数组为元素的数组了。

```
const points = [
  [ 1, 1 ],
  [ 2, 3 ],
  [ 3, 5 ],
  [ 4, 7 ],
  [ 5, 10 ],
  [ 6, 15 ]
]


```

甚至我们有的时候还需要一个数组中有着不同类型的元素，比如混杂着字符串和数值。

```
const array = [
  [ 'Hello', 1 ],
  [ 'World', 1 ]
]


```

这些更复杂的数组有什么实际的用途，我们下一节见分晓。

## 小结

数组是现实世界中绝大部分数据的主要呈现形式，学会如何灵活地使用数组类型的数据，对数组本身进行测量、对数组中的元素进行操作，那么你就已经可以非常自豪地大声说：我已经踏入了数据科学的大门了！

### 习题

1.  将数组 `[ 1, 2, 3, 4, 5 ]` 转换为 `[ 'a1', 'a2', 'a3', 'a4', 'a5' ]`；
2.  将数组 `[ 1, 2, 3, 4, 5 ]` 转换为 `[ 'a1', 'b2', 'c3', 'd4', 'e5' ]`；
3.  将数组 `[ 1, 2, 3, 4, 5 ]` 转换为 `[ 1, 4, 9, 16, 25 ]`；
4.  查询 JavaScript 中 `Array.prototype.map` 方法的详细文档，并将数组 `[ 0, 0, 0, 0, 0 ]` 转换为 `[ 'A', 'B', 'C', 'D', 'E' ]`；
5.  提取数组 `[ 1, 2, 3, 4, 5 ]` 中的 `[ 2, 3, 4 ]`。

# 第 5 节 基本数据处理 · 基本统计

学习了如何对 JavaScript 中的数组数据进行操作之后，我们就要回到刚开始选择购买这本小册的目的了：使用 JavaScript 开发灵活的数据应用。既然说是数据应用，那么便离不开统计计算，而数组就可以说是统计计算中的“第一要素”。

## 5.1 基本统计方法

我们经常能在各种地方听到这样的词语“平均”、“绝大部分”、“百分之三十”，这些都可以在统计学中找到对应的东西。比如“平均”就是平均值，或更专业的“**数学期望值**”，而“绝大部分”对应的就是“**众数**”。这些我们都可以将它们统称为数列的**数学特征值**。

### 5.1.1 平均值

如果没有学习过概率论的话，就可能会对平均值和数学期望值之间的关系和区别有所疑惑，那么我们这里就可以先简单地补一课。

数学期望值指的是在概率论中，一个数值集合总体中各种**可能性**的结合。举一个“栗子”，一个袋子中装有若干来自我国北方的板栗，以及若干来自我国南方的锥栗。那么经过**无限次**取出、记录并放回之后，我们可以假设计板栗为 `-1`，计锥栗为 `1`，经过简单的统计计算得出取出样本中板栗的概率 ![P_{\alpha}](https://juejin.im/equation?tex=P_%7B%5Calpha%7D) 为 ![\frac{3}{5}](https://juejin.im/equation?tex=%5Cfrac%7B3%7D%7B5%7D)，而锥栗的概率 ![P_\beta](https://juejin.im/equation?tex=P_%5Cbeta) 为 ![\frac{2}{5}](https://juejin.im/equation?tex=%5Cfrac%7B2%7D%7B5%7D)。

值

板栗（`-1`）

锥栗（`1`）

概率 ![P](https://juejin.im/equation?tex=P)

![\frac{3}{5}](https://juejin.im/equation?tex=%5Cfrac%7B3%7D%7B5%7D)

![\frac{2}{5}](https://juejin.im/equation?tex=%5Cfrac%7B2%7D%7B5%7D)

根据数学期望的计算公式可得，该袋子中栗子的期望值为 ![\text{-}\frac{1}{5}](https://juejin.im/equation?tex=%5Ctext%7B-%7D%5Cfrac%7B1%7D%7B5%7D)。

![E=\text{-}1\times\frac{3}{5}+1\times\frac{2}{5}=\text{-}\frac{1}{5}](https://juejin.im/equation?tex=E%3D%5Ctext%7B-%7D1%5Ctimes%5Cfrac%7B3%7D%7B5%7D%2B1%5Ctimes%5Cfrac%7B2%7D%7B5%7D%3D%5Ctext%7B-%7D%5Cfrac%7B1%7D%7B5%7D)

那么如果说我们假设这“**无限次**”的取出就是 **5** 次的话，就可以用这样的一个数组来表达记录的结果：`[ -1, 1, -1, -1, 1 ]`，其中板栗 3 次，锥栗 2 次。使用我们以往学习平均数的计算方法来计算的话就是。

![m=\frac{\text{-}1+1+\text{-}1+\text{-}1+1}{5}=\text{-}\frac{1}{5}](https://juejin.im/equation?tex=m%3D%5Cfrac%7B%5Ctext%7B-%7D1%2B1%2B%5Ctext%7B-%7D1%2B%5Ctext%7B-%7D1%2B1%7D%7B5%7D%3D%5Ctext%7B-%7D%5Cfrac%7B1%7D%7B5%7D)

可以发现其实数学期望值的计算方法和平均值的计算方法是非常相似的。不过从数学概念上，平均数是指在有限的样本空间内对样本的平均数值，而数学期望值是指总体空间中各种可能性（比如在这个“栗子”中的板栗和锥栗）的可能性结合。

扯了那么远，其实我们会发现在 JavaScript 中，我们使用 Lodash 来实现平均值的计算是那么的简单。

```
const array = [ 1, 2, 3, 4, 5 ]

const mean = _.mean(array)

console.log(mean) //=> 3

```

结合转换聚合的概念，我们来计算前面 4.1.4 节中部门人员数据的人员平均年龄。

```
const crew = [
  {
    name: 'Peter',
    gender: 'male',
    level: 'Product Manager',
    age: 32
  },
  {
    name: 'Ben',
    gender: 'male',
    level: 'Senior Developer',
    age: 28
  },
  {
    name: 'Jean',
    gender: 'female',
    level: 'Senior Developer',
    age: 26
  },
  {
    name: 'Chang',
    gender: 'male',
    level: 'Developer',
    age: 23
  },
  {
    name: 'Siva',
    gender: 'female',
    level: 'Quality Assurance',
    age: 25
  }
]

const ages = _.map(crew, function(person) {
  return person.age
})
const meanAge = _.mean(ages)

console.log(meanAge) //=> 26.8

```

当然 Lodash 还提供了更为简单的函数来应对这样的数组计算。

```
const meanAge = _.meanBy(crew, 'age')

// 或者

const meanAge = _.meanBy(crew, function(person) {
  return person.age
})

```

### 5.1.2 众数

除了平均数以外，我们最常用到的数学特征值恐怕就要数众数了，因为我们常常希望知道在一个群体中的最大多数是什么。而这就意味着众数并不代表只能用在数值数列上，也可以用于其他可以对比相同的元素上，比如字符串。

虽然说在众数计算中，除了先计算出所有可能性的频次以外，还可以使用[摩尔投票算法](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)（Boyer–Moore majority vote algorithm）。而摩尔投票算法的前提是数列中**绝对存在一个频次最高的元素**，即主要元素（Majority Element）。摩尔投票算法的好处是相比于使用哈希（Hash、Map、Object等）进行频次统计经典方法的非线性时空复杂度，摩尔投票算法有非常良好的 O(n) 时间复杂度和 O(1) 的空间复杂度。但由于在很多情况下我们并不仅仅是想要单一的一个众数，而是想要“频次出现最多的若干个情况”，所以我们这里暂不会对这种算法进行介绍。

而既然我们需要使用最经典的逐一计算每种可能性的频次，那么就让我们再次回到第 2 节中我们提出的词频统计吧。

### 5.1.3 词频统计

一般来对数组中的各种可能性进行频次统计，是先创建一个用于记录频次的对象，然后通过遍历数组中的每一个元素，并将其一个一个放入到前面创建的对象中以记录频次。但是自从我们学会了使用 Map 和 Reduce 开始我们就可以使用更直观的方式进行统计。

首先把每一个词使用变换函数将其变成一个以单词为第一元素，以 `1` 为第二元素的数组，我们可以将其称为 **Tuple**，相当于对象中的一个键值对。

```
"hello" -> [ "hello", 1 ]

```

既然我们将单词转换成了多个 Tuple 键值对的键，那么我们是不是可以使用这个特性更方便地进行 Reduce 呢？是的，我们可以将其称为 `reduceByKey`。在一般情况下的 Reduce 函数是用于遍历整个数组的，而 `reduceByKey` 则是根据 Tuple 集中的键首先进行一次分类组合，将具有相同键的值进行组合，然后对每一个组合集进行单独遍历。

不幸的是，无论是原生 JavaScript 中还是 Lodash 中并没有这样的 API。但是我们却可以使用 Lodash 的函数进行组合，对 Lodash 进行拓展。

```
_.reduceByKey = function(tuples, reduceCallback) {
  const grouped = _.groupBy(tuples, function(tuple) {
    return tuple[0]
  })
  
  return _.toPairs(_.mapValues(grouped, function(tuples) {
    return _.chain(tuples)
      .map(function(tuple) {
        return tuple[1]
      })
      .reduce(reduceCallback)
      .value()
  }))
}

```

![Map-Reduce-WordCount](https://user-gold-cdn.xitu.io/2018/4/7/162a021b2697a82d?w=601&h=340&f=png&s=35627)

我们在第 2 节中通过使用正则表达式将 MIT 开源协议中的一部分内容进行了数据清洗和分割。

```
const originalText = 'Permission is hereby granted, ...'

const words = originalText.toLowerCase().match(/\w+/g)

```

那么我们按照 Map 和 Reduce 的思路进行一下词频统计，首先将单词字符串转换为 Tuple，然后再使用 `reduceByKey` 进行聚合统计。

```
const tuples = words.map(function(word) {
  return [ word, 1 ]
})

const wordCountResult = _.reduceByKey(tuples, function(left, right) {
  return left + right
})

console.log(wordCountResult) //=> [["permission", 2], ["is", 4], ["hereby", 1], ["granted", 1], ["free", 1], …]

```

现在我们有了一个统计了不同单词在 MIT 开源协议中频次的统计结果，接下来让我们继续进行下一步操作。

### 5.1.4 排序

既然我们已经对不同的单词频次进行了统计，那么我们应该要知道哪些单词出现次数最多，哪些出现最少吧？所以我们需要对上面的统计结果按照频次从大到小或从小到大排序。

> 排序算法有非常多种，但是这并不在我们的讨论范围内，如果感兴趣的话，可以参考 Wikipedia 中的[排序算法](https://zh.wikipedia.org/wiki/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95)页面。

我们可以直接使用 JavaScript 中的 `array.sort` 方法进行简单的排序。

`array.sort` 方法需要传入一个回调函数，这个回调函数是用于比对两个元素，以确定两者之间的排序。而在这过程中也可以将元素中真正需要用于排序的“元素”取出，也可以先将元素进行转换。比如本小册 4.1.4 小节中的 `crew` 数组中的 `age` 是用于排序部门人员年龄大小的元素。而这里则是每一个 Tuple 中的值，也就是数组的第二元素。

```
const sorted = wordCountResult.sort(function(leftTuple, rightTuple) {
  return rightTuple[1] - leftTuple[1]
})

console.log(sorted) //=> [["the", 14], ["or", 9], ["software", 9], ["of", 8], ["to", 8], …]

```

### 5.1.5 裁剪

有了排序之后的统计结果，我们就可以从中取出一部分用于展示统计结果了，比如“频次最多的 5 个单词”和“频次最少的 5 个单词”等。

这里我们可以用到 JavaScript 原生的 `array.slice`，正如这个方法的字面意思那样，这个方法的用途就是对数组进行切片，比如前 5 个元素的切片、后 5 个元素的切片和中间某个范围的切片等。

比如我们需要知道词频统计结果中，频次最多的 5个单词是哪些。那么我们就可以对已经经过从大到小排序的统计结果中，选取前 5 个元素的切片。

```
const top5 = sorted.slice(0, 5)

```

`array.slice` 方法传入两个参数，一个是目标切片的起始位置，一个是结束位置。选取前 5 个元素也就是选取从下边为 0 的元素开始到下标为 5 的前一个元素结束。是不是觉得很复杂？那我们可以选择使用 Lodash 提供的 `_.take` 函数。

`_.take` 函数除了第一个参数为被处理数组外，还接受一个参数为个数 `n`，也就是该函数会返回数组中前 `n` 个元素的切片。

```
const top5 = _.take(sorted, 5).map(function(tuple) {
  return tuple[0]
})

console.log(top5) //=> ["the", "software", "or", "to", "of"]

```

而如果需要知道出现频次最少的 5 个单词，那就取统计结果的后 5 个元素即可。而 Lodash 同样提供了一个 `_.takeRight` 函数，用于从数组的右端（也就是末端）开始选取元素。

```
const minimal5 = _.takeRight(sorted, 5)

console.log(minimal5) //=> [["from", 1], ["out", 1], ["connection", 1], ["with", 1], ["above", 1]]

```

## 小结

在学会了如何使用数组存储和操作数据之后，在本节中我们学会了如何使用一些基本的数学和统计知识来对数组中的元素进行基本的运算。非常好，我们要保持好这样的学习节奏。

到此我们已经完成了 JavaScript 中基本数据结构及其基本处理方法的学习，接下来我们正式要开始学习较为复杂的数据处理、数据可视化以及动态数据应用的开发了，你准备好了吗？

### 习题

1.  设某次投票结果为如下 `[ 1, 2, 3, 2, 2, 3, 1, 4, 4, 1, 2, 1, 1, 3, 4 ]`，请统计投票结果并找出票数最多的选项；
2.  假设某一时间记录软件记录下一个人一天 24 小时中每一个小时的工作状态，其中分别以范围为 1 ~ 8 的自然数标识，1 为生产力最差的程度，而 8 则为生产力最佳的状态。而该软件记录了某人一天的数据为 `[ 1, 1, 1, 1, 1, 1, 1, 1, 6, 7, 8, 4, 3, 7, 8, 8, 6, 6, 4, 3, 3, 3, 1, 1 ]`。假设区间 1 ~ 3 为生产力较低，4 ~ 5 为生产力一般，6 ~ 8 为生产力较高。请统计并分析这份数据中一天的工作状态。

# 第 6 节 复杂数据处理 · 使用序列

我们常使用数组作为存储一**系列**数据的方式，而在 JavaScript 中数组是非常强调顺序的一种数据结构。但我们在日常使用的时候，并不是所有的数据都是完全遵守等间距的。可能每两个数据之间从特定的维度上观察是呈现的间断性，而非连续性。

其中最为典型的就是时间序列，从物理传感器传来的数据、智能穿戴设备的记录数据等等都是经常会出现不连续的现象，这些数据从时间维度上观测会发现基本上是无规律间隙性的。那么我们就不能简单地使用数组进行记录。

为了更为准确地记录和表达这些数据，我们需要使用到第 4 节中我们所提到的“更强”的数组。将用于表达顺序的标签从 JavaScript 数组中的下标，改变为对象元素中的某一个标签。

## 6.1 时间序列

时间序列一般用于表达一组建立在时间轴上的数据，比如工业生产设备中传感器所定时记录的数据，但由于传感器设备同样存在误差，而且由于实际应用中也有可能出现不工作的情况，所以真正所记录下来的数据很有可能是断断续续的。

![时间序列](https://user-gold-cdn.xitu.io/2018/4/7/162a021d0b5853c5?w=414&h=89&f=png&s=5731)

在这图中你会看到并不是每一秒钟都会记录有数据，这种数据集我们可以将其称为稀疏序列，虽然我们可以使用插值、拟合等方式将这些空缺的数据补上，但是这涉及更为复杂的数学运算所以不会在本小册的讨论范围之内，感兴趣的同学可以查询“数值计算”相关的文献和书籍进行学习。

回到正题上，我们需要使用“更强”的数组来进行对时间序列的存储，那么自然就意味着数组元素中必定包含用于存储每一个数据点所对应的时间戳（Timestamp）。在 JavaScript 中，一般使用毫秒级 Unix 时间戳作为时间的基本表达方式。毫秒级 Unix 时间戳即以格林尼治时间 1970 年 1 月 1 日 0 点 0 分开始，每经过一毫秒（![\frac{1}{1000}](https://juejin.im/equation?tex=%5Cfrac%7B1%7D%7B1000%7D) 秒）即记数 1。如 2018 年 1 月 1 日 0 时 0 分整点使用毫秒级 Unix 时间戳则为 1514736000000。

在 JavaScript 中我们可以使用 `Date.now()` 方法来获取当前设备中所记录的当前毫秒级 Unix 时间戳。同样的也可以使用 `new Date(timestamp)` 的方式将以数值为表达方式的时间戳转换为以 JavaScript 中用于表达时间（包括日期和时间）的 `Date` 类型。

假设我们有以下记账数据，并以时间序列的形式存储。

时间

类型

价格

Thu Mar 01 2018 08:31:32

餐饮

6.00

Thu Mar 01 2018 11:27:52

餐饮

12.00

Thu Mar 01 2018 18:24:09

餐饮

52.50

Fri Mar 02 2018 09:14:09

餐饮

4.50

Fri Mar 02 2018 11:58:22

餐饮

13.50

Fri Mar 02 2018 22:10:49

餐饮

104.25

这是某人两天的一日三餐记账记录，假设我们使用 JavaScript 的形式进行存储的话就可以使用以带有时间戳 `timestamp` 属性的对象作为数组的元素。

```
let transactions = [
  {
    timestamp: 1519864292535,
    category: '餐饮',
    price: 6.00
  },
  {
    timestamp: 1519874872261,
    category: '餐饮',
    price: 12.00
  },
  {
    timestamp: 1519899849526,
    category: '餐饮',
    price: 52.50
  },
  {
    timestamp: 1519953249020,
    category: '餐饮',
    price: 4.50
  },
  {
    timestamp: 1519963102270,
    category: '餐饮',
    price: 13.50
  },
  {
    timestamp: 1519999849526,
    category: '餐饮',
    price: 104.25
  }
]

```

如果我们将这些数据放到时间轴上，就可以发现这些记录值间歇性地分布在时间轴上，中间的间隔也并非一定。

![time-series-demo](https://user-gold-cdn.xitu.io/2018/4/7/162a021d0b6e7053?w=1625&h=318&f=png&s=27570)

### 6.1.1 处理时间

在时间序列中，顾名思义其最重要的参数便是序列元素中的时间戳。但由于在实际研究和应用中，我们大多并不需要将统计分析的精度精确到毫秒级或是秒级，更多的情况是以每天、每周、每月和每年的方式进行统计。

所以我们在处理时间序列的时候，首先需要做的是如何将时间序列中的高精确度记录数据进行整合，首先聚合出一定时间范围内的平均、总体记录结果。

在 JavaScript 中处理时间，我们可以首先使用 `Date` 类型将以整型为存储介质的时间戳转换为 `Date` 类型。

```
transactions = transactions.map(function(data) {
  data.timestamp = new Date(data.timestamp)
  return data
})

```

这样我们就已经将现有数据中的时间戳变成了可以用于进行详细操作的 `Date` 类型对象，接下来就可以将其进行时间范围的分组操作。

但是跟 JavaScript 中的数组一样，JavaScript 中的 `Date` 对象虽然本身也已经提供了非常多很实用的方法，但是这远远不足以满足我们的实际需求。所以我们这里再次引入一个第三方工具库 Moment.js。

#### 引入 Moment.js

Moment.js 是一个专门用于处理 JavaScript 中 `Date` 类型数据的工具库，它除了提供用于设置和提取时间对象中的各种参数（时、分、秒、日期等）外，还可以根据不同的表达格式进行字符串渲染，得到需要的时间格式。

将以下 HTML 标签直接加入到 `head` 头部中，或者在 CodePen 的设置中加入 Moment.js 的链接即可。

```
<script type="application/javascript" src="https://cdn.staticfile.org/moment.js/2.21.0/moment.min.js"></script>

```

在使用 Moment.js 之前，我们同样需要将时间戳转换为 `Moment` 类的对象，支持直接将以整型的时间戳或 `Date` 类型转换为 `Moment` 对象。

```
transactions = transactions.map(function(data) {
  data.moment = moment(data.timestamp)
  return data
})

```

#### 按天分组

对于记账数据来说，一般来说我们需要进行最小颗粒统计便是以天为单位的计算。那么我们首先就需要对记录数据中的时间戳进行处理，得到对应的日期。

使用 Moment.js 进行日期提取非常简单，Moment.js 允许对时间对象进行格式化。比如我们若需要将时间转换为以 `年-月-日` 为格式的字符串，就可以使用 `moment.format('YYYY-MM-DD')` 进行格式化。

结合前面我们学习过的 LoDash 工具库，我们可以使用 `_.groupBy` 函数进行分组。

```
const transactionsGroupedByDate = _.groupBy(transactions, function(transaction) {
  return transaction.moment.format('YYYY-MM-DD')
})

console.log(transactionsGroupedByDate)
// => {
//  "2018-03-01": [{...}, {...}],
//  "2018-03-02": [{...}, {...}]
// }

```

#### 按周分组

除了按天计算以外，我们对于我们的记账数据往往对每周的开销更为看重。然而实际上如何把时间按周分组确实是一个“技术活”，因为我们往往不能保证每年 1 月 1 日和每个月的第一天都是周日（每周的第一天）。

一般情况下一年有 365 天（闰年有 366 天），以一周 7 天为标准，所以一年就有 ![\frac{365}{7}=52\frac{1}{7}](https://juejin.im/equation?tex=%5Cfrac%7B365%7D%7B7%7D%3D52%5Cfrac%7B1%7D%7B7%7D) 周，也就是一年有 52 个星期多一到两天。

如果严格使用周日为一周的第一天原则，就需要精确到天来确定某一天处在于某一年的第几个星期。当然我们不需要太过于纠结于这个，因为 Moment.js 已经帮我们封装好这样的转换工具了。

在调用 `moment.format(pattern)` 方法时使用 `"WW"` 可以获取两位数的周数（`01` ~ `53`），为了根据周分组我们可以按 `"YYYY-WW"` 作为分组标签。

```
const transactionsGroupedByWeek = _.groupBy(transactions, function(transaction) {
  return transaction.moment.format('YYYY-WW')
})

console.log(transactionsGroupedByWeek)
// => {
//   "2018-09": [{…}, {…}, {…}, {…}, {…}, {…}]
// }

```

#### 按月、年分组

除了按星期作为分组以外，我们也需要看看我们一个月内我们究竟花了多少钱，而且对于个体户来说一个月的收支更是重要。按月分组跟按周分组非常相似，只是在调用 `moment.format(pattern)` 时，将 `"WW"` 改成 `"MM"` 即可。

```
const transactionsGroupedByMonth = _.groupBy(transactions, function(transaction) {
  return transaction.moment.format('YYYY-MM')
})

console.log(transactionsGroupedByMonth)
// => {
//   "2018-03": [{…}, {…}, {…}, {…}, {…}, {…}]
// }

```

按照年来分组则同理，对格式化方式进行更改就可以了。

```
const transactionsGroupedByYear = _.groupBy(transactions, function(transaction) {
  return transaction.moment.format('YYYY')
})

console.log(transactionsGroupedByYear)
// => {
//   "2018": [{…}, {…}, {…}, {…}, {…}, {…}]
// }

```

#### 分组整合

是否觉得我们一个一个时间单位地分别进行分组太麻烦了？不用担心，回想一下我们在进行数组操作的时候，我们曾为数组的操作封装过一个工具，那么我们也同样可以为时间序列封装一个工具来方便我们使用时间序列。

```
function createTimeSeries(timeSeriesArray) {
  const timeSeriesObj = {
    array: timeSeriesArray.map(function(data) {
      data.moment = moment(data.timestamp)

      return data
    }),

    groupByFormat(formatPattern) {
      return _.groupBy(timeSeriesObj.array, function(data) {
        return data.moment.format(formatPattern)
      })
    },

    groupByDate() {
      return timeSeriesObj.groupByFormat('YYYY-MM-DD')
    },

    groupByWeek() {
      return timeSeriesObj.groupByFormat('YYYY-WW')
    },

    groupByMonth() {
      return timeSeriesObj.groupByFormat('YYYY-MM')
    },

    groupByYear() {
      return timeSeriesObj.groupByFormat('YYYY')
    }

    // ...

  }

  return timeSeriesObj
}

const timeSeries = createTimeSeries(transactions)
console.log(timeSeries.groupByMonth())

```

### 6.1.2 时间序列统计计算

我们已经将账单数据按照时间进行了分组，但是当我们打开一个记账软件的时候难道只会看某一天我花了**哪些**钱吗？我自然希望能够知道这一天我花了多少钱、一周内花了多少钱、一个月内花了多少钱、一般是周几的时候花钱最多、一周平均每天花多少钱等等计算结果。

而我们前面已经将数据按周、月进行分组，但是我们同样需要在按周、月分组之后再进行按天分组，因为我们需要看到一个星期、一个月内每天的开销统计。

#### 计算每天开销情况

要计算每天的开销情况，不一定是需要先将数据分组好以后再进行处理，而我们在进行分组的时候就可以直接完成我们需要的统计计算。

首先我们第一步就是需要从知道每天花了**哪些**钱，变成知道每天花了多少钱，那么我们就需要进行求和计算。在第 4 节中我们介绍过 LoDash 工具库中的 `_.sum` 函数，而 LoDash 工具库同时还提供了一个 `_.sumBy` 函数以用于处理我们较为复杂的多维数组。

我们前面定义了一个 `timeSeriesObj.groupByFormat` 方法，该方法返回的结果是一个以 `{ [date]: array }` 为格式的对象（或叫映射集）。为了避免数据产生的大量冗余（重复、不必要的数据），我们可以再定义一个结果对象，将前面的日期集对象以 `map` 属性值存储，并且定义 `dates()` 以返回日期字符串集以便我们后面的使用。

再回到正题上，我们需要得到当前日期集中每一天的开销总和。但是让我们再次思考一个问题，是否一定要让每一天的统计值以实体数据（即内存变量）的方式存储呢？其实不必，我们可以以一种**虚拟映射**的方式表达这样的数据，即定义一个计算函数 `sum(date)`，只有当传入某一日期的时候才会返回该日期的统计结果，以节省内存空间。这些日期字符串我们就可以通过调用 `dates` 取得。

```
function createTimeSeries(timeSeriesArray) {
  const timeSeriesObj = {
  
    // ...

    groupByDate() {
      const groupedResult = {
        map: timeSeriesObj.groupByFormat('YYYY-MM-DD'),

        dates() {
          return _.keys(groupedResult.map)
        },

        sum(date) {
          return _.sumBy(groupedResult.map[date], 'price')
        }
      }

      return groupedResult
    },

    // ...

  }

  return timeSeriesObj
}

const timeSeries = createTimeSeries(transactions)
const groupedByDateSeries = timeSeries.groupByDate()

console.log(groupedByDateSeries.dates())
//=> ["2018-03-01", "2018-03-02"]

const firstDate = groupedByDateSeries.dates()[0]

console.log(groupedByDateSeries.sum(firstDate))
//=> 70.5

```

如果我们需要一次性打出所有日期的统计结果，我们可以简单地灵活使用 `Array.map` 方法即可。

```
groupedByDateSeries.dates().map(function(date) {
  return {
    date: date,
    sum: groupedByDateSeries.sum(date)
  }
})
//=> [
//   { date: "2018-03-01", sum: 70.5 },
//   { date: "2018-03-02", sum: 122.25 }
// ]

```

#### 计算每周开销情况

完成了对每天开销情况的统计以后，我们就可以对更大时间范围的数据进行进一步的统计了，比如我们需要知道一周内的使用情况。那就可以在前面按天计算的前提下完成这个需求。

事实上 Moment.js 库非常的“聪明”，它可以自动检测我们传入的时间参数的格式（整数时间戳、时间字符串、日期字符串等等），并转化为标准的 `Moment` 时间对象。那么这就意味着可以直接传入前面使用 `groupByDate.dates()` 方法所得到的日期集合来进行聚合。

![date-to-week](https://user-gold-cdn.xitu.io/2018/4/7/162a021d267a4771?w=581&h=223&f=png&s=12624)

相比于 `groupedByDate` 直接建立从日期到数据集的直接映射，出于避免数据过度冗余的原则，在进行对星期聚合的时候我们选择从星期到日期的映射，再使用前面 `groupedByDate` 所建立的虚拟映射来完成新的虚拟映射需求。

```
const timeSeriesObj = {

  // ...

  groupByWeek() {
    const groupedByDate = timeSeriesObj.groupByDate()

    const groupedResult = {
      map: _.groupBy(groupedByDate.dates(), function(date) {
        return moment(date).format('YYYY-WW')
      }),

      weeks() {
        return _.keys(groupedResult.map)
      },

      sum(week) {
        const dates = groupedResult.map[week]

       return _.sumBy(dates, function(date) {
          return groupedByDate.sum(date)
        })
      },

      average(week) {
        const dates = groupedResult.map[week]
        const sum = groupedResult.sum(week)

        return sum / dates.length
      }
    }

    return groupedResult
  },

  // ...

}

```

相比前面的 `groupByDate`，`groupByWeek` 还多了一个 `average` 虚拟映射以得到某一星期内每天开销的平均值。学会对星期进行分组聚合以后，对月和对年的实现就由你们来完成哦😃。

#### 更为简单的组合接口

是否觉得目前对于获取每一个范围内的开销总和以及平均日开销太过于复杂？不用担心，经过了前面几节的学习之后，我们已经知道了如何使用函数和对象进行一些逻辑的封装，那么我们自然可以再继续将封装的程度往上堆叠，将 `sum` 和 `average` 从聚合结果中抽出。

```
function createTimeSeries(timeSeriesArray) {
  const timeSeriesObj = {

    // ...

    dates() {
      return timeSeriesObj.groupByDate().dates()
    },

    weeks() {
      return timeSeriesObj.groupByWeek().weeks()
    },

    months() {
      return timeSeriesObj.groupByMonth().months()
    },

    years() {
      return timeSeriesObj.groupByYear().years()
    },

    sum(unit, point) {
      switch (unit) {
        case 'date':
          return timeSeriesObj.groupByDate().sum(point)

        case 'week':
          return timeSeriesObj.groupByWeek().sum(point)

        case 'month':
          return timeSeriesObj.groupByMonth().sum(point)

        case 'year':
          return timeSeriesObj.groupByYear().sum(point)
      }
    },

    average(unit, point) {
      switch (unit) {
        case 'week':
          return timeSeriesObj.groupByWeek().average(point)

        case 'month':
          return timeSeriesObj.groupByMonth().average(point)

        case 'year':
          return timeSeriesObj.groupByYear().average(point)
      }
    }
  }

  return timeSeriesObj
}

const timeSeries = createTimeSeries(transactions)
console.log(timeSeries.sum('month', '2018-03')) //=> 192.75
console.log(timeSeries.average('month', '2018-03')) //=> 96.375

```

#### 题外话：聚合缓存

经过了三层的封装之后，各种的聚合、提取的调用次数变得比较多。而为了能够让程序运行更加顺畅，内存调度更为节约，可以使用懒加载的方式缓存一些经常使用的聚合结果。

一般来说，缓存的存储位置是在封装层级较底层的位置，来进行存储和读取。而我们这里最为底层的封装位置则是最开始的 `groupByFormat` 函数。

我们可以在 `createTimeSeries` 函数中、创建 `timeSeries` 对象之前定义一个 `caches` 对象。然后在 `groupByFormat` 中首先检查 `caches` 对象中是否存在当前 `formatPattern` 的结果缓存，若存在则将其作为当前结果返回；在完成计算后就将其存储到 `caches` 对象中。

```
function createTimeSeries(timeSeriesArray) {
  const caches = {}
  
  const timeSeriesObj = {
    
    // ...
    
    groupByFormat(formatPattern) {
      if (caches[formatPattern]) {
        return caches[formatPattern]
      }

      const result = _.groupBy(timeSeriesObj.array, function(data) {
        return data.moment.format(formatPattern)
      })

      caches[formatPattern] = result

      return result
    },
    
    // ...
    
  }
  
  return timeSeriesObj
}

```

## 小结

在现代社会中，几乎所有的大小单位团体都已经离不开账本系统，无论大至国家公司，还是小至家庭个人，都需要账本来记录和分析收支情况。而你已经在本节中学会了如何使用数组类型来存储这些收支数据，非常棒。

### 习题

1.  请根据已有代码，完成以月聚合和以年聚合的处理方法。
2.  在实际情况中，我们同样需要根据不同的支出分类（`category`）进行分组计算，请完成按分类计算的同时，支持按天、周、月、年进行分拣范围的统计。

# 第 7 节 复杂数据处理 · 树形

什么是树形结构？树形结构无论是在计算机环境中，还是在我们的日常生活中都非常的常见，比如我们电脑中的文件夹结构、比如公司内部的人员结构，都是呈上一级元素和若干下一级元素组成的高维度结构。

![company-tree](https://user-gold-cdn.xitu.io/2018/6/19/16415e4ced2d5d14?w=900&h=350&f=png&s=29513)

为了能够在计算机程序中实现对树形结构的操作处理，工程师们发明了非常多不同种类的树形结构以及适合于它们的各种算法。但是我们这里不需要太深入地了解这些各式各样的结构和算法，我们只需要学习最朴素的简单树形即可。

当然如果有兴趣的话，您也可以通过《算法》、《算法导论》、《编程珠玑》等等经典的算法教材来学习更多树形结构及其相关算法。

## 7.1 创建节点

树形结构是由多个包含子节点内容的节点（Node）所组成的，也就是说树形结构由根节点开始至每一个叶节点为止，都是由同一种数据结构组成的。

一般来说，普通树形结构的节点由一个用于存储节点内容的空间，以及一个用于存储子节点的引用（在其他语言中也可以为指针）的数组所组成。

![Tree-Node](https://user-gold-cdn.xitu.io/2018/6/19/16415e4cea39fc04?w=352&h=241&f=png&s=7477)

### 7.1.1 JavaScript 中的类（Class）

在学习如何创建一个树形结构的节点之前，我们需要首先来学习下如何使用 JavaScript 中的类语法，因为接下来非常多的数据应用开发中都离不开对类的使用。

类（Class）可以比喻为生物学中的物种，自然界中生物种类多得数不清，但是生物学家们却可以将它们逐一使用[生物分类法](https://zh.wikipedia.org/wiki/%E7%94%9F%E7%89%A9%E5%88%86%E7%B1%BB%E5%AD%A6)进行分类，就好比威猛凶悍的狮子、老虎再到惹人喜爱的花猫、橘猫无论从体型、毛色上都有着天差地别，但都同属猫科动物。也好比我们**智人**这一种族也有非常多的“亲戚”，而哪怕是我们智人本身也有黄种人、黑种人、白种人，人与人之间也有着千差万别的多样性。

而在计算机中也同样有着这样的多样性，有着若干个相同性质的对象，但各自却有着不同的特性、内容、参数等等。这就需要类来进行表示和实现这种特性。

我们再次将具体事物抽象化，我们每一个人都有各自的名字，我们都能表达自己的欢迎之词，那么就用一个 `Person` 类来表达我们共同的物种——智人。

```
class Person {
  constructor(name) {
    this.name = name
  }
  
  hello() {
    return 'Hello, my name is ' + this.name
  }
}

const me = new Person('Will')
console.log(me.hello()) //=> Hello, my name is Will

```

`constructor` 为构建函数，定义包含一个 `name` 参数以将其赋予这个**人**实例本身以作为其名字；`hello` 方法用于表达作为智人的友好欢迎之词。

脱离动物世界，回到人类社会文明中来，我们不仅拥有名字，还有着更为复杂的家族关系，我们都有一个家族名（Family Name）也就是我们的姓氏，以组成我们完整的**姓名**。

```
class Person {
  constructor(givenName, familyName) {
    this.givenName = givenName
    this.familyName = familyName
  }
  
  hello() {
    return `Hello, my name is ${this.givenName} ${this.familyName}`
  }
}

const me = new Person('Will', 'Gunn')
console.log(me.hello()) //=> Hello, my name is Will Gunn

```

在这个 `Person` 类中，我们定义了 `givenName` 和 `familyName` 两个属性，而在 JavaScript 的类中同时还允许定义**虚拟属性**，也就是 Getter。比如我们可以将姓与名结合在一起以组成我们的全名。

```
class Person {
  constructor(givenName, familyName) {
    this.givenName = givenName
    this.familyName = familyName
  }
  
  get fullName() {
    return `${this.givenName} ${this.familyName}`
  }
  
  hello() {
    return `Hello, my name is ${this.fullName}`
  }
}

const me = new Person('Will', 'Gunn')
console.log(me.hello()) //=> Hello, my name is Will Gunn

```

类所产生的实例都是一个对象，所以我们在第 3 节中时就已经说明了，对象是一种可以表达万物的技术。非常好，你已经学会了如何使用 JavaScript 的类来表达一种事物了，那么就让我们开始回到正题上，创建一个树形结构中的节点吧。

### 7.1.2 定义节点类型

一般情况下每一个节点都包含一个用于存储内容的容器，我们可以使用一个简单的 `value` 属性来表达；而对于子节点的引用则可以使用一个数组属性 `children` 来承载。

```
class Node {
  constructor(value) {
    this.value = value
    this.children = []
  }
}

const node = new Node('node value')

```

有了节点以后，就需要将多个节点组合起来了，比如将两个节点加入到另外一个节点中以作为其子节点。那么我们可以先为这个 `Node` 类添加一个 `addChild` 方法。

```
class Node {
  // ...
  
  addChild(node) {
    this.children.push(node)
    
    return this
  }
}

```

你可能会问，为什么不直接使用 `node.children.push(childNode)`，而非要在外面包一层 `addChild` 方法？别着急，事情远没有那么简单，我们后面会慢慢讲解。

```
const root = new Node('root')
const node1 = new Node('node 1')
const node2 = new Node('node 2')
const node3 = new Node('node 3')

root.addChild(node1).addChild(node2)
node1.addChild(node3)

```

这段代码中我们定义了四个节点，其中一个包含内容 `root` 的节点作为根节点，而节点 `node 1` 和 `node 2` 作为根节点的子节点，节点 `node 3` 作为节点 `node 1` 的子节点。从而形成了一棵非常简单的树形结构。

![simple-tree](https://user-gold-cdn.xitu.io/2018/6/19/16415e4ce9e8a0ff?w=241&h=251&f=png&s=10198)

非常好，一个简单的树形已经构建出来了，那么接下来我们应该如何使用这棵树呢？一般来说每一种数据结构被发明出来就肯定会有其使用的方法和特征，就如数组结构有其长度特征 `length`，包含数字的数组则有平均值等等数学特征值。那么对于树形结构及其节点来说又有哪些特征值呢？

结构

树（Tree）

名称

翻译

解析

root

根

一棵树的根节点

**结构**

**节点（Node）**

名称

翻译

解析

parent

父节点

一个节点的父节点

children

子节点（复数）

一个节点的子节点集合

siblings

兄弟节点

与某一个节点拥有相同父节点的其他节点

degree

度

以某一节点为根节点的子树的个数，也可以简单地理解为子节点数目

depth

深度

一个节点的深度被定义为该节点到根节点之间边\*数

height

高度

一个节点到距离它最远的叶节点\*\*中间的边数

> \[ \* \] 边：Edge，节点与节点直接的连接被定义为边  
> \[ \*\* \] 叶节点：没有子节点的节点

在这份表格中你发现其实每一个节点还可以包含其父节点的信息，所以在之前的 `Node` 类中我们可以加入一个 `parent` 属性，以存储该节点的父节点。

而在前面定义的 `addChild` 方法中，我们就可以将定义父节点这个任务放在这里了。

```
class Node {
  constructor(name) {
    this.name = name
    this.parent = null // 默认一个节点在产生时为一个无父节点的根节点
    this.children = []
  }
  
  addChild(node) {
    node.parent = this
    this.children.push(node)
    
    return this
  }
}

```

### 7.1.3 扩展节点类型

#### Siblings 兄弟节点

当每一个节点有了其父节点的信息之后，就可以去尝试访问它的兄弟节点了，通过查询 `node.parent.children` 中的节点排除掉自己后便是它的兄弟节点。

```
class Node {

  // ...

  siblings() {
    const self = this
  
    if (this.parent) {
      return this.parent.children.filter(function(node) {
        return node !== self
      })
    } else {
      return []
    }
  }
}

const root = new Node('root')
const node1 = new Node('node 1')
const node2 = new Node('node 2')
const node3 = new Node('node 3')

root.addChild(node1).addChild(node2)
node1.addChild(node3)
console.log(node1.siblings()) //=> [Node{'node 2'}]

```

#### Degree 度

这个特征值的定义比较简单，在树形结构中，每一个节点的 `degree` 值就等于直接与它相连的子节点数。这里我们就可以用到前面学习到的“虚拟属性”了。

```
class Node {
  // ...
  
  get degree() {
    return this.children.length
  }
}

const root = new Node('root')
const node1 = new Node('node 1')
const node2 = new Node('node 2')

root.addChild(node1)
root.addChild(node2)

console.log(root.degree) //=> 2

```

#### Depth & Height 深度与高度

深度（Depth）和高度（Height）可以说是树形结构中比较抽象且很有意思的特征值了。深度的定义为从某一个节点到其所在的树形结构中的根节点所经过边的数目。

就好比上面的例子中，从节点 `node 3` 到节点 `node 1` 最后到根节点 `root` 中间经过了两个边，所以节点 `node 3` 的深度则为 2。这个在 JavaScript 中也是非常好实现的，只需不断检查经过的每一个父节点是否存在继续往根部走的父节点，并记录循环次数即可。当找到了没有父节点的节点时，则该节点就是这棵树的根节点，而循环次数便是目标节点的深度值。

```
class Node {

  // ...

  get depth() {
    let depth = 0
    let currNode = this

    while (currNode.parent != null) {
      depth++
      currNode = currNode.parent
    }

    return depth
  }

}

const root = new Node('root')
const node1 = new Node('node 1')
const node3 = new Node('node 3')

root.addChild(node1)
node1.addChild(node3)
console.log(node3.depth) //=> 2

```

而高度的定义则是以某一个节点为根所形成的树形结构（该树形结构可能是一棵更大的树形结构中的一部分，即子树）中，这个节点到最深的子节点中间经过的边的数目。

而深度和高度的关系，可以用一张图非常清晰的解释。

![](https://user-gold-cdn.xitu.io/2019/1/23/1687965199e9e04b?w=377&h=414&f=png&s=20068)

我们可以发现每一个节点的高度其实就是等于以该节点为根的的子树中，最深的一个子节点的深度。也就是说只要找到最深的一个子节点，并计算当前子树的深度即可。

但是我们前面在计算深度的时候是直接计算整棵树的深度，那么为了能够让每一个子节点都能够计算指定子树的深度，我们需要对前面的代码进行一些修改。

我们可以首先假设每一棵树的根目录都有一个虚拟的父节点 `null`，那么就直接在计算深度时候，将 `currNode.parent != null` 改成 `currNode.parent != root`，当 `root` 为 `null` 的时候将最后的结果加上 1 便是该节点到整棵树根节点的深度。

而为了能够将计算深度的算法使用在计算高度上，我们同时还需要将原本计算深度的算法提取出来，单独作为一个用于计算子树深度的方法。

```
class Node {

  // ...

  getDepthByRoot(root) {
    let depth = 0
    let currNode = this

    while (currNode.parent !== root) {
      depth++
      currNode = currNode.parent
    }

    return depth + 1
  }

  get depth() {
    return this.getDepthByRoot(null)
  }
}

```

那么问题来了，该如何找到一棵子树中的最深子节点呢？一般来说有两种方式，[BFS（Breadth-first Search，广度优先搜索）](https://en.wikipedia.org/wiki/Breadth-first_search)和 [DFS（Depth-first Search，深度优先搜索）](https://en.wikipedia.org/wiki/Depth-first_search)，而因为 DFS 的宗旨就是“不撞南墙不回头”，为了算法实现的简易性，我们这里选择 BFS 作为实现的方式。

如何使用 BFS 找到最深的子节点？那便是逐层给节点编号，直到最后一个就是最深的节点。当然我们也没有必要真的为它们编号，只需按层级顺序找到最深的一个即可。

![BFS](https://user-gold-cdn.xitu.io/2018/6/19/16415e4ceaaa3818?w=853&h=413&f=png&s=40352)

定义一个 FIFO（First In First Out，先进的先出）的队列，将每一个层的节点不断地推入到这个队列中，并不断取出前面被推入的节点，并检查是否有子节点，直到最后一个节点便是最深子节点。

```
class Node {

  // ...

  get height() {
    const queue = [ this ]
    let deepestNode = this

    while (queue.length > 0) {
      const len = queue.length

      for (let i = 0; i < len; ++i) {
        const currNode = queue.shift()

        deepestNode = currNode

        if (currNode.children.length > 0) {
          queue.push(...currNode.children)
        }
      }
    }

    return deepestNode.getDepthByRoot(this)
  }
}

const root = new Node('root')
const node1 = new Node('node 1')
const node2 = new Node('node 2')
const node3 = new Node('node 3')
const node4 = new Node('node 4')
const node5 = new Node('node 5')
const node6 = new Node('node 6')

root.addChild(node1)
root.addChild(node2)
node1.addChild(node3)
node1.addChild(node4)
node2.addChild(node5)
node5.addChild(node6)

console.log(root.height)  //=> 3
console.log(node1.height) //=> 1
console.log(node2.height) //=> 2

```

### 7.1.4 树形节点代码清单

最后我们便得到了一个完整的树形结构节点类，以用于完成一些我们需要的需求实现。

```
class Node {
  constructor(name) {
    this.name = name
    this.parent = null
    this.children = []
  }
  
  addChild(node) {
    node.parent = this
    this.children.push(node)

    return this
  }

  siblings() {
    const self = this
  
    if (this.parent) {
      return this.parent.children.filter(function(node) {
        return node !== self
      })
    } else {
      return []
    }
  }

  get degree() {
    return this.children.length
  }

  getDepthByRoot(root) {
    let depth = 0
    let currNode = this

    while (currNode.parent !== root) {
      depth++
      currNode = currNode.parent
    }

    return depth + 1
  }

  get depth() {
    return this.getDepthByRoot(null)
  }

  get height() {
    const queue = [ this ]
    let deepestNode = this

    while (queue.length > 0) {
      const len = queue.length

      for (let i = 0; i < len; ++i) {
        const currNode = queue.shift()

        deepestNode = currNode

        if (currNode.children.length > 0) {
          queue.push(...currNode.children)
        }
      }
    }

    return deepestNode.getDepthByRoot(this)
  }

  toString(join = true) {
    let parts = [ this.name ]

    if (this.children.length > 0) {
      parts = parts.concat(this.children
        .map(function(node) {
          return node.toString(false)
        })
        .reduce(function(left, right) {
          return left.concat(right)
        })
        .map(function(line) {
          return '  ' + line
        })
      )
    }

    if (join) {
      return parts.join('\n')
    } else {
      return parts
    }
  }
}

```

## 7.2 定义树形结构

完成了节点的定义后，事实上我们已经可以实现树形结构的定义，并将数据存储在节点上了。但就如数组和序列之类的数据结构那样，除了对单个元素的操作以外，还需要对整个数据结构进行处理和计算。

所以我们需要定义一个专用的树形类，命名为 `Tree`，以完成一些需要对整棵树进行的计算。

```
class Tree {

  constructor(root) {
    this.root = root
  }

  addNode(node, parent = this.root) {
    parent.addChild(node)
  }
  
}

```

创建一棵树时首先要传入一个根节点对象，还可以使用 `tree.addNode` 代替直接调用 `node.addChild`，并默认将传入的节点作为根节点的子节点，以便进行管理。

### 7.2.1 查询节点

当一个树形结构被其他程序创建好以后，通过各种方式传入到你的程序中。而你需要知道这棵树中是否包含某一节点时，就需要使用一些算法来实现这个需求了。

我们前面在实现寻找一棵树中最深子节点的时候用到了 BFS 搜索算法来实现为每一层的子节点进行编号，BFS 的好处是可以搜索到树形结构中的大部分分支。但如果说要找到树形结构中的特定的某一个节点，BFS 显然不是最优的方案。

那么从拓扑学的角度上看一棵树形结构，BFS 的概念便是横向搜索，而 DFS 则是纵向搜索，“不撞南墙不回头”。这种搜索方式的好处是在一棵广度非常大的树形结构中，一旦能找到符合的节点，就能结束对其他分支的搜索。

DFS 在实现上也并不困难，从根节点开始，不断往下搜索第一个子节点（因为在本小册中所使用的树形结构节点都只会使用数组来存储子节点，所以自带顺序结构）。如果符合要求则返回该节点，如果不符合则先检查是否存在下一层或检查下一个兄弟节点。这里我们配合使用第 4 节中所封装的 `arrayUtils` 进行实现。

![DFS](https://user-gold-cdn.xitu.io/2018/6/19/16415e4ce88409fc?w=811&h=421&f=png&s=47337)

```
class Tree {

  // ...

  search(validator) {
    const queue = [ this.root ]
    const result = []

    while (queue.length > 0) {
      const currNode = queue.shift()

      if (validator(currNode)) {
        result.push(currNode)
        continue
      }

      if (currNode.children.length > 0) {
        arrayUtils.prepend(queue, ...currNode.children)
      }
    }

    return result
  }
}

const root = new Node('root')
const node1 = new Node('node 1')
const node2 = new Node('node 2')
const node3 = new Node('node 3')
const node4 = new Node('node 4')
const node5 = new Node('node 5')
const node6 = new Node('node 6')

const tree = new Tree(root)
tree.addNode(node1)
tree.addNode(node2)
tree.addNode(node3, node1)
tree.addNode(node4, node1)
tree.addNode(node5, node2)
tree.addNode(node6, node5)

console.log(tree.search(function(node) {
  return node.name == 'node 4')
})) //=> [ Node{node 4} ]

```

### 7.2.2 统计树形大小

当我们使用各种程序来创建和扩展一棵树形结构之后，我们并不一定知道这棵树中究竟有多少节点，因为这些节点很有可能是由多个相互独立的程序所创建和插入的。那么我们就需要有一个方法来统计一棵树内究竟有多少节点（包括根节点）。

既然需要统计所有的节点，那必定要遍历整棵树以进行计数。而我们已经学会了使用 BFS 和 DFS 两种搜索方式了，那么我们可以使用其中的一种来进行遍历，并去掉其中的判断逻辑以遍历整棵树。这里我们先用我们刚刚学会的 DFS 来进行遍历统计。

```
class Tree {

  // ...

  get size() {
    let size = 0
    const bag = [ this.root ]

    while (bag.length > 0) {
      const currNode = bag.shift()

      size++

      if (currNode.children.length > 0) {
        arrayUtils.prepend(bag, ...currNode.children)
      }
    }

    return size
  }

}

const root = new Node('root')
const node1 = new Node('node 1')
const node2 = new Node('node 2')
const node3 = new Node('node 3')
const node4 = new Node('node 4')
const node5 = new Node('node 5')
const node6 = new Node('node 6')

const tree = new Tree(root)
tree.addNode(node1)
tree.addNode(node2)
tree.addNode(node3, node1)
tree.addNode(node4, node1)
tree.addNode(node5, node2)
tree.addNode(node6, node5)

console.log(tree.size) //=> 7

```

### 7.2.3 树形结构代码清单

```
class Tree {

  constructor(root) {
    this.root = root
  }

  addNode(node, parent = this.root) {
    parent.addChild(node)
  }

  search(validator) {
    const queue = [ this.root ]

    while (queue.length > 0) {
      const currNode = queue.shift()

      if (validator(currNode)) {
        return currNode
      }

      if (currNode.children.length > 0) {
        arrayUtils.prepend(queue, ...currNode.children)
      }
    }
  }

  get size() {
    let size = 0
    const bag = [ this.root ]

    while (bag.length > 0) {
      const currNode = bag.shift()

      size++

      if (currNode.children.length > 0) {
        arrayUtils.prepend(bag, ...currNode.children)
      }
    }

    return size
  }

  get height() {
    return this.root.height
  }
  
  toString() {
    return this.root.toString()
  }
}


```

## 小结

我们已经学会了创建一个树形结构并且如何对其进行操作和检索，虽然现在看来你可能还会对树形结构究竟能完成些什么实际需求感到疑惑。但是别着急，我们接下来会接触更多的数据结构，我们需要通过接触不同的数据结构来进行组合学习，才能更好地理解每一种数据结构的特点和使用场景。

### 习题

请使用 BFS 方法来实现统计树形结构中节点的数量。

# 第 8 节 复杂数据处理 · 关系图谱

我们在上一节中用了一个企业中的人员结构作为树形结构的例子，在这棵树中其实节点与节点之间的边（Edge）所表达的是人员之间的上下级关系。而在日常生活中我们还有非常多并不存在上下层次的关系（人际关系、事物关系等等），就好比我们的人际交往中绝大部分都是平等的。而且在树形结构中节点之间的关系严格遵守不形成环的原则，然而在我们的人际交往关系中，环形关系结构则是必然存在的。

![环形关系结构](https://user-gold-cdn.xitu.io/2018/4/29/16311ff0140c3772?w=271&h=221&f=png&s=9971)

当需要使用计算机编程来实现这种结构的时候，就需要用到**关系图谱**（Graph）。需要注意的是，关系图谱与我们平时所听到的图片是完完全全的两回事。图片是用于表达视觉效果的二维格式（包含点阵图和矢量图），而图形是一种多维的抽象结构，主要用于表达抽象事物之间的关系。

有趣的是我们刚学习完的树形结构其实是一种特殊的关系图谱，树形结构中所规定的是一种节点之间只能有上下级的关系，且不再重复。一棵树形结构中必然存在着根节点和叶节点，但是在关系图谱中却不一定存在明确的头尾节点，它可能是由一群看似杂乱无章的节点相互连接，并且彼此的连接还有一些各式各样的差异，如连接强度不同等。

## 8.1 无向图

在 7.1.2 节中我们定义了一个用于树形结构的节点，在每一个树形节点中拥有一个来自父节点的引用、存储自身数据的空间和存储子节点引用的数组。

不过与树形结构存在区别的是，树形结构中节点之间的连接是带有方向属性的，即从父节点指向其子节点。而关系图谱存在着两种类型，**无向图（Graph）**和**有向图（Directed Graph，或 Digraph）**。其中树形结构正是一种特殊的、每两个顶点之间只有**单向边**的有向图。但正如上面的图所示，实际应用中同样存在着具有双向关系的有向图。所以在关系图谱中并不存在子节点这一概念，取而代之的则是**相邻顶点（Adjacent Vertice）**。

### 8.1.1 定义顶点

一般来说，因为关系图谱具有非常高的复杂性和不确定性，节点与节点之间的关系经常需要发生不同的变化，如果采取像上一节中树形结构中一样，使用引用来表示节点之间的关系，就会产生非常深的引用嵌套。而且 JavaScript 中的引用也无法满足我们表达节点之间关系数据的需求。

所以我们需要另辟蹊径，使用另外一种方式来描述一个关系图谱。我们在上一节的树形结构中，定义一个节点并不需要对其进行编号，因为稳定且单向的层级关系可以使得节点在树形结构中的位置会较为稳定。但这在关系图谱中情况则变得非常的复杂，而导致没办法使用这种简单的方式来完成，那么我们就需要对节点进行编号了。

```
class Vertex {
  constructor(id, property) {
    this.id = id
    this.property = property
  }
}

const me = new Vertex(1, [ 'Will', 'male' ])

```

那么问题来了，如果说我们在创建顶点的时候原数据并不带有能够标识每一个个体信息的标识符，就需要有一个能够产生具有唯一性的的标识符。业界通常会使用如 UUID（Universally Unique Identifier，通用唯一识别码）、数据库自增键值等方式。而我们这里可以简单地定义一个用于创建带有标识符的顶点的函数以方便我们使用。

```
let vertexId = 0
function newVertex(property) {
  return new Vertex(++vertexId, property)
}

const me = newVertex([ 'Will', 'male' ])

```

### 8.1.2 定义边

定义好了关系图谱中的顶点之后，就需要开始定义我们用于表达节点之间关系的边了。而因为 JavaScript 中的引用并不能满足稀疏存储和附带信息的需求，所以我们同样需要为边定义一个类型以创建一个边对象。

```
class Edge {
  constructor(leftId, rightId, property) {
    this.leftId = leftId
    this.rightId = rightId
    this.property = property
  }
}

const will = newVertex({
  name: 'Will',
  gender: 'male'
})
const ru = newVertex({
  name: 'Rrrruu',
  gender: 'female'
})

const relation = new Edge(will.id, ru.id, 'couple')

```

现在我们可以通过顶点对象中的 `id` 属性取得该顶点的标识符，但若需要使用标识符来获取顶点对象本身，就需要别的实体来完成这个需求。而这样的任务我们可以交由关系图谱本身来完成。

### 8.1.3 图

既然我们对关系图谱中的顶点对象进行编号，以便进行检索，那么我们是不是可以使用同样的方式来对边对象进行编号以便进行检索呢？答案自然是肯定的。

相比于顶点对象只会从自身的 `id` 被检索，边对象则会从与边相连的两个顶点被检索，所以更需要进行编号以提升检索的速度。通过对边对象的编号和关系的变换，我们可以整理出顶点与边的关系。

![顶点与边的关系](https://user-gold-cdn.xitu.io/2018/4/29/16311ff01433688d?w=1598&h=814&f=png&s=101488)

整理好这些关系之后，我们就可以通过已经梳理好的逻辑来定义一个关系图谱的 JavaScript 类了。

1.  对顶点进行编号，以优化对顶点的检索；
2.  对边进行编号，并存储好边与顶点的关系。

```
class Graph {
  constructor(vertices, edges) {

    // Vertices
    this.vertexIds = []
    this.vertices = {}

    for (let i = 0; i < vertices.length; ++i) {
      const vertex = vertices[i]

      this.vertexIds.push(vertex.id)
      this.vertices[vertex.id] = vertex
    }

    const edgesWithId = edges.map(function(edge, i) {
      edge.id = i + 1
      return edge
    })
    
    // Edges
    this.edgeIds = []
    this.edges = {}
    this.edgeRelations = {}

    for (let i = 0; i < edgesWithId.length; ++i) {
      const edge = edgesWithId[i]

      this.edgeIds.push(edge.id)
      this.edges[edge.id] = edge
      
      // 初始化顶点与边的关系
      if (typeof this.edgeRelations[edge.leftId] === 'undefined') {
        this.edgeRelations[edge.leftId] = []
      }

      if (typeof this.edgeRelations[edge.rightId] === 'undefined') {
        this.edgeRelations[edge.rightId] = []
      }

      this.edgeRelations[edge.leftId].push(edge.id)
      this.edgeRelations[edge.rightId].push(edge.id)
    }

  }
}

const vertices = [
  new Vertex(1, 'A'),
  new Vertex(2, 'B'),
  new Vertex(3, 'C'),
  new Vertex(4, 'D')
]

const edges = [
  new Edge(1, 2, 1),
  new Edge(1, 3, 2),
  new Edge(2, 4, 1),
  new Edge(3, 4, 1)
]

const graph = new Graph(vertices, edges)

```

![graph](https://user-gold-cdn.xitu.io/2018/4/29/16311ff014b4caf0?w=231&h=231&f=png&s=8876)

### 8.1.4 操作图形

完成了关系图谱的建立后，自然需要将其运用起来以配合算法来解决一些我们所面临的问题。但在实现算法之前自然不可能让算法直接涉及图对象中的内部元素，所以我们也需要先为关系图谱定义一些操作方法，如获取某一个顶点、遍历所有顶点、遍历所有边等。

#### 获取某一个顶点

前面在关系图谱类中使用了 `vertexIds` 存储顶点的标识符和使用 `vertices` 来存储顶点对象。那么要获取图形中的某一个顶点，保险起见首先要确保在 `vertexIds` 中存在该节点标识符，否则就直接返回 `null`。然后再从 `vertices` 中获取该节点的实例对象以返回。

```
class Graph {
  // ...

  getVertex(vertexId) {
    if (!_.includes(this.vertexIds, vertexId)) {
      return null
    }

    return this.vertices[vertexId]
  }
}

```

#### 遍历顶点/边

虽然在 JavaScript 中我们默认所使用的数组都是自带有序特性的，但是在关系图谱的定义中，顶点之间并不存在顺序。所以我们自然也不会允许对图对象中的顶点进行直接的循环操作，而采用回调函数的方式进行循环，以模糊其有序性。

```
class Graph {
  // ...
  
  eachVertices(callbackFunc) {
    const self = this

    return self.vertexIds.forEach(function(vertexId) {
      return callbackFunc(self.vertices[vertexId])
    })
  }
  
  eachEdges(callbackFunc) {
    const self = this

    return self.edgeIds.forEach(function(edgeId) {
      return callbackFunc(self.edges[edgeId])
    })
  }
}

```

### 8.1.5 特征值

我们将关系图谱建立了起来以后，就需要开始对这个图进行一些操作了。首先我们前面就说到树形结构是一种特别的图形结构，那么在树形结构中的一些节点特性在图形结构中也同样适用。

#### Degree

Degree 在树形结构的节点中表示的是某一个节点的子节点数量，而因为在关系图谱中的顶点并不存在“子节点”或“子顶点”的概念，取而代之的则是相邻顶点。而相邻顶点的数量就等于与该顶点相连的边的数量。那么要获取相邻边的数量则首先需要定义一个方法以传入顶点标识符并得到相邻边数组。

```
class Graph {
  // ...
  
  getEdgesByVertexId(vertexId) {
    if (!_.includes(this.vertexIds, vertexId)) {
      return []
    }

    if (!_.has(this.edgeRelations, vertexId)) {
      return []
    }

    const self = this

    return self.edgeRelations[vertexId].map(function(edgeId) {
      return self.edges[edgeId]
    })
  }
}

```

得到相邻边后返回其长度则便是该顶点的度。

```
class Graph {
  // ...
  
  degree(vertexId) {
    return this.getEdgesByVertexId(vertexId).length
  }
}

```

#### 最大的度（Max Degree）

在关系图谱中每一个顶点的度是图论算法中非常重要的计算对象。就好比使用关系图谱描述一个社交群体中人与人的相关关系，每一个人作为一个顶点时，顶点的度则代表了对应的个人社交关系，数量越多则代表该名成员在该群体中的重要性越高。显而易见，如果某一个节点的度最大，则说明他很有可能是这个群体中的核心人物。

寻找一个关系图谱中带有最大度数的顶点并不困难，只需全部先计算出所有顶点的度，然后找出最大数即可。

当然，光是找出最大的度可不能满足算法的需要。除了找出最大的度数以外，自然还需要知道是哪一个顶点具有最大的度数。所以我们需要准备两个函数，一个用于找出带有最大度数的顶点，而另外一个则用于获取其度数。

```
class Graph {
  // ...

  largestVertex() {
    const self = this

    const degrees = self.vertexIds.map(function(vertexId) {
      return {
        degree: self.degree(vertexId),
        id: vertexId
      }

    })

    return self.getVertex(_.maxBy(degrees, 'degree').id)
  }

  maxDegree() {
    return this.degree(this.largestVertex().id)
  }
}

const vertices = [
  new Vertex(1, 'A'),
  new Vertex(2, 'B'),
  new Vertex(3, 'C'),
  new Vertex(4, 'D'),
  new Vertex(5, 'E')
]

const edges = [
  new Edge(1, 2, 1),
  new Edge(1, 3, 2),
  new Edge(2, 4, 1),
  new Edge(3, 4, 1),
  new Edge(1, 4, 2),
  new Edge(4, 5, 3)
]

const graph = new Graph(vertices, edges)
console.log(graph.largestVertex().property) //=> D
console.log(graph.maxDegree()) //=> 4

```

#### 平均度（Average Degree）

在一个关系图谱中，除了最大的顶点度以外，每一个顶点的平均度数也是一个非常重要的数学特征值。在这里我们可以有一个比较巧妙的计算方式，而不需要像上面计算最大度的时候那样子先把所有的顶点度数求出来。这有一个用于计算关系图谱中平均度的公式。

![\overline{D}=\frac{2\times N_e}{N_v}](https://juejin.im/equation?tex=%5Coverline%7BD%7D%3D%5Cfrac%7B2%5Ctimes%20N_e%7D%7BN_v%7D%0A)

其中 ![\overline{D}](https://juejin.im/equation?tex=%5Coverline%7BD%7D) 为一个图中的平均度，而 ![N_e](https://juejin.im/equation?tex=N_e) 是该图中边的数量，![N_v](https://juejin.im/equation?tex=N_v) 则是顶点的数量。这个公式还是非常好理解的，因为一条边同时属于左右两个顶点，所以在计算平均度的时候首先需要将边数乘以 ![2](https://juejin.im/equation?tex=2)，然后再除以顶点的数量即可得到平均度。

```
class Graph {
  // ...

  avgDegree() {
    return 2 * this.edgeIds.length / this.vertexIds.length
  }
}

const vertices = [
  new Vertex(1, 'A'),
  new Vertex(2, 'B'),
  new Vertex(3, 'C'),
  new Vertex(4, 'D'),
  new Vertex(5, 'E')
]

const edges = [
  new Edge(1, 2, 1),
  new Edge(1, 3, 2),
  new Edge(2, 4, 1),
  new Edge(3, 4, 1),
  new Edge(1, 4, 2),
  new Edge(4, 5, 3)
]

const graph = new Graph(vertices, edges)
console.log(graph.avgDegree()) //=> 2.4

```

#### 自环（Self-Loop）

自环是一种十分抽象的概念，其定义是在一个关系图谱内，某一个顶点存在一条边再与自己相连。这种情况我们很难使用一个现实生活中的例子来说明，但是我们可以将一个关系图谱中的每一个顶点看作是事件序列中的某一个事件，顶点间的边则表示多种情况下事件之间的连续关系。那么在这种场景下，顶点的自环就可以被解释为某一个事件的连续发生可能性。

![self-loop](https://user-gold-cdn.xitu.io/2018/5/1/16319f91981d945a?w=251&h=256&f=png&s=11009)

在多种事件顺序发生的可能性中，如果某一个事件的连续发生次数（我们可以使用边的属性值表示）越多，则表示该事件的重要性越强。这种概念在一些对用户行为进行分析的应用中十分重要。

而找到自环的边的方法也非常简单，只需要找到那些左右顶点相同的边即可。

```
class Graph {
  // ...
  
  loops() {
    const self = this

    return self.edgeIds
      .map(function(edgeId) {
        return self.edges[edgeId]
      })
      .filter(function(edge) {
        return edge.leftId === edge.rightId
      })
  }
}

const vertices = [
  new Vertex(1, '1'),
  new Vertex(2, '2'),
  new Vertex(3, '3')
]

const edges = [
  new Edge(1, 1, 3),
  new Edge(1, 2, 1),
  new Edge(1, 3, 1),
  new Edge(2, 3, 2)
]

const graph = new Graph(vertices, edges)
console.log(graph.loops()) //=> [ Edge{ leftId: 1, rightId: 1, property: 3 } ]

```

### 8.1.6 无向图代码清单

```
class Vertex {
  constructor(id, property) {
    this.id = id
    this.property = property
  }
}

class Edge {
  constructor(leftId, rightId, property) {
    this.leftId = leftId
    this.rightId = rightId
    this.property = property
  }
}

let vertexId = 0
function newVertex(property) {
  return new Vertex(++vertexId, property)
}


class Graph {
  constructor(vertices, edges) {

    // Vertices
    this.vertexIds = []
    this.vertices = {}

    for (let i = 0; i < vertices.length; ++i) {
      const vertex = vertices[i]

      this.vertexIds.push(vertex.id)
      this.vertices[vertex.id] = vertex
    }

    const edgesWithId = edges.map(function(edge, i) {
      edge.id = i + 1
      return edge
    })
    
    // Edges
    this.edgeIds = []
    this.edges = {}
    this.edgeRelations = {}

    for (let i = 0; i < edgesWithId.length; ++i) {
      const edge = edgesWithId[i]

      this.edgeIds.push(edge.id)
      this.edges[edge.id] = edge
      
      if (typeof this.edgeRelations[edge.leftId] === 'undefined') {
        this.edgeRelations[edge.leftId] = []
      }

      if (typeof this.edgeRelations[edge.rightId] === 'undefined') {
        this.edgeRelations[edge.rightId] = []
      }

      this.edgeRelations[edge.leftId].push(edge.id)
      this.edgeRelations[edge.rightId].push(edge.id)
    }

  }

  getVertex(vertexId) {
    if (!_.includes(this.vertexIds, vertexId)) {
      return null
    }

    return this.vertices[vertexId]
  }

  eachVertices(callbackFunc) {
    const self = this

    return self.vertexIds.forEach(function(vertexId) {
      return callbackFunc(self.vertices[vertexId])
    })
  }

  eachEdges(callbackFunc) {
    const self = this

    return self.edgeIds.forEach(function(edgeId) {
      return callbackFunc(self.edges[edgeId])
    })
  }

  getEdgesByVertexId(vertexId) {
    if (!_.includes(this.vertexIds, vertexId)) {
      return []
    }

    if (!_.has(this.edgeRelations, vertexId)) {
      return []
    }

    const self = this

    return self.edgeRelations[vertexId].map(function(edgeId) {
      return self.edges[edgeId]
    })
  }

  degree(vertexId) {
    return this.getEdgesByVertexId(vertexId).length
  }

  largestVertex() {
    const self = this

    const degrees = self.vertexIds.map(function(vertexId) {
      return {
        degree: self.degree(vertexId),
        id: vertexId
      }

    })

    return self.getVertex(_.maxBy(degrees, 'degree').id)
  }

  maxDegree() {
    return this.degree(this.largestVertex().id)
  }

  avgDegree() {
    return 2 * this.edgeIds.length / this.vertexIds.length
  }

  loops() {
    const self = this

    return self.edgeIds
      .map(function(edgeId) {
        return self.edges[edgeId]
      })
      .filter(function(edge) {
        return edge.leftId === edge.rightId
      })
  }
}

```

## 8.2 有向图

相比于无向图，有向图则是将无向图中的边加上方向特征，即从原本的一条边即代表两个顶点共同拥有一个平等的关系，变成允许顶点之间存在单向的关系。就好像我们在人际社交中，朋友之间互相认识所以我们是平等的，但是我们也有很多我们只认识他们，他们却不认识我们的人。

### 8.2.1 有向边

因为我们前面所定义的边是不存在方向特性的，所以我们直接使用了 `leftId` 和 `rightId` 来存储与边相连的两个顶点的信息。而有向图的边是带有方向特征的，虽然我们也可以像数学一样定义一个向量的表达方式就是从左到右的（如 ![\overrightarrow{AB}](https://juejin.im/equation?tex=%5Coverrightarrow%7BAB%7D)），直接使用前面所定义的边来表示有向边。

但是出于对程序严谨性和语义的考虑，我们还是需要另外定义一个有向边类型以使用。

```
class DirectedEdge {
  constructor(originalId, targetId, property) {
    this.originalId = originalId
    this.targetId = targetId
    this.property = property
  }
}

```

### 8.2.2 有向图 Digraph

因为使用的边不再是无方向特性的边，所以之前所定义的无向图类型也不能直接当做有向图使用了。有向图和无向图最大的区别就是顶点之间从平行的关系变成了有**出**或**入**的单边关系。出代表着某一个顶点存在一个单向的关系指向另外一个顶点，而入则表示某一个顶点被另外一个顶点所指向。

那么前面使用 `edgeRelations` 来存储顶点与边的关系时则需要加以改动了，我们可以简单地分开 `inEdgeRelations` 和 `outEdgeRelations` 来分别存储顶点与入边、出边的关系。

```
class Digraph {
  constructor(vertices, edges) {

    // Vertices
    this.vertexIds = []
    this.vertices = {}

    for (let i = 0; i < vertices.length; ++i) {
      const vertex = vertices[i]

      this.vertexIds.push(vertex.id)
      this.vertices[vertex.id] = vertex
    }

    const edgesWithId = edges.map(function(edge, i) {
      edge.id = i + 1
      return edge
    })

    // Edges
    this.edgeIds = []
    this.edges = {}
    this.inEdgeRelations = {}
    this.outEdgeRelations = {}

    for (let i = 0; i < edgesWithId.length; ++i) {
      const edge = edgesWithId[i]

      this.edgeIds.push(edge.id)
      this.edges[edge.id] = edge

      if (typeof this.outEdgeRelations[edge.originalId] === 'undefined') {
        this.outEdgeRelations[edge.originalId] = []
      }

      if (typeof this.inEdgeRelations[edge.targetId] === 'undefined') {
        this.inEdgeRelations[edge.targetId] = []
      }

      this.inEdgeRelations[edge.targetId].push(edge.id)
      this.outEdgeRelations[edge.originalId].push(edge.id)
    }
    
  }
}

```

完成了有向图的基本构建后，我们就可以将无向图中的一些数学特征值计算方法应用到有向图中。但其中度的概念在有向图中被分开为入度和出度，所以平均值、最大值等等都需要分别计算。

```
class Graph {
  // ...

  getVertex(vertexId) {
    if (!_.includes(this.vertexIds, vertexId)) {
      return null
    }

    return this.vertices[vertexId]
  }

  eachVertices(callbackFunc) {
    const self = this

    return self.vertexIds.forEach(function(vertexId) {
      return callbackFunc(self.vertices[vertexId])
    })
  }

  eachEdges(callbackFunc) {
    const self = this

    return self.edgeIds.forEach(function(edgeId) {
      return callbackFunc(self.edges[edgeId])
    })
  }

  getInEdgesByVertexId(vertexId) {
    if (!_.includes(this.vertexIds, vertexId)) {
      return []
    }

    if (!_.has(this.inEdgeRelations, vertexId)) {
      return []
    }

    const self = this

    return self.inEdgeRelations[vertexId].map(function(edgeId) {
      return self.edges[edgeId]
    })
  }

  getOutEdgesByVertexId(vertexId) {
    if (!_.includes(this.vertexIds, vertexId)) {
      return []
    }

    if (!_.has(this.outEdgeRelations, vertexId)) {
      return []
    }

    const self = this

    return self.outEdgeRelations[vertexId].map(function(edgeId) {
      return self.edges[edgeId]
    })
  }

  inDegree(vertexId) {
    return this.getInEdgesByVertexId(vertexId).length
  }

  outDegree(vertexId) {
    return this.getOutEdgesByVertexId(vertexId).length
  }

  largestInDegreeVertex() {
    const self = this

    const inDegrees = self.vertexIds.map(function(vertexId) {
      return {
        inDegree: self.inDegree(vertexId),
        id: vertexId
      }
    })

    return self.getVertex(_.maxBy(inDegrees, 'inDegree').id)
  }

  largestOutDegreeVertex() {
    const self = this

    const outDegrees = self.vertexIds.map(function(vertexId) {
      return {
        outDegree: self.outDegree(vertexId),
        id: vertexId
      }
    })

    return self.getVertex(_.maxBy(outDegrees, 'outDegree').id)
  }

  maxInDegree() {
    return this.inDegree(this.largestInDegreeVertex().id)
  }

  maxOutDegree() {
    return this.outDegree(this.largestOutDegreeVertex().id)
  }

  avgInDegree() {
    const self = this

    const totalInEdgesCount = self.vertexIds
      .map(function(vertexId) {
        if (typeof self.inEdgeRelations[vertexId] !== 'undefined') {
          return self.inEdgeRelations[vertexId]
        } else {
          return []
        }
      })
      .map(function(edges) {
        return edges.length
      })
      .reduce(function(a, b) {
        return a + b
      })

    return totalInEdgesCount / this.vertexIds.length
  }

  avgOutDegree() {
    const self = this

    const totalOutEdgesCount = self.vertexIds
      .map(function(vertexId) {
        if (typeof self.outEdgeRelations[vertexId] !== 'undefined') {
          return self.outEdgeRelations[vertexId]
        } else {
          return []
        }
      })
      .map(function(edges) {
        return edges.length
      })
      .reduce(function(a, b) {
        return a + b
      })

    return totalOutEdgesCount / this.vertexIds.length
  }

  loops() {
    const self = this

    return self.edgeIds
      .map(function(edgeId) {
        return self.edges[edgeId]
      })
      .filter(function(edge) {
        return edge.originalId === edge.targetId
      })
  }

}

```

![有向图](https://user-gold-cdn.xitu.io/2018/4/29/16311ff84929c604?w=373&h=243&f=png&s=15547)

如图所示，我们首先建立一个有向图以备后续使用。

```
const vertices = [
  new Vertex(1, 'A'),
  new Vertex(2, 'B'),
  new Vertex(3, 'C'),
  new Vertex(4, 'D'),
  new Vertex(5, 'E')
]

const edges = [
  new DirectedEdge(1, 2, 1),
  new DirectedEdge(1, 3, 2),
  new DirectedEdge(2, 4, 1),
  new DirectedEdge(3, 4, 1),
  new DirectedEdge(1, 1, 3),
  new DirectedEdge(3, 5, 4),
  new DirectedEdge(4, 5, 5)
]

const graph = new Digraph(vertices, edges)

```

## 8.3 有向图的最短路径

有向图的意义在于能够以抽象的方式表示一些实际生活中的事物，人际关系、地点之间路网关系等等。在心理学中有一个非常重要的理论叫做[“六度隔离”（Six Degrees of Separation）](https://en.wikipedia.org/wiki/Six_degrees_of_separation)，而假如我们将一个足够大的人际关系网络使用关系图谱的方式表示出来，那么就可以一探这个理论的究竟了。

而在交通系统中，则可以使用顶点表示地点、交叉路口，用边表示路程，而边的值则可以表示路程的长度。那么这个关系图谱则可以用于计算地点之间的路程，这也是我们所使用的导航系统中的基本原理。

要计算在一个关系图谱中两个顶点之间的最短距离，有非常多的算法用于计算，这里我们介绍一个非常直观和常用的算法——Dijkstra 算法。

Dijkstra 算法是一种适用于有向图的最短路径计算算法，它需要遍历所有的可能性之后，然后返回其中的最短路程。

假设我们使用前面所创建的有向图模型，并将每一条边的距离（边的属性值）作为计算指标，对一整条路径的总距离进行计算。

![加权有向图](https://user-gold-cdn.xitu.io/2018/4/29/16311ff84916e881?w=373&h=258&f=png&s=16872)

但由于 Dijkstra 算法涉及的逻辑十分的复杂，有兴趣的同学可以参考掘金上一篇不错的[介绍文章](https://juejin.im/post/5a38c27d6fb9a045132ad188)进行学习，这里本节仅提供实现代码以供参考学习。

```
class Digraph {
  // ...
  
  // Dijkstra's algorithm
  shortestPath(fromVertexId, toVertexId) {
    const self = this

    const preferQueue = []
    const rootNode = new Node(fromVertexId)
    const candidateTree = new Tree(rootNode)
    
    preferQueue.push(...self.getOutEdgesByVertexId(fromVertexId).map(function(edge) {
      return [ fromVertexId, edge.targetId ]
    }))

    while (preferQueue.length > 0) {
      const pair = preferQueue.shift()
      const parentVertexId = pair[0]
      const currentVertexId = pair[1]

      // Add the edge to the candidate tree
      const parentNodes = candidateTree.search(function(node) {
        return node.name === parentVertexId
      })
      const currentNode = new Node(currentVertexId)
      parentNodes.forEach(function(parentNode) {
        candidateTree.addNode(currentNode, parentNode)
      })

      if (currentVertexId === toVertexId) {
        continue
      }

      // Add the next vertex into the prefer queue
      let outEdges = self.getOutEdgesByVertexId(currentVertexId)

      if (outEdges.length <= 0) {
        continue
      }

      outEdges = outEdges.filter(function(edge) {
        return candidateTree.search(function(node) {
          return node.name === edge.targetId
        }).length === 0
      })

      preferQueue
        .push(...outEdges.map(function(edge) {
          return [ currentVertexId, edge.targetId ]
        }))
    }

    const targetNodes = candidateTree.search(function(node) {
      return node.name === toVertexId
    })

    if (targetNodes.length > 0) {
      const pathsWithDistance = targetNodes
        .map(function(node) {
          const vertexId = node.name
          const path = [ vertexId ]
          let lastNode = node

          while (lastNode.parent != null) {
            path.push(lastNode.parent.name)
            lastNode = lastNode.parent
          }

          return path.reverse()
        })
        .map(function(path) {
          const distance = path
            .map(function(vertexId, index) {
              const nextVertexId = path[index + 1]

              if (typeof nextVertexId === 'undefined') {
                return
              }

              const edge = self.getOutEdgesByVertexId(vertexId)
                .find(function(edge) {
                  return edge.targetId === nextVertexId
                })
              
              return edge
            })
            .filter(function(edge) {
              return typeof edge !== 'undefined'
            })
            .map(function(edge) {
              return edge.property
            })
            .reduce(function(distanceA, distanceB) {
              return distanceA + distanceB
            })

          return { path, distance }
        })
      
      const shortestPath = _.minBy(pathsWithDistance, 'distance')

      shortestPath.path = shortestPath.path.map(function(vertexId) {
        return self.getVertex(vertexId)
      })

      return shortestPath
    } else {
      return {
        path: [],
        distance: Infinity
      }
    }
  }
}

const vertices = [
  new Vertex(1, 'A'),
  new Vertex(2, 'B'),
  new Vertex(3, 'C'),
  new Vertex(4, 'D'),
  new Vertex(5, 'E')
]

const edges = [
  new DirectedEdge(1, 2, 1),
  new DirectedEdge(1, 3, 2),
  new DirectedEdge(2, 4, 1),
  new DirectedEdge(3, 4, 1),
  new DirectedEdge(1, 1, 3),
  new DirectedEdge(3, 5, 4),
  new DirectedEdge(4, 5, 5)
]

const graph = new Digraph(vertices, edges)
console.log(graph.shortestPath(1, 5)) //=>
// {
//   distance: 6,
//   path: [
//     Vertex{ A },
//     Vertex{ C },
//     Vertex{ E }
//   ]
// }


```

## 小结

在本节中，我们学习了如何构建一个没有方向特征的关系图谱，也就是无向图，来表示一些事物之间的平等关系网络；还学习了在无向图的基础上，为顶点之间的边加上方向特性，构成具有传递性的关系网络，以表示一些更为具体的事物关系；并且对一种最短路径寻路算法 Dijkstra 进行了探索。

### 习题

1.  请自行并认真地学习 Dijkstra 算法，并思考如何对 Dijkstra 算法进行变化，使其可以应用在无向图中。
2.  使用加权无向图构建一个你身边朋友圈的关系图谱，并使用习题 1 中所得到的 Dijkstra 算法变种，探索“六度隔离”理论在你身边朋友圈中的适用性。并且通过使用**度**的概念，寻找你身边朋友圈中的“核心人物”。
3.  学习了最短路径计算算法之后，请思考如何寻找一个关系图谱中两个点之间的最长路径。

# 第 9 节 复杂数据处理 · 结构转换（上）

前面我们相继介绍了多种数据结构，它们各自都承担着不同类型数据的承载功能。不同的数据之间有着不同的表现方式，而在实际工作中我们却常常需要将不同的数据类型进行相互转换，以满足不同的需求。

## 9.1 Any ↔ 字符串

在开发数据应用的时候，有大部分的数据都不会是由 JavaScript 或用户的操作实时生成的，更多的是直接从服务端的数据存储设施中提取出来，然后通过网络协议传输到客户端以用于展示。

这样的话我们可以首先引入一个题外话，既然我们知道前端使用的数据大部分都需要通过网络协议从服务端传往前端，那这样一个传输过程就是抽象内容的编码和解编码的过程。而且在计算机科学中，通信协议基本上都是以字符串（或二进制）为基础承载数据结构，也就是说在一个服务端与客户端的通信架构中，会需要将各种数据结构首先转换为字符串，经过了网络的传输过程而达到另一端之后，再以相同的方式转换为原本的数据结构。

![network-transport](https://user-gold-cdn.xitu.io/2018/5/28/163a4bdd7c3eb81f?w=861&h=261&f=png&s=24905)

### 9.1.1 JSON

JSON，全称为 JavaScript Object Notation，是目前最流行的网络数据传输格式之一。相比于 CSV（Comma-Separated Values，逗号分隔值）、XML（Extensible Markup Language，可扩展标记语言）等历史更为悠久的格式化数据传输格式，JSON 同时拥有着易读性强（完全符合 JavaScript 标准）、格式不敏感和轻量化的特点。

```
{
  "name": "Chaoyang Gan",
  "nickname": "iwillwen"
}

```

JSON 是一个 JavaScript 语言标准的子集，它完全可以直接运行在 JavaScript 引擎中。当然因为 JavaScript 语言本身是具有可被攻击的可能性的，所以在解析 JSON 数据内容的时候，并不能直接作为一段 JavaScript 代码运行。

JavaScript 引擎中提供了一个 `eval` 函数以用于运行一段 JavaScript 代码，所以假如一段 JSON 数据内容是绝对安全的，确实可以使用 `eval` 函数当做是 JSON 解析器。

```
const jsonStr = `{
  "name": "Chaoyang Gan",
  "nickname": "iwillwen"
}`

eval('var me = ' + jsonStr)

console.log(me.name) //=> Chaoyang Gan

```

但如果需要解析的 JSON 数据并不能保证安全甚至可以被恶意篡改（通过中间人劫持、XSS 攻击等方式），就会出现非常不安全的情况，严重者会导致用户私密信息被盗取。

```
const somethingImportant = 'some secret'

const jsonStr = `{
  "attack": (function(){
    alert(somethingImportant)
  })()
}`

eval('var me = ' + jsonStr) //=> some secret

```

为了避免这种情况的出现，我们必须使用现代 JavaScript 引擎中提供的或其他可信任的 `JSON.parse` 函数进行解码和 `JSON.stringify` 函数进行编码。

```
JSON.parse(`{
  "attack": (function(){
    alert(somethingImportant)
  })()
}`) //=> SyntaxError: Unexpected token ( in JSON

```

言归正传，通常来说，我们可以把将非字符串类型的数据通过某种算法转换为字符串的过程称为**序列化**（字符串也是一种有序序列），而利用 JSON 格式便是目前最流行的序列化方法之一。

```
const jsonStr = JSON.stringify({
  name: 'Chaoyang Gan',
  nickname: 'iwillwen'
})

console.log(jsonStr) //=> {"name":"Chaoyang Gan","nickname":"iwillwen"}

```

### 9.1.2 直接转换

JSON 格式的好处是将结构不确定的数据转换为字符串格式，但同时也会强行带来可能不必要的内容，比如 JSON 的边界字符（如 `"`、`{}` 等）。在需要转换的目标数据类型是确定的，而且将序列化后的字符串数据进行解析的接收方也是可控的的情况下，可以选择直接对数据进行类型转换。

**数值类型**

在 JavaScript 中所有的对象都会默认带有一个 `toString` 方法，而对于数值类型来说，可以直接使用这个方法来进行向字符串类型的转换。

```
const n1 = 1
const n2 = 1.2

const s1 = n1.toString()
const s2 = n2.toString()

console.log(s1, typeof s1) //=> 1 string
console.log(s2, typeof s2) //=> 1.2 string

```

除了将数值直接转换为字符串之外，我们常常需要实现一个将数据类型的小数点后的值固定在一个长度范围内，比如 `5 -> 5.00` 和 `3.1415 -> 3.14`，这个主要用于表格和图表的展示上。`3.1415` 可以通过数值计算得到需要的 `3.14`，但是 `5` 没办法直接通过计算得到 `5.00`。因为 JavaScript 并不像其他语言那样区分开整型和非整型的数值，所以它提供了一个用于实现这个需求的方法 `Number.toFixed`。这个方法接受一个数值参数，即小数点后的保留位数，一般来说这个参数需要是非负整型数值，当然如果传入一个非整型数值，该方法也会自动取整进行计算。

```
const int = 5
const pi = Math.PI //=> 3.141592653589793 (约等于)

console.log(int.toFixed(2)) //=> '5.00'
console.log(pi.toFixed(2)) //=> '3.14'
console.log(int.toFixed(pi)) //=> '5.000'

```

转换成字符串之后还可以通过 `parseInt` 和 `parseFloat` 将以字符串形式存储的数值转换为整型或浮点型。

```
console.log(parseInt('5.00')) //=> 5
console.log(parseFloat('3.14')) //=> 3.14

```

**布尔型（逻辑型）**

布尔型也就是**真**与**假**（幸亏 JavaScript 并不存在中间态），在 JavaScript 中表现为 `true` 与 `false`。显而易见，这两个值各自都有一个以英文单词来表示的意义，那么我们自然可以非常简单地对其进行转换了。

```
console.log(true.toString()) //=> 'true'
console.log(false.toString()) //=> 'false'

```

但是要将其再转换成布尔型就没那么简单了，因为 JavaScript 中并没有直接提供 `parseBoolean` 这样的函数，而且作为弱类型语言，JavaScript 在进行一些判断时也有不少让人非常费解的“操作”。

```
true == 'true' //=> false
false == 'false' //=> false

true == 1 //=> true
false == 0 //=> true

```

所以一般来说我们可以使用强类型判断 `===` 分别判断一个字符串是否是 `"true"`，不是则为 `false`。

```
function parseBoolean(string) {
  return string === 'true'
}

console.log(parseBoolean('true')) //=> true
console.log(parseBoolean('false')) //=> false

```

**数组**

事实上，我们在第 2 节中就已经接触过字符串中的 `split` 方法，它用于将一个字符串以指定字符串为分隔符分割成一个数组。

```
const str = '1,2,3,4,5'
const arr = str.split(',')

console.log(arr) //=> [ 1, 2, 3, 4, 5 ]

```

对应地，数组也可以进行组合变成一个字符串，使用的是 `Array.join` 方法。

```
const arr = [ 1, 2, 3, 4, 5 ]

console.log(arr.join()) //=> 1,2,3,4,5
console.log(arr.join('#')) //=> 1#2#3#4#5

```

## 9.2 对象 ↔ 数组

我们在第 5 节中介绍对象字面量的时候曾经介绍过，在 JavaScript 中的数组实际上是一个特殊的对象字面量，那么在从属关系上看数组应该是对象字面量的一个子集 ![Array \subseteq Object](https://juejin.im/equation?tex=Array%20%5Csubseteq%20Object)。

但为什么我们这里还是要提到对象和数组之间的互相转换呢？假设我们需要将一个对象字面量中的属性以列表的形式展示出来：

![object-as-list](https://user-gold-cdn.xitu.io/2018/5/28/163a4bdd78d715d4?w=911&h=191&f=png&s=25744)

虽然各种框架都有相关的函数或者工具来完成这个需求，但是为了更好地理解数据结构之间的差异及对其的应用，我们还是需要了解其中如何进行数据格式的转换。

JavaScript 中提供了一个 `Object.keys()` 函数，可以提取出对象的所有属性键，并以数组的形式表示。

```
const object = {
  "name": "Chaoyang Gan",
  "title": "Engineer",
  "subject": "Maths"
}

const keys = Object.keys(object)
console.log(keys) //=> ["name", "title", "subject"]

```

得到了目标对象的属性键数组后，配合数组的 `.map` 方法便可以将每一个属性键对应的值提取出来。

```
const list = keys.map(key => {
  return {
    key, value: object[key]
  }
})

console.log(list)
//=> [
// {key: "name", value: "Chaoyang Gan"},
// {key: "title", value: "Engineer"},
// {key: "subject", value: "Maths"}
// ]

```

当然我们可以将第二层中的对象也使用数组表示。

```
const pairs = keys.map(key => {
  return [ key, object[key] ]
})

console.log(pairs)
// => [
// ["name", "Chaoyang Gan"],
// ["title", "Engineer"],
// ["subject", "Maths"]
// ]

```

同样，我们也可以使用 Lodash 中提供的 `_.toPairs` 方法将对象转换为以双元素为键值对表达方式的数组。

```
const pairs = _.toPairs(object)


```

完成了从对象到数组的转换后自然需要一个将其进行逆转换的方法，可以直接使用 Lodash 中提供的 `_.fromPairs`。

```
const object = _.fromPairs(pairs)
console.log(object)
// => {
// name: "Chaoyang Gan",
// title: "Engineer",
// subject: "Maths"
// }


```

事实上，我们在第 5 节中用过的 `_.groupBy` 函数也是一种将数组转换为对象的方法，但它更多的是为了将数组根据其中的某一个字段或某一种变换结果来进行字典化，而不是单纯地将其进行转换。

**我们需要明确的原则是，数据转换的出发点和目的都是为了服务需求，而不是单纯地将其进行数据结构上的转换，在思考如何对数据进行处理之前，首先要明确目标需求究竟需要怎样的数据形式。** 究竟是需要一个以数值作为元素的数组（如人工神经网络的输入和输出值），还是以对象作为元素类型的数组以用于表格的展示（每一个对象元素代表表格中的一行），或是以列为单位存储的数据框对象（如 ECharts 框架中常用）。

```
// Input data for ANN
const xorArray = [ 1, 0, 0, 1, 1, 0, 1 ]

// Row-base dataset
const rDataset = [
  { name: "iwillwen", gender: "male" },
  { name: "rrrruu", gender: "female" }
]

// Column-base dataset
const cDataset = {
  name: [ "iwillwen", "rrrruu" ],
  gender: [ "male", "female" ]
}


```

## 小结

我们在这一节中学习了字符串、对象以及数组间的相互转化，这些都是比较常见也比较简单的数据转换需求和方法，一般用于数据的预处理和使用过程中的转换步骤。

### 习题

1.  我们分别介绍了两种可以存储一个对象信息的数组格式，请分别实现它们的逆转换过程 `fromList`（用于以 `{ key: "key", value: "value" }` 为元素的数组）和 `fromPairs`。
2.  请分别实现 Row-base dataset 和 Column-base dataset 之间的转换过程。

# 第 10 节 复杂数据处理 · 结构转换（下）

在上一节中，我们学习了如何实现一些简单数据结构的转换，在这一节中我们将继续学习更为复杂的数据格式之间的转换。

## 10.1 数据集

在上一节的最后，我们提到了两种用于存储表格数据的结构：行式数据集（Row-oriented Dataset）和列式数据集（Column-oriented Dataset）。这两种数据集在二维空间中都同样标识了一个矩阵式数据集，但它们存储的方式和适用的范围不一样。

例如，以下这个数据集存储了某公司的一部分人员信息。该数据集包含了五个数据列和五个数据行，其中每一行代表了一个员工的信息，而每一列对应的则是不同的信息维度。

RowId

EmpId

Lastname

Firstname

Salary

001

10

Smith

Joe

40000

002

12

Jones

Mary

50000

003

11

Johnson

Cathy

44000

004

22

Jones

Bob

55000

005

24

Steve

Mike

62000

如果将这个数据集分别使用行式数据集和列式数据集两种数据结构进行存储的话，则将会是以下形式的实际结构。

![datasets](https://user-gold-cdn.xitu.io/2018/6/17/1640ce29a28642cd?w=641&h=433&f=png&s=66145)

**行式数据集**

```
// Row-oriented Dataset
const empsRows = [
  { RowId: '001', EmpId: '10', Lastname: 'Smith', Firstname: 'Joe', Salary: 40000 },
  { RowId: '002', EmpId: '12', Lastname: 'Jones', Firstname: 'Mary', Salary: 50000 },
  { RowId: '003', EmpId: '11', Lastname: 'Johnson', Firstname: 'Cathy', Salary: 44000 },
  { RowId: '004', EmpId: '22', Lastname: 'Jones', Firstname: 'Bob', Salary: 55000 },
  { RowId: '005', EmpId: '24', Lastname: 'Steve', Firstname: 'Mike', Salary: 62000 }
]

```

**列式数据集**

```
// Column-oriented Dataset
const empsColumns = {
  RowId: [ '001', '002', '003', '004', '005' ],
  EmpId: [ '10', '12', '11', '22', '24' ],
  Lastname: [ 'Smith', 'Jones', 'Johnson', 'Jones', 'Steve' ],
  Firstname: [ 'Joe', 'Mary', 'Cathy', 'Bob', 'Mike' ],
  Salary: [ 40000, 50000, 44000, 55000, 62000 ]
}

```

这两种数据集存储结构各有其不同的优势和优化方式，在数据库领域中有分别基于这两种结构实现的不同数据库软件，如基于行式的 [MySQL](https://www.mysql.com) 以及基于列式的 [Apache HBase](http://hbase.apache.org/)。行式数据集有直观、单一行内的数据结构稳定、利于行式切分存储等优点，而列式数据集的好处是可以通过忽略非必要列以加速数据读取、查询等操作。甚至有些框架或者语言中的数据集就是以列式进行存储的，比如在广泛用于统计领域的 R 语言中的数据框 `data.frame`，其中的每一列都是以一个向量 `vector` 进行存储的。

当然讨论数据库和其他语言并不在本小册的范围，所以还是让我们回到正题上来。事实上我们在很多的数据 API 中都会发现，API 所提供的数据结构基本上都是以行式数据提供的。这是因为后端服务所使用的大部分都是行式数据库，再者行式数据在后端程序的处理中也更为方便直接。

### 10.1.1 为什么要使用列式数据集

为什么在前端我们还要使用到这两种数据结构呢，或者说在前端开发中，列式数据集又有哪些应用场景呢？我们再次将目光放回到上面这张员工数据集上，如果要统计数据集中各员工的收入水平，我们可以选用最大公约数（![\text{gcd}](https://juejin.im/equation?tex=%5Ctext%7Bgcd%7D)）再乘以 10 作为约数，然后进行取整的结果作为统计区间。

![w=\text{gcd} * 10 \\
W_i=\lfloor \frac{S_i}{w} \rfloor](https://juejin.im/equation?tex=w%3D%5Ctext%7Bgcd%7D%20*%2010%20%5C%5C%0AW_i%3D%5Clfloor%20%5Cfrac%7BS_i%7D%7Bw%7D%20%5Crfloor)

然而这里我们只需要用到数据集中的 `Salary` 这一个字段，如果该数据集的尺寸远比 ![5 \times 5](https://juejin.im/equation?tex=5%20%5Ctimes%205) 大的话，使用整个数据集进行计算显然会浪费非常多的计算资源（CPU 时间、内存空间、IO 等）。这时候列式数据集的优势便体现出来了，只取该一列的数据进行计算即可。

```
function gcd(a, b) {
  if (b === 0) {
    return a
  }
  
  return gcd(b, a % b)
}

const w = empsColumns.Salary.reduce(gcd) * 10
const W = empsColumns.Salary
  .map(function(s) {
    return Math.floor(s / w)
  })

console.log(W) //=> [4, 5, 4, 5, 6]

```

得到了各数据所落到的区间后，再进行统计，最后得到的结果便可用于图表绘制了。同样，我们可以使用前面编写的 `_.reduceByKey` 进行统计计算。

```
const salaryAnalysis = _.reduceByKey(
  W.map(function(W_i) {
    return [ W_i, 1 ]
  }),
  function(a, b) {
    return a + b
  }
)

console.log(salaryAnalysis)
//=> [
//   ["4", 2],
//   ["5", 2],
//   ["6", 1]
// ]

```

如果要找出不同收入层次的人的名字，需要使用到其他列的数据，那么在列式数据集中该如何使用呢？其实非常简单，无论是在 JavaScript 中的列式数据集还是基于列式的数据库，当需要使用到其他列的时候使用相同的下标即可。

```
const groupedNames = _.mapValues(
  _.groupBy(
    empsColumns.Salary
      .map(function(s) {
        return Math.floor(s / w)
      })
      .map(function(W_i, i) {
        return {
          w: W_i,
          name:`${empsColumns.Firstname[i]} ${empsColumns.Lastname[i]}`
        }
      }),
    'w'
  ),
  function(items) {
    return items.map(_.iteratee('name'))
  }
)

console.log(groupedNames)
//=> {
//   4: [ "Joe Smith", "Cathy Johnson" ],
//   5: [ "Mary Jones", "Bob Jones" ],
//   6: [ "Mike Steve" ]
// }

```

### 10.1.2 行式数据集 → 列式数据集

了解完列式数据集的好处和实际使用方式之后，我们来学习下如何将前端生成或者从后端服务中取得的行式数据集转换为列式数据集。

首先我们要了解数据集并不一定是完全密集的，也就是说某些字段是允许为空的，在以对象字面量作为一行的行式数据集中便有某一个字段不存在或为 `null/undefined`。同样，在列式数据集中也可以使用 `null` 或 `undefined` 来表示空字段。

假设我们并不知道某个行式数据集究竟有哪些字段列，因为很有可能前面所有的数据行中都不存在的某个字段，在最后一行出现了。而且在实际业务开发中很有可能数据并非一次性加载完成，而是通过数据流的形式不断添加的。因此我们需要能够随时检查是否有新字段列产生，如果有，将其添加到目标列式数据集中。

首先定义一个用于初始化列式数据集中新字段的函数，逻辑很简单，检查目标数据集中是否已经存在目标字段，如果不存在将其初始化为一个空数组。

```
function applyColumn(colDataset, columnName) {
  if (!_.has(colDataset, columnName)) {
    colDataset[columnName] = []
  }

  return colDataset
}

```

然后将行式数据集中的每一个对象字面量所包含的字段都插入到对应行列位置上即可。

```
function rowOriented2ColOriented(rowDataset) {
  let colDataset = {}

  rowDataset.forEach(function(row, i) {
    const columnNames = _.keys(row)

    columnNames.forEach(function(columnName) {
      colDataset = applyColumn(colDataset, columnName)
      colDataset[columnName][i] = row[columnName]
    })
  })

  return colDataset
}

const transformedDataset = rowOriented2ColOriented(empsRows)

console.log(transformedDataset)
//=> {
//  RowId: [ '001', '002', '003', '004', '005' ],
//  EmpId: [ '10', '12', '11', '22', '24' ],
//  Lastname: [ 'Smith', 'Jones', 'Johnson', 'Jones', 'Steve' ],
//  Firstname: [ 'Joe', 'Mary', 'Cathy', 'Bob', 'Mike' ],
//  Salary: [ 40000, 50000, 44000, 55000, 62000 ]
// }

```

### 10.1.3 列式数据集 → 行式数据集

当需求变成将列式数据集转换为行式数据集时，需要考虑的技术点也会相应地发生改变。在行式转列式的过程中需要注意的是未知字段列的添加，而列式转行式时则需要注意跳过空字段。

而且因为列式数据集是必须带有顺序的，所以很有可能会出现当前最后一行数据并不是完整的数据，即所有的字段列的长度并不一定相等。

因此在开始遍历每一个字段列之前，需要先检查该数据集究竟有多少个数据行，方法也很简单，就是找出最长的那个字段列。

```
function rowOriented2ColOriented(colDataset) {
  const columnNames = _.keys(colDataset)

  const n = _.max(columnNames.map(function(colName) {
    return colDataset[colName].length
  }))

  const rowDataset = []

  for (let i = 0; i < n; ++i) {
    const row = {}

    columnNames.forEach(function(colName) {
      if (!_.isNil(colDataset[colName][i])) {
        row[colName] = colDataset[colName][i]
      }
    })

    rowDataset[i] = row
  }

  return rowDataset
}

const empsRows = rowOriented2ColOriented(empsColumns)

console.log(empsRows)
//=> [
//   { RowId: '001', EmpId: '10', Lastname: 'Smith', Firstname: 'Joe', Salary: 40000 },
//   { RowId: '002', EmpId: '12', Lastname: 'Jones', Firstname: 'Mary', Salary: 50000 },
//   { RowId: '003', EmpId: '11', Lastname: 'Johnson', Firstname: 'Cathy', Salary: 44000 },
//   { RowId: '004', EmpId: '22', Lastname: 'Jones', Firstname: 'Bob', Salary: 55000 },
//   { RowId: '005', EmpId: '24', Lastname: 'Steve', Firstname: 'Mike', Salary: 62000 }
// ]

```

## 10.2 序列集 & 树形结构 & 关系图谱

假设我们有这样一个数据表，它存储着一些有序序列，比如像下面这种的。

![sequences](https://user-gold-cdn.xitu.io/2018/6/17/1640ce284452b9e5?w=221&h=401&f=png&s=19351)

使用 JavaScript 中的数组进行表达的话，它可能会是这样的。

```
const sequences = [
  [ 'A', 'B', 'C' ],
  [ 'B', 'C', 'D' ],
  [ 'B', 'D', 'E' ],
  [ 'B', 'F', 'G' ],
  [ 'F', 'G', 'H' ],
  [ 'F', 'G', 'H', 'I' ],
  [ 'J', 'G' ],
  [ 'J', 'G', 'H' ],
  [ 'J', 'G', 'H', 'I' ]
]

```

这种数据结构常用于一些事件流程、关系网络等，因为在后端数据库中通常只能用这种形式存储事物之间的联系，而很难直接存储一张关系图谱或者树形结构，但实际业务开发中又需要使用真正的关系图谱或树形结构，所以我们需要懂得如何将离散存储的关系对或关系序列转换为所需要的数据结构。

### 10.2.1 序列集 → 树形结构

当我们将一系列有序序列整合成一个或多个树形结构时，需要注意以下几点：

1.  每个序列的不同首端是否都为独立的根节点；
2.  节点是否可重复；
3.  序列中是否允许存在回环；
4.  每个序列的末端是否都为独立的叶节点。

**每个序列的不同首端是否都为独立的根节点**

每个序列的不同首端是否都为独立的根节点，会直接影响到转换所得的树形结构的数量，我们以下图来理解每个互不相同的首端是与不是独立根节点的区别。

![uniq-roots](https://user-gold-cdn.xitu.io/2018/6/17/1640ce28443241b2?w=766&h=530&f=png&s=43746)

**节点是否可重复**

节点是否可重复决定了，转换的目标数据结构究竟是树形结构还是一个关系图谱，当多个序列中出现相同的节点时，因为顺序的关系它们并不处于同一个分支中，就会变成多个分支中有着相同的节点。

![duplicate-nodes](https://user-gold-cdn.xitu.io/2018/6/17/1640ce283cd1833c?w=751&h=351&f=png&s=26044)

**序列中是否允许存在回环**

当序列集中允许出现重复节点时，很可能会出现同一个序列中有着重复的节点，从而形成回环。而在树形结构中，一旦出现回环树形结构便会变为更为立体的关系图谱结构。

![loop-tree](https://user-gold-cdn.xitu.io/2018/6/17/1640ce283cb81d7f?w=742&h=236&f=png&s=19470)

**每个序列的末端是否都为独立的叶节点**

在树形结构中叶节点的定义为树形结构中没有子节点的节点，但是在序列集转换为树形结构时会产生特殊的情况。当序列 ![A](https://juejin.im/equation?tex=A) 的节点为序列 ![B](https://juejin.im/equation?tex=B) 的前半部分，序列 ![B](https://juejin.im/equation?tex=B) 较序列 ![A](https://juejin.im/equation?tex=A) 的后端多出一个或多个节点时，如果序列 ![A](https://juejin.im/equation?tex=A) 的末端节点被定义为一个独立的叶节点，那么在构建树形结构时如何界定，一个分支中的中间节点（如序列 ![A](https://juejin.im/equation?tex=A) 的末端节点）是原本序列集中的一个叶节点呢？这也是后面我们将要讨论的内容。

![leaf-node](https://user-gold-cdn.xitu.io/2018/6/17/1640ce28444aa4cd?w=871&h=314&f=png&s=23906)

#### 1\. 各首端为独立根节点

当我们所需要转换的序列集中，每一个不同的首端都为独立的根节点时，就意味着不同的首端会成为一个独立的树形结构，从而转换为一个包含多个树形结构的“森林”。

比如本节开头的有序序列集，其中就包含了 4 个不同的首端：A、B、F 和 J。那么这个序列集就可以转换为这样的一个“森林”。

![seqs-to-trees-uniq](https://user-gold-cdn.xitu.io/2018/6/17/1640ce2868273600?w=691&h=321&f=png&s=27575)

这种情况下，我们可以使用一个虚拟的根节点来完成每一个序列的转换：

1.  创建一个虚拟的根节点，所有的实际根节点都以它为父节点；
2.  遍历序列中的每一个节点；
3.  从虚拟的根节点作为父节点开始，检查父节点是否包含序列中当前的节点，若不存在则往父节点中添加当前节点；
4.  以最新的节点作为父节点，遍历到序列中的下一个节点并重复步骤 3，直至当前序列的末端。

我们可以利用下面这张图来更好地理解这个转换算法。

![uniq-roots-steps](https://user-gold-cdn.xitu.io/2018/6/17/1640ce288881f902?w=875&h=751&f=png&s=70029)

配合第 7 节中我们所使用的树形结构代码，来完成这个转换算法，然后使用 `Node.toString()` 将其展示出来以确认是否成功并正确地完成转换。

```
// 虚拟一个根节点
const root = new Node('*')

sequences.forEach(function(sequence) {
  let lastNode = root
  
  sequence.forEach(function(nodeName, i) {
    // 寻找已存在的节点
    const index = lastNode.children.findIndex(function(child) {
      return child.name === nodeName
    })

    if (index >= 0) {
      lastNode = lastNode.children[index]
    } else {
      // 创建节点
      const node = new Node(nodeName)

      lastNode.addChild(node)

      lastNode = node
    }
  })
})

console.log(root.toString())
//=>
// *
//   A
//     B
//       C
//   B
//     C
//       D
//     D
//       E
//     F
//       G
//   F
//     G
//       H
//         I
//   J
//     G
//       H
//         I

```

#### 2\. 各首端不为独立根节点

当序列集中的各首端并不为独立根节点时，整个序列集所转换成的树形结构数量会大大减少，甚至整个序列集都会以一棵树形结构呈现。

![seqs-to-trees](https://user-gold-cdn.xitu.io/2018/6/17/1640ce28906329d8?w=797&h=460&f=png&s=49890)

可以看到，在这个转换得到了两个树形结构，分别为以节点 A 和节点 J 为根节点。进行转换之前我们需要知道的是，序列集很有可能是无序的，也就是说，并非序列集中第一个序列的第一个节点必定是一个根节点。

因此，在转换的过程中需要完成的第一步便是寻找序列集中，既定存在的若干个根节点。回想一下我们在第 7 节和第 8 节中学过的内容，一般来说，如何辨别树形结构和关系图谱中的节点究竟是根节点、中间节点还是叶节点的？

根节点的定义是：没有父节点的节点。那么换过来说，如果要寻找序列集中的根节点，第一步便是对其中的所有节点进行整理统计，最直观的办法是将每个序列拆分成多个节点对，即父节点和子节点的配对关系。

![seq-to-edges](https://user-gold-cdn.xitu.io/2018/6/17/1640ce288a8e352d?w=701&h=301&f=png&s=18633)

针对上面 `sequences` 这种形式的序列集，我们可以通过遍历序列中除了最后一个节点以外的所有节点，并返回与其下一个元素所组成的元组，以表达树形结构中的父子节点关系。

![n_i \rightarrow (n_i, n_{i+1})](https://juejin.im/equation?tex=n_i%20%5Crightarrow%20(n_i%2C%20n_%7Bi%2B1%7D))

```
function isLast(i, length) {
  return (i + 1) === length
}

function seq2Pairs(seq) {
  return seq
    .map(function(node, i) {
      if (isLast(i, seq.length)) {
        return false
      }

      return [ node, seq[i + 1] ]
    })
    .filter(function(pair) {
      return pair && pair.length === 2
    })
}

const pairs = seq2Pairs([ 'A', 'B', 'C' ])

console.log(pairs)
//=> [
//   ["A", "B"],
//   ["B", "C"]
// ]

```

得到了一系列的父子对之后，我们便可以分别统计每一个出现的节点各自的入度和出度，以找出根节点集并提前向虚拟根节点插入这些根节点。

```
// 统计节点的度
function analyzeDegrees(pairs) {
  const analysis = {}

  for (const pair of pairs) {
    const [ left, right ] = pair

    if (!analysis[left]) {
      analysis[left] = {
        in: 0, out: 0
      }
    }
    if (!analysis[right]) {
      analysis[right] = {
        in: 0, out: 0
      }
    }

    analysis[left].out += 1
    analysis[right].in += 1
  }

  return _.toPairs(analysis)
    .map(function([ node, degrees ]) {
      return { node, ...degrees }
    })
}

// 通过判断入度找出根节点
function findRootNodes(analysis) {
  return analysis.filter(function({ in: inDegree }) {
    return inDegree === 0
  })
}

const analysis = analyzeDegrees(pairs)
const rootNodes = findRootNodes(analysis).map(function(result) {
  return result.node
})

console.log(rootNodes)
//=> [ 'A' ]

const root = new Node('*')

for (const nodeName of rootNodes) {
  const node = new Node(nodeName)

  root.addChild(node)
}

```

接下来的处理其实跟序列各首端为独立根节点时很相似，不过有一点区别：在前面的情况中，每一个序列的首端都必定会出现在虚拟根节点的子节点中，因此可以直接从根节点开始操作，而在现在的条件下，每个序列的首端节点并不一定是虚拟根节点的子节点，因此除了一个虚拟根节点以外还需要建立使用第 7 节中的树型结构，并使用 `Tree.search()` 方法来完成上层节点的搜索。

```
const tree = new Tree(root)

// 任务队列
const penddingSeqs = sequences.slice()

while (penddingSeqs.length > 0) {
  const currentSeq = penddingSeqs.shift()

  // 搜索首端节点
  const hit = tree.search(function(node) {
    return node.name === currentSeq[0]
  }).shift()

  // 如果不存在，则将当前序列重新加入任务队列
  if (!hit) {
    penddingSeqs.push(currentSeq)
    continue
  }

  let lastNode = hit
  currentSeq.shift()

  while (currentSeq.length > 0) {
    const currentNodeName = currentSeq.shift()

    const currentNode = lastNode.children.find(function(node) {
      return node.name === currentNodeName
    })

    if (currentNode) {
      lastNode = currentNode
    } else {
      const node = new Node(currentNodeName)
      lastNode.addChild(node)

      lastNode = node
    }
  }
}

console.log(root.toString())
//=>
// *
//   A
//     B
//       C
//         D
//       D
//         E
//       F
//         G
//           H
//             I
//   J
//     G
//       H
//         I

```

### 10.2.2 序列集 → 关系图谱

与树形结构相比，将序列集转换为关系图谱的方法要简单许多。在第 8 节中我们曾说到，关系图谱的存储方式一般会由一个顶点集和一个边集组成，而序列集从数据结构上可以看做是一个更高维度的边集。因此，我们如果需要将序列集转换为关系图谱，只需要将序列集中的所有独立节点提取出来，然后将序列拆分成有向边或无向边并完成去重即可。

可以参考上面提取序列集中父子对的过程，此处不再演示代码，这个任务由你来完成。

## 小结

本节中我们学习了多种复杂数据结构之间的转换，其中也包括了上一节中我们所留下的两种数据集结构之间的转换。

这些数据结构在我们日常的开发中都是非常常见的，而且由于数据存储格式的限制或数据传输的约束，我们往往需要通过另一种方式来存储难以表达的数据结构，如序列集与树形结构、关系图谱的关系。

### 习题

1.  参考 10.2.1 节中第 2 小节里的转换算法，完成序列集到关系图谱的转换。
2.  思考：树形结构作为特殊的关系图谱，我们如何将其一般化，转换为关系图谱呢？

# 第 11 节 基于 ECharts 的基础表达性统计图表 · 散点图与折线图

经过了对 JavaScript 中各种数据结构的学习和应用，我们已经掌握了绝大部分在实际开发中所需要使用到的数据操作技能。而接下来，我们便可以开始将这些技能应用到我们所收集到的数据上，并将经过处理的数据使用可视化数据图表进行展示。

## 11.1 散点图 Scatter

在数据统计处理开发中，最主要的数据类型通常是离散型单值数值类型，比如学校班级中的每一个学生的身高体重信息、记账本中的每一次支出的价格等等。

而就比如班级中的身高体重信息，因为严格意义上人与人之间并没有一定的顺序，所以要展示每一个个体的数据应该选用散点图来展示离散的数值数据。

我们首先假设男生的一般身高范围在 155 厘米到 180 厘米之间，而女生的身高范围在 145 厘米到 170 厘米之间。一般来说两者身高在这两个范围会呈正态分布，但为了方便学习，我们假设性地将其看作是均匀分布。我们可以利用这两个范围随机生成一些学生的身高数据，此处假设男女比例相等即各占 50%。

```
const students = []
const n = 50

const heightRanges = {
  male: [ 155, 180 ],
  female: [ 145, 170 ]
}

function getRandomInt(min, max) {
  return Math.round(min + Math.random() * (max - min))
}

for (let i = 0; i < 50; ++i) {
  const gender = Math.random() > 0.5 ? 'male' : 'female'
  const [ min, max ] = heightRanges[gender]
  
  const student = {
    id: i + 1,
    gender: gender,
    height: getRandomInt(min, max)
  }

  students.push(student)
}

console.log(students)
//=> 
// [
//   { id: 1, gender: "male", height: 157 },
//   { id: 2, gender: "male", height: 165 },
//   { id: 3, gender: "female", height: 157 },
//   { id: 4, gender: "female", height: 169 },
//   ...
// ]

```

## 11.2 ECharts 简单入门

ECharts 是由百度开发并开源的一个基于 JavaScript 和 Canvas（在 4.0 中支持了 SVG 渲染）数据可视化图表工具库，而且目前已经被捐赠与 Apache 基金会，更名为 Apache ECharts。它并不需要开发者有非常丰富的数据可视化经验，便可以利用所提供的[参考实例](http://echarts.baidu.com/examples/)开发出美观、实用、性能优秀的可视化图表。

### 11.2.1 可视化图表基本元素

使用 ECharts 绘制可视化图表需要提供以下几种元素（对应不同的图表组件），以组成一个完整的数据图表。

1.  数据（必需）
2.  数据系列（必需）
3.  坐标轴

除此之外还有如辅助线、标记文本、图例等等元素。ECharts 以图表配置为主要使用方式，使用的时候将所需要展示在图表上的元素加入到图表配置中即可。

### 11.2.2 数据集 `dataset`

从 ECharts 4.0 版本开始，它提供了一个非常适合我们学习和使用的数据集配置方法 `dataset`，它的主要用法是使用我们在第 10 节中所学习过的行式数据集以及列式数据集。

**行式数据集**

```
// Row-oriented Dataset
const empsRows = [
  { RowId: '001', EmpId: '10', Lastname: 'Smith', Firstname: 'Joe', Salary: 40000 },
  { RowId: '002', EmpId: '12', Lastname: 'Jones', Firstname: 'Mary', Salary: 50000 },
  { RowId: '003', EmpId: '11', Lastname: 'Johnson', Firstname: 'Cathy', Salary: 44000 },
  { RowId: '004', EmpId: '22', Lastname: 'Jones', Firstname: 'Bob', Salary: 55000 },
  { RowId: '005', EmpId: '24', Lastname: 'Steve', Firstname: 'Mike', Salary: 62000 }
]

const option = {
  dataset: {
    source: empsRows
  }
}

```

**列式数据集**

```
// Column-oriented Dataset
const empsColumns = {
  RowId: [ '001', '002', '003', '004', '005' ],
  EmpId: [ '10', '12', '11', '22', '24' ],
  Lastname: [ 'Smith', 'Jones', 'Johnson', 'Jones', 'Steve' ],
  Firstname: [ 'Joe', 'Mary', 'Cathy', 'Bob', 'Mike' ],
  Salary: [ 40000, 50000, 44000, 55000, 62000 ]
}

const option = {
  dataset: {
    source: empsColumns
  }
}

```

### 11.2.3 数据系列 `series`

准备好数据集以后，便需要将其与所需要的数据系列（如本节将会介绍的散点图和折线图）进行绑定，使数据可以真正地展示在数据图表上。

```
// 散点图 Scatter
const option = {
  {
    type: 'scatter',
    encode: {
      x: 'Firstname',
      y: 'Salary'
    }
  }
}

```

在这个数据系列中，我们指定了数据系列的类型为 `scatter`，即我们需要的散点图。然后通过 `encode` 绑定前面在 `dataset` 中数据的维度，如 `x` 坐标轴绑定到 `Firstname`，`y` 坐标轴绑定到 `Salary` 上。

### 11.2.4 坐标轴 `axis`

准备好了数据集和用于展示的数据系列之后，因为我们所需要展示的数据图表类型为散点图，所以至少需要一个坐标轴来作为数据的载体，而在一般情况下我们所使用的坐标轴为直角坐标轴（即一个 X 坐标轴和一个 Y 坐标轴）。

```
const option = {
  xAxis: {
    type: 'category' // X 坐标轴数据为名义数据（分类数据）
  },
  yAxis: {
    type: 'value' // Y 坐标轴为计量数据（数值数据）
  }
}

```

### 11.2.5 组合图表元素

我们将上面准备好的三个图表元素组合在一起，然后将得到的图表配置传到 ECharts 的实例中，这里以行式数据集为例。

```
const empsRows = [
  { RowId: '001', EmpId: '10', Lastname: 'Smith', Firstname: 'Joe', Salary: 40000 },
  { RowId: '002', EmpId: '12', Lastname: 'Jones', Firstname: 'Mary', Salary: 50000 },
  { RowId: '003', EmpId: '11', Lastname: 'Johnson', Firstname: 'Cathy', Salary: 44000 },
  { RowId: '004', EmpId: '22', Lastname: 'Jones', Firstname: 'Bob', Salary: 55000 },
  { RowId: '005', EmpId: '24', Lastname: 'Steve', Firstname: 'Mike', Salary: 62000 }
]

const option = {
  dataset: {
    source: empsRows
  },
  xAxis: {
    type: 'category'
  },
  yAxis: {
    type: 'value'
  },
  series: {
    type: 'scatter',
    encode: {
      x: 'Firstname',
      y: 'Salary'
    }
  }
}

```

在 CodePen 中我们创建一个新的 Pen，然后加入一个新的 JavaScript 依赖，详细方法请见第 1 节。

```
https://cdn.staticfile.org/echarts/4.1.0/echarts.min.js

```

要让数据图表展现在页面上，首先得有一个用于承载图表的容器元素，我们在 CodePen 的 HTML 代码框中创建一个简单的 `div` 元素，并在 CSS 代码框中为其定义合适的尺寸样式。

```
<!-- HTML -->
<div id="chart"></div>

```

```
/* CSS */
#chart {
  width: 600px;
  height: 400px;
}

```

然后在 JavaScript 代码框中我们使用 ECharts 工具库的 API 将该元素进行 ECharts 图表的初始化。

```
const chartEl = document.querySelector('#chart')
const myChart = echarts.init(chartEl)

```

最后，将前面准备好的图表配置应用到该 ECharts 实例上，我们便可以在预览框中看到可视化图表的效果了。

```
const option = {
  dataset: {
    source: empsRows
  },
  xAxis: {
    type: 'category'
  },
  yAxis: {
    type: 'value'
  },
  series: {
    type: 'scatter',
    encode: {
      x: 'Firstname',
      y: 'Salary'
    }
  }
}

myChart.setOption(option)

```

![ECharts 简单散点图示例](https://user-gold-cdn.xitu.io/2018/7/21/164bb2ce33f4535f?w=600&h=400&f=png&s=17605)

## 11.3 使用 ECharts 实现散点图绘制

学会了 ECharts 的基本使用方式后，让我们回到正题，学习如何使用 ECharts 绘制一个用于展示班级内各同学身高的散点图。

实际上我们只需要将上面这个图表配置中的数据集换成所需要展示的 `students`，然后将数据系列中的 `encode` 维度绑定更改为学生 ID 和身高信息。

```
{
  type: 'scatter',
  encode: {
    x: 'id',
    y: 'height'
  }
}

```

于是便可以得到一个初步的可视化图表。

![身高图表 - 1](https://user-gold-cdn.xitu.io/2018/7/21/164bbb10d771b392?w=600&h=400&f=png&s=22205)

### 11.3.1 优化图表

虽然我们确实使用了 ECharts 来将我们所生成的数据进行了可视化，但我们也同样发现这个图表并不尽如人意：

1.  图表中数据点都分布在图表的上方，图表的下半部分有一大片的空白区域；
2.  坐标轴上没有任何的提示信息，单从图表数据无法判断数据的语义信息；
3.  除了身高数据以外，数据中还提供了每一位学生的性别信息 `gender`（分别为 `male` 和 `female`），希望能够在图表中有所表示。

我们可以一步一步地来对既有图表进行优化，首先便是解决图表空白区域太多的问题。产生这个问题的原因是因为数据普遍分布在 145 ~ 180 之间，所以 0 ~ 145 这个区间便完全空白。

**拉伸数轴**

要解决这个问题只需要在 Y 坐标轴上让 ECharts 对数轴进行拉伸，去掉空白区域。

```
const option = {
  yAxis: {
    type: 'value',
    scale: true
  }
}

```

![身高图表 - 2](https://user-gold-cdn.xitu.io/2018/7/21/164bbb10d7680342?w=600&h=400&f=png&s=20053)

非常好！接下来让我们继续对图表进行优化，因为我们前面并没有在图表中加入任何提示信息，所以在图表上并没有显示任何关于数据内容的文本说明。

**添加数据信息**

这显然不是一个优秀的可视化图表所应该有的问题，所以我们需要为我们的图表数据加上一些提示信息。我们可以分别在 X 轴和 Y 轴上加入对应数据的名称，并让它们显示在指定的位置。

```
const option = {
  xAxis: {
    type: 'category',
    name: '学号',
    nameLocation: 'middle',
    nameGap: 25
  },
  yAxis: {
    type: 'value',
    scale: true,
    
    name: '身高',
    nameLocation: 'end'
  }
}

```

`name` 属性对应的是指定坐标轴所需要显示的名字（即数据名称），`nameLocation` 为名字的显示方位。详细请参考 [ECharts 配置项文档](http://echarts.baidu.com/option.html#xAxis.nameLocation)。

![身高图表 - 3](https://user-gold-cdn.xitu.io/2018/7/21/164bbb10d7986aaa?w=600&h=400&f=png&s=21051)

最后一个需要优化的东西便是我们希望能够在图表上体现出男生和女生之间的身高差异，这个需要将图表中的数据散点体现出性别的差异。一般来说我们会使用 ECharts 中的图例组件（`legend`）来表示不同的数据分组，但比较遗憾的是目前 ECharts 并不支持直接使用 `dataset` 中的某一个数据进行直接分组（截至 ECharts 版本 4.1.0）。

**数据分组**

要实现这个需求，目前需要将男生的数据和女生的数据分别使用各自的数据系列进行表示，但是因为使用了 `dataset` 来统一集中数据配置，而通过 `encode` 也并不支持对 `dataset` 中的数据维度进行筛选。

所以我们可以另辟蹊径，使用 ECharts 中的另外一个组件视觉映射（`visualMap`）来实现这个功能。一般来说这个组件主要用于表示不同范围或不同程度的数据所对应的不同表现方式（如不同的颜色），比如 0 ~ 10、10 ~ 20、20 ~ 30 等。

但 ECharts 的 VisualMap 组件除了支持区间范围之外，还支持完全匹配某一个值来作为一个区间。那么我们便可以利用这个特性来匹配不同的性别参数，只需要在将其匹配目标指定为我们的性别维度 `gender` 即可。更详尽的关于 VisualMap 使用方法请参考[官方文档](http://echarts.baidu.com/option.html#visualMap)。

```
const option = {
  visualMap: {
    type: 'piecewise', // piecewise 表示的是分段式，continuous 则为连续式
    dimension: 'gender',
    pieces: [
      { value: 'male', label: '男生', color: '#1890ff' },
      { value: 'female', label: '女生', color: '#f5222d' }
    ],
    orient: 'horizontal'
  }
}

```

![身高图表 - 4](https://user-gold-cdn.xitu.io/2018/7/21/164bbb10d784e7f9?w=600&h=400&f=png&s=22908)

## 11.4 折线图 Lines

折线图与散点图相比，虽然都是用于表示一个或多个计量数据，但折线图因为其视觉效果的设计使其更适合用于表示**计量数据随时间或某种特定有序排列的数值变化趋势**。

就好比我们经常能在电视新闻中看到国家统计局会公布国内 GDP 值的环比、同比变化率以及呈现 GDP 值变化的折线图。而在企业中也非常喜欢使用折线图来表示企业的增长，如企业市值的变化等等。

同样是为了方便学习，我们可以假设性地设定一个会随时间年份变化的计量数据，即每一年的数据都有一点差别。而因为折线图所表示的是变化趋势，所以为了迎合该特性，我们在生成随机数据的时候，也可以采用随机生成变化率，而不是直接生成每一个点的数据。

### 11.4.1 生成随机时间序列

假设我们限定每一个单位时间内，当前值较前一个值的变化率绝对值不会超过 ![r \quad (0 < r < 1)](https://juejin.im/equation?tex=r%20%5Cquad%20(0%20%3C%20r%20%3C%201))。

![\left| \frac{T_{i} - T_{i-1}}{T_{i-1}}\right| < r](https://juejin.im/equation?tex=%5Cleft%7C%20%5Cfrac%7BT_%7Bi%7D%20-%20T_%7Bi-1%7D%7D%7BT_%7Bi-1%7D%7D%5Cright%7C%20%3C%20r)

那么我们便可以使用 JavaScript 中用于生成均匀分布在开区间 ![(0, 1)](https://juejin.im/equation?tex=(0%2C%201))（不包含 0 与 1）随机数的 `Math.random()` 生成需要的随机数 ![rand](https://juejin.im/equation?tex=rand)，然后通过以下公式得到一个均匀分布在区间 ![(-r, r)](https://juejin.im/equation?tex=(-r%2C%20r)) 的随机系数。

![\text{coefficient} = 2r(rand - 0.5)](https://juejin.im/equation?tex=%5Ctext%7Bcoefficient%7D%20%3D%202r(rand%20-%200.5))

该公式的推演过程如下。

![random-range](https://user-gold-cdn.xitu.io/2018/7/21/164bd31c470076a9?w=527&h=388&f=png&s=27488)

使用 JavaScript 实现便为如下代码。

```
function randomCoefficient(r) {
  const rand = Math.random()
  const coefficient = (rand - 0.5) * 2 * r

  return coefficient
}

```

这样每一项数据便为上一项数据加上该变化率。

![T_i = T_{i-1}*(1+\text{coefficient}) \quad (i=1,2,\cdots, n)](https://juejin.im/equation?tex=T_i%20%3D%20T_%7Bi-1%7D*(1%2B%5Ctext%7Bcoefficient%7D)%20%5Cquad%20(i%3D1%2C2%2C%5Ccdots%2C%20n))

我们假设数据集的第一项为 100，数据项总数目为 50，得到以下数据生成代码。

```
const X = [ 100 ]
const n = 50 - 1
const r = 0.1

function randomCoefficient(r) {
  const rand = Math.random()
  const coefficient = (rand - 0.5) * 2 * r

  return coefficient
}

for (let i = 0; i < n; ++i) {
  const coefficient = randomCoefficient(r)
  const newValue = X[i] * (1 + coefficient)

  X.push(newValue)
}

console.log(X) //=> [ 100, 95.23, ... ]

const data = X.map(function(x, i) {
  return { time: i + 1, value: x }
})


```

### 11.4.2 绘制折线图

得到了绘制所需要的数据集后，我们便可以将其应用到我们上面所使用到的数据图表中，替换掉原本的散点图数据。当然你也可以直接重新创建一个图表配置，以加深对知识的印象。

```
const option = {
  dataset: {
    source: data
  },
  xAxis: {
    type: 'value',
    name: 'i',
    nameLocation: 'middle',
    nameGap: 25
  },
  yAxis: {
    type: 'value',
    scale: true,
    name: 'x',
    nameLocation: 'end'
  },
  series: {
    type: 'line',
    encode: {
      x: 'time',
      y: 'value'
    }
  }
}


```

![line-chart-1](https://user-gold-cdn.xitu.io/2018/7/21/164bd31c4d3e92ad?w=600&h=400&f=png&s=31407)

### 11.4.3 优化折线图

我们已经得到了一个看着还不错的折线图，但是“图”如其名，数据图表中的线条都是以直线相连的折线。

受数据采集、图表规模等因素的限制和影响，数据点之间的区间有的时候并不是完全空白的。就好比某空气质量传感器每小时整时记录当前的空气质量，那么就在这个时间区间两端的两个空气质量值是否能代表这中间的 58 分钟呢？这在数学上需要使用到插值的方法进行数据的填充，当然这也不在本小册的范围之内，感兴趣的同学可以自行参考《数值分析》相关的教材。

在 ECharts 中折线图直接提供了一个使用方法非常简单的功能，能将原本的折线变成光滑的曲线图，我们只需要在类型为 `line` 的数据系列中加入一项 `smooth` 即可。

```
{
  type: 'line',
  smooth: true,
  encode: {
    x: 'time',
    y: 'value'
  }
}


```

![line-chart-2](https://user-gold-cdn.xitu.io/2018/7/21/164bd45e519a9f82?w=600&h=400&f=png&s=31274)

## 小结

我们在这一节中学习了如何使用 ECharts 创建简单的散点图和折线图，并知道了如何根据实际的需求选择合适的可视化类型。同时我们还学会了如何创建简单的随机数据以满足我们绘制图表的需要。最后还学会了如何对我们所创建的图表一步一步地进行优化，以更好地满足我们的可视化需求。

### 习题

1.  请阅读 ECharts 配置项文档，研究如何将 11.3 中创建的散点图中的数据点根据数值的大小变化点的大小；
2.  请创建两个具有相同时间范围的不同随机时间序列数据，并展示在同一个图表中，其中都采用折线图的方式展示；
3.  请对上一题中你所创建的图表进行优化。

# 第 12 节 · 基于ECharts 的基础表达性统计图表 · 柱状图与饼图

在上一节中我们学习了 ECharts 的基本使用方法以及如何使用 ECharts 绘制散点图与折线图。散点图主要用于表示多个数据点在一维或二维特征空间中的分布情况，而折线图主要用于表示某一个计量数据在一定的顺序范围内的变化情况。

学习了这两种数据可视化图表之后，相信你已经对数据可视化的原理有了一定的认识。数据可视化的出发点永远是数据本身，图表绘制过程中需要通过理解数据所包含的实际意义，并根据需求选择合适的可视化图表。那么，我相信你已经准备好学习接下来更多的数据可视化图表了。

## 12.1 柱状图 Bar

在我们日常生活中能看到的数据可视化图表中，柱状图可能会占据着大多数，因为它非常适合用于展示同一量纲下不同计量数据值的区别。比如需要对比某年某市多所高中的本科录取人数、本科率等，柱状图绝对是最适合的选择。

### 12.1.1 准备数据

柱状图所需要的数据集非常简单，每一个类目对应着一个柱状数据，柱状的高度对应着该类目的计量数据。假设某年某市 4 所高中的本科录取人数以及本科率如下表所示。

学校

高中 A

高中 B

高中 C

高中 D

本科录取人数

![N_A](https://juejin.im/equation?tex=N_A)

![N_B](https://juejin.im/equation?tex=N_B)

![N_C](https://juejin.im/equation?tex=N_C)

![N_D](https://juejin.im/equation?tex=N_D)

本科率

![P_A](https://juejin.im/equation?tex=P_A)

![P_B](https://juejin.im/equation?tex=P_B)

![P_C](https://juejin.im/equation?tex=P_C)

![P_D](https://juejin.im/equation?tex=P_D)

其中，我们假设数列 ![N](https://juejin.im/equation?tex=N) 中的每一个元素都为大于 1000 小于 1500 的随机数，而数列 ![P](https://juejin.im/equation?tex=P) 中的元素则为大于 0.85 小于 1 的随机数。同样，我们使用 JavaScript 生成一个符合这些约束的数据集，以便于学习。

```
const N = []
const P = []
const n = 4

function getRandomInt(min, max) {
  return Math.round(min + Math.random() * (max - min))
}

for (let i = 0; i < n; ++i) {
  N.push(getRandomInt(1000, 1500))
  P.push(getRandomInt(85, 100) / 100)
}

console.log(N) //=> [ 1395, 1318, 1447, 1437 ]
console.log(P) //=> [ 0.96, 0.89, 0.98, 0.99 ]

```

得到了两个数列之后，还需要将它们整合起来成为一个 ECharts 可用的行式数据集。

```
const schools = []

for (let i = 0; i < n; ++i) {
  schools.push({
    name: String.fromCharCode(65 + i),
    N: N[i],
    P: P[i]
  })
}

console.log(schools) //=> [ { name: 'A', N: 1395, P: 0.96 }, ... ]

```

### 12.1.2 绘制柱状图

我们还是继续使用上一节中设计好的图表配置进行修改，首先将 `dataset.source` 改成我们现在需要用的 `schools` 学校数据集。

```
const option = {
  dataset: {
    source: schools
  }
}

```

然后将 `series` 中的 `type` 改成目前我们需要使用的柱状图 `bar`，并同时修改 `encode` 中的维度绑定以符合我们新的数据集。

```
const option = {
  series: {
    type: 'bar',
    encode: {
      x: 'name',
      y: 'N'
    }
  }
}

```

最后得到完整的图表配置项，将其应用到 ECharts 实例中查看效果。

```
const option = {
  dataset: {
    source: schools
  },
  xAxis: {
    type: 'category'
  },
  yAxis: {
    type: 'value'
  },
  series: [
    {
      type: 'bar',
      encode: {
        x: 'id',
        y: 'N'
      }
    }
  ]
}

myChart.setOption(option)

```

![bar-chart-1](https://user-gold-cdn.xitu.io/2018/7/28/164df2fd8440161a?w=600&h=400&f=png&s=16131)

由此可见，我们在使用 ECharts 的时候，只需要关注如何组织数据、如何选择可视化图表类型便可以在一定程度上得到所需要的数据图表。

### 12.1.3 优化图表

在日常生活中柱状图应该会是我们使用频率最多的一种数据可视化图表类型，而在平常所看到的柱状图中除了柱状图本身以外，还有很多辅助元素供图表阅读者更好地理解数据。接下来就来为我们所创建的柱状图表添加一些元素来进行优化，使得这个图表更加完善和直观。

**添加数据标签**

在上面的图表中，虽然左侧有 Y 坐标轴提供数值指示的功能，但因为 Y 坐标轴所能标识的数值有限，而柱状图本身也并不具备标明精确数值的功能，所以我们需要添加数据标签以准确指明数值。

在 `bar` 数据系列中添加一个 `label` 配置，以显示一个数值标签。

```
const option = {
  series: [
    {
      type: 'bar',
      encode: {
        x: 'id',
        y: 'N'
      },
      label: {
        normal: {
          show: true,
          position: 'top'
        }
      }
    }
  ]
}

```

![bar-chart-2](https://user-gold-cdn.xitu.io/2018/7/30/164eb68364b10877?w=600&h=400&f=png&s=18792)

**添加平均值辅助线**

除了对数值进行标识之外，对于标识不同分类数值的柱状图来说，通常还需要向读者传递一些统计信息，比如该数值的平均值等。在 ECharts 中为图表添加这些信息可以用到 `markLine` 组件来添加带数值的辅助线。

比如我们需要为某一个柱状图数据系列添加一个表示均值的辅助线，可以如下修改配置项。

```
const option = {
  series: [
    {
      type: 'bar',
      encode: {
        x: 'id',
        y: 'N'
      },
      label: {
        normal: {
          show: true,
          position: 'top'
        }
      },
      markLine: {
        data: [
          { type: 'average', name: '平均值' }
        ]
      }
    }
  ]
}

```

![bar-chart-3](https://user-gold-cdn.xitu.io/2018/7/30/164eb683669a5eb4?w=600&h=400&f=png&s=20252)

可以看到已经有一条横向的虚线表示了该柱状图数据序列的平均值，但因为这条线的颜色与柱状图的颜色相同，视觉效果上并不如意。所以我们可以为这条线加一些样式，使其与柱状图相区分开来。

```
const option = {
  series: [
    {
      type: 'bar',
      encode: {
        x: 'id',
        y: 'N'
      },
      label: {
        normal: {
          show: true,
          position: 'top'
        }
      },
      markLine: {
        data: [
          {
            type: 'average',
            name: '平均值',
            lineStyle: {
              color: '#ffa39e'
            }
          }
        ]
      }
    }
  ]
}

```

![bar-chart-4](https://user-gold-cdn.xitu.io/2018/7/30/164eb6837de1ddff?w=600&h=400&f=png&s=19751)

### 12.1.4 绘制多个数据系列

我们在准备数据的时候，除了每一个学校的本科录取人数以外，还有该学校的本科率。而上面我们所绘制的图表中只使用到了一个数据系列来表示本科录取人数，所以我们接下来为了让可视化图表更好地表达我们所准备的数据内容，需要将本科率也展示在图表上。

我们可以首先在 `series` 配置中添加一个新的 `bar` 数据系列，并将数据绑定 `encode.y` 改为 `P` 即各学校的本科率。

```
const option = {
  series: [
    {
      type: 'bar',
      encode: {
        x: 'id',
        y: 'N'
      },
      label: {
        normal: {
          show: true,
          position: 'top'
        }
      },
      markLine: {
        data: [
          {
            type: 'average',
            name: '平均值',
            lineStyle: {
              color: '#ffa39e'
            }
          }
        ]
      }
    },
    {
      type: 'bar',
      encode: {
        x: 'id',
        y: 'P'
      },
      label: {
        normal: {
          show: true,
          position: 'top'
        }
      },
      markLine: {
        data: [
          {
            type: 'average',
            name: '平均值',
            lineStyle: {
              color: '#096dd9'
            }
          }
        ]
      }
    }
  ]
}

```

![bar-chart-5](https://user-gold-cdn.xitu.io/2018/7/30/164eb68368169972?w=600&h=400&f=png&s=23375)

为什么并没有看到另外一个柱状图？这是因为本科录取人数的数据范围在 1000 到 1500 之间，而本科率的范围则在 0 到 1 之间，而且量纲也相异。所以我们需要借助其他辅助手段对图表进行优化。

虽然说我们比较常用的坐标轴为笛卡尔坐标系也就是直角坐标系，只有一个 X 坐标轴和一个 Y 坐标轴。但若需要将不同量纲的数据在同一个数据图表中展示，就可以使用多个不同的 Y 坐标轴表示。

我们需要在 `yAxis` 上添加一个新的 Y 坐标轴，然后把本科率的数据系列绑定到这个坐标轴上。

```
const option = {
  yAxis: [
    {
      type: 'value',
      name: '本科录取人数'
    },
    {
      type: 'value',
      name: '本科率'
    }
  ],
  series: [
    {
      type: 'bar',
      encode: {
        x: 'id',
        y: 'N'
      },
      label: {
        normal: {
          show: true,
          position: 'top'
        }
      },
      markLine: {
        data: [
          {
            type: 'average',
            name: '平均值',
            lineStyle: {
              color: '#ffa39e'
            }
          }
        ]
      }
    },
    {
      type: 'bar',
      yAxisIndex: 1, // 绑定副 Y 坐标轴
      encode: {
        x: 'id',
        y: 'P'
      },
      label: {
        normal: {
          show: true,

          position: 'top'
        }
      },
      markLine: {
        data: [
          {
            type: 'average',
            name: '平均值',
            lineStyle: {
              color: '#096dd9'
            }
          }
        ]
      }
    }
  ]
}

```

![bar-chart-6](https://user-gold-cdn.xitu.io/2018/7/30/164eb68377785366?w=600&h=400&f=png&s=30784)

## 12.2 饼图

我们知道柱状图可以用于展示不同组别的数值数据的大小，而饼图的作用则是将不同组别的数值数据合并在同一个数轴上，并以更直观的方式展示不同组别之间的大小关系。

### 12.2.1 绘制基本饼图

同样是通过修改数据系列的类型为 `pie`，然后更改数据绑定 `encode` 中的维度信息。因为饼状图并不需要使用到直角坐标系，所以我们这里可以将前面一直都有使用到的 `xAxis` 和 `yAxis` 删除。

```
const option = {
  dataset: {
    source: schools
  },
  series: {
    type: 'pie',
    encode: {
      itemName: 'name',
      value: 'N'
    }
  }
}

```

![pie-chart-1](https://user-gold-cdn.xitu.io/2018/8/3/164fda5368832041?w=600&h=400&f=png&s=18748)

### 12.2.2 添加数据标签

与柱状图相同，这个饼图虽然已经能够比较直观地表达出不同组别之间数据的大小关系，但是却无法直观地表达准确的数据值。所以我们也需要为饼图添加数据标签以表明准确的数值数据。

可以通过修改 ECharts 中饼图的 `label` 也就是标签，来显示每一个组别的组别名、准确数值及其百分比。在 `label.formatter` 中添加 `{@name}` 以显示组别名（维度 `name`），添加 `{@N}` 以显示每一个学校的本科录取人数，以及内置的变量 `{d}` 以显示每一个学校的百分比。

```
const option = {
  dataset: {
    source: schools
  },
  series: {
    type: 'pie',
    label: {
      formatter: '{@name}: {@N} ({d}%)'
    },
    encode: {
      value: 'N',
      itemName: 'name'
    }
  }
}

```

![pie-chart-2](https://user-gold-cdn.xitu.io/2018/8/3/164fda5368a0616f?w=600&h=400&f=png&s=26799)

## 小结

在这一节中我们学习了另外两种简单的数据图表——柱状图和饼图的使用，至此我们已经学习了 4 种基本的数据图表类型，这已经足够我们将它们使用到 90% 以上的数据可视化任务中了。但这其实还不够，我们还需要学习一些更为复杂的数据图表类型，以将它们相互组合完成更多样化的需求。

# 第 13 节 复杂数据图表 · 箱线图

箱线图是一种结合了散点图和柱状图特性的复合数据图表，它主要用于展示一组或多组离散型数值数据的多个特征值及离散程度。

箱线图可以非常好地表现一组数据中大致的整体状况，从而配合统计学方法对数据进行分析和评价。

## 13.1 准备数据

### 13.1.1 箱线图统计量

箱线图是利用离散数据中的 5 个统计量进行绘制的：最小值、第一四分位数 ![Q_1](https://juejin.im/equation?tex=Q_1)、中位数、第三四分位数 ![Q_3](https://juejin.im/equation?tex=Q_3) 以及最大值，并利用 ![Q_1](https://juejin.im/equation?tex=Q_1) 和 ![Q_3](https://juejin.im/equation?tex=Q_3) 两个四分位数计算得到四分位距 ![IQR](https://juejin.im/equation?tex=IQR)。

![IQR](https://juejin.im/equation?tex=IQR) 是一种用于表示离散数据离散程度的统计量，其定义为一组离散数据中的第一四分位数与第三四分位数的差值。

![IQR=Q_3-Q_1](https://juejin.im/equation?tex=IQR%3DQ_3-Q_1)

得到了 ![IQR](https://juejin.im/equation?tex=IQR) 之后便可以继续推导出离散数据箱线图的内限 ![[Q_1-1.5 \times IQR,Q_3+1.5 \times IQR]](https://juejin.im/equation?tex=%5BQ_1-1.5%20%5Ctimes%20IQR%2CQ_3%2B1.5%20%5Ctimes%20IQR%5D)，超出这个内限的值便为该组数据中的离群值（outlier），需要被单独标记。

虽然在 ECharts 中已经提供了这些数据的计算工具，但是为了能够更好地理解其中的统计学含义，这里将会一步步地计算这些我们需要使用到的数据。

### 13.1.2 计算统计量

跟前几节不一样的是，使用 `Math.random()` 所生成的数据在不加以处理的情况下都会呈均匀分布，而像 ![IQR](https://juejin.im/equation?tex=IQR) 这些用于表示数据离散程度的统计量在这里便失去了意义。因此，这一节会使用一组真实的数据来作为将要使用的数据。

这份数据来自非常著名的物理实验 —— 迈克耳孙-莫雷实验，它是一项用于验证“以太”物质是否存在的实验。

```
const data = [ 850, 740, 900, 1070, 930, 850, 950, 980, 980, 880, 1000, 980, 930, 650, 760, 810, 1000, 1000, 960, 960 ]

```

**最小值 & 最大值**

最小值和最大值可以说是我们非常熟悉的一对数据统计量，在 JavaScript 中计算这两个值的方法非常多，可以先使用排序然后取头尾两值。当然在 JavaScript 中我们有更好的方法，可以分别使用 `Math.min()` 和 `Math.max()` 来进行计算。

```
const min = Math.min(...data) //=> 650
const max = Math.max(...data) //=> 1070

```

**中位数**

中位数的定义为离散数据 ![S](https://juejin.im/equation?tex=S) 在数轴上的中间值：

*   如果离散数据的个数 ![n](https://juejin.im/equation?tex=n) 为奇数，则中位数就为第 ![\frac{n+1}{2}](https://juejin.im/equation?tex=%5Cfrac%7Bn%2B1%7D%7B2%7D) 个数值，![median=S_{\frac{n+1}{2}}](https://juejin.im/equation?tex=median%3DS_%7B%5Cfrac%7Bn%2B1%7D%7B2%7D%7D)；
*   若数据的个数 ![n](https://juejin.im/equation?tex=n) 为偶数，则中位数为最中间两个数值的平均值，![median=\frac{1}{2}(S_{\frac{n}{2}}+S_{\frac{n}{2}+1})](https://juejin.im/equation?tex=median%3D%5Cfrac%7B1%7D%7B2%7D(S_%7B%5Cfrac%7Bn%7D%7B2%7D%7D%2BS_%7B%5Cfrac%7Bn%7D%7B2%7D%2B1%7D))。

```
let median = 0
const n = data.length
const sortedData = data.sort(function(a, b) {
  return a - b
})

if (n % 2 == 1) {
  median = sortedData[((n + 1) / 2) - 1]
} else {
  median = (sortedData[(n / 2 + 1) - 1] + sortedData[(n / 2) - 1]) / 2
}

console.log(median) //=> 940

```

**四分位数**

中位数实际上就是一个四分位数，将离散数据画在数轴上，然后以最小值和最大值作为范围，将数轴切分成四份。第一和第二份的边界点为第一四分位数，第二和第三份的边界点为中位数，而第三和第四份的边界点则为第三四分位数。

![四分位数](https://user-gold-cdn.xitu.io/2018/8/12/1652bb63557e8b98?w=452&h=175&f=png&s=9623)

当中位数的位置上不存在某一个特定的数值时，则取最中间两个数值的平均数。而第一和第三四分位数则除了需要合并两个相邻的数值以外，还需要根据各自的位置进行相应的计算。

比如当第一四分位数的位置上并不是特定的一个数值时，则取前一个数乘以 ![\frac{1}{4}](https://juejin.im/equation?tex=%5Cfrac%7B1%7D%7B4%7D) 的乘积加上后一个数乘以 ![\frac{3}{4}](https://juejin.im/equation?tex=%5Cfrac%7B3%7D%7B4%7D) 的乘积，而并不是两者的平均数，因为这样才更符合第一四分位数的定义。第三四分位数同理。

![\begin{equation}
\left\{
  \begin{array}{lr}
    Q_1=S_{\frac{n}{4}} & n=2k(k \ne 0) \\
    Q_1=\frac{1}{4}S_{\left \lfloor \frac{n}{4} \right \rfloor} + \frac{3}{4}S_{\left \lfloor \frac{n}{4} \right \rfloor + 1} & n=2k+1(k \ne 0)
  \end{array}
\right.
\end{equation}](https://juejin.im/equation?tex=%5Cbegin%7Bequation%7D%0A%5Cleft%5C%7B%0A%20%20%5Cbegin%7Barray%7D%7Blr%7D%0A%20%20%20%20Q_1%3DS_%7B%5Cfrac%7Bn%7D%7B4%7D%7D%20%26%20n%3D2k(k%20%5Cne%200)%20%5C%5C%0A%20%20%20%20Q_1%3D%5Cfrac%7B1%7D%7B4%7DS_%7B%5Cleft%20%5Clfloor%20%5Cfrac%7Bn%7D%7B4%7D%20%5Cright%20%5Crfloor%7D%20%2B%20%5Cfrac%7B3%7D%7B4%7DS_%7B%5Cleft%20%5Clfloor%20%5Cfrac%7Bn%7D%7B4%7D%20%5Cright%20%5Crfloor%20%2B%201%7D%20%26%20n%3D2k%2B1(k%20%5Cne%200)%0A%20%20%5Cend%7Barray%7D%0A%5Cright.%0A%5Cend%7Bequation%7D)

![\begin{equation}
\left\{
  \begin{array}{lr}
    Q_3=S_{\frac{3n}{4}} & n=2k(k \ne 0) \\
    Q_3=\frac{3}{4}S_{\left \lfloor \frac{3n}{4} \right \rfloor} + \frac{1}{4}S_{\left \lfloor \frac{3n}{4} \right \rfloor + 1} & n=2k+1(k \ne 0)
  \end{array}
\right.
\end{equation}](https://juejin.im/equation?tex=%5Cbegin%7Bequation%7D%0A%5Cleft%5C%7B%0A%20%20%5Cbegin%7Barray%7D%7Blr%7D%0A%20%20%20%20Q_3%3DS_%7B%5Cfrac%7B3n%7D%7B4%7D%7D%20%26%20n%3D2k(k%20%5Cne%200)%20%5C%5C%0A%20%20%20%20Q_3%3D%5Cfrac%7B3%7D%7B4%7DS_%7B%5Cleft%20%5Clfloor%20%5Cfrac%7B3n%7D%7B4%7D%20%5Cright%20%5Crfloor%7D%20%2B%20%5Cfrac%7B1%7D%7B4%7DS_%7B%5Cleft%20%5Clfloor%20%5Cfrac%7B3n%7D%7B4%7D%20%5Cright%20%5Crfloor%20%2B%201%7D%20%26%20n%3D2k%2B1(k%20%5Cne%200)%0A%20%20%5Cend%7Barray%7D%0A%5Cright.%0A%5Cend%7Bequation%7D)

```
function quantile4(data, pos) {
  if (pos < 1 || pos > 3 || pos % 1 !== 0) {
    throw 'the second argument pos should be an interger and should be 1, 2 or 3'
  }

  const sortedData = data.sort(function(a, b) {
    return a - b
  })

  const n = sortedData.length

  if ((pos * n / 4) % 1 !== 0) {
    // pos * n / 4 不为整数时
    return sortedData[pos * n / 4 - 1]
  } else {
    return (pos / 4) * (sortedData[Math.floor(pos * n / 4) - 1]) + ((4 - pos) / 4) * (sortedData[Math.floor(pos * n / 4)])
  }
}

// 使用中位数作为验证
console.log(quantile4(data, 2)) //=> 940

const Q1 = quantile4(data, 1)
const Q3 = quantile4(data, 3)

```

**IQR**

IQR 即四分位距，定义为第一四分位数和第三四分位数的差值。

```
const IQR = Q3 - Q1 //=> 130

```

**内限 & 离群值**

若需要判断一个数据点是否为离群值，就需要先通过四分位数和 IQR 计算出内限，再通过对比该数据点与内限来判断它是否为离群值。

```
const limit = [
  Q1 - 1.5 * IQR,
  Q3 + 1.5 * IQR
]

const outliers = data.filter(function(k) {
  return k < limit[0] || k > limit[1]
})

console.log(outliers) //=> [ 650 ]

```

## 13.2 绘制箱线图

凑齐了这些需要用到的数据之后，我们便可以将它们放进图表上了。与前面我们学习使用过的数据图表不一样的是，箱线图一个数据系列就要使用到五个维度的数据。所以我们在做数据绑定的时候也需要分别为这五个维度的数据进行绑定。

### 13.2.1 准备数据集

跟其他数据图表一样，箱线图的数据同样可以使用 `dataset` 来提供数据支持。分别需要将 6 个不同的维度数据传入：箱线图标识、最小值、第一四分位数、中位数、第三四分位数、最大值。

```
const option = {
  dataset: {
    source: [
      [ 1 /* 第一个箱线图 */, min, Q1, median, Q3, max ]
    ]
  }
}

```

### 13.2.2 准备笛卡尔坐标系

因为箱线图的数据类型是计量数据，所以我们所使用的依然是最熟悉的笛卡尔坐标系。而由于这里暂时只有一个箱线图，为了能够更好地表达数轴的概念，我们将 X 轴作为数据轴，Y 轴作为系列轴。

```
const option = {
  xAxis: {
    type: 'value',
    scale: true
  },
  yAxis: {
    type: 'category'
  },
}

```

### 13.2.3 绑定数据系列

绑定数据系列是使用 ECharts 绘制数据图表中最重要的一环，因为这一步直接关系到如何将数据展示在图表上。而箱线图的特殊性在这一步中则显得格外突出，它需要绑定 5 个不同维度的数据。

```
const option = {
  series: {
    type: 'boxplot',
    encode: {
      y: 0,
      x: [ 1, 2, 3, 4, 5 ]
    }
  }
}

```

完成了上面的工作以后，我们便可以将图表配置应用到图表实例中查看效果了。

![boxplot-chart-1](https://user-gold-cdn.xitu.io/2018/8/14/1653767273e14024?w=600&h=400&f=png&s=11983)

非常好，我们已经在这个图表上看到了一个很好的效果，箱线图非常好地表现了数据的数值范围、离散程度以及中位数特征值。

### 13.2.4 绘制离群值

除了箱线图以外，我们还知道箱线图有一个内限，用于判断数值是否离群。如果数据中出现了离群值，我们可以将其单绘制到图表上表示出来。离群值可以使用散点 `scatter` 绘制在图表上。

我们可以多增加一个数据集来存储离群值的数据，以绑定 `scatter` 数据系列。

```
const option = {
  dataset: [
    {
      source: [
        [ 1, min, Q1, median, Q3, max ]
      ]
    },
    {
      source: outliers.map(function(outlier) {
        return [ 1, outlier ]
      })
    }
  ]
}

```

然后我们需要进一步修改数据系列，包括前面的箱线图系列。

```
const option = {
  series: [
    {
      type: 'boxplot',
      datasetIndex: 0,
      encode: {
        y: 0,
        x: [ 1, 2, 3, 4, 5 ]
      }
    },
    {
      type: 'scatter',
      datasetIndex: 1,
      encode: {
        y: 0,
        x: 1
      }
    }
  ]
}

```

![boxplot-chart-2](https://user-gold-cdn.xitu.io/2018/8/14/1653767273f4ff86?w=600&h=400&f=png&s=12143)

## 13.3 多系列箱线图

前面我使用一组数据绘制了一个更偏向于一维数轴的箱线图，但是在实际开发和应用中，我们往往需要在一张图表上绘制多组不同数据的箱线图。就比如在进行统计试验的时候，不同的测试水平需要进行多次试验得到数据并进行分析。而多次试验的数据结果需要进行可视化，便可以使用到箱线图进行表达。

比如上面的迈克耳孙-莫雷实验，真正记录的数据肯定不止这一次，我们可以引入多组试验的数据。

```
const data = [
    [850, 740, 900, 1070, 930, 850, 950, 980, 980, 880, 1000, 980, 930, 650, 760, 810, 1000, 1000, 960, 960],
    [960, 940, 960, 940, 880, 800, 850, 880, 900, 840, 830, 790, 810, 880, 880, 830, 800, 790, 760, 800],
    [880, 880, 880, 860, 720, 720, 620, 860, 970, 950, 880, 910, 850, 870, 840, 840, 850, 840, 840, 840],
    [890, 810, 810, 820, 800, 770, 760, 740, 750, 760, 910, 920, 890, 860, 880, 720, 840, 850, 850, 780],
    [890, 840, 780, 810, 760, 810, 790, 810, 820, 850, 870, 870, 810, 740, 810, 940, 950, 800, 810, 870]
]

```

### 13.3.1 整合统计量计算

前面我们为一组数据计算了多个统计量以展示在箱线图上，那么在多组数据中我们可以将前面的计算过程进行封装。

```
function boxplotDatas(data) {
  const min = Math.min(...data)
  const max = Math.max(...data)

  let median = 0
  const n = data.length
  const sortedData = data.sort(function(a, b) {
    return a - b
  })

  if (n % 2 == 1) {
    median = sortedData[((n + 1) / 2) - 1]
  } else {
    median = (sortedData[(n / 2 + 1) - 1] + sortedData[(n / 2) - 1]) / 2
  }

  const Q1 = quantile4(data, 1)
  const Q3 = quantile4(data, 3)

  const IQR = Q3 - Q1

  const limit = [
    Q1 - 1.5 * IQR,
    Q3 + 1.5 * IQR
  ]

  const outliers = data.filter(function(k) {
    return k < limit[0] || k > limit[1]
  })

  return {
    min, max, median, Q1, Q3, outliers,
  }
}

const boxplotData = data.map(function(exprData, i) {
  return Object.assign({ id: i }, boxplotDatas(exprData))
})
//=> [
//   {id: 0, min: 650, max: 1070, median: 940, Q1: 850, …},
//   {id: 1, min: 760, max: 960, median: 845, Q1: 800, …},
//   ...
// ]

const outliers = boxplotData
  .map(function({ id, outliers }) {
    return outliers.map(function(outlier) {
      return [ id, outlier ]
    })
  })
  .reduce(function(left, right) {
    return left.concat(right)
  })
//=> [
//   [0, 650], [2, 620], ...
// ]

```

得到数据之后，我们就可以进行数据图表绘制了。

```
const option = {
  dataset: [
    {
      source: boxplotData
    },
    {
      source: outliers
    }
  ],
  xAxis: {
    type: 'category'
  },
  yAxis: {
    type: 'value',
    scale: true
  },
  series: [
    {
      type: 'boxplot',
      datasetIndex: 0,
      encode: {
        x: 'id',
        y: [ 'min', 'Q1', 'median', 'Q3', 'max' ]
      }
    },
    {
      type: 'scatter',
      datasetIndex: 1,
      encode: {
        x: 0,
        y: 1
      }
    }
  ]
}

```

![boxplot-chart-3](https://user-gold-cdn.xitu.io/2018/8/14/1653767273fda531?w=600&h=400&f=png&s=16437)

## 小结

这一节中我们学习了一个比较复杂的数据图表，它相较于前面学习和使用过的数据图表使用到了更多维度的数值数据，其自身所具有的统计分析意义也能更直观地表达。

### 习题

思考箱线图、散点图、折线图、柱状图这几种用于表达计量数据的不同数据图表的异同。

# 第 14 节 复杂数据图表 · 关系图谱

关系图谱可以说是笔者我最喜欢的一种数据图表了，因为从很多年前的好莱坞科幻电影开始，主人公都会在一个悬浮在半空中的操作界面中进行操作。其中最具酷炫感的便是不同的球形之间通过一条线进行连接，操作者点击其中一个球体便会带动其他相连的球体，这简直太酷了。

还记得我们在第 8 节中所学习过的关系图谱数据结构吗？我们可以将其应用起来，变成一个非常酷炫的关系图谱。

## 14.1 准备数据

我们再次使用在第 8 节中准备好的关系图谱数据，一个简单却能够包含大部分情况的图谱数据。

![有向图](https://user-gold-cdn.xitu.io/2018/9/4/165a223cc5768407?w=373&h=243&f=png&s=15547)

```
const vertices = [
  new Vertex(1, 'A'),
  new Vertex(2, 'B'),
  new Vertex(3, 'C'),
  new Vertex(4, 'D'),
  new Vertex(5, 'E')
]

const edges = [
  new DirectedEdge(1, 2, 1),
  new DirectedEdge(1, 3, 2),
  new DirectedEdge(2, 4, 1),
  new DirectedEdge(3, 4, 1),
  new DirectedEdge(1, 1, 3),
  new DirectedEdge(3, 5, 4),
  new DirectedEdge(4, 5, 5)
]

```

虽然我们在第 8 节中创建了如 `Vertex`、`Edge` 和 `DirectedEdge` 这样的类以方便进行关系图谱算法实现以及代码的理解，但 ECharts 的关系图谱图表则相对简单许多，并不需要使用到特定的类进行内容的标识，只需准备好符合规则结构的数据即可。

对应上面的关系图谱数据，我们可以编写出以下数据集。

```
const vertices = [
  { name: 'A' },
  { name: 'B' },
  { name: 'C' },
  { name: 'D' },
  { name: 'E' }
]

const edges = [
  { source: 'A', target: 'B' },
  { source: 'A', target: 'C' },
  { source: 'B', target: 'D' },
  { source: 'C', target: 'D' },
  { source: 'A', target: 'A' },
  { source: 'C', target: 'E' },
  { source: 'D', target: 'E' }
]

```

比较特殊的是，因为关系图谱数据系列同时需要使用到两个数据集，所以没办法使用前面学习到的 `dataset` 和 `encode` 来进行数据绑定。

## 14.2 关系图谱数据系列

在将数据集应用到数据图表中之前，首先我们需要了解的是虽然 ECharts 中的关系图谱图表也是建立在二维空间中的，但其与前面学习过的饼图类似，默认情况下并不需要使用到直角坐标系。

所以关系图谱中的每一个节点在二维空间中的位置是需要特定标明的，也就是每一个节点（Node）都需要带有二维空间的坐标信息（`x` 和 `y`）。但显然这个任务由我们来完成并不现实，所以 ECharts 提供了几个比较实用的“模板”（Layout），以便于将关系图谱以比较好的形式展示在图表上。

*   Circular 环形，整个关系图谱的节点会围绕成一个环形结构，并根据实际节点之间的关系进行排列；
*   Force 力引导，关系图谱的分布会根据节点之间连接的“能量”来尽可能保持边长的一致和尽可能少地出现交叉。

![Les Miserables Circular](https://user-gold-cdn.xitu.io/2018/9/5/165a7f2334474f79?w=1132&h=840&f=png&s=419685)

![Les Miserables Force](https://user-gold-cdn.xitu.io/2018/9/5/165a7f9e2e8a3cd8?w=1132&h=840&f=png&s=126804)

从这两张图我们可以看出同一份关系图谱数据分别使用两种不同的展示方式，便有着区别非常大的展示效果。环形井然有序中透露着不少的科技感，而力引导虽然看似杂乱无章却处处透露着数学的魅力。两种不同的模板可以根据实际的需要进行合理的选择，以达到更好的展示效果。

当然 ECharts 也同样支持在数据集中录入每一个节点的位置参数，通常用于展示由其他软件生成的关系图谱数据。

### 14.2.1 编写配置

因为关系图谱在一般情况下都不需要使用到坐标系来进行辅助，所以我们只需要直接指定数据系列的类别为 `graph` 即可，并将我们的数据传入到数据系列中。

```
const option = {
  series: {
    type: 'graph',
  
    // Dataset
    data: vertices,
    links: edges
  }
}

```

然后我们以环形模板为例子，以展示一个较为简单的关系图谱，只需要添加一个 `layout` 配置即可。

```
const option = {
  series: {
    // ...
    layout: 'circular'
  }
}

```

![graph-chart-1](https://user-gold-cdn.xitu.io/2018/9/5/165a7983519ae543?w=600&h=400&f=png&s=14537)

### 14.2.2 润色图表

看完前面章节的同学肯定都知道我们在绘制图表的时候都是采用循序渐进的形式进行绘制的，在完成了最基础的图表绘制以后，需要逐步地根据实际需求对图表进行优化润色。

在这个关系图谱中我们可以总结出以下需要优化的地方：

1.  节点图标大小需要调整，目前的节点图标过小而导致画面空洞，当然这也跟节点数量有关；
2.  有向边没有直观的标识，我们在准备节点之间的边时便已经标明了这些边均为有向边，而目前在图表上边的有向性并没有表现出来；
3.  节点的名称没有标明。

**节点大小**

我们也是逐个将上面的问题进行解决，首先就是需要调整节点的大小，一般情况下只需要填写一个合适的大小即可。

```
const option = {
  series: {
    // ...
    symbolSize: 50
  }
}

```

![graph-chart-2](https://user-gold-cdn.xitu.io/2018/9/5/165a7983520da76c?w=600&h=400&f=png&s=18405)

**展示标签**

回想一下我们前面学习过的数据图表，当我们需要在数据系列上进行标签展示的时候需要添加什么配置？

```
const option = {
  series: {
    // ...
    label: {
      normal: {
        show: true
      }
    }
  }
}

```

![graph-chart-3](https://user-gold-cdn.xitu.io/2018/9/5/165a798351e9a66b?w=600&h=400&f=png&s=19575)

**展示有向边**

因为在进行图表展示的时候，有向图的意义与无向图有着非常大的差别，而准确地表达数据集所包含的内容则是可视化工程的首要原则。

```
const option = {
  series: {
    type: 'graph',
    layout: 'circular',
    symbolSize: 50,
  
    // Dataset
    data: vertices,
    links: edges,
    
    label: {
      normal: {
        show: true
      }
    },
    
    edgeSymbol: [ 'circle', 'arrow' ],
    edgeSymbolSize: [ 4, 10 ]
  }
}

```

![graph-chart-4](https://user-gold-cdn.xitu.io/2018/9/5/165a798352fceda6?w=600&h=400&f=png&s=20604)

### 14.2.3 力引导关系图谱

前面我们使用了环形模板来展示我们的关系图谱数据，而 ECharts 还提供了力引导模板以供使用。力引导模板运用了一系列的数学原则以及算法来计算每一个节点的位置和节点之间的距离，综合得到最终的完整关系图谱。

将前面的环形配置更换为力引导 `force`。

```
const option = {
  series: {
    // ...
    layout: 'force'
  }
}

```

![graph-chart-5](https://user-gold-cdn.xitu.io/2018/9/5/165a79835308bb5b?w=600&h=400&f=png&s=9132)

咦？为什么更换了力引导模板以后整个关系图谱的节点都挤在了一起？因为我们前面设置的节点图标大小与力引导模板默认的大小相差太大，而在距离的计算上也并没有自动地进行适应，所以就出现了全部节点挤在了一起的现象。

为了修复这一问题，我们还需要对力引导模板进行配置，比如力引导中模板中节点之间的斥力大小以及边长的取值范围。

```
const option = {
  series: {
    // ...
    force: {
      repulsion: 100,
      edgeLength: [ 100, 500 ]
    }
  }
}

```

![graph-chart-6](https://user-gold-cdn.xitu.io/2018/9/5/165a798352eef0ba?w=600&h=400&f=png&s=23520)

## 小结

这一节我们学习到了，如何将我们在第 8 节中学习到的关系图谱数据使用直观的图表展示出来，虽然图表并不能像图论算法一样通过计算节点之间的关系以进行动态调整，但却能更直观地将数据表达出来。

# 第 15 节 复杂数据图表 · 树形图

翻过了关系图谱这座小山丘之后，让我们继续回忆一下我们在前面就提到过的一种特殊的关系图谱——树形。树形结构有着非常明确的上下级关系，可以非常直观地表达出事物的因果关系，且其规则复杂却足够灵活，因而能够很好地使用在各种算法和场景中。

使用 ECharts 来对树形结构进行可视化，可以为这种强大的数据结构加上更强的交互性，能让你的用户更好地梳理和理解树形结构中所承载的内容。

## 15.1 准备数据

在第 7 节中我们介绍了如何利用树形结构及其相关的运算算法来对一系列关系数据进行处理，而本章节我们将要使用 ECharts 进行具有可交互特性的图表可视化。

和在上一节中的关系图谱中所使用的数据集类似，由于 ECharts 对数据集进行应用之前都需要先进行一系列的预处理，而这过程中很有可能会与我们所实现的类中的某些属性或方法相冲突。

所以我们需要准备一个转换函数，将我们第 7 节中所生成的树形结构数据转换为更纯粹的 JavaScript 对象数据，也就是俗称的 JSON 数据。

```
const root = new Node('root')
const node1 = new Node('node 1')
const node2 = new Node('node 2')
const node3 = new Node('node 3')
const node4 = new Node('node 4')
const node5 = new Node('node 5')
const node6 = new Node('node 6')

const tree = new Tree(root)
tree.addNode(node1)
tree.addNode(node2)
tree.addNode(node3, node1)
tree.addNode(node4, node1)
tree.addNode(node5, node2)
tree.addNode(node6, node5)

function treeDataHelper(treeNode) {
  const node = {
    value: treeNode.value
  }

  if (treeNode.children && treeNode.children.length > 0) {
    node.children = node.children || []

    treeNode.children.forEach(function(childNode) {
      node.children.push(treeDataHelper(childNode))
    })
  }

  return node
}

const pureRoot = treeDataHelper(tree.root)

```

## 15.2 编写配置

ECharts 在这种较为复杂的数据图表中有着非常优秀的封装，它可以帮助我们很快地将复杂的数据结构根据图表配置展示出我们所希望看到的可视化图表，这一特点在树形图中尤为明显。

```
const option = {
  series: {
    type: 'tree',
    data: [ pureRoot ]
  }
}

```

![tree-chart-1](https://user-gold-cdn.xitu.io/2018/9/22/16600db1765073ff?w=600&h=400&f=png&s=12604)

Live DEMO: [https://codepen.io/iwillwen/pen/zJXBEV](https://codepen.io/iwillwen/pen/zJXBEV)

Bravo！极其简单的配置便可以得到样式良好且可交互的树形图表，但我们发现在树形图中节点并没有将节点的名称展示出来，那么接下来我们依然是进入我们非常熟悉的图表优化环节。

### 图表优化

**显示节点名称**

事实上 ECharts 的树形图在不需要添加任何配置项的情况下也可以展示节点的名称，不过因为我们在第 7 节中所指定的节点类 `Node` 中代表节点值或名称的属性为 `value` 而 ECharts 树形图中则需要使用 `name`，所以便无法直接展示出节点的名称。

而这一问题也并不是无法解决的，我们可以使用 `label.formatter` 的方式为节点添加标签。

```
const option = {
  series: {
    // ...
    
    label: {
      formatter: '{@value}' // 绑定到 value 属性上
    }
  }
}

```

![tree-chart-2](https://user-gold-cdn.xitu.io/2018/9/22/16600db17685bc08?w=600&h=400&f=png&s=15647)

Live DEMO: [https://codepen.io/iwillwen/pen/aaxZEo](https://codepen.io/iwillwen/pen/aaxZEo)

但是我们还发现这个配置虽然满足了在图表上显示节点名称的需求，却出现了名称与节点图标重叠的情况。那么我们便需要对这个情况进行调整，以帮助图表的使用者和阅读者更好地使用。

**调整节点名称位置**

在对数据进行调整之前，我们需要明确调整的思路。因为树形结构中的节点除了根节点都会与上一层的父节点有一条连线以表示节点之间的父子关系，但相对的每一个节点与上一层父节点的关系数量只有一个，但却有可能有很多的子节点与自身有关系，所以如果将节点的名字放在了节点的右方便很有可能会出现严重的与节点的边相重叠。

所以对于叶节点以外的所有节点，我们需要将名字显示在节点的左边，而因为叶节点是没有子节点的，所以叶节点的名称可以显示在节点图标的右边。

```
const option = {
  series: {
    // ...
    
    label: {
      position: 'left'
    },
    
    leaves: {
      label: {
        position: 'right'
      }
    }
  }
}

```

![tree-chart-3](https://user-gold-cdn.xitu.io/2018/9/22/16600db176625b85?w=600&h=400&f=png&s=16749)

Live DEMO: [https://codepen.io/iwillwen/pen/qMwNxd](https://codepen.io/iwillwen/pen/qMwNxd)

## 15.3 其他树形图形态

就如上一节中关系图谱的两种模板，除了默认的从左往右伸展的树形图结构以外，ECharts 的树形图表也提供了多种不同的结构模板。

除了最基本的从左往右展开排列以外，还有其他三个方向的模板（如从上往下）。除此以外还有一个十分好看的放射形树形图。

```
const option = {
  series: {
    type: 'tree',
    layout: 'radial',
    data: [ treeDataHelper(tree.root) ],
    label: {
      formatter: '{@value}'
    }
  }
}

```

![tree-chart-4](https://user-gold-cdn.xitu.io/2018/9/22/16600db1767d9abb?w=600&h=400&f=png&s=16740)

Live DEMO: [https://codepen.io/iwillwen/pen/LJvZdG](https://codepen.io/iwillwen/pen/LJvZdG)

这里的效果稍微有点差，当然这是因为节点的数量太少了。

## 小结

这一节中我们学习了另外一种复杂数据结构——树形结构的可视化图表的使用，其中因为 ECharts 对数据的内部处理导致了我们需要利用其他手段将我们原本实现的树形结构转换为更纯净的 JavaScript 对象数据集，那么我们本节的习题也会从这一个点上进行练习。

### 习题

在上一节中我们并没有为关系图谱数据设计类似 `treeDataHelper` 这样的函数，而在本节中我们利用了递归的方式进行了逐层的转换来对树形结构进行了转换。

请模仿 `treeDataHelper` 编写出适用于上一节中关系图谱数据的 `graphDataHelper`。

# 第 16 节 数据分析师的好帮手 · 辅助线

经过了一系列的学习，我们已经掌握了多种日常开发中最常用的数据图表，并且可以对各种结构的数据集进行预处理。但是在我们进行可视化图表开发的时候经常会发现如果仅仅将数据使用数据系列展示在图表上的话，是没办法非常直观地展示所有数据信息的。

而这个时候，辅助线便成了帮助开发人员和分析人员更好地利用可视化图表的强有力工具。

## 16.1 为什么要使用辅助线

我们在第 5 节中学习了如何利用 JavaScript 对数据中的一些数学特征值进行计算，而这些数学特征值往往可以更好、更直观地将数据的基本状况表达出来。

但是这些数学特征值往往只是通过对一组数值进行计算过后得到的另一组数值，那么辅助线便是帮助开发者和数据分析人员更好地使用这些数学特征值的最好工具。

![](https://user-gold-cdn.xitu.io/2018/10/26/166ae5b6d639c844?w=1600&h=960&f=png&s=94875)

## 16.2 辅助线基本操作

在 ECharts 中辅助线并不是一种独立的数据类型，它需要依附在某一个数据系列上以表示其与该数据系列的关系。

假设我们有以下数据集，并将其绘制成一个简单的柱状图。

```
const chartEl = document.querySelector('#chart')
const myChart = echarts.init(chartEl)

const data = [ 50, 61, 56, 46, 72, 53 ]

const option = {
  dataset: {
    source: data.map((y, i) => ({
      x: i + 1,
      y
    }))
  },

  xAxis: {
    type: 'category'
  },
  yAxis: {
    type: 'value'
  },
  
  series: {
    type: 'bar',
    encode: {
      x: 'x',
      y: 'y'
    }
  }
}

```

![](https://user-gold-cdn.xitu.io/2018/10/26/166ae5cdef22deb9?w=600&h=400&f=png&s=14755)

然后接下来我们通过非常简单的计算，得出这一组数据的平均数。

```
const mean = data.reduce((left, right) => left + right) / data.length
console.log(mean) //=> 56.333333333333336

```

假如说需要将这个计算结果展示在图表上，那么根据目前所设定的坐标系可知我们需要添加一条横向的水平线，而这条水平线的纵向位置应该为 ![y](https://juejin.im/equation?tex=y) 坐标轴上该数值所对应的位置。

那么在 ECharts 中便需要在对应的数据系列上添加一个 `markLine` 配置，并在 `markLine.data` 中添加一个 `yAxis` 值为对应平均值的配置。

```
const option = {
  // ...
  
  series: {
    // ...
    
    markLine: {
      data: [
        {
          name: '平均线',
          yAxis: mean
        }
      ]
    }
  }
}

```

![](https://user-gold-cdn.xitu.io/2018/10/26/166ae5d4b84c7618?w=600&h=400&f=png&s=16371)

### 16.2.1 ECharts 的自带辅助线

除了我们可以自行计算目标辅助线的数值以外，ECharts 自身也提供了一些比较常用的辅助线，除了前面我们自行计算的平均值外，还有最大值和最小值。

```
const option = {

  // ...
  
  series: {
    // ...
    
    markLine: {
      data: [
        { name: '平均值', type: 'average' },
        { name: '最大值', type: 'max' },
        { name: '最小值', type: 'min' }
      ]
    }
  },

}

```

![](https://user-gold-cdn.xitu.io/2018/10/26/166ae5eaf624325f?w=600&h=400&f=png&s=17787)

## 16.3 辅助线高级用法

是否觉得前面的辅助线都太简单而没有挑战性了？恭喜你已经拥有了成为大牛的一个非常重要的优秀特点，那么我们接下来便需要向更复杂、更具有功能性的辅助线应用进发吧。

### 16.3.1 SPC 控制图

在传统的统计学领域中，有一种广泛用于工业生产的统计方法——质量管理。在工业生产领域中，企业为了能够稳定且长期地发展产品的质量和销量，必须要对产品生产过程中的各种数据进行监控和分析，比如生产原料、成本、产品特性、质量指标、销量等等。

而其中成本和质量指标直接关系到了企业的长期生存条件，所以对这些数据的监控和分析则显得尤为重要。其中有一种名为 SPC 控制图的数据可视化图表的应用非常广泛，它通过对数据进行计算并将计算结果作为辅助线绘制在图表上。这些辅助线可以帮助数据分析人员非常直观地看到数据中的总体状况和突发的异常情况等。

SPC 控制图事实上是多种控制图表的总称，但其核心都是相似的。SPC 控制图主要通过计算三个控制线：UCL（控制上限）、CL（中心线）和 LCL（控制下限）。在一些情况下还可以将控制图的上下限的中间区域分为 6 等份，并分别标记为控制 A 区、B 区以及 C 区，并通过记录数据点落在这三个控制区域的数量来对数据的稳定性进行直观的判定。

![](https://user-gold-cdn.xitu.io/2018/10/26/166ae6040e8e262f?w=230&h=200&f=png&s=7682)

> 注：图片来源 Wikipedia —— [Western Electric rules](https://en.wikipedia.org/wiki/Western_Electric_rules)

### 16.3.2 建立数据集

假设我们通过随机方法生成一组数值数据（参考第 11 节），并将其绘制到折线图上。

```
const X = [ 100 ]
const n = 50 - 1
const r = 0.1

function randomCoefficient(r) {
  const rand = Math.random()
  const coefficient = (rand - 0.5) * 2 * r

  return coefficient
}

for (let i = 0; i < n; ++i) {
  const coefficient = randomCoefficient(r)
  const newValue = X[i] * (1 + coefficient)

  X.push(newValue)
}

console.log(X) //=> [ 100, 95.23, ... ]

const data = X.map(function(x, i) {
  return { time: i + 1, value: x }
})

const option = {
  dataset: {
    source: data
  },
  xAxis: {
    type: 'value',
    name: 'i',
    nameLocation: 'middle',
    nameGap: 25
  },
  yAxis: {
    type: 'value',
    scale: true,
    name: 'x',
    nameLocation: 'end'
  },
  series: {
    type: 'line',
    encode: {
      x: 'time',
      y: 'value'
    }
  },
}

```

![](https://user-gold-cdn.xitu.io/2018/10/26/166ae5fa40ead45b?w=600&h=400&f=png&s=35972)

### 16.3.3 计算 SPC 控制图的必要数值

SPC 控制图所使用的数据主要需要计算数据的平均值和标准差（Standard deviation，并非标准误 Standard error）。平均值的计算我们使用 Lodash 中的 `_.mean` 即可，但 Lodash 并没有提供标准差 ![\sigma](https://juejin.im/equation?tex=%5Csigma) 的计算方法，所以我们这里也需要自行实现一下标准差的计算方法。![N](https://juejin.im/equation?tex=N) 为数组 ![x](https://juejin.im/equation?tex=x) 的长度，![\overline{x}](https://juejin.im/equation?tex=%5Coverline%7Bx%7D) 为数组 ![x](https://juejin.im/equation?tex=x) 的平均值。

![\begin{align*}
\overline{x} &= \frac{\sum^{N}_{i=1} x_i}{N} \\
\sigma &= \sqrt{\frac{\sum^{N}_{i=1}(x_i - \overline{x})^2}{N - 1}}
\end{align*}](https://juejin.im/equation?tex=%5Cbegin%7Balign*%7D%0A%5Coverline%7Bx%7D%20%26%3D%20%5Cfrac%7B%5Csum%5E%7BN%7D_%7Bi%3D1%7D%20x_i%7D%7BN%7D%20%5C%5C%0A%5Csigma%20%26%3D%20%5Csqrt%7B%5Cfrac%7B%5Csum%5E%7BN%7D_%7Bi%3D1%7D(x_i%20-%20%5Coverline%7Bx%7D)%5E2%7D%7BN%20-%201%7D%7D%0A%5Cend%7Balign*%7D)

```
function sd(array) {
  const mean = _.mean(array)
  
  const top = array
    .map(function(x) {
      return Math.pow(x - mean, 2)
    })
    .reduce(function(left, right) {
      return left + right
    })
  const bottom = array.length - 1
  
  return Math.sqrt(top / bottom)
}

```

计算所得数据的平均值和标准差后，便可以计算 SPC 控制图中的 UCL 和 LCL 控制值了。UCL 和 LCL 的值分别为以下：

![UCL = \overline{x} + 3 \times \sigma \\
LCL = \overline{x} - 3 \times \sigma](https://juejin.im/equation?tex=UCL%20%3D%20%5Coverline%7Bx%7D%20%2B%203%20%5Ctimes%20%5Csigma%20%5C%5C%0ALCL%20%3D%20%5Coverline%7Bx%7D%20-%203%20%5Ctimes%20%5Csigma)

其中从上面的图中我们可以看到，SPC 控制图可以将从 LCL 到 UCL 中间的区域等分为 6 份，显然可以得出控制区域的区间为以下：

![\begin{align*}
& A = \left\{
             \begin{array}{lr}
             [\overline{x} + 2 * \sigma, \overline{x} + 3 * \sigma], &  \\
             [\overline{x} - 3 * \sigma, \overline{x} - 2 * \sigma] &  
             \end{array}
\right. \\
& B = \left\{
             \begin{array}{lr}
             [\overline{x} + \sigma, \overline{x} + 2 * \sigma], &  \\
             [\overline{x} - 2 * \sigma, \overline{x} - \sigma] &  
             \end{array}
\right. \\
& C = [\overline{x} - \sigma, \overline{x} + \sigma]
\end{align*}](https://juejin.im/equation?tex=%5Cbegin%7Balign*%7D%0A%26%20A%20%3D%20%5Cleft%5C%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%5Cbegin%7Barray%7D%7Blr%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%5B%5Coverline%7Bx%7D%20%2B%202%20*%20%5Csigma%2C%20%5Coverline%7Bx%7D%20%2B%203%20*%20%5Csigma%5D%2C%20%26%20%20%5C%5C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%5B%5Coverline%7Bx%7D%20-%203%20*%20%5Csigma%2C%20%5Coverline%7Bx%7D%20-%202%20*%20%5Csigma%5D%20%26%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%5Cend%7Barray%7D%0A%5Cright.%20%5C%5C%0A%26%20B%20%3D%20%5Cleft%5C%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%5Cbegin%7Barray%7D%7Blr%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%5B%5Coverline%7Bx%7D%20%2B%20%5Csigma%2C%20%5Coverline%7Bx%7D%20%2B%202%20*%20%5Csigma%5D%2C%20%26%20%20%5C%5C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%5B%5Coverline%7Bx%7D%20-%202%20*%20%5Csigma%2C%20%5Coverline%7Bx%7D%20-%20%5Csigma%5D%20%26%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%5Cend%7Barray%7D%0A%5Cright.%20%5C%5C%0A%26%20C%20%3D%20%5B%5Coverline%7Bx%7D%20-%20%5Csigma%2C%20%5Coverline%7Bx%7D%20%2B%20%5Csigma%5D%0A%5Cend%7Balign*%7D)

在 EChart 中，除了辅助线以外还提供了一个非常实用的工具 —— `visualMap`。它可以将图表中某一个区域内的元素统一为一种颜色，这正好可以应用到 SPC 控制图的三个控制区域上。

首先我们需要计算所需要的数据。

```
const mean_X = _.mean(X)
const sd_X = sd(X)

const ucl = mean_X + 3 * sd_X
const lcl = mean_X - 3 * sd_X

const areaA = [
  [ mean_X + 2 * sd_X, mean_X + 3 * sd_X ],
  [ mean_X - 3 * sd_X, mean_X - 2 * sd_X ]
]
const areaB = [
  [ mean_X + sd_X, mean_X + 2 * sd_X ],
  [ mean_X - 2 * sd_X, mean_X - sd_X ]
]
const areaC = [
  [ mean_X - sd_X, mean_X + sd_X ]
]

```

### 16.3.4 绘制 SPC 控制图

首先我们将控制线通过 `markLine` 组件绘制在图表上。

```
const option = {
  // ...
  
  yAxis: {
    type: 'value',
    name: 'x',
    nameLocation: 'end',
    
    max: Math.max(ucl + 5, Math.max(...X)),
    min: Math.min(lcl - 5, Math.min(...X))
  },
  
  series: {
    // ...
    
    markLine: {
      data: [
        { name: 'UCL', yAxis: ucl },
        { name: 'Area B', yAxis: areaB[0][1] },
        { name: 'Area C', yAxis: areaC[0][1] },
        { name: 'Mean', yAxis: mean_X },
        { name: 'Area C', yAxis: areaC[0][0] },
        { name: 'Area B', yAxis: areaB[1][0] },
        { name: 'LCL', yAxis: lcl }
      ]
    }
  }
}

```

![](https://user-gold-cdn.xitu.io/2018/10/26/166ae602a9bf03e7?w=600&h=400&f=png&s=38657)

然后结合 `visualMap` 我们便可以将完整的 SPC 控制图绘制出来了。

```
const option = {
  // ...
  
  visualMap: {
    top: 10, right: 10, // visualMap 图例位置
    pieces: [
      /* Area A */ { gt: areaA[0][0], lte: areaA[0][1], color: '#cc0033' },
      /* Area B */ { gt: areaB[0][0], lte: areaB[0][1], color: '#ffde33' },
      /* Area C */ { gt: areaC[0][0], lte: areaC[0][1], color: '#096' },
      /* Area B */ { gt: areaB[1][0], lte: areaB[1][1], color: '#ffde33' },
      /* Area A */ { gt: areaA[1][0], lte: areaA[1][1], color: '#cc0033' }
    ]
  }
}

```

![](https://user-gold-cdn.xitu.io/2018/10/26/166ae608250a756d?w=600&h=400&f=png&s=50239)

## 小结

在我们进行数据分析的时候，如果只有独立的数据图表而没有加以辅助的工具，数据分析工作的效率就会大大降低。所以利用数学计算配合图形展示的方式为数据图表添加辅助线以及其他辅助工具（如标注区域等），可以为数据图表的使用者带来极大的便利性。

# 第 17 节 更高维度的数据可视化图表

我们在前面的 16 个章节中分别学习了如何使用 JavaScript 对各种类型的数据结构进行操作处理、通过结合不同的数据结构来进行简单的数据统计，以及各种数据可视化图表的创建使用。而在这一节内容正式开始之前，我们先来简单地复习和梳理一下数据统计的基本原理，以便我们在进入后面的章节时能够更好地理解实际问题。

我们在第 11 节中曾经学习过名义数据和计量数据的概念，而这些都是对于不同维度的数据进行定义。而数据分析最基本的前提条件便是找出所需要进行分析的数据维度，就比如一个商家销售记录中的订单价格、订单类型、下单时间都是各种不同的维度。

而多维数据（多个数据，两个及以上）的组合是数据分析的基础，因为在很多情况下只针对一维数据的分析（如平均值、方差等）是很难反应出实际情况也无法直接与目标问题相关联的。所以我们在进行实际的分析之前，首先要找出我们需要进行分析的维度组合，比如交易额与时间（计量数据与有序数据）、交易类型和订单金额（分类数据与计量数据）等等。这些二维数据我们在前面的数据图表学习中都有不同程度的学习和使用过，而在这一节中我们将学习比二维更高的多维数据可视化。

## 17.1 多维数据集

实际上我们在进行各种数据分析的工作时，所能遇到的数据集基本上都会是拥有多个维度的，比如我们在第 5 节中所使用的 `crew` 人员数据集，每一个人便有 4 个维度的数据。

但这个数据集并不适合用来演示本章节所需要学习的内容，所以我们还需要其他数据集来进行使用。

还记得我们这本小册的介绍页面中的这个数据图表吗？

![](https://user-gold-cdn.xitu.io/2018/2/26/161cff7f3ffe1c5e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

这张图表是 ECharts 官方实例中的一个实例，光是在这张图表中就已经使用了 5 个不同的数据维度：

1.  X 轴：日期（时间）
2.  Y 轴：AQI 指数
3.  散点颜色：城市（分类）
4.  散点大小：PM2.5 指数
5.  颜色明暗度：二氧化硫指数

而其实在实际的数据集中，除此以外还有 PM10 指数、一氧化碳、二氧化氮以及空气污染程度，足足 9 个维度的数据。且其中有 6 个维度的数据都是数值数据，所以这个数据集非常适合作为多维数据分析的用例。

### 17.1.1 组合并转换数据集

在 ECharts 的官方实例代码中，我们可以得到这个数据集。

```
const data = [
  ['北京',1,55,9,56,0.46,18,6,'良'],
  ['北京',2,25,11,21,0.65,34,9,'优'],
  ['北京',3,56,7,63,0.3,14,5,'良'],
  ['北京',4,33,7,29,0.33,16,6,'优'],
  ['北京',5,42,24,44,0.76,40,16,'优'],
  ['北京',6,82,58,90,1.77,68,33,'良'],
  ['北京',7,74,49,77,1.46,48,27,'良'],
  ['北京',8,78,55,80,1.29,59,29,'良'],
  ['北京',9,267,216,280,4.8,108,64,'重度污染'],
  ['北京',10,185,127,216,2.52,61,27,'中度污染'],
  ['北京',11,39,19,38,0.57,31,15,'优'],
  ['北京',12,41,11,40,0.43,21,7,'优'],
  ['北京',13,64,38,74,1.04,46,22,'良'],
  ['北京',14,108,79,120,1.7,75,41,'轻度污染'],
  ['北京',15,108,63,116,1.48,44,26,'轻度污染'],
  ['北京',16,33,6,29,0.34,13,5,'优'],
  ['北京',17,94,66,110,1.54,62,31,'良'],
  ['北京',18,186,142,192,3.88,93,79,'中度污染'],
  ['北京',19,57,31,54,0.96,32,14,'良'],
  ['北京',20,22,8,17,0.48,23,10,'优'],
  ['北京',21,39,15,36,0.61,29,13,'优'],
  ['北京',22,94,69,114,2.08,73,39,'良'],
  ['北京',23,99,73,110,2.43,76,48,'良'],
  ['北京',24,31,12,30,0.5,32,16,'优'],
  ['北京',25,42,27,43,1,53,22,'优'],
  ['北京',26,154,117,157,3.05,92,58,'中度污染'],
  ['北京',27,234,185,230,4.09,123,69,'重度污染'],
  ['北京',28,160,120,186,2.77,91,50,'中度污染'],
  ['北京',29,134,96,165,2.76,83,41,'轻度污染'],
  ['北京',30,52,24,60,1.03,50,21,'良'],
  ['北京',31,46,5,49,0.28,10,6,'优'],
  ['广州',1,26,37,27,1.163,27,13,'优'],
  ['广州',2,85,62,71,1.195,60,8,'良'],
  ['广州',3,78,38,74,1.363,37,7,'良'],
  ['广州',4,21,21,36,0.634,40,9,'优'],
  ['广州',5,41,42,46,0.915,81,13,'优'],
  ['广州',6,56,52,69,1.067,92,16,'良'],
  ['广州',7,64,30,28,0.924,51,2,'良'],
  ['广州',8,55,48,74,1.236,75,26,'良'],
  ['广州',9,76,85,113,1.237,114,27,'良'],
  ['广州',10,91,81,104,1.041,56,40,'良'],
  ['广州',11,84,39,60,0.964,25,11,'良'],
  ['广州',12,64,51,101,0.862,58,23,'良'],
  ['广州',13,70,69,120,1.198,65,36,'良'],
  ['广州',14,77,105,178,2.549,64,16,'良'],
  ['广州',15,109,68,87,0.996,74,29,'轻度污染'],
  ['广州',16,73,68,97,0.905,51,34,'良'],
  ['广州',17,54,27,47,0.592,53,12,'良'],
  ['广州',18,51,61,97,0.811,65,19,'良'],
  ['广州',19,91,71,121,1.374,43,18,'良'],
  ['广州',20,73,102,182,2.787,44,19,'良'],
  ['广州',21,73,50,76,0.717,31,20,'良'],
  ['广州',22,84,94,140,2.238,68,18,'良'],
  ['广州',23,93,77,104,1.165,53,7,'良'],
  ['广州',24,99,130,227,3.97,55,15,'良'],
  ['广州',25,146,84,139,1.094,40,17,'轻度污染'],
  ['广州',26,113,108,137,1.481,48,15,'轻度污染'],
  ['广州',27,81,48,62,1.619,26,3,'良'],
  ['广州',28,56,48,68,1.336,37,9,'良'],
  ['广州',29,82,92,174,3.29,0,13,'良'],
  ['广州',30,106,116,188,3.628,101,16,'轻度污染'],
  ['广州',31,118,50,0,1.383,76,11,'轻度污染'],
  ['上海',1,91,45,125,0.82,34,23,'良'],
  ['上海',2,65,27,78,0.86,45,29,'良'],
  ['上海',3,83,60,84,1.09,73,27,'良'],
  ['上海',4,109,81,121,1.28,68,51,'轻度污染'],
  ['上海',5,106,77,114,1.07,55,51,'轻度污染'],
  ['上海',6,109,81,121,1.28,68,51,'轻度污染'],
  ['上海',7,106,77,114,1.07,55,51,'轻度污染'],
  ['上海',8,89,65,78,0.86,51,26,'良'],
  ['上海',9,53,33,47,0.64,50,17,'良'],
  ['上海',10,80,55,80,1.01,75,24,'良'],
  ['上海',11,117,81,124,1.03,45,24,'轻度污染'],
  ['上海',12,99,71,142,1.1,62,42,'良'],
  ['上海',13,95,69,130,1.28,74,50,'良'],
  ['上海',14,116,87,131,1.47,84,40,'轻度污染'],
  ['上海',15,108,80,121,1.3,85,37,'轻度污染'],
  ['上海',16,134,83,167,1.16,57,43,'轻度污染'],
  ['上海',17,79,43,107,1.05,59,37,'良'],
  ['上海',18,71,46,89,0.86,64,25,'良'],
  ['上海',19,97,71,113,1.17,88,31,'良'],
  ['上海',20,84,57,91,0.85,55,31,'良'],
  ['上海',21,87,63,101,0.9,56,41,'良'],
  ['上海',22,104,77,119,1.09,73,48,'轻度污染'],
  ['上海',23,87,62,100,1,72,28,'良'],
  ['上海',24,168,128,172,1.49,97,56,'中度污染'],
  ['上海',25,65,45,51,0.74,39,17,'良'],
  ['上海',26,39,24,38,0.61,47,17,'优'],
  ['上海',27,39,24,39,0.59,50,19,'优'],
  ['上海',28,93,68,96,1.05,79,29,'良'],
  ['上海',29,188,143,197,1.66,99,51,'中度污染'],
  ['上海',30,174,131,174,1.55,108,50,'中度污染'],
  ['上海',31,187,143,201,1.39,89,53,'中度污染']
]

```

### 17.1.2 语义化代码

由于数据集中的每一行的承载方式都是一个数组而不是我们前面所习惯使用的对象字面量，所以为了能让我们接下来所编写的代码更具可读性，需要使用一个用于“翻译”维度信息的工具来帮助我们和这份代码的阅读者更好地进行理解。

我们可以通过配合 LoDash 库所提供的方法来实现一个用于描述实际维度与数组下标之间关系的字典或对象，标识为 `d`（维度，dimension）。

```
const d = _.mapValues(_.invert(
  [ 'city', 'date', 'aqi', 'pm25', 'pm10', 'co', 'no2', 'so2', 'pollution' ]
), parseInt)
//=> {
//   city: 0,
//   date: 1,
//   ...
// }

```

这样我们就可以比较好地获取到某一个维度的数组下标了。

```
data[0][d['aqi']] //=> 55

```

> 事实上在 ECharts 中可以直接把 `[ 'city', 'date', ... ]` 这个维度数据放在 `dataset.source` 的第一行来实现类似的效果。

另外由于在 ECharts 中分组的表现形式必须为多个数据系列（Series），而我们的数据集是混在一起的，所以我们可以通过再实现一个简单的小工具来完成数据集的分割。

```
function subDataset(dataset, property, targetValue) {
  return dataset.filter(function(row) {
    return row[property] === targetValue
  })
}

console.log(subDataset(data, 0, '广州'))
//=> [
//  [ '广州', 1, 26, 37, 27, 1.163, 27, 13, '优' ],
//  [ '广州', 2, 85, 62, 71, 1.195, 60, 8, '良' ],
//  [ '广州', 3, 78, 38, 74, 1.363, 37, 7, '良' ],
//  ...
// ]

```

## 17.2 ECharts 中的多维表现方式

在我们前面学习过的数据可视化图表中，绝大部分都是对一维或二维数据进行可视化的图表。而实际上我们在 ECharts 中可以通过各种不同的方式将各种组件组合在一维或二维图表上，以表达更高维度的信息。

就以我们上面的散点图作为例子，散点的颜色、大小、明暗程度都算是不同的维度，这些都是可以用来增加图表维度容量的组件，我们可以按需使用。

### 17.2.1 基础图表

在加入表示更高维度的组件之前，我们需要先将最基础的图表绘制出来。

这里我们选取数据集中的日期 `date` 以及 AQI 指数 `aqi` 作为直角坐标轴的二维数据。

```
const option = {
  dataset: [
    { source: subDataset(data, 0, '北京') },
    { source: subDataset(data, 0, '广州') },
    { source: subDataset(data, 0, '上海') }
  ],
  
  legend: {},
  xAxis: {
    type: 'category'
  },
  yAxis: {
    type: 'value'
  },
  
  series: [
    {
      name: '北京',
      datasetIndex: 0,
    
      type: 'scatter',
      encode: {
        x: d['date'],
        y: d['aqi']
      }
    },
    {
      name: '广州',
      datasetIndex: 1,
      
      type: 'scatter',
      encode: {
        x: d['date'],
        y: d['aqi']
      }
    },
    {
      name: '上海',
      datasetIndex: 2,
      
      type: 'scatter',
      encode: {
        x: d['date'],
        y: d['aqi']
      }
    }
  ]
}

```

![](https://user-gold-cdn.xitu.io/2018/11/11/16702732abc065d1?w=600&h=400&f=png&s=35831)

可以不难地发现其实这个基础的图表中就已经包含了 3 个维度了，那么接下来就使用更多的数据组件将不同的数据维度表示出来。

### 17.2.2 添加组件

在 ECharts 中有一个非常强大的组件名为 `visualMap`，字面意思是“视觉映射组件”。它的作用是将维度数据中的**数据**与图表上的**视觉元素**进行映射，并依托 Web 应用的天然优势为可视化图表添加可操作特性，使得图表更具实用性。

> 我们在上一节中就曾经使用过该组件。

官方文档中说明 `visualMap` 支持绑定图表元素中的以下视觉元素：

*   `symbol`: 图元的图形类别。
*   `symbolSize`: 图元的大小。
*   `color`: 图元的颜色。
*   `colorAlpha`: 图元的颜色的透明度。
*   `opacity`: 图元以及其附属物（如文字标签）的透明度。
*   `colorLightness`: HSL 颜色的明暗度。
*   `colorSaturation`: HSL 颜色的饱和度。
*   `colorHue`: HSL 颜色的色调。

其中图元的类别、颜色（比如前面的按组分类）可以与离散型数据（如分类数据）相绑定，而其余的（包括颜色在内）可以与连续型数据（数值数据）相绑定。

作为例子我们先使用图元的大小和数据集中的 PM2.5 数据相绑定。

```
const option = {
  // ...
  
  visualMap: [
    {
      type: 'continuous',       // 定义为连续型数据
      text: [ 'PM2.5' ],
      dimension: d['pm25'],     // 绑定到 PM2.5 数据
      inRange: {                // 范围内的图元样式
        symbolSize: [ 10, 70 ]  // 图元大小范围
      },
      
      // 定义图例位置
      left: 'right',
      top: '10%'
    }
  ]
}

```

![](https://user-gold-cdn.xitu.io/2018/11/11/16702738a3331183?w=1180&h=740&f=gif&s=536656)

`visualMap` 还支持为范围内的内容设置可视区间，即在原本的范围内，图表的使用者还可以根据自己的需求自行调整所需要的区间大小。

```
const option = {
  // ...
  
  visualMap: [
    {
      // ...
      
      calculable: true  // 展示区间手柄
    }
  ]
}

```

![](https://user-gold-cdn.xitu.io/2018/11/11/1670273f25a56fac?w=1180&h=740&f=gif&s=982630)

### 17.2.3 多个 `visualMap` 组件

既然已经通过 `visualMap` 组件将第四个维度展示出来了，那么我们就趁热打铁把更多的维度展示在图表上吧，详细的使用方法可以自行查看 ECharts 的[官方文档](http://www.echartsjs.com/option.html#visualMap)，这里不再赘述。

*   图形类别 → 污染等级（`pollution`）
*   图元的颜色的透明度 → 二氧化硫浓度（`so2`）

```
const option = {
  // ...
  
  visualMap: [
    // ...
    
    {
      type: 'piecewise',
      text: [ '污染等级' ],
      dimension: d['pollution'],
      pieces: [
        { value: '优', symbol: 'circle' },
        { value: '良', symbol: 'rect' },
        { value: '轻度污染', symbol: 'roundRect' },
        { value: '中度污染', symbol: 'triangle' },
        { value: '重度污染', symbol: 'diamond' },
      ],
      left: 'right'
    },
    {
      type: 'continuous',
      text: [ '二氧化硫浓度' ],
      dimension: d['so2'],
      min: 0,
      max: 50,
      inRange: {
        colorAlpha: [ 0.5, 1 ],
      },
      
      calculable: true,
      orient: 'horizontal',
      top: 'bottom'
    }
  ]
}

```

![](https://user-gold-cdn.xitu.io/2018/11/11/1670274c2ce6e246?w=600&h=400&f=png&s=38154)

## 17.3 有趣的特殊维度——时间

时间是一个非常有趣的维度，因为时间维度在进行统计和可视化的过程中并没有一个标准的计量单位。比如在上面这个数据集中，时间的计量单位是天（day），总容量为 31。31 并不算是一个较大的基数，而若需要将时间维度的容量增大却有两种截然不同的方法：

1.  计量单位不变，向单边或双边扩容，即扩大时间范围；
2.  时间范围不变，缩小计量单位，即更细化维度颗粒度。

第一种方法不必作过多的说明，我们需要详细认识的是第二种方法。时间维度的计量单位有非常多：年、季、月、周、日、时、分、秒，更小的甚至还有毫秒、微秒。

一般来说，我们每向更细的方向修改一次时间计量单位，时间维度的总容量都会出现激增的情况。还记得我们在第 6 节中曾经学习和使用过的时间序列控制工具吗？通过不同的组合方式并加以统计方法，就可以运用到时间维度上并展示在数据图表上。

在 ECharts 中有一个非常厉害的组件叫 `dataZoom`，它的作用跟前面的 `visualMap.calculable` 有些类似。它可以在一个维度上设定一个范围值，并通过可自由操控的方式对数据的**视野范围**进行移动和缩放。

这个工具经常会被使用在时间维度上，因为它的使用方式和设计原理与时间维度实在是太相称了，话不多说我们直接看代码。

```
const option = {
  // ...
  
  dataZoom: [
    {
      type: 'slider' // 定义为独立的、可操作的滑动组件
    }
  ]
}

```

是的，就这么简单的几行代码就完成了对 `dataZoom` 的引入和配置。当图表上只有一个图表并且 X 坐标轴（当然也可以使用在 Y 坐标轴）唯一时，ECharts 内部会自动完成其配置。而更详细的配置方法可以自行参考[官方文档](http://www.echartsjs.com/option.html#dataZoom)。

![](https://user-gold-cdn.xitu.io/2018/11/11/1670275ca29eb128?w=1200&h=820&f=gif&s=3083820)

而若出现原本时间维度的总容量较大，而希望图表的初始展示范围只选取其中的一部分时，通过为 `dataZoom` 添加初始配置即可。

```
const option = {
  // ...
  
  dataZoom: [
    {
      type: 'slider',
      
      startValue: 15,
      endValue: 31
    }
  ]
}

```

![](https://user-gold-cdn.xitu.io/2018/11/11/1670276b3920e19d?w=600&h=400&f=png&s=33409)

## 小结

这一节中我们学习了如何充分利用 ECharts 所提供的各种工具来扩张我们在二维平面上数据图表的维度容量。同时我们也看到了 Web 为传统统计、数据可视化所带来的新变化，非常简单、直接、直观的可操作性使得数据可视化图表变得非常的生动和更加实用。

本节为本小册中，介绍关于 ECharts 使用的最后一节。从下一节开始，我们将正式进入开发 JavaScript 数据应用的环节，学习开发一个真正具有实际价值的数据应用。

### 习题

请自行收集身边的各种数据，并整理成多维度数据集。结合本节所学知识自行开发一个具有良好可用性的多维度数据图表。

# 第 18 节 动态数据应用 · 用数据流概念重新理解数据转换

我们在前面的章节（如第 8 节和第 9 节）中学习了如何对我们不同的数据内容进行转换从而得到另外一种新的数据以满足我们的实际需求。

而在动态数据应用的开发中，数据的来源很有可能是多个数据源甚至来自不确定的数据源（统一数据接口的不同数据源），而这些数据源中很有可能包括流式数据源。流式数据源与传统统计学中的数据集有着非常大的差别，一般来说统计学所使用的数据源都来自于静态数据集，也就是说统计结果的时效性依赖于数据集的时效性，一旦数据集过时也就意味着统计结果的过时。而流式数据集的好处是数据内容可以不断地更新，比如股票数据、人口数据、天气数据等等，具有较强的时效性。

而在 JavaScript 的数据应用中，流式数据集的来源可以有很多种类，可以使来自服务端的实时数据（实时通讯或服务器推送等）也可以是来自用户的实时操作（数据录入等）。当然如何接受实时数据并不是我们这本小册所关注的内容，我们要学习的是当我们需要利用流式数据集时，如何更好地处理数据处理或者进行数据统计。

## 18.1 数据层面 · 不断产生的数据——流式数据

流式数据顾名思义就是以流的方式产生的数据，最显而易见的流式数据就是随着时间发展不断产生新元素的时序数据。

### 18.1.1 两种不同的流式数据

一般来说数据流是以块（chunk）的形式不断到达数据处理层的，比如时序数据中，数据流会不断地把每一个单位时间内数据块产生和传递到数据消费方。

```
dataSource.on('tick', function(time, chunkData) {
  console.log(time, chunkData)
})
//=> 1 {...}
//=> 2 {...}
//=> 3 {...}
//=> ...

```

数据块是数据流的基本形式，但有的时候数据提供方（如一些第三方服务的 SDK）会先行对实时数据进行处理，并通过如响应式（Reactive）的方式将变更后的完整实时数据集提供给数据消费方。 如果需要进行处理和统计的实时数据集形式为后者，那么作为消费方只需要关心如何响应新数据集即可，其余的与静态数据集并无差别。

```
setInterval(function() {
  console.log(dataSource.dataset.length)
}, 1e3) // run per second
//=> 0
//=> 1
//=> 2
//=> ...

```

### 18.1.2 流式数据的处理和计算

我们可以使用两个非常简单且熟悉的例子，来说明这两种不同的数据流的差别，分别为平均数和众数。对一组数据进行数学统计时，这两个数学特征值在中都是非常重要的指标。在第 5 节中我们就已经学习过这两个数学特征值的计算方法。

假设我们有一个实时数据源，它会随着时间不断地产生一个数值（实时温度、股票价格、股票交易量等等），而我们需要计算其在一个时间周期内的平均数和众数。

假若该实时数据源是以完整的数据集提供给我们的时候我们就可以直接使用前面的方法进行计算即可。（`_.reduceByKey` 方法请参考第 5 节）

```
function mode(array) {
  if (array.length <= 0) {
    return []
  }

  const countTuples = _.reduceByKey(
    array.map(function(item) {
      return [ item, 1 ]
    }),
    function(left, right) {
      return left + right
    }
  )
  
  return _.chain(countTuples)
    .map(function(tuple) {
      // Reverse the tuple
      return [ tuple[1], tuple[0] ]
    })
    .groupBy(0)
    .mapValues(function(tuples) {
      return tuples.map(function(tuple) {
        return tuple[1]
      })
    })
    .toPairs()
    .map(function(tuple) {
      return [ parseInt(tuple[0]), tuple[1] ]
    })
    .sort(function(leftTuple, rightTuple) {
      return rightTuple[0] - leftTuple[0]
    })
    .head()
    .value()[1]
    .map(function(item) {
      return parseFloat(item)
    })
}

setInterval(function() {
  const mean = _.mean(dataSource.dataset)
  const modeNums = mode(dataSource.dataset)
  
  console.log(dataSource.dataset, mean, modeNums)
}, 1e3)
//=> [] NaN []
//=> [ 1 ] 1 [ 1 ]
//=> [ 1, 2 ] 1.5 [ 1, 2 ]
//=> [ 1, 2, 2 ] 1.67 [ 2 ]
//=> ...

```

但若数据源是通过数据块的形式提供给消费方时，情况就有点不一样了，我们先来看看平均值的计算。

我们知道平均值的计算公式是数组的总和 ![\sum_{i=1}^n x_i](https://juejin.im/equation?tex=%5Csum_%7Bi%3D1%7D%5En%20x_i) 除以数组的长度 ![n](https://juejin.im/equation?tex=n)，假设我们要给数组 ![x](https://juejin.im/equation?tex=x) 的子集，前 ![n - 1](https://juejin.im/equation?tex=n%20-%201) 个元素的数组进行求平均，就可以得到以下两条公式。

![\begin{gather}
m_n = \frac{\sum_{i=1}^n x_i}{n} \\
m_{n-1}= \frac{\sum_{i=1}^{n-1} x_i}{n-1}
\end{gather}](https://juejin.im/equation?tex=%5Cbegin%7Bgather%7D%0Am_n%20%3D%20%5Cfrac%7B%5Csum_%7Bi%3D1%7D%5En%20x_i%7D%7Bn%7D%20%5C%5C%0Am_%7Bn-1%7D%3D%20%5Cfrac%7B%5Csum_%7Bi%3D1%7D%5E%7Bn-1%7D%20x_i%7D%7Bn-1%7D%0A%5Cend%7Bgather%7D)

我们通过对 2 式对 1 式进行变形就可以得到我们所需要的公式。

![m_n = \frac{{m_{n-1} \times (n-1) + x_n}}{n}](https://juejin.im/equation?tex=m_n%20%3D%20%5Cfrac%7B%7Bm_%7Bn-1%7D%20%5Ctimes%20(n-1)%20%2B%20x_n%7D%7D%7Bn%7D)

这条公式表示了长度为 ![n](https://juejin.im/equation?tex=n) 的数组 ![x](https://juejin.im/equation?tex=x) 的平均值等于数组 ![x](https://juejin.im/equation?tex=x) 的前 ![n - 1](https://juejin.im/equation?tex=n%20-%201) 个元素的平均值乘以 ![n-1](https://juejin.im/equation?tex=n-1) 再加上第 ![n](https://juejin.im/equation?tex=n) 个元素后除以 ![n](https://juejin.im/equation?tex=n)。其中 ![x_n](https://juejin.im/equation?tex=x_n) 就可以理解为数据流中的数据块，我们只需要维护上一个平均值和上一个数据集长度即可。以下例子假设数据集不断“吐出”数值 `1,2,3,4` 并以此类推。

```
let mean = 0
let n = 0

dataSource.on('tick', function(time, chunkData) {
  mean = ((mean * n) + chunkData） / (++n)
  console.log(mean)
})
//=> 1
//=> 1.5
//=> 2
//=> 2.5
// ...

```

但众数就不一样了，众数的每次计算都需要对数据集的整体进行计算，而没办法像平均值一样简单地通过增量式的计算方法进行统计。所以对于以数据块形式进行消费的数据集来说，如统计众数这种整体计算或同比环比等错位计算需求，消费方需要自行维护所接收到的所有数据块，并组合成一个完整的数据集，然后在对数据集进行统计。

当然只针对于统计众数来说，也可以通过维护一个元素和频次的的哈希表来减少计算的次数以满足增量计算的需求，但这也只是一个变相的维护完整数据集而已。当我们需要对数据集进行多种不同的处理和统计需求时，更稳妥的方式还是需要维护完整的数据集。

## 18.2 逻辑层面 · 流式处理数据 —— 函数串流

上面我们先从数据层面利用数据流的概念重新理解了数据集，而现在我们把目光往上移动，看看如何利用数据流的概念重新理解数据处理。

事实上我们在本小册的前面这么多章节中就已经接触了很多这样的例子了，就比如最常见的词频统计，我们将一个一维的字符串数据一步一步地进行拆分、转换处理，最后得到一个二维的数据集，中间经过了以下步骤：

1.  分割单词：`"foo foo bar"` → `["foo", "foo", "bar"]`
2.  添加频次：`["foo", "foo", "bar"]` → `[["foo", 1], ["foo", 1], ["bar", 1]]`
3.  合并同类项：`[["foo", 1], ["foo", 1], ["bar", 1]]` → `[["foo", 2], ["bar", 1]]`
4.  数据形式转换：`[["foo", 2], ["bar", 1]]` → `[{ word: "foo", count: 2 }, { word: "bar", count: 1 }]`

如果我们把每一个步骤都单独以一个函数的形式编写，便可以得到以下处理函数。

```
// 分割单词
function splitWords(string) {
  return string.split(/\s+/g)
}

// 添加频次
function addCount(words) {
  return words.map(function(word) {
    return [ word, 1 ]
  })
}

// 合并同类项
function sumWordCount(tuples) {
  return _.reduceByKey(tuples, function(left, right) {
    return left + right
  })
}

// 数据形式转换
function convertTuplesToDataset(tuples) {
  return tuples.map(function(tuple) {
    return {
      word: tuple[0],
      count: tuple[1]
    }
  })
}

```

把这些处理函数组合起来，便完成了整个词频统计流程。

```
const rawText = "foo foo bar"
const dataset = convertTuplesToDataset(
  sumWordCount(
    addCount(
      splitWords(rawText)
    )
  )
)

console.log(dataset)
//=> [
//=>   { word: "foo", count: 2 },
//=>   { word: "bar", count: 1 }
//=> ]

```

以函数封装的方式将数据处理的的逻辑抽象出来，第一可以让代码逻辑变得比较简洁干净，二来可以避免代码中副作用（原数据被修改）的产生，减少数据上出现以外的情况。

### 18.2.1 虚拟实体 Getter

而且暂且抛开词频统计不说，假设我们将上面的四个步骤用 A、B、C、D 表示，可以表示为以下流程。

![](https://user-gold-cdn.xitu.io/2018/12/28/167f2f7450059f3c?w=471&h=111&f=png&s=5637)

如果说我们有另外一个数据转换的流程可以复用其中的步骤 A 和 B，并在其后接着完成步骤 E 和 F。

![](https://user-gold-cdn.xitu.io/2018/12/28/167f2f769e40de48?w=471&h=231&f=png&s=10040)

而因为两个流程的起始点都是一样的，所以不仅仅可以复用前端重合的步骤，就连结果也是可以被复用的。这里就要介绍到一种编程语言中的概念 Getter。

Getter 指的是通过定义一个**无传入参数**函数，在函数中经过若干处理逻辑后返回一个值，而 Getter 的使用方并知道该对象是一个函数，使用的时候只像在调用一个变量。

```
const object = {
  name: 'iwillwen',
  
  // Getter
  get message() {
    return `Hello ${this.name}`
  }
}

console.log(object.message) //=> Hello iwillwen

object.name = 'juejin'
console.log(object.message) //=> Hello juejin

```

我们可以把前面的词频统计流程利用 Getter 整合起来。

```
const wordCountAnalyzer = {
  rawText: '',
  
  get splittedWords() {
    return splitWords(this.rawText)
  },
  
  get wordsWithOne() {
    return addCount(this.splittedWords)
  },
  
  get wordsWithCount() {
    return sumWordCount(this.wordsWithOne)
  },
  
  get wordCountDataset() {
    return convertTuplesToDataset(this.wordsWithCount)
  }
}

wordCountAnalyzer.rawText = 'hello world'
console.log(wordCountAnalyzer.wordCountDataset)
//=> [
//   { word: 'hello', count: 1 },
//   { word: 'world', count: 1 }
// ]

wordCountAnalyzer.rawText = 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua'
console.log(wordCountAnalyzer.wordCountDataset)
//=> [
//   { word: 'Lorem', count: 1 },
//   { word: 'ipsum', count: 1 },
//   ...
// ]

```

我们可以发现当我们修改 `wordCountAnalyzer.rawText` 后，`wordCountAnalyzer.wordCountDataset` 也同时随着改变，而中间流程的 Getter 也会随着变化。

应用到我们前面假设的多处理流程中就会是这样的：

```
const object = {
  originalValue: '<something>',        // ─┐   数据源
                                       //  │
  get A() {                            // <┘
    return methodA(this.originalValue) // ─┐
  },                                   //  │
                                       //  │
  get B() {                            // <┘
    return methodB(this.A)             // ─┬─┐
  },                                   //  │ │
                                       //  │ │
  get C() {                            // <┘ │ 流程 1
    return methodC(this.B)             // ─┐ │
  },                                   //  │ │
                                       //  │ │
  get D() {                            // <┘ │
    return methodD(this.C)             //    │
  },                                   //    │
                                       //    │
  get E() {                            // <──┘ 流程 2
    return methodE(this.B)             // ─┐
  },                                   //  │
                                       //  │
  get F() {                            // <┘
    return methodF(this.E)
  }

}

```

### 18.2.2 Getter with Vue.js

而 Getter 的特性在一些开发框架中则通过其内部的响应逻辑完成，比如 Vue.js 中则提供 `computed` 的接口以完成对其 `data` 内容的转换。

> 后面的章节中将会使用 Vue.js 作为本小册的 UI 开发框架，不了解的同学可以自行通过官方文档、其他教程或小册进行学习。

```
const vm = new Vue({
  // ...
  
  data: {
    originalValue: '<something>'
  },
  
  computed: {
  
    A() {
      return methodA(this.originalValue)
    },
    
    B() {
      return methodB(this.A)
    }
    
  }
})

console.log(vm.B) //=> something processed by method B

```

## 18.3 动态地流式处理数据流

上面我们分别介绍了数据流的概念以及通过封装函数和应用 Getter 的方式对数据进行流式的处理。Getter 的好处是可以让数据处理的过程在数据的不断变化中自动化起来，而不断变化恰恰正是数据流的特点，那么将这两者组合起来便会将数据流的流动路径往后延长，让流式数据集也能享受全自动数据处理的优势。

但有的时候在我们的数据处理逻辑中，需要处理的对象并不只有原数据本身，有一些数据处理逻辑是需要引入参数的，比如**筛选过滤**需要引入一个或多个筛选条件，这样便跟上面 Getter 的“无参数函数”有所冲突了。更甚者，这些需要引入的参数本身也是动态的，无法写死在处理逻辑中。

对于这种情况，我们一般的做法便是将这些参数也看作是一个流式数据源，并将其一同引入到处理流程中，当然其切入的点可能并一定是从流程的最初始位置，而是在其被需要的位置直接引入。

这里我们直接使用 Vue.js 作为例子，我们通过模拟一个不断有新数据产生的实时数据集，然后我们需要通过一个过滤参数将过滤后的数据展示出来。

```
<!-- Vue.js App -->
<div id="app">
  <label for="type">Type Filter: </label>
  <select name="type" id="type" v-model="typeFilter">
    <option value="none">None</option>
    <option>----------</option>
    <option v-for="type in typesSet" :value="type" :key="type">{{type}}</option>
  </select>
  
  <ul>
    <li v-for="item in filteredDataset" :key="item.timestamp">Timestamp: {{item.timestamp}} - Type: {{item.type}} - Value: {{item.value}}</li>
  </ul>
</div>

```

```
// app.js
const vm = new Vue({
  
  el: '#app',
  
  data: {
    dataset: [],
    typeFilter: 'none',
    
    typesSet: [ 'foo', 'bar', 'test' ]
  },
  
  computed: {
    filteredDataset() {
      if (this.typeFilter === 'none') {
        return this.dataset
      }
      
      return this.dataset.filter(item => item.type === this.typeFilter)
    }
  },
  
  mounted() {
    // 模拟流式数据集
    setInterval(() => {
      const randomType = this.typesSet[Math.round(Math.random() * (this.typesSet.length - 1))]

      this.dataset.push({
        type: randomType,
        timestamp: Date.now(),
        value: Math.random().toString(32).substr(2)
      })
    }, 1e3)
  }
  
})

```

DEMO 在线地址：[https://codepen.io/iwillwen/pen/ebEwZE?editors=1010](https://codepen.io/iwillwen/pen/ebEwZE?editors=1010)

![](https://user-gold-cdn.xitu.io/2018/12/28/167f2f83b5002f04?w=1180&h=712&f=gif&s=1725210)

## 小结

我们终于在这一小节中介绍到了跟本小册标题相关的动态数据了，这对许多只了解和使用过静态数据集的同学来说会是一个非常新鲜的事物。我们还学习了两种不同的数据流数据和它们对应的处理消费方式，知道了如何使用合适的方式进行相应的处理。

在接下来的章节中我们将更深入动态数据应用的开发中来。

### 习题

1.  请模仿平均数和众数的应用，分别举出一对可以应用在数据块和整体流式数据集的例子；
2.  请利用 18.3 中的 DEMO，对其中的动态数据集和过滤条件进行添加和调整，寻找更多的可能性；
3.  请模仿 18.3 的 DEMO，将我们前面所学习到的词频统计使用到 Vue.js 应用中。

# 第 19 节 动态数据应用 · 使用 Vue.js 为数据流添加动态转换过滤器

在上一节中我们学习了如何利用 Vue.js 配合我们之前所学习过的数据处理方法来处理流式数据。在这一节中，我们将继续学习如何利用 Vue.js 来应对结构不确定的数据流。

## 19.1 基于数据的动态转换过滤器

在上一节中我们通过一个既定的 `typeSet` 来模拟一个不断产生数据的流式数据集，而在我们使用 Vue.js 进行构建的数据应用中，也是根据这个 `typeSet` 来**提前**生成了一个用于过滤数据的过滤器。

但有的时候前端的数据应用并不知道来自其他数据服务的数据内容究竟有哪些过滤项，那么我们便需要根据数据应用所得到的实际数据来生成过滤器。

```
function mockDataSource(typesSet) {
  const dataset = []

  const timer = setInterval(() => {
    const randomType = typesSet[Math.round(Math.random() * (typesSet.length - 1))]

    dataset.push({
      type: randomType,
      timestamp: Date.now(),
      value: Math.random().toString(32).substr(2)
    })
  }, 1e3)

  return {
    dataset,
    stop() {
      clearInterval(timer)
    }
  }
}

const dataSource = mockDataSource(Array(10).fill(1).map((_, i) => `type${i + 1}`))

```

这段代码中我们模拟了一个包含多种可过滤数据 `type` 的流式数据集，且该数据集过滤字段内容是“不可预知”的。那么我先把它利用 Vue.js 展示到页面上看一下。

![](https://user-gold-cdn.xitu.io/2019/1/22/16873ea78a8e44ef?w=434&h=326&f=gif&s=138516)

DEMO 在线地址：[https://codepen.io/iwillwen/pen/oJKMaK?editors=1010](https://codepen.io/iwillwen/pen/oJKMaK?editors=1010)

很好，现在我们再把上一节中层间实现过的类型过滤器应用到这里来。但不一样的是这一次这个流式数据集中所包含的类型都是不可知的，那么按照我们上一节中所学习到的方法，我们则可以将流式数据集中的类型集通过流失处理的方式也处理成一个数据流，应用到过滤器选项中。

我们可以通过使用 Lodash 中的 `groupBy` 方法先将流式数据集按照 `type` 字段进行聚合，然后再通过 `keys` 方法得到聚合后的聚合键集，从而得到动态的过滤选项。

```
<div id="app">
  <label for="type">Type Filter: </label>
  <select name="type" id="type" v-model="typeFilter">
    <option value="none">None</option>
    <option>----------</option>
    <option v-for="type in typesSet" :value="type" :key="type">{{type}}</option>
  </select>

  <table>
    <tr>
      <th>Type</th>
      <th>Timestamp</th>
      <th>Value</th>
    </tr>
    <tr v-for="item in filteredDataset" :key="item.timestamp">
      <td>{{item.type}}</td>
      <td>{{item.timestamp}}</td>
      <td>{{item.value}}</td>
    </tr>
  </table>
</div>

```

```
function mockDataSource(typesSet) {
  const dataset = []

  const timer = setInterval(() => {
    const randomType = typesSet[Math.round(Math.random() * (typesSet.length - 1))]

    dataset.push({
      type: randomType,
      timestamp: Date.now(),
      value: Math.random().toString(32).substr(2)
    })
  }, 1e3)

  return {
    dataset,
    stop() {
      clearInterval(timer)
    }
  }
}

const dataSource = mockDataSource(Array(10).fill(1).map((_, i) => `type${i + 1}`))

new Vue({
  el: '#app',
  data: {
    typeFilter: 'none',
    dataset: dataSource.dataset
  },
  computed: {
    typesSet() {
      return _.keys(_.groupBy(this.dataset, 'type'))
    },
    
    filteredDataset() {
      if (this.typeFilter === 'none') {
        return this.dataset
      }
      
      return this.dataset.filter(item => item.type === this.typeFilter)
    }
  }
})

```

DEMO 在线地址：[https://codepen.io/iwillwen/pen/PXMBvb?editors=1010](https://codepen.io/iwillwen/pen/PXMBvb?editors=1010)

![](https://user-gold-cdn.xitu.io/2019/1/22/16873eab27e72097?w=434&h=326&f=gif&s=277568)

## 19.2 更复杂的动态转换过滤器

笔者在工作中经常会遇到一些非常复杂的动态数据开发需求，其中不乏如 BI（Business Intelligence）之类的项目，具体可以参考如 Superset、Metabase 等等优秀的开源项目。在这些项目中，数据与数据应用之间是相隔离的（特别是通用的开源项目），也就是数据应用除了知道数据源以一个二维表的形式存在以外，对这个数据集的内容和结构完全不清楚。

一般这种情况会出现一个可配置的方案，也就是数据应用本身是一个可配置的通用转换、过滤、展示工具，而数据源和表结构则以配置的方式传递给数据应用。为了表达这种情况的极端性，我们先从模拟一个较为复杂的数据集开始。

```
function genTypes(columnName, count = 10) {
  return Array(count).fill(1).map((_, i) => `${columnName}-type${i + 1}`)
}

function genColumns(count = 10) {
  return Array(count).fill(1).map((_, i) => {
    const columnName = `column${i + 1}`
    const types = genTypes(columnName)

    return {
      name: columnName, types
    }
  })
}

function mockDataSource(columnsCount = 10) {
  const dataset = []

  const columns = genColumns(columnsCount)

  const timer = setInterval(() => {
    const timestamp = Date.now()
    const value = Math.random().toString(32).substr(2)
    
    const item = {
      timestamp, value
    }

    columns.forEach(({ name, types }) => {
      const randomType = types[Math.round(Math.random() * (types.length - 1))]

      item[name] = randomType
    })

    dataset.push(item)
  }, 1e3)

  return {
    dataset,
    stop() {
      clearInterval(timer)
    }
  }
}

const dataSource = mockDataSource(4)

setInterval(() => {
  console.log(dataSource.dataset[dataSource.dataset.length - 1]) // The last inserted one
}, 1e3)
//=> { timestamp: 1547970415609, value: '4ta9d9chh9o', column1: 'column1-type1', column2: 'column2-type7', column3: 'column3-type2', column4: 'column4-type6' }
//=> { timestamp: 1547970416612, value: 'cobh86f288', column1: 'column1-type7', column2: 'column2-type6', column3: 'column3-type9', column4: 'column4-type1' }
//=> ...

```

### 19.2.1 将未知结构的数据集展示在页面上

在这个例子中我们模拟了一个拥有多个不同字段的数据集，而且其中的每一个字段都有多种不确定的可过滤值。现在我们需要将这个数据集展示到页面上，由于数据应用在开发的时候是不清楚数据集的结构的，所以在展示之前首先需要对数据集进行转换以得到该数据集的字段列表。

因为数据结构在数据到达之前是不可知的，而且一般情况下我们需要约定数据集中的每一个个体数据都严格符合整体结构。这样的情况下，便可以通过取得数据集中的第一个记录来取得该数据集的整体结构。

```
<div id="app">
  <table>
    <tr>
      <th v-for="column in columnNames" :key="column">{{column}}</th>
    </tr>
    <tr v-for="(item, i) in dataset" :key="i">
      <td v-for="column in columnNames" :key="column">
        {{item[column]}}
      </td>
    </tr>
  </table>
</div>

```

```
// ...

new Vue({
  el: '#app',

  data: {
    dataset: dataSource.dataset
  },

  computed: {
    columnNames() {
      if (this.dataset && this.dataset.length > 0) {
        return _.keys(this.dataset[0])
      }

      return []
    }
  }
})

```

DEMO 在线地址：[https://codepen.io/iwillwen/pen/NeQmRX?editors=1010](https://codepen.io/iwillwen/pen/NeQmRX?editors=1010)

![](https://user-gold-cdn.xitu.io/2019/1/22/16873eae36e84a83?w=772&h=412&f=gif&s=433108)

### 19.2.2 为未知结构的数据集添加动态过滤器

我们已经将这个复杂的数据集通过动态地感知到其结构以后展示在了页面上，那么接下来便需要对这个数据集进行转换过滤了，因为对各种不确定的数据集进行各种操作正正就是 BI 项目的基本需求。

而且对于这种拥有多个不同字段的数据集，数据应用拥有高度可配置的过滤机制往往是最起码的要求。数据表格展示作为最基本的数据展示方式，我们可以回想一下数据应用领域中的“老大哥”——Microsoft Excel。对于 Excel 本身来说，每一个处理的表格文件都是一个不确定的数据集，而作为用户的我们可以通过其中的“筛选过滤”功能对数据集中的各种字段进行过滤，而且这个机制是可以多字段叠加的。那么在我们开发的数据应用中该如何进行开发呢？

首先需要设计好的是，因为在这个需求中我们要控制两个对象，一个是数据集本身，另外一个则是控制过滤器本身的配置集。我们将这个过滤器的配置集单独处理，每个过滤器包含两个值：字段名和过滤目标值。默认情况下每一个过滤器都是为了将数据集中的制定字段的指定值记录过滤出来，当然在实际应用开发中很有可能还有有更多的选项，比如大于小于之类的过滤方式。

```
const filters = [
  {
    column: '<column>',
    value: '<value>'
  },
  // ...
]

```

当我们只有一个过滤器的时候，我们可以直接判断数据集中的每一个记录中的指定字段是否为指定过滤值。那么当有多个过滤器时，我们可以使用 JavaScript 中的一个原生 API `Array.prototype.every` 来完成这一操作。

```
const row = { /* ... */ }
const filters = [ /* ... */ ]

const isPassed = filters.every(filter => {
  if (filter.column === 'none' || filter.value === 'none') {
    return true
  }
  
  return row[filter.column] === filter.value
})

```

通过 Vue.js 的一些比较基本的使用方法，我们可以非常方便地对过滤器的配置集进行管理。

```
new Vue({
  
  // ...
  
  data: {
    // ...
  
    filters: []
  },
  
  methods: {
    addFilter() {
      this.filters.push({
        column: 'none',
        value: 'none'
      })
    },
    removeFilter(index) {
      this.filters.splice(index, 1)
    }
  }
})

```

但是要让用户能够通过数据应用所提供的功能，来为数据集添加动态过滤器，那么首先就得让用户知道当前有哪些可选值。所以跟需要知道数据集结构中有哪些字段一样，过滤器的可选值还包含了每一个字段中有哪些现有值可以作为过滤的目标值。那么还记得我们在第 10 节中曾经学习过的行式数据集和列式数据集的转换方法吗？在默认的行式数据集中，我们很难通过某一个字段名取得该字段的所有可选值。但是使用列式数据集在处理这个需求时，则变得有着非常好的天然优势。配合着 Lodash 的 `_.uniq` 取得每一个字段中的所有唯一值。

```
new Vue({
  // ...
  
  data: {
    // ...
  
    dataset: dataSource.dataset
  },
  
  computed: {
    colOrientedDataset() {
      return rowOriented2ColOriented(this.dataset)
    },

    columnNames() {
      if (this.dataset && this.dataset.length > 0) {
        return _.keys(this.dataset[0])
      }

      return []
    },

    optionsOfColumns() {
      return _.fromPairs(
        this.columnNames.map(columnName => [ columnName, _.uniq(this.colOrientedDataset[columnName]) ])
      )
    },
  }
})

```

取得这些信息之后，就可以在页面上开发过滤器的控制组件了。我们使用一个简单的列表来表示这个过滤器的配置集，而列表中的每一个元素包含两个 `<select>` 组件分别对应着过滤器的对应字段和过滤目标值。`<select>` 组件中则分别使用前面准备好的 `columnNames` 和 `optionsOfColumns` 来生成 `<option>` 可选项。

```
<div id="app">
  <button @click="addFilter">Add Filter</button>

  <ul id="filters">
    <li v-for="filter, i in filters" :key="i">
      <select v-model="filter.column">
        <option value="none">None</option>
        <option
          v-for="columnName in columnNames"
          :value="columnName"
          :key="columnName"
        >
          {{columnName}}
        </option>
      </select>
      =
      <select v-model="filter.value">
        <option value="none">None</option>
        <option
          v-for="option in (optionsOfColumns[filter.column] || [])"
          :value="option"
          :key="option"
        >
          {{option}}
        </option>
      </select>

      <button @click="removeFilter(i)">x</button>
    </li>
  </ul>
</div>

```

最后我们将这些元素都整合起来便可以得到一个相当不错的效果。

```
<div id="app">
  <button @click="addFilter">Add Filter</button>

  <ul id="filters">
    <li v-for="filter, i in filters" :key="i">
      <select v-model="filter.column">
        <option value="none">None</option>
        <option
          v-for="columnName in columnNames"
          :value="columnName"
          :key="columnName"
        >
          {{columnName}}
        </option>
      </select>
      =
      <select v-model="filter.value">
        <option value="none">None</option>
        <option
          v-for="option in (optionsOfColumns[filter.column] || [])"
          :value="option"
          :key="option"
        >
          {{option}}
        </option>
      </select>

      <button @click="removeFilter(i)">x</button>
    </li>
  </ul>

  <table>
    <tr>
      <th v-for="column in columnNames" :key="column">{{column}}</th>
    </tr>
    <tr v-for="(item, i) in filteredDataset" :key="i">
      <td v-for="column in columnNames" :key="column">
        {{item[column]}}
      </td>
    </tr>
  </table>
</div>

```

```
// ...

const dataSource = mockDataSource(4)

function applyColumn(colDataset, columnName) {
  if (!_.has(colDataset, columnName)) {
    colDataset[columnName] = []
  }

  return colDataset
}

function rowOriented2ColOriented(rowDataset) {
  let colDataset = {}

  rowDataset.forEach(function(row, i) {
    const columnNames = _.keys(row)

    columnNames.forEach(function(columnName) {
      colDataset = applyColumn(colDataset, columnName)
      colDataset[columnName][i] = row[columnName]
    })
  })

  return colDataset
}

new Vue({
  el: '#app',

  data: {
    filters: [],
    dataset: dataSource.dataset
  },

  computed: {
    colOrientedDataset() {
      return rowOriented2ColOriented(this.dataset)
    },

    columnNames() {
      if (this.dataset && this.dataset.length > 0) {
        return _.keys(this.dataset[0])
      }

      return []
    },

    optionsOfColumns() {
      return _.fromPairs(
        this.columnNames.map(columnName => [ columnName, _.uniq(this.colOrientedDataset[columnName]) ])
      )
    },

    filteredDataset() {
      return this.dataset.filter(row => {
        return this.filters.every(({ column, value }) => {
          if (column === 'none' || value === 'none') {
            return true
          }

          return row[column] === value
        })
      })
    }
  },

  methods: {
    addFilter() {
      this.filters.push({
        column: 'none',
        value: 'none'
      })
    },
    removeFilter(index) {
      this.filters.splice(index, 1)
    }
  }
})

```

DEMO 在线地址：[https://codepen.io/iwillwen/pen/Rvbeox?editors=1010](https://codepen.io/iwillwen/pen/Rvbeox?editors=1010)

![](https://user-gold-cdn.xitu.io/2019/1/22/16873eb14d43bd23?w=780&h=503&f=gif&s=1742963)

## 小结

在本小节中我们从较为简单的流式数据集触发，一步一步地尝试添加动态过滤器，并且也从简单的、确定的数据集向更常见的复杂且不确定结构的数据集学习，最后在这种数据集上结合软件工程中的“分治”手段将复杂的问题切分为三个部分：动态数据源、动态数据源的转换过滤展示以及过滤机制的控制管理。相信从第一节开始学习到现在的你已经掌握了非常多的各种数据结构和对他们进行逻辑处理的方法，那么在最后一节中我们将着手开发一个实际的项目，把我们曾经学习过的东西应用起来。

### 习题

尝试在过滤器机制中为每一个过滤器添加过滤方法，即从原本的等于添加如大于、小于、不等于、包含、不包含等等，完成后在评论区提交你的 CodePen 地址。

# 第 20 节 动态数据应用 · 应用高大上的动态数据流（上）

我们已经学会了如何将一个不断生成的数据源展示在页面上并加以转换处理和过滤，但是到现在为止我们使用的都是通过自行模拟的实时数据源，并没有真正地接触到从其他系统得到的实时数据源。那么在本节中我们将要自己实现一个真正的实时数据源，并将其应用到我们的数据应用中。

## 20.1 构建真实实时数据源

笔者是一个游戏爱好者，前些天发现家里的网络在某一个时间段会变得非常不稳定，而在笔者喜欢的其中一款游戏中便自带了一个用于监控网络情况的数据图表工具。而这个图表的数据源其实就是通过对游戏服务器的 IP 进行持续的发送 Ping 请求，并记录其返回耗时。

### 20.1.1 Ping

而 Ping 工具事实上就是我们对网络情况进行监控的一个基本工具，我们可以使用这一行命令检查我们本地设备到 `www.baidu.com` 所在服务器（经过 DNS 解析后的 CDN 节点）的网络情况。

```
$ ping www.baidu.com

```

```
PING www.a.shifen.com (14.215.177.39): 56 data bytes
64 bytes from 14.215.177.39: icmp_seq=0 ttl=56 time=9.315 ms
64 bytes from 14.215.177.39: icmp_seq=1 ttl=56 time=7.433 ms
64 bytes from 14.215.177.39: icmp_seq=2 ttl=56 time=7.327 ms
64 bytes from 14.215.177.39: icmp_seq=3 ttl=56 time=19.945 ms
64 bytes from 14.215.177.39: icmp_seq=4 ttl=56 time=6.379 ms
64 bytes from 14.215.177.39: icmp_seq=5 ttl=56 time=9.088 ms
...

```

我们可以看到其中的一个重要指标便是 `time` 字段，这个字段所表示的便是从我们本地设备连接到 `www.baidu.com` 所在 CDN 节点并完成返回的耗时。一般来说 Ping 工具会在上一次发送 Ping 信号后一秒进行下一次发送，而如果网络情况良好的话，几毫秒的耗时基本可以忽略不计并把这个数据看做一个平均一秒生成一个新数据的实时数据源。

### 20.1.2 Commands Piping

在 Unix 或 Linux 系统中，命令行有一个非常有用的特性叫命令串流（Pipe），意思是在一行命令中运行多个程序，每一个程序通过标准输入输入（Standrad Input/Output）按顺序串联形成一个串流。

假设有程序 `a` 和程序 `b`，在命令行中运行 `a | b`。其中 `a` 程序会向标准输出中打出 `Hello, World.`，那么在 `b` 程序中就可以通过标准输入得到 `Hello, World.`。

比如假设在某一个文件夹下有许多不同的文件，我希望查找该文件夹根目录下文件名包含 `.md` 的文件，就可以通过串流 `ls` 命令（列出当前文件夹根目录下所有文件、文件夹）和 `grep` 命令（按指定匹配模式匹配输入数据中的内容）来实现。

```
$ ls -l | grep .md

```

```
-rw-r--r--@  1 iwillwen  staff   7527 11 22  2017 15113423841119.md
-rw-r--r--@  1 iwillwen  staff   1159 11 22  2017 15113423841120.md
-rw-r--r--@  1 iwillwen  staff  16474 11 23  2017 15113424478129.md
-rw-r--r--@  1 iwillwen  staff    461 11 23  2017 15113991864913.md
-rw-r--r--@  1 iwillwen  staff   6021 11 26  2017 15116829262749.md
...

```

那么我们这里可以使用 `ping` 命令对 `www.baidu.com` 进行网络通信检查，并使用 GNU 中的 `awk` 命令对 Ping 工具所返回的数据进行处理和提取。

```
$ ping www.baidu.com | awk 'match($0, /time=(.*)ms|timeout/) { print (RLENGTH > 7) ? substr($0, RSTART+5, RLENGTH-8) : 9999; fflush() }'

```

```
6.243
9.578
9.297
7.441
8.182
35.143
...

```

现在我们已经得到了不断生成的实时数据源，但是我们要怎么将它变成一个我们的数据应用能够使用的数据源呢？我们还需要将其变成一个 Web 服务。

### 20.1.3 Pipe STDOUT to Web with Node.js

我们需要将来自标准输入的内容变成一个 Web 服务以提供给基于 JavaScript 的数据应用使用，而比较可惜的我们目前找不到一个现成的通用工具来实现这个需求。所以我们需要借助 Node.js 的 Web 服务能力来实现这个需求，另外我们还需要使用到 Unix 或 Linux 自带的 `nc` 工具。

`nc` 工具可以将来自标准输入的数据通过 TCP 连接传递到指定服务上。而我们还需要使用 Node.js 分别构建一个 TCP 服务和 HTTP 服务，分别用于接收来自 `nc` 的 TCP 数据流和为 JavaScript 数据应用提供 HTTP 接口。

![](https://user-gold-cdn.xitu.io/2019/1/24/1687f5b9edd33604?w=711&h=192&f=png&s=14681)

具体 Node.js 程序代码如下：

```
// shells-web.js
const http          = require('http')
const net           = require('net')
const url           = require('url')
const { Transform } = require('stream')

const streams = {}

const HTTP_PORT = 8080
const NET_PORT  = 1337

const httpServer = http.createServer((req, res) => {
  const id = url.parse(req.url).pathname.substr(1)

  if (!streams[id]) {
    res.writeHead(404)
    res.end('Stream not found.')
    return
  }

  res.writeHead(200, {
    'Connection': 'keep-alive',
    'Content-Type': 'text/plain',
    'Access-Control-Allow-Origin': '*'
  })
  res.flushHeaders()
  streams[id]
    .on('')
    .pipe(res)
})

const ncServer = net.createServer(connection => {
  const id = Math.random().toString(32).substr(2)

  const stream = new Transform({
    transform(chunk, encoding, callback) {
      callback(null, chunk)
    }
  })

  connection.pipe(stream)
  connection.once('end', () => {
    stream.destroy()
    delete streams[id]
  })

  streams[id] = stream

  connection.write(`Pipeline is served on http://localhost:${HTTP_PORT}/${id}\n`)
  connection.write(`Example: curl -v http://localhost:${HTTP_PORT}/${id}`)
})

ncServer.listen(NET_PORT, () => {
  console.log(`Net Server is binding on localhost:${NET_PORT}`)
})
httpServer.listen(HTTP_PORT, () => {
  console.log(`HTTP Server is binding on localhost:${HTTP_PORT}`)
})

```

使用 Node.js 运行这个程序便可以分别在 `1337` 端口和 `8080` 端口开启 TCP 服务和 HTTP 服务。

```
$ node shells-web.js
Net Server is binding on localhost:1337
HTTP Server is binding on localhost:8080

```

### 20.1.4 Ping → Web

当前面的准备工作都完成以后，我们就可以将这些都串联起来，为 JavaScript 服务提供一个基于 Ping 工具的实时数据源了。

```
$ ping www.baidu.com | awk 'match($0, /time=(.*)ms|timeout/) { print (RLENGTH > 7) ? substr($0, RSTART+5, RLENGTH-8) : 9999; fflush() }' | nc localhost 1337
Pipeline is served on http://localhost:8080/<random id>
Example: curl -v http://localhost:8080/<random id>

```

现在我们使用 `curl` 工具来验证一下我们的转换工具是否生效了。

```
$ curl -v http://localhost:8080/<random id>

```

```
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 8080 (#0)
> GET /lb83qkua0eo HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Connection: keep-alive
< Content-Type: text/plain
< Access-Control-Allow-Origin: *
< Date: Wed, 23 Jan 2019 14:31:28 GMT
< Transfer-Encoding: chunked
<
6.428
7.196
7.643
7.356
28.557
7.423
8.584
...

```

非常好！值得一提的是，我们使用 Node.js 所创建的 HTTP 服务是以 HTTP 长连接的方式将 `ping` 的数据不断输出的，而不是利用普通的 TCP 连接。而这种形式的 HTTP 接口在浏览器端的 JavaScript 中该如何使用呢？

## 20.2 应用“高大上的动态数据流”

得到了真正的动态实时数据流以后，我们就要在我们的 JavaScript 数据应用中进行应用了。但是我们前面使用 Node.js 作为基础平台提供的 HTTP 接口是一个长连接的方式不断提供数据的，那么我们应该如何将长连接中不断出现的新数据获得得到呢？

### 20.2.1 读取长连接数据

Fetch API 是浏览器为 JavaScript 提供的一个用于访问 HTTP 接口的 API。但是一般来说我们在使用 `fetch` 函数都是用于获取一次性输出完成的 HTTP 接口，其实对于这种长连接接口，我们也使用 `fetch` 来获取其中的实时数据。

`fetch` 函数执行之后在其返回的 `Promise` 对象中会提供一个 `Response` 对象，我们可以通过这个对象再取得一个 `ReadableStreamDefaultReader` 对象，这个 `reader` 对象便是我们读取数据流中的数据的入口。

```
fetch('http://localhost:8080/<random id>')
  .then(res => res.body.getReader())
  .then(reader => {
    // ...
  })

```

我们调用 `reader` 对象的 `read` 方法，该方法会返回一个 `Promise` 对象，该 `Promise` 对象的结果是一个包含 `value` 和 `done` 的对象。`value` 是一个 `Uint8Array` 数组，我们可以将其理解为二进制数据，我们需要首先将其转换为字符串然后再解析为数字也就是我们需要的网络延迟值。而 `done` 则是当前长连接是否已经被关闭，如果还没有被关闭我们则需要继续读取。

```
const read = ({ value, done }) => {
  if (done) return
  
  // 解析数据
  const ping = parseFloat(new TextDecoder("utf-8").decode(value))
  if (ping > 0) {
    // 输出获取到的 ping 值
    console.log(ping)
  }
  
  // 继续读取
  if (!done) reader.read().then(read)
}

reader.read().then(read)

```

最后我们把这些都整合到一起，做成一个封装好的数据源对象。

```
class PingSource {

  constructor(streamUrl, onData) {
    this.streamUrl = streamUrl
    this.dataset = []
    this.onData = onData
  }

  load(streamUrl) {
    this.streamUrl = streamUrl
    this.loadStream()
  }

  loadStream() {
    this.controller = new AbortController()
    const signal = this.controller.signal

    fetch(this.streamUrl, {
      signal
    })
      .then(res => res.body.getReader())
      .then(reader => {
        const read = ({ value, done }) => {
          if (done || signal.aborted) return

          const ping = parseFloat(new TextDecoder("utf-8").decode(value))

          if (ping > 0) {
            const now = Date.now()
            if (this.onData) {
              this.onData(ping, now)
            }

            this.dataset.push({
              ping, timestamp: now
            })
          }

          if (!done && !signal.aborted) {
            reader.read()
              .then(read)
              .catch(handleError)
          }
        }

        reader.read()
          .then(read)
          .catch(handleError)
      })
  }

  stop() {
    if (this.controller) {
      this.controller.abort()
    }
  }

}

function handleError(err) {
  return false
  // console.error(err)
}

// 创建数据源
const pingSource = new PingSource()
// 加载数据
pingSource.load('http://localhost:8080/<random id>')
// 验证数据源实时性
setInterval(() => {
  console.log(pingSource.dataset.length)
}, 1e3)
//=> 1
//=> 2
//=> 3
// ...

```

这样我们的实时数据源便已经准备好了！接下来我们把他跟我们前面所学过的知识结合起来吧。

### 20.2.2 展示数据

我们所得到的实时数据流是一个以数值为内容的时间序列，所以我们可以使用折线图来表达这些数据。我们在 `PingSource` 的构建函数中提供了一个 `onData` 的回调函数接口，我们可以通过这个回调函数不断地将新数据传递给 ECharts，已达到数据不断出现在图表上的目的。

```
const chartEl = document.querySelector('#chart')
const myChart = echarts.init(chartEl)

// 创建数据源
const pingSource = new PingSource('http://localhost:8080/<random id>', () => {
  myChart.setOption({
    dataset: {
      source: pingSource.dataset
    },
    xAxis: {
      type: 'time'
    },
    yAxis: {
      type: 'value'
    },
    tooltip: {
      trigger: 'axis'
    },
    series: {
      type: 'line',
      name: 'ping',
      encode: {
        x: 'timestamp',
        y: 'ping'
      }
    }
  })
})
// 开始加载数据
pingSource.loadStream()

```

DEMO 在线地址（需自行更改数据流地址）：[https://codepen.io/iwillwen/pen/pGJvNe?editors=0010](https://codepen.io/iwillwen/pen/pGJvNe?editors=0010)

![](https://user-gold-cdn.xitu.io/2019/1/24/1687f5bd9c690f74?w=596&h=391&f=gif&s=791928)

## 20.3 优化数据源——为实时数据流添加自动过期机制

我们已经将我们的实时数据源与我们的图表连接起来，并可以在页面上看到不断生成的实时数据了。但是过不了多久我们就会发现，实时数据不断在图表上堆积但实时数据很多时候是具有较短的时效性的，而我们现在所使用的网络监控数据便是其中的一种。我们并不需要关心 1 分钟甚至 30 秒以前的网络状态，我只需要知道很短的时间内我的网络状况即可。

那么为了避免出现图表上存在过多无用数据，我们需要为我们的实时数据源添加自动过期的机制。一般来说自动过期的实现方法是为数据添加 TTL 参数（Time To Live，存活时间），但是 TTL 的实际实现细节是十分复杂的，在这里我们可以进行取巧。前面说到 Ping 工具我们可以看做是一个每秒钟产生一次数据的数据源，那么加入我们需要为我们的每一个 ping 数据添加一个 30 秒的 TTL 参数，即每一个数据可存活 30 秒。在当前场景中可以近似的看做是我们只存 30 秒的数据，即只存储最近 30 个实时数据，一旦数据集中的数据个数超过 30 便将前面的数据删除。

```
class PingSource {
  // ...
  
  setTTL(seconds) {
    this.ttl = seconds
  }

  loadStream() {
    // ...
    const read = ({ value, done }) => {
      // ...
      if (ping > 0) {
        // 检查超时
        if (this.ttl && this.ttl > 0 && this.dataset.length === this.ttl) {
          // 到达超时上线，删除第一个数据
          this.dataset.shift()
        }
        // ...
      }
      // ...
    }
    // ...
  }
}

```

我们将这个特性应用到图表上便可以实现这样的效果。

```
// ...

// 设置超时
pingSource.setTTL(30)
// 开始加载数据
pingSource.loadStream()


```

DEMO 在线地址：[https://codepen.io/iwillwen/pen/XObmNd?editors=0010](https://codepen.io/iwillwen/pen/XObmNd?editors=0010)

![](https://user-gold-cdn.xitu.io/2019/1/24/1687f5bfaf06d77c?w=560&h=351&f=gif&s=220586)

## 20.4 为网络监控应用添加异常检测机制

一个像是心电图的实时网络状况监控图表已经通过我们的努力展示在了我们的数据应用上，但是一般来说在有监控功能的同时，还需要具有异常检测功能。也就是说我们用于监控网络波动的数据应用，除了让我们知道当前 ping 值情况以外，还需要具有异常检测、报警的功能。

但是我们又如何才能知道当前数据是否异常呢？这里便需要使用到一些统计方法。用于检测某一个数据点是否为一个数据系列中的异常值（统计学中可以称之为离群值，Outlier）的方法有很多，比如均值检验、方差检验等等。但这些对于不熟悉统计学的同学来说还是有一些复杂，所以我们这里会使用一种非常简单、便于计算也便于理解的方法来进行判断。

### 20.4.1 异常值检测

还记得我们在第 16 节中曾经学习过一种名为 SPC 控制图的复杂数据图表吗？SPC 控制图引入了一种叫做控制区域的概念，我们便可以借用这个概念来对异常值进行检测。若当前 ping 值超过区域 A 的控制上限（数据均值加 3 倍的标准差）时，便将其判定为异常值。

![](https://user-gold-cdn.xitu.io/2019/1/24/1687f5cbfa8da122?w=460&h=400&f=png&s=25799)

其中我们选用当前值以前的数据作为判定依据，因为假若当前数据为一个非常离谱的异常值（比如前面我们在配置 `awk` 工具时便设定了如果 ping 信号超时便返回 9999 毫秒），所计算出来的标准差也会大得离谱，可能导致判定失准。

```
function sdBy(array, path) {
  const mean = _.meanBy(array, path)
  
  const top = array.map(row => _.get(row, path))
    .map(function(x) {
      return Math.pow(x - mean, 2)
    })
    .reduce(function(left, right) {
      return left + right
    })
  const bottom = array.length - 1
  
  return Math.sqrt(top / bottom)
}

// 对前面的数据进行计算判断
const previousPings = pingSource.dataset.slice(0, pingSource.dataset.length - 1)
const meanPing = _.meanBy(previousPings, 'ping')
const sigma = sdBy(previousPings, 'ping')

const isOutlier =
  !(this.pingSource.dataset.length < 5) &&                            // 数据量判断
  (this.threshold && this.threshold > 0 && ping > this.threshold) &&  // 阈值判断
  ping > (meanPing + 3 * sigma)                                       // 均值 + 3 * Sigma 判断


```

当判定一个 ping 值为异常值时，我们便将其记录下来。为了能够方便地对异常值进行管理和展示，我们也同样为异常值准备一个控制单元，并且将其应用到图表上进行展示。

```
class OutlierSource {
  constructor(pingSource, onSlow) {
    this.pingSource = pingSource
    this.onSlow = onSlow
    this.dataset = []
  }
  
  setTTL(seconds) {
    this.ttl = seconds
  }
  
  computeAndAdd(ping, timestamp) {
    // 对前面的数据进行计算判断
    const previousPings = this.pingSource.dataset.slice(0, this.pingSource.dataset.length - 1)
    const meanPing = _.meanBy(previousPings, 'ping')
    const sigma = sdBy(previousPings, 'ping')
  
    const isOutlier = ping > (meanPing + 3 * sigma) // 均值 + 3 * Sigma 判断
  
    if (this.ttl && this.ttl > 0 && this.dataset.length === this.ttl) {
      this.dataset.shift()
    }

    if (isOutlier) {
      this.dataset.push({
        ping, timestamp
      })

      if (this.onSlow) {
        try {
          this.onSlow(ping, timestamp)
        } catch(err) {
          handleError(err)
        }
      }
    } else {
      this.dataset.push({
        ping: 0, timestamp
      })
    }
  }
}


```

但这个时候我们又发现在一般情况下网络都是非常好的，只是稍微出现了一些小波动，而这个判定方法依然将其判定为异常值。所以除了通过控制上限进行判定外，我们还需要添加量个阈值控制。两个阈值分别代表不同的程度，一个较低的阈值代表超过该阈值的 ping 才**有可能**是异常值，而另外一个较高的阈值代表超过该阈值的 ping **一定**是异常值。

```
class OutlierSource {
  // ...

  setThreshold(ping) {
    this.threshold = ping
  }

  setMaxPing(ping) {
    this.max = ping
  }
  
  computeAndAdd(ping, timestamp) {
    // ...
  
    const isOutlier =
      (this.max && this.max > 0 && ping > this.max) ||                    // 接受上限判断
      (this.threshold && this.threshold > 0 && ping > this.threshold) &&  // 阈值判断
      ping > (meanPing + 3 * sigma)                                       // 均值 + 3 * Sigma 判断
      
    // ...
  }
}

// 设置异常阈值
outlierSource.setThreshold(30)
// 设置接受上限
outlierSource.setMaxPing(460)


```

### 20.4.2 展示异常情况

这些都整合起来以后，让我们看一下在图表上表达的效果。

```
class OutlierSource {
  constructor(pingSource, onSlow) {
    this.pingSource = pingSource
    this.onSlow = onSlow
    this.dataset = []
  }
  
  setTTL(seconds) {
    this.ttl = seconds
  }

  setThreshold(ping) {
    this.threshold = ping
  }

  setMaxPing(ping) {
    this.max = ping
  }
  
  computeAndAdd(ping, timestamp) {
    // 对前面的数据进行计算判断
    const previousPings = this.pingSource.dataset.slice(0, this.pingSource.dataset.length - 1)
    const meanPing = _.meanBy(previousPings, 'ping')
    const sigma = sdBy(previousPings, 'ping')
  
    const isOutlier =
      (this.max && this.max > 0 && ping > this.max) ||                    // 接受上限判断
      (this.threshold && this.threshold > 0 && ping > this.threshold) &&  // 阈值判断
      ping > (meanPing + 3 * sigma)                                       // 均值 + 3 * Sigma 判断
  
    if (this.ttl && this.ttl > 0 && this.dataset.length === this.ttl) {
      this.dataset.shift()
    }

    if (isOutlier) {
      this.dataset.push({
        ping, timestamp
      })

      if (this.onSlow) {
        try {
          this.onSlow(ping, timestamp)
        } catch(err) {
          handleError(err)
        }
      }
    } else {
      this.dataset.push({
        ping: 0, timestamp
      })
    }
  }
}

function handleError(err) {
  return false
  // console.error(err)
}

function sdBy(array, path) {
  const mean = _.meanBy(array, path)
  
  const top = array.map(row => _.get(row, path))
    .map(function(x) {
      return Math.pow(x - mean, 2)
    })
    .reduce(function(left, right) {
      return left + right
    })
  const bottom = array.length - 1
  
  return Math.sqrt(top / bottom)
}

const chartEl = document.querySelector('#chart')
const myChart = echarts.init(chartEl)

const outliers = []

// 创建数据源
const pingSource = new PingSource()
// 创建离群值处理实例
const outlierSource = new OutlierSource(pingSource, ping => {
  console.warn(`NETWORK SLOW!! ${ping}ms`)
})
pingSource.load('http://localhost:8080/77sl06lf3io', (ping, timestamp) => {
  outlierSource.computeAndAdd(ping, timestamp)
  myChart.setOption({
    dataset: [
      {
        source: pingSource.dataset
      },
      {
        source: outlierSource.dataset
      }
    ],
    xAxis: {
      type: 'time'
    },
    yAxis: {
      type: 'value'
    },
    tooltip: {
      trigger: 'axis'
    },
    series: [
      {
        type: 'line',
        name: 'ping',
        encode: {
          x: 'timestamp',
          y: 'ping'
        }
      },
      {
        type: 'bar',
        name: 'outlier',
        datasetIndex: 1,
        encode: {
          x: 'timestamp',
          y: 'ping'
        }
      }
    ],
    animation: false
  })
})
// 设置超时
const TTL = 30
pingSource.setTTL(TTL)
outlierSource.setTTL(TTL)
// 设置异常阈值
outlierSource.setThreshold(30)
// 设置接受上线
outlierSource.setMaxPing(460)


```

DEMO 在线地址：[https://codepen.io/iwillwen/pen/bzdKYY?editors=0010](https://codepen.io/iwillwen/pen/bzdKYY?editors=0010)

![](https://user-gold-cdn.xitu.io/2019/1/24/1687f5c50f57c457?w=576&h=352&f=gif&s=182306)

### 20.4.3 展示异常总体情况

除了可以在图表上展示出每一个异常值情况外，我们还可以用一个**仪表盘**图表来展示最近一段时间内的网络情况，比如我们就用异常值的个数占一段时间内的总数据数量的比例作为我们评价网络情况的一个指标。

我们可以在 `OutlierStore` 上添加一个 Getter 来取得真实的异常值数量，然后在 ECharts 图表的配置上添加一个仪表盘图表以展示当前整体网络状况。

```
class OutlierSource {
  // ...
  
  get count() {
    return this.dataset.filter(({ ping }) => ping > 0).length
  }
}

// ...

myChart.setOption({
  // ...
  
  series: [
    // ...
    {
      name: '异常率',
      type: 'gauge',
      center: [ '80%', '55%' ],
      detail: { formatter:'{value}%' },
      animation: true,
      axisLine: {
        lineStyle: {
          color: [[0.2, '#91c7ae'], [0.6, '#63869e'], [1, '#c23531']]
        }
      },
      data: [
        {
          value: (outlierSource.count / TTL * 100).toFixed(2),
          name: '异常率'
        }
      ]
    }
  ]
})


```

DEMO 在线地址：[https://codepen.io/iwillwen/pen/OdVddO?editors=0010](https://codepen.io/iwillwen/pen/OdVddO?editors=0010)

![](https://user-gold-cdn.xitu.io/2019/1/24/1687f5c6f4349677?w=912&h=353&f=gif&s=608366)

## 小结

这一节中，我们学习了如何使用一系列简单的工具创造一个真实的实时数据源，并将其应用到了我们的 JavaScript 数据应用中。我们还是用了一些非常简单的统计方法来对我们的数据进行一些检验和判定。

下一节将会是本小册的最后一节，我们会从一个更为实际的场景出发，开发一个具有实际用途的、可交互的数据应用。

### 习题

像笔者一样，从身边的实际体验中寻找灵感，看看有哪些是可以变成我们数据应用的统计、研究对象的，做成一个小 DEMO 跟大家分享一下吧！

