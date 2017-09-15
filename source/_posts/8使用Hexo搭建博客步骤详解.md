---
title: 使用Hexo搭建博客步骤详解
date: 2017-08-09 11:10:30
tags:
categories: [知识扩展]
---
## 简介
hexo —— 简单、快速、强大的Node.js静态博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

## 特性
<<<<<<< HEAD

-  风一般的速度
Hexo基于Node.js，支持多进程，几百篇文章也可以秒生成。

-  流畅的撰写
支持GitHub Flavored Markdown和所有Octopress的插件。

-  扩展性
Hexo支持EJS、Swig和Stylus。通过插件支持Haml、Jade和Less.

## 基本配置和使用的工具


> 操作系统：Windows 7

> 使用的工具：**Git**和**Node.js**

Git用于把部署在GitHub的内容获取至本地，Node.js用于安装Hexo。

## 基本步骤
### 安装Git
-  安装 Git,目前支持 Linux/Unix、Solaris、Mac和 Windows 平台上运行
-  配置，关联GitHub

	> 根据已有的GitHub账号，配置本地Git（其实就是建立本地Git与服务器上GitHub账号的关联）之后，就可以将本地的Git仓库上传拷贝到自己的公共库中了，当然下面这种方法是通过git-bash命令窗口输入命令来实现的，

	- 在git-bash命令窗口中输入一下命令
		```sh
		$ mkdir test                                       # 表示在Git的安装目录下，创建一个名为“test”的文件夹
		$ cd test/                                          # 进入测试目录
		$ echo "# 第一次使用 Git ，进行测试" >> README.md     # 创建 README.md 文件并写入内容
		$ ls                                               # 查看目录下的文件
		$ git init                                         # 初始化，在test目录下生成.git文件夹
		$ git add README.md                                # 添加文件
		$ git commit -m "添加 README.md 文件"               # 提交并备注信息
		```
	- 执行提交并备注信息的指令后，输出信息
   ![提交并备注信息的指令](/images/commitinfo.png)
	
	- 通过git命令将本地的文件上传到GitHub账户对应的仓库中提交到 Github

		```sh
		$ git remote add origin git@github.com:dby124/test.git
		$ git push -u origin master
		```

	- 效果如下图：
   ![提交到GitHub上](/images/commit2github.png)
	
	- 在git\logs\refs\heads文件夹中的master文件，其内容如下：
![master文件](/images/master.png)

