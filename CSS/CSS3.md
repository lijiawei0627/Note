# 一、color

## 颜色半透明

文字颜色到了`CSS3`，我们可以采取半透明的格式了。其格式如下：

```css
color: rgba(r, g, b, a); // a是alpha 不透明的意思 取值范围是0 ~ 1 之间
color: rgba(0, 0, 0, 0.3);
```

## 文字阴影

```css
text-shadow: 1px 1px 1px lightgray；

第一个1px: 是向x坐标方向的偏移

第二个1px: 是向y坐标方向的偏移

第三个1px: 是模糊距离 (可选)

第四的参数：颜色 (可选)
```

### 凹凸文字效果

```css
body {
	background-color: #ccc;
}
div {
	color: #ccc;
	font: 700 80px "微软雅黑";
}
div:first-child {
	text-shadow: 1px 1px 1px #000, -1px -1px 1px #fff;
}
div:last-child {
	text-shadow: -1px -1px 1px #000, 1px 1px 1px #fff;
}
```

```html
<body>
  <div>我是凸起的文字</div>
  <div>我是凹下去的文字</div>
</body>
```

## 盒子阴影

```css
box-shadow: 水平阴影 垂直阴影 模糊距离 阴影尺寸 阴影颜色 内/外阴影

box-shadow: 5px 5px 3px 4px rgba(0, 0, 0, .4) inset;  // 默认为ouset外阴影
```

# 二、background

background 简写属性在一个声明中可以设置所有的背景属性。

