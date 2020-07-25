# 一、浏览器：
1、shell：外壳
2、core：内核（JS执行引擎、渲染引擎）

IE：Trident

Firfox：Gecko

Chrom：Webkit / Blink

Safari：Webkit

Opera：Presto / Blink

# 二、版本和兼容性
HTML5、CSS3

HTML5：2014年

CSS3：目前还没有制定完成

XHTML：可以认为是HTML的一种一个版本，完全符合XML的规范。

---

# 三、语义化
## 1、什么是语义化：
#### 每一个HTML元素都有具体的含义
**如：**
```
a元素：超链接
p元素：段落
h1元素：一级标题
```
#### 所有元素与展示效果无关
* 元素展示到页面中的效果，应该由CSS决定。
* 因为浏览器带有默认的CSS样式，所以每个元素有一些默认样式
**注意：选择什么元素，取决于内容的含义，而不是显示出的效果**
# 2、为什么需要语义化
#### 为了搜索引擎优化（SEO）
* 搜索引擎：百度、搜搜、Bing、Google
* 每隔一段时间，搜索引擎会从整个互联网中抓取网页源代码
#### 为了让浏览器理解网页
阅读模式、语音模式

---

# 四、文本元素
## 1、HTML5元素周期表
[HTML5元素周期表]([http://demo.yanue.net/HTML5element/](http://demo.yanue.net/HTML5element/)
)
![image.png](https://upload-images.jianshu.io/upload_images/16269351-646cce0469d1a6a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 2、h元素
* h1-h6：表示一级标题~六级标题
## 3、p元素
* 段落，paragraphs
> vscode可以直接使用lorem生成乱数假文，没有任何实际含义的文字
## 4、span元素【无语义】
* 没有语义，仅用于设置样式
>以前：某些元素在显示时会独占一行（块级元素），而某些元素不会（行内元素）

>到了HTML5，已经弃用了这种说法
## 5、pre元素
* 预格式化文本元素
* 空白折叠：在源代码中的连续空白字符（空格、换行、制表），在页面显示时，会被折叠为一个空格
* 例外：在pre元素中的内容不会出现空白折叠。
* 在pre元素内部出现的内容，会按照源代码格式显示到页面上。该元素通常用于在网页中显示一些代码。
* **pre元素功能的本质，它有一个css属性（white-space: pre;）**
* 例：
```html
//此处使用code标签只是为了是文本具有语义
<code>
    <pre>
      if (true) {
        console.log('hello, world')
      }
    </pre>
</code>
```
---

# 五、HTML实体
* 实体字符，HTML Entity
* 实体字符通常用于在页面中显示一些特殊字符
[链接]([https://www.w3school.com.cn/html/html_entities.asp](https://www.w3school.com.cn/html/html_entities.asp)
)
---

# 六、a元素
## 1、普通链接
（href）hyper reference：通常表示跳转地址
## 2、锚链接（锚点）
```html
// 目录(点击a链接可以跳转到相应章节所在位置，
// 通过标签的id属性值链接)
<a href="#chapter1">章节一</a>

// 章节
<h2 id="chapter1">章节一</h2> 
<p>lorem</p>
```
## 3、功能链接
点击后，触发某个功能
* 执行JS代码，JavaScript：
```html
<a href="javascript:alert('hello, wrold')">点击</a>
```
* 发送邮件
要求用户计算机上安装有邮件发送软件
* 拨号
## 4、target属性
表示跳转窗口位置
target的取值：

* _self：在当前页面窗口中打开，默认值
* _blank：在新窗口中打开
---
# 七、路径的写法
## 1、站内资源和站外资源
* 站内资源：当前网站的资源
* 站外资源：非当前网站的资源
## 2、绝对路径和相对路径

* 站外资源：绝对路径
* 站内资源：相对路径

* 绝对路径的书写格式：
url地址：
```
协议名://主机名:端口号/路径
scheme://host:port/path
```
https://www.baidu.com/
http://www.lijiawei0627.xyz:70/#/login
file:///D:/practice.html
协议名：http、https、file
主机名：域名、IP地址
端口号：http协议，默认端口是80；https协议默认端口是443
**当跳转目标和当前页面的协议相同时，可以省略协议**

* 相对路径
以./开头，./表示当前资源所在目录
可以书写../表示返回上一级目录
相对路径中：./可以省略
# 八、容器元素
容器元素：该元素代表一个区域，内部用于放置其他元素
## 1、div元素
没有语义
## 2、语义化元素（显示效果跟div一样，只不过增加了语义）
* header：通常用于表示页头，也可以用于表示文章的头部
* footer：通常用于表示页脚，也可以用于表示文章的尾部
* article：通常用于表示整篇文章
* section：通常用于表示文章的章节
* aside：通常用于表示侧边栏
* nav：通常用于表示导航栏
# 九、元素包含关系
**以前：块级元素可以包含行内元素，行内元素不可以包含块级元素，a元素除外**

**现在：元素的包含关系由元素的内容类别决定。**

**总结：**
* 容器元素可以包含任何元素
* a元素中几乎可以包含任何元素
* 某些元素有固定的子元素（ul>li,ol>li,dl>dt + dd）
* 标题元素和段落元素不能相互嵌套，并且不能包含容器元素

# 十、表单元素



