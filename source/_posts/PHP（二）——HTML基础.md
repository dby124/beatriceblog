
--- 
title: PHP（二）——HTML基础
date: 2018-04-14 20:22:38 
tags: [HTML] 
categories: [PHP] 
---

## HTML简介
HTNL是超文本标记语言 (Hyper Text Markup Language)，是一种文本标记语言，不需要编译，可以直接被浏览器执行（属于浏览器解释型语言）。

## HTML语法
HTML是一种标记语言，组成HTML语法的元素有**HTML标签和HTML属性**。

### HTML页面设计文件基本结构

编写HTML程序要按照其基本结构形式编写，一个完整的HTML文件结构由HTML主体标记、头部标记、主体区标记组成。

- **主体标签**是`<html>...</html>`，HTML文件中包含`<head></head>`和`<body></body>`，HTML文档内容都应该包含在这两个标记之间。

- **头部标记**用于放置页面的标题及文件信息等内容，通常将其两个标记之间的内容统称HTML的头部，形式为：`<head>...</head>`。HTML主要包括页面的一些基本描述的语句，如引用的JavaScript和CSS一般都在定义HEAD头元素里，其常用的头部标有：
    - `<title>`，显示浏览器标题栏上的文件标题，用于说明文件的性质。
    - `<meta>`，定义页面中的信息。标记是通过属性来定义文件信息的名称、内容等，其能为文档提供关键字、描述等多种信息，但这些文件信息并不会出现在浏览器页面的显示中，只会显示在源代码中。
    - `<base>`，设定URL地址，一般常用来设定浏览器中文件的绝对路径。在浏览器中浏览的时候这些位置会自动附在绝对路径的后面，成为完整的路径。
    - `<style>`，设定CSS层叠样式表内容。

- **主体区标记**是网页的主要部分，绝大多数HTML的内容都放置在这个区域里，通常位于`</head>`之后，`</html>`之前，结构形式：`<body>...</body>`。`<body>`常用的属性有text、bgcolor、background、link（默认链接颜色）、alink（单击时链接颜色）、vlink（访问后链接颜色）。

> 主体区标记中会用到的标签有字体标签（如`<h1>/<h2>/<h3>`、`<ALIGN=LEFT/CENTER/RIGHT>`、`<b>/<i>/<em>/<u>/<s>/<sup>/<sub>/<big>/<small>/<var>/<samp>/tt(打印字体)`、`<font>`等），段落标记`<p>`，行中断标签`<br>`，不换行标签`<nobr>`
    
```
<!-- 这是一个html编程示例 -->
<!doctype html>  
<html>  <!-- html标签开始 -->
    <head>  <!-- 头标记开始，头部信息，包括要引入的文件也在这部分引用 -->
		<meta charset="utf-8">  <!-- 这个是必须的 -->
		<title>文件上传及数据库存储</title>  <!-- 页面标题，在打开页面时，头部显示的名字 -->
	</head>  
	<!-- 页面部分，显示在浏览器页面的内容，在这部分编写 -->
    <body>
        <div>  <!-- 块元素的开始 -->
            <h1>我的第一个标题</h1>
            <p>我的第一个段落。</p>
        </div>
    </body>
</html>
```
    
> 一个HTML文档总是以`<html>`开始，以`</html>`结束。

解析结果如下：

![html_first](/images/html_first.png)

### 页面标题


```
<!-- 此处是在页面中显示的标题，也可用h2,h3,h4,h5,h6标签 -->
<h1 align="center">h1页面标题</h1>
<h2 align="center">h1页面标题</h2>
<h3 align="center">h1页面标题</h3>
<h4 align="center">h1页面标题</h4>
<h5 align="center">h1页面标题</h5>
```
效果：

![html_title](/images/html_title.png)

### 块元素
```
<div>
    <p align="center">p标签表示段落，一个段落之后是换行</p>
	<p><a href="http://www.w3school.com.cn/html/html_links.asp">超链接可以是一个字，一个词，或者一组词，也可以是一幅图像，您可以点击这些内容来跳转到新的文档或者当前文档中的某个部分。</a></p>
	<p><span>span标签是被用来组合文档中的行内元素，本身没有任何属性。其在行内定义一个区域，也就是一行内可以被它划分成好几个区域，从而实现某种特定效果。</span></p>
</div>
```

效果：

![html_div](/images/html_div.png)

### 表单设计