![image-20200306153708615](http://img.lijiawei0627.xyz/img/image-20200306153708615.png)

## 背景图片及其平铺

```css
background-image: url(image/l.jpg);
background-repeat: no-repeat;  // 默认值为repeat
```

## 背景位置

规定背景图片在背景中的位置

```css
background-position: left top;
background-position: left; // 如果方位名词只写了一个，另外一个默认为center
background-position: 0px 0px;  // 第一个值为x坐标方向，第二个值为y坐标方向
background-position: left 0px;
background-position: 0% 0%;
```

## 背景附着（背景固定）

设置背景图片是随着对象内容滚动还是固定的

语法：

```css
background-attachment: scroll | fixed;
```

参数：

* scroll：背景图像是随着对象内容滚动
* 
* fixed：背景图像固定

## 背景简写

background属性的值的书写顺序官方并没有强制标准的。为了可读性，建议如下写

```css
background：背景颜色 背景图片地址 背景平铺 背景滚动 背景位置；
background: transoarent url(image.jpg) repeat scroll 50% 0;
```

## 背景透明

`CSS3`支持背景半透明的写法语法格式是

```css
background: rgba(0, 0, 0, 0.3);
```

最后一个参数是alpha不透明度 取值范围0 ~ 1之间

注意：背景半透明是指盒子背景半透明，盒子里面的内容不受影响

## 背景缩放

通过`backgroun-size`设置背景图片的尺寸，就像我们设置`img`尺寸一样

其参数设置如下：

* 可以设置长度单位（`px`）或者百分比 （设置百分比时，参照盒子的宽高）
* 设置为cover时，会自动调整缩放比例，保证图片始终填充满背景区域，如有溢出部分则会隐藏
* 设置为contain会自动调整缩放比例，保证图片始终完整显示在背景区域

```css
background-image: url(image.jpg);
background-size: 300px 100px;
background-size: 100px;  // y方向等比例缩放
background-size: cover;
background-size: contain;
```

## 多背景图片

* 一个元素可以设置多重背景图像
* 每组属性间使用逗号分隔
* 如果设置的多重背景图之间存在着交集（即存在着重叠关系），前面的背景图会覆盖后面的背景图之上
* 为了避免背景色将图像盖住，背景色通常都定义在最后一组上

```css
background: url(image/1.jpg) no-repeat left top,
						url(image/2.jpg) no-repeat right bottom red;
```

# 三、border-radius

每个半径的四个值的顺序是：左上角，右上角，右下角，左下角。如果省略左下角，右上角是相同的。如果省略右下角，左上角是相同的。如果省略右上角，左上角是相同的。

```css
border-radius: 10px 20px;
border-radius: 10px 20px 10px 20px;
```

# 四、`CSS3`中盒模型

* `box-sizing: content-box`  盒子大小为`width + padding + border`
* `box-sizing: border-box`  盒子大小为`width`  就是说`padding`和`border`是包含到`width`里面的，此时content的值会自动调整

# 五、过渡效果`transition`

```css
transition: 要过渡的属性  花费的事件 运动曲线  何时开始;
transition: width 0.5s ease-in 0s;  // 默认是ease
// 如果想要所有属性都有过渡效果，可以直接写个all
transition: all 0.5s ease-in 0s;
transition: background-color 0.5s ease-in 0s;

// css
div {
  width: 200px;
  height: 100px;
  background-color: pink;
  transition: width 0.6s ease 0s, height 0.3s ease-in 1s;
}
div:hover {
  width: 600px;
  height: 300px;
}
```

![image-20200306174554278](http://img.lijiawei0627.xyz/img/image-20200306174554278.png)

# 六、变形`transform`

`transform`的属性包括：`rotate() 、 skew() 、 scale() 、 translate(,)` ，分别还有`x、y`之分，比如：`rotateX()` 和 `rotateY()` ，以此类推。

## `2D`变形

### translate（`2D`平移）

元素从其当前位置移动，根据给定的 left（x 坐标） 和 top（y 坐标）进行`2D`移动

```css
transform: translate(50px, 100px);

transform: translate(50%);  // 此50%不是以父级宽度为准，还是以自身宽度为准																								 //  此处没有设置y方向距离， 则默认为0
注：translate可以用来实现元素居中效果。例如
position: absolute;
left: 50%;
transform: translate(-50%);
```

### scale（`2D`缩放）

可以对元素进行水平和垂直方向的缩放。

```css
scale()的默认取值为1，当值设置为0.01到0.99之间的任何值，作用使一个元素缩小，而大于1.0的值，作用让它放大

transform: scale(0.8, 1)  //该语句使得该元素在水平方向上缩小了20%，垂直方向不缩放
```

### rotate（`2D`旋转）

```css
transform: rotate(360deg);  // deg为度数

// 还可以设置变形的中心点
img {
  transition: all 0.6s;
  transform-origin: 20px 30px; // 也可以使用buttom、top、left、right等方位名词
}
img:hover {
  transform: rotate(360deg)
}
```

### skew（`2D`倾斜）

```css
 transform: skew(30deg, 30deg);
```

## `3D`变形

### `3D`坐标轴

x左边为负，右边为正；

y上面为负，下面为正；

z里面为负，外面为正。

![image-20200307135502434](http://img.lijiawei0627.xyz/img/image-20200307135502434.png)

### `rotate3d`

下图分别为绕X、Y、Z轴旋转

```css
transform: rotateX(140deg);
transform: rotateY(140deg);
transform: rotateZ(140deg);

transform: rotate3d(1, 1, 1, 90deg);
```

![image-20200307141227526](http://img.lijiawei0627.xyz/img/image-20200307141227526.png)

### `perspective`透视效果

`perspective`：视距，表示视点距离屏幕的长度。视点，用于模拟视透效果时人眼的位置

perspective一般作为一个属性，设置给父元素，作用于所有3D转换的子元素

![image-20200307142017013](http://img.lijiawei0627.xyz/img/image-20200307142017013.png)

```css
body {
  perspective: 100px;  //  视距  眼睛到屏幕的距离
}
img {
  transition: all 5s;
  transform-origin: left;  //  设置变形中心点
}
img:hover {
  transform: rotateY(360deg);
}
```

```html
<body>
  <img url="image.jpg"/>
</body>
```

### `translate3d`

`translateZ`的直观表现形式就是大小变化，实质上时`XY`平面相对于视点的远近变化（说远近就一定会说到离什么参照物远或近，在这里参照物就是perspective属性）。比如设置了perspective为`200px`；那么`translateZ`的值越接近`200px`，就是离得越近，看上去也就越大，超过`200px`就看不到了，因为相当于跑到后脑勺去了。

```css
body {
  perspective: 1000px;
}
div {
  width: 100px;
  height: 100px;
  margin: auto;
  background-color: aquamarine;
  transition: all 2s ease 0s;
}
div:hover {
  transform: translateZ(1000px);
  /* transform: translate3d(100px, 100px, 1000px); */  // x和y可以是px，也可以是%，而z只能是px
}
```

```html
<body>
  <div></div>
</body>
```

# 七、`CSS3`动画

`animation` 属性是一个简写属性，用于设置动画属性：

| *[animation-name](https://www.runoob.com/cssref/css3-pr-animation-name.html)* | 指定要绑定到选择器的关键帧的名称                             |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| *[animation-duration](https://www.runoob.com/cssref/css3-pr-animation-duration.html)* | 动画指定需要多少秒或毫秒完成                                 |
| *[animation-timing-function](https://www.runoob.com/cssref/css3-pr-animation-timing-function.html)* | 设置动画将如何完成一个周期                                   |
| *[animation-delay](https://www.runoob.com/cssref/css3-pr-animation-delay.html)* | 设置动画在启动前的延迟间隔。                                 |
| *[animation-iteration-count](https://www.runoob.com/cssref/css3-pr-animation-iteration-count.html)* | 定义动画的播放次数。                                         |
| *[animation-direction](https://www.runoob.com/cssref/css3-pr-animation-direction.html)* | 指定是否应该轮流反向播放动画。                               |
| [animation-fill-mode](https://www.runoob.com/cssref/css3-pr-animation-fill-mode.html) | 规定当动画不播放时（当动画完成时，或当动画有一个延迟未开始播放时），要应用到元素的样式。 |
| *[animation-play-state](https://www.runoob.com/cssref/css3-pr-animation-play-state.html)* | 指定动画是否正在运行或已暂停。                               |

```css
body {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
div {
  width: 100px;
  height: 100px;
  background-color: aqua;
  animation-name: hd;
  animation-duration: 2s; // 动画完成时间需要2秒
  animation-delay: 1s; // 延迟1秒
  animation-fill-mode: forwards; // 最终停留在最后一帧的状态
}
@keyframes hd {
  0% {
    background: white;  // 如果不设置0%时的状态，则默认为初始状态
  }
  25% {
    transform: scale(2);
  }
  65% {
    transform: scale(1);
    background-color: #9b59b6;
  }
  100% {
    background-color: yellowgreen;  // 如果不设置100%时的状态，则默认为原状态
  }
}
```

弹跳小球动画示例：

```css
body {
  width: 100vw;
  height: 100vh;
  background-color: #34495e;
  display: flex;
  justify-content: center;
  align-items: center;
}
div {
  width: 100px;
  height: 100px;
  background: radial-gradient(at center, #e67e22, #e74c3c);
  border-radius: 50%;
  animation-name: ball;
  animation-duration: 1s;
  animation-iteration-count: infinite; // 无限次循环
  animation-direction: alternate-reverse; // 动画在奇数次（1、3、5...）反向播放，																						          // 在偶数次（2、4、6...）正向播放。
}
section {
  width: 300px;
  height: 40px;
  background: rgba(0, 0, 0, .3);
  position: absolute;
  transform: translateY(50px);
  z-index: -1;
  border-radius: 50%;
  filter: blur(5px);
  animation-name: shadow;
  animation-duration: 1s;
  animation-iteration-count: infinite;
  animation-direction: alternate-reverse;
}
@keyframes shadow {
  to {
    width: 300px;
    height: 20px;
    filter: blur(35px);
    background-color: rgba(0, 0, 0, .1);
  }
}
@keyframes ball {
  to {
    transform: translateY(-300px);
  }
}
```

```html
<div></div>
<section></section>
```

## 起始帧与终点帧特性

当不配置起始帧和终点帧时，会默认使用原始状态

## 同时声明关键帧规则

当有共同样式时，可以组合在一起写，并不会进行覆盖

```css
@keyframes hd {
  0% {
    background: white;
  }
  25% {
    transform: scale(2);
  }
  50% {
    transform: scale(1.5);
  }
  75% {
    transform: scale(1);
  }
  100% {
    background-color: yellowgreen;
  }
  25%,
  75% {
    background-color: #9b59b6;
    border-radius: 50%;
  }
}
```

## 多个动画使用及时间配置

```css
animation-name: translate, background, radius;
animation-duration: 4s, 2s, 1s;  // 各个动画相对应时间为4s、2s、1s

animation-duration: 4s, 2s;  // 此时各个动画相对应时间为4s、2s、4s
```

## 属性重叠对动画的影响

当有重叠属性时，谁出现在后面，谁的优先级高。

```css
animation-name: translate, background, radius;  // 当background与translate在同一帧有属性重叠时，会优先使用background动画
```



# 八、`CSS`中的常见单位

* `px`：绝对单位，页面按精确像素展示
* em：相对单位，基准点为父节点字体的大小，如果自身定义了font-size按自身来计算（浏览器默认字体是`16px`），整个页面内`1em`不是一个固定的值。
* rem：相对单位，可理解为”root em”, 相对根节点`html`的字体大小来计算，`CSS3`新加属性，`chrome/firefox/IE9+`支持。
* `vw：viewpoint width`，视窗宽度，`1vw`等于视窗宽度的1%。
* `vh：viewpoint height`，视窗高度，`1vh`等于视窗高度的1%。
* `vmin：vw`和`vh`中较小的那个。
* `vmax：vw`和`vh`中较大的那个。
* %：百分比

