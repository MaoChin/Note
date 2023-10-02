# `HTML`编写

##  标签

所有的标签构成一个 `DOM` 树。(`Document Object Mode` )  有父子，兄弟关系。

```html 
<html>    <!-- 根标签 -->
	<head>  <!-- 属性标签 -->
		<title>第一个页面</title>
	</head>
	<body>
		hello world
    <!-- 注释写法 -->
	</body>
</html>
```

```shell
<html>  </html>  # 根标签 
<head>  </head>  # 属性标签
<body>  </body>  # 内容标签
```

**`vscode`快速生成前端代码框架：`! + Enter`：**

```shell
<!DOCTYPE html>     # 指定html版本为html5
<html lang="en">    # 语言
<head>
  <meta charset="UTF-8">   # 浏览器解码规则，按照UTF-8方式解码
  <meta name="viewport" content="width=device-width, initial-scale=1.0">                # 移动端适配规则
  <title>Document</title>
</head>
<body>
  
</body>
</html>
```

### 1. 标题标签

```html
<!-- 标题大小依次减小 -->
<h1>hello</h1>
<h2>hello</h2>
<h3>hello</h3>
<h4>hello</h4>
<h5>hello</h5>
<h6>hello</h6>
```

### 2. 段落标签

```html
<!-- html不会自己分段，只能通过段落标签 -->
<p>这是一个段落</p>
<p>这是一个段落</p>
<p>这是一个段落</p>
```

### 3. 换行标签(单标签)

换行：<br/>换行标签是单标签，不是成对的。 			

```html
<p>这是一个段落,这里换行<br/> 可以看到，这里已经是下一行了！！</p>
```

### 4. 格式化标签

实际开发中文本格式化大多是通过`CSS`实现的，但是`html`也能实现一定的格式化。

下面的四类操作中各有两种方法，**第一种方法 “强调” 的意味更明显，**当获取到源html代码时第一种方法会更显眼！！

```html
<strong> 加粗这里的文本 </strong>
<b> 也是加粗文本 </b>

<em> 斜体字 </em>
<i> 也是斜体字 </i>

<del> 文本中间有删除线 </del>
<s> 也是删除线 </s>

<ins> 文本有下划线 </ins>
<u> 也是下划线 </u>
```

### 5. `img`标签(单标签)

展示图片。**`img`标签必须有`src`属性指定图片路径。**

路径有三种：绝对路径，相对路径和网络上的图片链接。

```html
<!-- src 指定图片的路径 -->
<img src="/home/img/dog.jpg">
```

`img`标签的其他属性：

1. alt: 替换文本. 当文本不能正确显示的时候, 才会显示一个替换的文字。
2. title: 提示文本. 鼠标放到图片上, 就会有提示。
3. width/height: 控制宽度高度，高度和宽度一般改一个就行, 另外一个会等比例缩放. 否则就会图片失衡。(`px`：像素，一种长度单位)
4. border: 边框, 参数是宽度的像素，但是一般使用 CSS 来设定。

```html
<img src="./rose.jpg" alt="图片加载失败！" title="这是一朵鲜花" width="500px" height="800px" border="5px">
```

### 6. 超链接标签

```html
<a href="http://www.baidu.com">跳转到百度</a>
<a href="./html01.html">跳转到我自己写的页面</a>
<a href="#">相当于刷新当前页面</a>

<!-- 通过图片跳转 -->
<a href="http://www.baidu.com">
	<img src="/home/img/baidu.jpg" title="转到百度">
</a>
```

属性：

1. ==`href`: 必须具备, 表示点击后会跳转到对应的页面。==
2. `target`: 可选属性。打开方式，默认是 _self，就是在当前页面打开链接，如果是 _blank 则用新的标签页打开。 

### 7. 表格标签

```html
<table>
  <!-- 这个是专指表头 -->
  <thead>
    <tr>
      <th> 会把表头加粗	</th>
    </tr>
  </thead>
  <!-- 这个是专指表格内容 -->
  <tbody>
    <!-- 表格的一行 -->
    <tr>  
      <td> 一个单元格，就是一列 </td>
      <td> 下一个单元格 </td>
    </tr>
    <tr>
      <td> 一个单元格 </td>
    </tr>  
  </tbody>
</table>
```

相关属性：不过一般用`CSS`设置这些属性

1. `border` ：设置表格边框的宽度(单位：`px`)
2. `width`：设置表格宽度，单位：`px`
3. `height`：设置表格高度，单位：`px`
4. `cellpadding`: 内容距离左边框的距离, 默认 1 像素
5. `cellspacing`: 单元格之间的距离. 默认为 2 像素  
6. `align`：表格相对于周围元素的对齐方式. align="center" (不是内部元素的对齐方式)  

```html
<table border="1" cellspacint="0" align="center" width="200" height="150">
  <!-- .... -->
</table>
```

##### 合并单元格

跨行合并: `rowspan="n"`
		跨列合并: ` colspan="n"`

### 8. 列表标签

#### 1. 无序列表

`type`属性：指定列表前的标记，如"黑点..."

```html
<ul type="disc">
  <li>内容1</li>
  <li>内容2</li>
</ul>
```

#### 2. 有序列表

`type`属性：指定序号样式。a：小写英文，A：大写英文，i：小写罗马数字，I：大写罗马数字，1：数字(默认)

`start`属性：从序号几开始。

```html
<ol type="1">
  <li>内容1</li>
  <li>内容2</li>
  <li>内容3</li>
</ol>
```

#### 3. 自定义列表

```html
<dl>
  <dt> 自定义列表标题
    <dd>内容1</dd>
    <dd>内容2</dd>
    <dd>内容3</dd>	
  </dt>
</dl>
```

### 9. 表单标签(重要)

==用于完成和服务端的各种交互。==

#### 1. `form`标签

描述了要把数据按照什么方式，提交到哪个页面中。

```html
<form action="./html5.html">
  
</form>
```

#### 2. `input`标签(单标签)

获取用户输入，如单行文本框, 按钮, 单选框, 复选框。

属性：

1. `type`：具体输入类型。如`button`,  `text`, `file`, `image`, `password`, `radio`(单选框) ，`checkbox`(多选框)等。

```html
<form action="./html5.html">
  账号<input type="text"> <br/>
  密码<input type="password"> <br/>
  性别<input type="radio" name="sex" checked="checked">男 
  	  <input type="radio" name="sex">女 <br/>
  爱好<input type="checkbox">读书
  		<input type="checkbox">运动
  		<input type="checkbox">打代码 <br/>
  <input type="button" value="普通按钮" onclick="alert('hello')">
</form>
```

```html
<form action="./html5.html">
  课程<input type="text" name="course">
  <input type="submit">
  <input type="reset">	
</form>
```

```html
<form action="./html5.html">
  <!-- 上传文件 -->
  <input type="file">
</form>
```

一些说明：

1. `radio`单选中**多个选项必须配有相同的`name`值才能实现单选，**`checked="checked"`表示这是默认选项。
2. `submit`标签 把用户输入的course以URL传到action的页面。

### 10. 无语义标签

就是没有固定语义，主要用于==网页布局。==

#### 1. `div`标签

`div` 标签, `division` 的缩写, 含义是 分割。独占一行，是一个“大盒子”。

#### 2. `span`标签

span 标签, 含义是跨度，不独占一行，是一个“小盒子”。

```html
<div>
  <!--这些每个一行-->
  <div>aaa</div>
  <div>bbb</div>
  <div>ccc</div>
  
  <!--这些整体在一行-->
  <div>
    <span>111</span>
    <span>222</span>
    <span>333</span>
  </div>
</div>
```

 

