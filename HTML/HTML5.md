# 一、表单新增的`type`属性

* email：提供了默认的电子邮箱的验证
* tel：并不是来实现验证，本质目的是为了能够在移动端打开数字键盘。意味着数字键盘限制了用户只能输入数字。
* `url`：验证只能输入合法的网址，必须包含网络协议名
* `number`：只能输入数字（包含小数点），不能输入其他的字符。
  * max：最大值
  * min：最小值
  * value：默认值
  * step：每次增加或减少固定的值

* range：范围，默认值为最大值的一半

  ```html
  <input type="range" max="100" min="0" value="50">
  ```

* search：可以提供更人性化的输入体验

* color：选取颜色
* time：时间 时分秒
* date：日期 年月日

* placeholder：提示文本，提示占位

* `autofocus`：自动获取焦点

* required：要求必须输入信息

* pattern：正则表达式验证

  ```javascript
  <input type="tel" required pattern="^(\+86)?\d{10}$">
  ```


* `file`：上传文件
  * 通过`files`拿到上传的文件内容
  * 添加`multiple`属性，可以同时上传多个文件

# 二、进度条

* `progress`：max最大值 value当前值

  ```html
  <progress max="100" value="100"></progress>
  ```

* meter：度量器 hight：规定的较高的值， low：较低的值 ， max：最大值 ，min：最小值

  ```html
  <meter max="100" min="0" high="80" low="40" value="30"></meter>
  ```

# 三、多媒体标签

## audio

属性：

* `src`：播放的音频文件的路径

* `constrols`：音频播放器的控制面板

* `autoplay`：自动播放

* loop：循环

  ```html
  <audio src="../mp3/aa.mp3"></audio>
  ```

## video

* `src`：视频的文件路径

* control：控制面板

* `autoplay`：自动播放

* `loop`：循环播放

* width：宽度

* height：高度

* poster：视频封面

* 当设置宽高时，一般情况下只会设置宽度或者高度，让其自动的等比缩放

  ```html
  <video src="../mp4/aa.mp4" poster="../images/1.jpg" controls height="600"></video>
  ```


## `source`

因为不同浏览器支持的视频文件格式不一样，所以我们在进行视频添加的时候，需要考虑浏览器是否支持。我们可以准备多个格式的视频文件，让浏览器自动的选择。

```html
<video>
  <!-- 视频源，可以有多个 -->
  <source src="../mp3/flv.flv" type="video/flv">
  <source src="../mp3/mp4.mp4" type="video/mp4">
  您的浏览器不支持当前的视频播放
</video>
```

# 四、自定义属性

规范：

* `data-`开头
* `data-`后面至少有一个字符，多个单词使用`-`连接

```html
<p data-school-name="itcast">传智播客</p>
<script>
  window.onload = function () {
    let p = document.querySelector("p");
    // 使用驼峰命名法取值
    let value = p.dataset["schoolName"];
    console.log(value); // itcast
  }
</script>
```

# 五、接口的使用

## 网络接口

```javascript
window.addEventListener("online", function () {
	alert('网络连通了')
})

window.addEventListener("offline", function () {
	alert("网络断开了")
})
```

## `FileReader`文件读取

```html
<from action="">
  文件：<input type="file" name="myFile" id="myFile" onchange="getFileContent();">
  <input type="submit">
</from>
<img src="" alt="">

<script>
	// FileReader: 读取文件内容
 	/* 1、`readAsText`读取文本文件（可以使用`Txt`打开的文件），返回文本字符串，默认编码是`UTF-8`
     2、`readAsBinaryString`读取任意类型的文件，返回二进制字符串。这个方法不是用来读取文件展示给用户看	       的，而是存储文件。例如：读取文件的内容，获取二进制数据，传递给后台，后台接受数据之后，再将数据存储
 	   3、`readAsDataURL`：读取文件获取一段以data开头的字符串，这段字符串的本质就是`DataURL`。`DataURL`是一种将文件（这个文件一般就是指图像或者能够嵌入到文档的文件格式）嵌入到文档的方案。`DataURL`是将资源转换为`base64`编码的字符串形式，并且将这些内容直接存储在`url`中》优化网站的加载速度和执行效率*/
  
  function getFileContent () {
      // 1.创建文件读取对象
      let reader = new FileReader();
      /* 2.读取文件，获取DataURL
         2.1没有任何的返回值，但是读取完文件之后，会将读取结果存储在文件读取对象的result中
         2.2需要传递一个参数
         2.3文件存储在file表单元素的files属性中，它是一个数组
      */
      let file = document.querySelector("#myFile").files;
      reader.readAsDataURL(file[0]);
      // FileReader提供了一个完整的事件模型，用来捕获读取文件时的状态
      // onabort：读取文件中断时触发
      // onerror：读取错误时触发
      // onload：读取成功完成时触发
      // onloadend：读取完成时触发，无论成功或失败
      // onloadstart：开始读取时触发
      // onprogress：读取文件过程中持续触发
      reader.onload = function () {
        document.querySelector("img").src = reader.result;
      }
</script>
```

