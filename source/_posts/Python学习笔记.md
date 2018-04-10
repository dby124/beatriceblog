---
title: Python学习笔记
date: 2018-03-27 09:50:53
tags: [python]
categories: [科研]
---

## 1. 使用PyCharm
打开PyCharm软件，第一次运行的时候出现下图的窗口，需要进行相关的设置，其中最重要的就是选择python解释器。点击红圈里的下拉菜单按钮即可选择电脑上已安装的某个版本。完成这一步之后就可以开始运行代码了。

![PyCharm](/images/PyCharm.png)


## 2. 包和模块有两种导入方式：

- 精确导入
    
    ```
    from Root.Pack1 import Pack1Class
    import Root.Pack1.Pack1Class
    ```
- 模糊导入
        
    ```
    from Root.Pack1 import *
    ```
> 模糊导入中的*中的模块是由`__all__`来定义的，`__init__.py`的另外一个作用就是定义package中的`__all__`，用来模糊导入。

## 3. `__init__.py`的作用
- **package的标识**。每个package实际上是一个目录（Directory),IDE通过`__init__.py`识别它为package。
-  **定义__all__用来模糊导入**。
    - 在`__init__.py`文件中定义package中的`__all__`
    
        ```
        __all__ = ["Pack1Class","Pack1Class1"]
        ```
    
    - 外包调用
        ```
        from Root.Pack1 import *
        a = Pack1Class.Pack1_AA("Alvin")
        a.PrintName()
        ```

-  **编写Python代码**，不建议在__init__中写python模块，可以在包中在创建另外的模块来写，尽量保证__init__.py简单。

## 4. IPython的使用

使用中出现下列问题：
```
from IPython import embed
# ImportError: No module named IPython
```

> 这是因为没有安装，可以在命令行输入`ipython --version` 验证是否安装，如果没有通过`pip install ipython[all]`  或者是 `easy_install ipython`安装。

> 若出现pip不是最新版本时，输入命令 `python -m pip install --upgrade pip`更新pip，再重新安装IPython。


[IPython](http://ipython.org/ipython-doc/stable/index.html)是一种交互式计算和开发环境，作用有：
- 支持自动补全和缩进的交互shell。
- 具有魔术方法(Magic function)。
    
具体如下：

![Ipython](/images/Ipython.png)

进入了ipython，ipython会提示键入下面四个命令之一去试着使用ipython:

命令名 | 说明 | 功能
---|---|---
`?` | ipython特性的介绍和概述 | `?`事实上可以查看一切的变量，若把`?`替换为`??`，一般来说会得到更详尽的信息
`%quickref` | 一份手册，包含了所有的命令 | 
`help` | python的帮助系统 | 
`object?` | 关于object的详细信息，如果键入object??会更详细 | 

> 建议首先使用`%quickref`。这个命令以%开头。**所有以%开头的方法，都是所谓的魔术方法(Magic function)，也就是ipython内置的一些方法**。需注意：魔术方法有`%`和`%%`之分，`%`叫line magic（专门针对一行的命令）,如`%timeit`；`%%`叫cell magic（针对多行的命令），如`%%timeit`。所有的魔术方法都可以查看源码，仔细阅读的话，对自己写代码也很有裨益，具体方法是在魔术方法后面加??，譬如%timeit??。

## 5. 运行python代码，报错：计算机中丢失`MSVCP140.dll`

解决方法是安装了一个VC++运行库，由于电脑是64位的，64位VC++运行库：[Visual C++ Redistributable for Visual Studio 2015下载地址](https://www.microsoft.com/zh-cn/download/confirmation.aspx?id=48145)，安装完成之后这个问题就解决了。

[python_安装模块后出错:无法启动程序丢失vcom140.dll](https://blog.csdn.net/m0_38034312/article/details/78820113)

## 6. 文件读取
对于读写实际的数据文件，Python 提供了必要的函数和方法进行默认情况下的文件基本操作。可以采用 file 对象做大部分的文件操作。

操作文件之前，首先需要打开文件，**Python内置的open()函数**可以打开一个文件，创建一个file对象，相关的方法才可以调用它进行读写。

语法：`file object = open(file_name [, access_mode][, buffering])`

不同模式（access_mode）打开文件的完全列表：

![python文件打开模式](/images/python文件打开模式.png)


示例：
```
#在C盘下面有一个文件abc.txt。  
#open()  
with open(r'c:\\abc.txt') as demo:  
for line in demo:  
print(line)  
```

## 7. Python升级已经安装的第三方库

主要两步操作，查看需要升级库，升级库。如下：

```
pip list # 列出安装的库
pip list --outdated # 列出有更新的库
pip install --upgrade library_name # 升级库library_name
```

> 升级时将library_name替换为需要升级的库即可。如果在环境中同时安装了python3，应该替换为pip3命令。

## 8. 当我在dcase_unit中运行示例时，evaluation部分中名为sed_eval的模块无法找到。

报错如下：

```
Traceback (most recent call last):
File "D:/py_code/firstpython/venv/first_python/test.py", line 4, in 
	import sed_eval
ImportError: No module named sed_eval
```

尝试从示例代码中发现`sed_eval`不依赖与dcase_unit，是一个独立的模块，所以在命令窗口中尝试输入命令`pip install sed_eval`，通过pip查找安装这个模块，最后发现安装成功。

## 9. [python类class中_init_函数以及参数self的简单解释](https://blog.csdn.net/ly_ysys629/article/details/54893185)

1) 首先类中包含两部分：
- 数据成员（类的属性）：类中所涉及的变量
- 类方法：类中的函数。

2) \__init__函数（应该相当于构造函数）：

- 带有两个下划线开头的函数是声明该属性为私有,不能在类地外部被使用或直接访问。 
- init函数（方法）支持带参数的类的初始化 ，也可为声明该类的属性 
- init函数（方法）的第一个参数必须是 self（self为习惯用法，也可以用别的名字），后续参数则可 以自由指定，和定义函数没有任何区别。

3)函数定义
Python编程中对于某些需要重复调用的程序，可以使用函数进行定义，基本形式为： `def 函数名(参数1, 参数2, ……, 参数N)`，其代码形式如下面所示：

```
def function_name (parameters): 
    block
    return value
```
> 注意在类的内部，使用def关键字可以为类定义一个函数（方法），与一般函数定义不同，**类方法必须包含参数self,且为第一个参数**！

## 10. [python的可变参数和关键字参数(*args **kw)](https://blog.csdn.net/sanbingyutuoniao123/article/details/56016008)

- `*args`是可变参数，args接收的是一个**tuple**；
- `**kw`是关键字参数，kw接收的是一个**dict**。

> **可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple,而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。**注意默认参数一定要用不可变对象，如果是可变对象，程序运行时会有逻辑错误！