```
<!-- 表单中包含输入控件，用于用户与网站的交互 -->
<!-- form标签用于包含一个表单，其中包含各种输入控件，通过设置控件的type属性来确定输入控件的形式，submit用于交用户输入的数据通过get或是post提交到action属性指定的后台，然后在后台对数据进行处理 -->
<form name="form1" action="" method="post" enctype="multipart/form-data">
  <b>填写表单</b><br /><!-- br表示换行 --><hr>
	<div>
		<!-- 单选按钮 -->
		<p><b>单选按钮</b></p>
	  	<input name="myRadio" type="radio" value="163" onclick="getRadioValue();"/>163邮箱
		<input name="myRadio" type="radio" value="189" onclick="getRadioValue();"/>189邮箱  
		<input name="myRadio" type="radio" value="gmail" onclick="getRadioValue();"/>gmail邮箱
		<br />
	</div>
	
	<div>
		<!-- 文件上传 -->
		<p><b>文件上传</b></p>
	    <input type="file" name="myFile[]" multiple="multiple"/><br/>  
		<br />
	</div>

	<div>
		<!-- 按钮 -->
		<p><b>按钮</b></p>
	    <input name="setValue" type="button" value="选择163邮箱" onclick="setRadioValue('163');"/>
		<br />
	</div>
	
	<div>
	    <!-- 多选框 -->
		<p><b>多选框checkBox:</b></p>  
		<input name="myCheckBox" type="checkbox" value="126" />126邮箱  
		<input name="myCheckBox" type="checkbox" value="163" />163邮箱  
		<input name="myCheckBox" type="checkbox" value="189" />189邮箱  
		<input name="myCheckBox" type="checkbox" value="gmail" />gmail邮箱  
		<input type="checkbox" id="checkAll" onclick="checkall()" />  全选
	</div><br />

	<div>
		<!-- 单行文本输入框 -->
		<p></p>登录：</b></p>
		<p>账号：<input name="name" type="text" value="126" /></p>
		<p>密码：<input name="password" type="password" value="126" /></p>
		<p>邮箱：<input type="email" name="user_email" class="form-control" placeholder="邮箱" required></p>
		<br />
	</div>

	<div>
		<!-- 多行输入框 -->
		<p><b>输入内容:</b></p>
		<p><textarea name="content" style="width:300px;height:100px;"></textarea> </p>
		<br />
	</div>

	<div>
		<!-- 选择框 -->
		<p><b>选择框</b></p>
		<select name="id">
        	<option value="1" >选择1</option>
        	<option value="2" >选择2</option>
        	<option value="3" >选择2</option>
        	<option value="4" >选择4</option>
        </select>
		<br />
	</div><br />
	<!-- 在一个表单中一定要有一个提交的输入控件，或者是有submit属性的按钮，否则无法将用户体现的数据提交到后台页面 -->
	<input type="submit" value="提交"/>  
	<input type="reset" value="重置"/> 
</form><br><hr>
```
效果：

![html_form](/images/html_form1.png)

### 表格设计

```
<!-- 表格标签 -->
<div>
	<p><b>表格</b></p>
	<table cellpadding="1" border="1">
		<!-- 表格的第一行 -->
		<tr>
			<td>
				<span>表格的第一列</span>
			</td>
			<td>
				<span>表格的第二列</span>
			</td>
			<td>
				<span>表格的第...列</span>
			</td>
			<td>
				<span>表格的第n列</span>
			</td>
		</tr>
		<!-- 表格的第二行 -->
		<tr>
			<td>
				<span>表格的第一列</span>
			</td>
			<td>
				<span>表格的第二列</span>
			</td>
			<td>
				<span>表格的第...列</span>
			</td>
			<td>
				<span>表格的第n列</span>
			</td>
		</tr>
		<!-- 第n行 -->
		<tr>
			<td>
				<span>表格的第一列</span>
			</td>
			<td>
				<span>表格的第二列</span>
			</td>
			<td>
				<span>表格的第...列</span>
			</td>
			<td>
				<span>表格的第n列</span>
			</td>
		</tr>
	</table>
</div>
```
效果：

![html_table](/images/html_table.png)

### 行和列标签


```
<!-- 行列的标签 -->
<div>
	<p>行和列</p>
	<ul>
		<li>
			第一行
		</li>
		<li>
			第二行
		</li>
		<li>
			第三行
		</li>
	</ul>
	<ul>第一行</ul>
	<ul>第二行</ul>
	<ul>第三行</ul>
</div>
```

效果：

![html_ulli](/images/html_ulli.png)


## 网页设计应注意的问题

网页设计的应从用户体验、网站的流量、网页的调出率等方面，尤其注意下面几个问题：

- **响应式Web开发**，针对不同设备优化网页。在设计网页的时候应注意智能手机、平板电脑等移动设备进行网页优化。确保用户在任何设备上浏览网页时，得到优秀的阅读体验。
- **游客浏览网页内容**。
- **简洁的用户提交表单**，应尽可能的减少表单的内容。
- **合理的搜索功能**。
- **使用合适的字体**，让用户轻松的看懂网页。
- **避免大块文章，图文结合**。
- **使用清晰的图片**。