---
title: Python相关的概念
date: 2017-09-27 09:16:53
tags: [python]
categories: [科研]
---

# Python简介
-  Python 3.0在设计的时候没有考虑向下兼容，但是Python 2.x的相比于3.x版本语言兼容性较差，但是其运行效率远高于3.x版本。

-  Python是一种解释型、面向对象、动态数据类型的高级程序设计语言。
Python由Guido van Rossum于1989年底发明，第一个公开发行版发行于1991年。

-  像Perl语言一样, Python 源代码同样遵循 GPL(GNU General Public License)协议。

# Python环境搭建
-  安装Python
	-  windows下面的Python安装一般是通过软件安装包安装而不是命令行，所以我们首先要在[Python的官方主页上面下载最新的Python安装包](https://www.python.org/downloads/)。其中本文中使用的是Python 2.7.14`Windows x86-64 MSI installer`的安装包，MSI指的是exe程序安装。

	-  在下载完成之后，一直点击下一步就OK了，安装完成。

	-  打开控制台(cmd命令窗口)，输入“Python”，会发现无法启动Python，此时就需要手动添加环境变量。

	-  手动添加环境变量步骤：鼠标右键我的电脑  -> 属性 -> 点击高级系统设置 -> 点击环境变量 -> 点击PATH -> 

	-  在最后面加上我们的Python安装路径 -> 点击确定。要注意的是：添加PATH路径的时候，要在最后面添加一个分号。

	-  现在我们再次在浏览器里面输入“Python”，就能直接在控制台出入命令了


-  安装pip

	-  pip 是一个安装和管理 Python 包的工具(有点像ios开发中的pod依赖库管理工具) , 是 easy\_install 的一个替换品。`distribute`是`setuptools`的取代(Setuptools包后期不再维护了)，pip是easy\_install的取代。pip的安装需要setuptools 或者 distribute，如果你使用的是Python3.x那么就只能使用distribute因为Python3.x不支持setuptools。

	-  安装步骤
		- 在Python的官网上去下载，[pip下载地址](https://pypi.python.org/pypi/pip#downloads)，下载`pip-9.0.1.tar.gz (md5, pgp) `文件。

		- 下载完成之后，解压到一个文件夹，用CMD控制台进入解压目录，输入`python setup.py install`。
		- 安装好之后，我们直接在命令行输入pip，同样会显示‘pip’不是内部命令，也不是可运行的程序。因为我们还没有添加环境变量。
		- 按照上面相同的方法，在环境变量path的后面添加`C:\Python27\Scripts;`（前面是Python安装目录）。

	-  	[windows下面安装Python和pip终极教程](http://www.cnblogs.com/yuanzm/p/4089856.html)，[Python pip命令](http://www.cnblogs.com/274914765qq/p/4913461.html)，[安装pip教程](https://pip.pypa.io/en/stable/installing/)

	-  [查看python,numpy,scipy,matplotlib的版本及安装位置](http://blog.sina.com.cn/s/blog_8f70642d0102wov5.html)

	> 注意：安装好了之后，在cmd命令行窗口，分别输入`python`和`pip`，如果结果是相关的信息，说明安装成功，否则，可能需要将这两个的安装路径加入到环境变量中，这样才能通过cmd运行Python，以及使用cmd窗口输入pip相关安装包的指令。

-  安装第三方库
	- 首先确保在pip外，还安装了wheel库。如果没有：打开cmd，输入`pip install wheel`进行安装。

	- 此方法通过二进制文件安装第三方库，需要在安装前确定好能够安装的版本。

		- 输入`import pip;print(pip.pep425tags.get_supported())`，界面上输出当前python的版本信息。

		- 在[第三方库安装包下载地址](www.lfd.uci.edu/%7Egohlke/pythonlibs/)上找到相应的whl文件，下载到本地。注意文件的版本信息，应从网页上下载对应的版本为：XXXX-cp35-cp35m-win_amd64.whl

		- 在此处，scipy对应的安装包（请替换为你的可安装版本）。scipy依赖于numpy+mkl，安装scipy前需要先安装好numpy+mkl。通过whl安装库的方法如下：

		- 安装`numpy+mkl`：进入whl文件下载到的文件夹位置，按住shift键的同时，点击鼠标右键，在弹出菜单中选择“在此处打开命令窗口”,输入`pip install numpy-1.13.3+mkl-cp27-cp27m-win_amd64.whl`（注意将文件名替换为你的可用版本的名称），按Enter后，等待安装完成。

		- 安装scipy，输入`pip install scipy-0.19.1-cp27-cp27m-win_amd64.whl`（注意将文件名替换为你的可用版本的名称），按Enter后，等待安装完成。

	> 参考:[Windows下安装python的scipy等科学计算包](http://jingyan.baidu.com/article/ca41422f27c56a1eae99ed39.html)

# python中怎样转到指定目录

```sh
import os
\#获取当前工作目录
os.getcwd()
\#更改当前工作目录
os.chdir('d:\')
os.getcwd()
```


# Python代码调试
-  错误信息
	-  程序存在错误时，Python解释器将竭尽所能帮助你找出问题所在。程序无法成功地运行时，解释器会提供一个`traceback`，`traceback`是一条记录，指出了解释器尝试运行代码时，在什么地方陷入困境。

参考资料：