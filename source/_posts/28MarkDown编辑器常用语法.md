---
title: 'MarkDown编辑器常用语法'
date: 2017-09-05 22:21:17
tags: [markdown]
categories: [知识扩展]
---

# 分割线

-  三个或更多-\_\*，必须单独一行，可含空格，如`---`、 `***`、 `___`。

-  效果如下：

---
***
___

> 如果上面的符号是紧挨着上文的文字，就是细线，否则就是上面显示的粗线。

# 图片链接

-  `![图片的名称](图片的本地连接或网络连接)`，如：`![markdown_fontcolor](/images/markdown_fontcolor.png)`

# 设置字体、字号和颜色

　　Markdown是一种可以使用普通文本编辑器编写的标记语言，通过类似HTML的标记语法，它可以使普通文本内容具有一定的格式。但是它本身是不支持修改字体、字号与颜色等功能的！
	
```sh
<font face="黑体">我是黑体字</font>
<font face="微软雅黑">我是微软雅黑</font>
<font face="STCAIYUN">我是华文彩云</font>
<font color=#0099ff size=12 face="黑体">黑体</font>
<font color=#00ffff size=3>null</font>
<font color=gray size=5>gray</font>
```

　　Size：规定文本的尺寸大小。可能的值：从 1 到 7 的数字。浏览器默认值是 3。

![markdown_fontcolor](/images/markdown_fontcolor.png)

# 表格

	```sh
	| Tables        | Are           | Cool  |
	| ------------- |:-------------:| -----:|
	| col 3 is      | right-aligned | $1600 |
	| col 2 is      | centered      |   $12 |
	| zebra stripes | are neat      |    $1 |
	```

	```sh
	项目     | 价格
	-------- | ---
	Computer | $1600
	Phone    | $12
	Pipe     | $1
	```

# 注释
`<!-- 注释 -->`,加上HTML的注释符号，其中的内容在网页中不会显示。

# 实现文件下载

在网上HEXO如何实现文件下载功能，但是一直没有找到比较简便的方法，大部分都是，先把文件上传到网盘或其他云平台上，再给出文件链接，提供下载。

想着在显示图片的时候可以直接用`![]()`的形式加载图片，用`[]()`加载相关的网页链接，就尝试是否也可与图片加载的形式，显示文件。

方法：先在`yourblog_name\source`目录下新建名为`files`的文件夹，将文件存放到文件夹中，在MarkDown编辑器中`[显示的文件名](/files/文件名.扩展名)`，如`[设计模式培训.pdf](/files/设计模式培训.pdf)`，此时将内容部署后，访问这个链接，就可以下载文件。

> yourblog_name是对应博客存放的文件夹名。