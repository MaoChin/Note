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

### 3. 换行标签

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

### 5. `img`标签

展示图片。**`img`标签必须有`src`指定图片路径。**

路径有两种：绝对路径和相对路径。

```html
<!-- src 指定图片的路径 -->
<img src="/home/img/dog.jpg">
```