-  [参考GitHub简明教程](http://www.runoob.com/w3cnote/git-guide.html)

### 安装Node.js
-  下载安装文件，[nodejs官网](https://nodejs.org/en/download/)，我这里下载的是node-v6.11.2-x64.msi.
-  安装nodejs
	> 双击"node-v6.11.2-x64.msi"，开始安装nodejs，自定义安装在D:\dev\nodejs下面。
	
	> 在cmd控制台输入：`node -v`，控制台将打印出：`v6.11.2`，出现版本提示表示安装成功。
	
	> 该引导步骤会将node.exe文件安装到D:\dev\nodejs\目录下，并将该目录添加进PATH环境变量。
-  安装相关环境
    
		npm install express -g
		npm install jade -g
		npm install mysql -g
		npm install coffee-script -g

	> 默认情况下上述组件都是安装在D:\dev\nodejs\node_modules文件夹下，这也是nodejs相关组件的自动查找路径。确认安装的命令：`coffee -v`，出现版本号表示成功安装。

以上环境基本就配置完成

### 安装Hexo
-  这一步的前提是必须先安装 Node.js和Git

		npm install hexo -g
-  升级，更新hexo到最新版（升级仅需一步就把 Hexo 本体和所有相依套件安装完毕）

		npm update hexo -g
-  初始化如果指定 `<folder>`，便会在目前的资料夹建立一个名为` <folder> `的新资料夹；否则会在目前资料夹初始化。在Git命令行窗口中输入（指定 <folder>的方法是）：

		cd [需要指定的文件夹路径]
-  跳转到对应的文件夹目录下后，进行初始化：

		hexo init
- 初始化后对应的资料夹中有一下文件（_config.yml, theme/, source/, scaffolds/, package.json, .gitignore）;文件作用：
	
	> _config.yml：整个站点的配置，包括基本介绍，所使用的主题，关联的仓库等；
	>
	> theme/：所用的主题以及主题的配置；
	>
	> source/：目前所写的所有文章以及404页面等；
	>
	> scaffolds/：Hexo所提供的模板文件；
	>
	> package.json：说明站点使用了哪些包；
	>
	> gitignore：放置在git端时忽略的文件；


### 安装Markdown编辑器
-  简介：**Markdown**是一种可以使用普通文本编辑器编写的标记语言，通过类似HTML的标记语法，它可以使普通文本内容具有一定的格式。git上上传的文本一般都是用Markdown编辑的，而且它的语法也很简单，所以这很实用。

-  安装，[下载地址](http://www.markdownpad.com/)，下载得到 `markdownpad2-setup.exe`，双击并完成安装。

-  作用：由于使用Hexo写博客，其中博客文章的格式就是.md格式，需要使用Markdown编辑器完成编辑，将纯文本转化为html，在浏览器上显示出来。

-  [Markdown语法说明](http://www.appinn.com/markdown/)

### 安装主题
可以到[Hexo官网主题页](https://hexo.io/themes/)去搜寻自己喜欢的theme。我安装的是[hexo-theme-next](http://theme-next.iissnan.com/)。

### 发布文章
1） 新建文章

在对应的博客文件夹下的git-bash命令行中运行以下命令

		$ hexo new "filename"        #新建文章
新建文章之后，在博客对应的文件夹`source\_posts`中找到对应的`.md`文件，然后用Markdown编辑器打开，对文章内容进行编辑。

2）本地服务器上测试

保存文章后，在git-bash命令行中输入以下命令

		$ hexo clean                 # 清除Hexo的缓存
		$ hexo s                     # 打开本地服务器

3）部署到对应的网站上去

本地测试没有问题后，执行下面命令

		$ hexo g                     # 生成静态网页
		$ hexo d                     #上传到GitHub对应的仓库中

## 自动备份Hexo博客源文件

参考：[hexo个人博客搭建教程](https://cherryzzangg.github.io/2017/08/12/hexo%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E6%95%99%E7%A8%8B/)


每次更新博文并`deploy`到服务器上之后，备份就会自动启动并完成备份。


## [【Hexo+Github】域名和github绑定的问题](http://www.jianshu.com/p/1d427e888dda)
=======

-  风一般的速度
Hexo基于Node.js，支持多进程，几百篇文章也可以秒生成。

-  流畅的撰写
支持GitHub Flavored Markdown和所有Octopress的插件。

-  扩展性
Hexo支持EJS、Swig和Stylus。通过插件支持Haml、Jade和Less.

## 基本配置和使用的工具


> 操作系统：Windows 7

> 使用的工具：**Git**和**Node.js**

Git用于把部署在GitHub的内容获取至本地，Node.js用于安装Hexo。

## 基本步骤
### 安装Git
-  安装 Git,目前支持 Linux/Unix、Solaris、Mac和 Windows 平台上运行
-  配置，关联GitHub

	> 根据已有的GitHub账号，配置本地Git（其实就是建立本地Git与服务器上GitHub账号的关联）之后，就可以将本地的Git仓库上传拷贝到自己的公共库中了，当然下面这种方法是通过git-bash命令窗口输入命令来实现的，

	- 在git-bash命令窗口中输入一下命令
		```sh
		$ mkdir test                                       # 表示在Git的安装目录下，创建一个名为“test”的文件夹
		$ cd test/                                          # 进入测试目录
		$ echo "# 第一次使用 Git ，进行测试" >> README.md     # 创建 README.md 文件并写入内容
		$ ls                                               # 查看目录下的文件
		$ git init                                         # 初始化，在test目录下生成.git文件夹
		$ git add README.md                                # 添加文件
		$ git commit -m "添加 README.md 文件"               # 提交并备注信息
		```
	- 执行提交并备注信息的指令后，输出信息
   ![提交并备注信息的指令](/images/commitinfo.png)
	
	- 通过git命令将本地的文件上传到GitHub账户对应的仓库中提交到 Github

		```sh
		$ git remote add origin git@github.com:dby124/test.git
		$ git push -u origin master
		```

	- 效果如下图：
   ![提交到GitHub上](/images/commit2github.png)
	
	- 在git\logs\refs\heads文件夹中的master文件，其内容如下：
![master文件](/images/master.png)

-  [参考GitHub简明教程](http://www.runoob.com/w3cnote/git-guide.html)

### 安装Node.js
-  下载安装文件，[nodejs官网](https://nodejs.org/en/download/)，我这里下载的是node-v6.11.2-x64.msi.
-  安装nodejs
	> 双击"node-v6.11.2-x64.msi"，开始安装nodejs，自定义安装在D:\dev\nodejs下面。
	
	> 在cmd控制台输入：`node -v`，控制台将打印出：`v6.11.2`，出现版本提示表示安装成功。
	
	> 该引导步骤会将node.exe文件安装到D:\dev\nodejs\目录下，并将该目录添加进PATH环境变量。
-  安装相关环境
    
		npm install express -g
		npm install jade -g
		npm install mysql -g
		npm install coffee-script -g

	> 默认情况下上述组件都是安装在D:\dev\nodejs\node_modules文件夹下，这也是nodejs相关组件的自动查找路径。确认安装的命令：`coffee -v`，出现版本号表示成功安装。

以上环境基本就配置完成

### 安装Hexo
-  这一步的前提是必须先安装 Node.js和Git

		npm install hexo -g
-  升级，更新hexo到最新版（升级仅需一步就把 Hexo 本体和所有相依套件安装完毕）

		npm update hexo -g
-  初始化如果指定 `<folder>`，便会在目前的资料夹建立一个名为` <folder> `的新资料夹；否则会在目前资料夹初始化。在Git命令行窗口中输入（指定 <folder>的方法是）：

		cd [需要指定的文件夹路径]
-  跳转到对应的文件夹目录下后，进行初始化：

		hexo init
- 初始化后对应的资料夹中有一下文件（_config.yml, theme/, source/, scaffolds/, package.json, .gitignore）;文件作用：
	
	> _config.yml：整个站点的配置，包括基本介绍，所使用的主题，关联的仓库等；
	>
	> theme/：所用的主题以及主题的配置；
	>
	> source/：目前所写的所有文章以及404页面等；
	>
	> scaffolds/：Hexo所提供的模板文件；
	>
	> package.json：说明站点使用了哪些包；
	>
	> gitignore：放置在git端时忽略的文件；


### 安装Markdown编辑器
-  简介：**Markdown**是一种可以使用普通文本编辑器编写的标记语言，通过类似HTML的标记语法，它可以使普通文本内容具有一定的格式。git上上传的文本一般都是用Markdown编辑的，而且它的语法也很简单，所以这很实用。

-  安装，[下载地址](http://www.markdownpad.com/)，下载得到 `markdownpad2-setup.exe`，双击并完成安装。

-  作用：由于使用Hexo写博客，其中博客文章的格式就是.md格式，需要使用Markdown编辑器完成编辑，将纯文本转化为html，在浏览器上显示出来。

-  [Markdown语法说明](http://www.appinn.com/markdown/)

### 安装主题
可以到[Hexo官网主题页](https://hexo.io/themes/)去搜寻自己喜欢的theme。我安装的是[hexo-theme-next](http://theme-next.iissnan.com/)。

### 发布文章
1） 新建文章

在对应的博客文件夹下的git-bash命令行中运行以下命令

		$ hexo new "filename"        #新建文章
新建文章之后，在博客对应的文件夹`source\_posts`中找到对应的`.md`文件，然后用Markdown编辑器打开，对文章内容进行编辑。

2）本地服务器上测试

保存文章后，在git-bash命令行中输入以下命令

		$ hexo clean                 # 清除Hexo的缓存
		$ hexo s                     # 打开本地服务器

3）部署到对应的网站上去

本地测试没有问题后，执行下面命令

		$ hexo g                     # 生成静态网页
		$ hexo d                     #上传到GitHub对应的仓库中

## 自动备份Hexo博客源文件

参考：[hexo个人博客搭建教程](https://cherryzzangg.github.io/2017/08/12/hexo%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E6%95%99%E7%A8%8B/)


每次更新博文并`deploy`到服务器上之后，备份就会自动启动并完成备份。
>>>>>>> 93b7cb8a0a49d9f6481de53a40ac647fe2c08a05
