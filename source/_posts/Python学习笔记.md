---
title: Python学习笔记
date: 2018-03-27 09:50:53
tags: [python]
categories: [科研]
---

## 使用PyCharm
打开PyCharm软件，第一次运行的时候出现下图的窗口，需要进行相关的设置，其中最重要的就是选择python解释器。点击红圈里的下拉菜单按钮即可选择电脑上已安装的某个版本。完成这一步之后就可以开始运行代码了。

![PyCharm](/images/PyCharm.png)

## 输出
python3 输出语法为`print()`，对数字或者字符串进行拼接，直接调用print函数，参数使用逗号隔开的方式。示例如下：

```
summm = 2
print("一共可以拼出", summm, "个不同的等式")
# 输出：一共可以拼出 2 个不同的等式
print("一共可以拼出%d个不同的等式"%summm)
# 输出：一共可以拼出2个不同的等式
```
> 注意：加逗号那个数字和字符串之间会有一个空格；加%的不会有空格，除非在字符串中加空格。

## 导入模块

**导入模块的所有方法**：
```
import module_name		#表示将模块中的所有代码加载到这个位置并赋值给变量module_name，并执行模块
import libs.module_name 	#表示从libs目录下导入模块文件module_name，调用方法：libs.module_name.func()
import module1_name,module2_name	#同时导入多个模块
from module_name import login,logout 	#相当于将module_name\login中的代码拿到当前位置执行
from libs.module_name import func 	#从目录下的模块文件中导入方法func，调用：func()
from module_name import login as module_name_login 	#对导入模块中的方法取别名
```

**包和模块有两种导入方式**：

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


**import文件的顺序**： 

- 标准库（如sys、os） 
- 相关的第三方的库（如numpy、pandas、matplotlib） 
- 自定义的.py文件或者自定义的库

**导入一个模块时，Python 解析器对模块位置的搜索顺序是**：

- 当前目录
- 如果不在当前目录，Python 则搜索在 shell 变量 PYTHONPATH 下的每个目录。
- 如果都找不到，Python会察看默认路径。UNIX下，默认路径一般为/usr/local/lib/python/。

> 模块搜索路径存储在 system 模块的 sys.path 变量中。变量里包含当前目录，PYTHONPATH和由安装过程决定的默认目录。

参考：[python成长之路【第十八篇】：python模块介绍、模块导入和重载](https://www.cnblogs.com/wooya/p/6495222.html)



## `__init__.py`的作用
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

## IPython的使用

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

## 运行python代码，报错：计算机中丢失`MSVCP140.dll`

解决方法是安装了一个VC++运行库，由于电脑是64位的，64位VC++运行库：[Visual C++ Redistributable for Visual Studio 2015下载地址](https://www.microsoft.com/zh-cn/download/confirmation.aspx?id=48145)，安装完成之后这个问题就解决了。

[python_安装模块后出错:无法启动程序丢失vcom140.dll](https://blog.csdn.net/m0_38034312/article/details/78820113)

## 文件读取
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

## Python升级已经安装的第三方库

主要两步操作，查看需要升级库，升级库。如下：

```
pip list # 列出安装的库
pip list --outdated # 列出有更新的库
pip install --upgrade library_name # 升级库library_name
```

> 升级时将library_name替换为需要升级的库即可。如果在环境中同时安装了python3，应该替换为pip3命令。

## 当我在dcase_util中运行示例时，evaluation部分中名为sed_eval的模块无法找到。

报错如下：

```
Traceback (most recent call last):
File "D:/py_code/firstpython/venv/first_python/test.py", line 4, in 
	import sed_eval
ImportError: No module named sed_eval
```

尝试从示例代码中发现`sed_eval`不依赖与dcase_unit，是一个独立的模块，所以在命令窗口中尝试输入命令`pip install sed_eval`，通过pip查找安装这个模块，最后发现安装成功。

## [python类class中_init_函数以及参数self的简单解释](https://blog.csdn.net/ly_ysys629/article/details/54893185)

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

## [python的可变参数和关键字参数(\*args \**kw)](https://blog.csdn.net/sanbingyutuoniao123/article/details/56016008)

- `*args`是可变参数，args接收的是一个**tuple**；
- `**kw`是关键字参数，kw接收的是一个**dict**。

> **可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple,而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。**注意默认参数一定要用不可变对象，如果是可变对象，程序运行时会有逻辑错误！


参考：[函数的参数](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431752945034eb82ac80a3e64b9bb4929b16eeed1eb9000)

## [python类的继承](https://www.cnblogs.com/bigberg/p/7182741.html)

　　通过继承创建的新类称为“子类”或“派生类”，被继承的类称为“基类”、“父类”或“超类”，继承的过程，就是从一般到特殊的过程。在某些面向对象编程(OOP)语言中，一个子类可以继承多个基类。但是一般情况下，一个子类只能有一个基类，要实现多重继承，可以通过多级继承来实现。

继承概念的实现方式主要有2类：

- 实现继承：直接使用基类的属性和方法。
- 接口继承：仅使用属性和方法的名称、但是子类必须提供实现的能力。（针对的是抽象类）

继承的代码示例如下：
```
class Person(object):   # 定义一个父类
     def talk(self):    # 父类中的方法
        print("person is talking....")  
class Chinese(Person):    # 定义一个子类， 继承Person类
    def walk(self):      # 在子类中定义其自身的方法
        print('is walking...')
 ```


## Mixin实现多重继承

像C或C++这类语言都支持多重继承，一个子类可以有多个父类，这样的设计常被人诟病。因为继承应该是个”is-a”关系。

**1）多重继承存在的问题**：

- **结构复杂化**：如果是单一继承，一个类的父类是什么，父类的父类是什么，都很明确，因为只有单一的继承关系，然而如果是多重继承的话，一个类有多个父类，这些父类又有自己的父类，那么类之间的关系就很复杂了。
- **优先顺序模糊**：假如我有A，C类同时继承了基类，B类继承了A类，然后D类又同时继承了B和C类，所以D类继承父类的方法的顺序应该是D、B、A、C还是D、B、C、A，或者是其他的顺序，很不明确。
- **功能冲突**：因为多重继承有多个父类，所以当不同的父类中有相同的方法是就会产生冲突。如果B类和C类同时又有相同的方法时，D继承的是哪个方法就不明确了，因为存在两种可能性。

> 《松本行弘的程序世界》一书中，作者列举了以上三点。来源：知乎-Wealong C回答的：[Mixin是什么概念?](https://www.zhihu.com/question/20778853/answer/55983839)

**2）解决方法**：为能够利用多继承的优点又解决多继承的问题，提出了**规格继承和实现继承**。规格继承指的是一堆方法名的集合，而实现继承除了方法名还允许有方法的实现。

Java选择了规格继承，提供了接口interface功能，来实现多重继承。Ruby 选择了实现继承，也可以叫Mixin，在 Ruby 中叫 module。在**Python也通过Mixin来实现多重继承**。这里的Mixin，表示混入(mix-in)，它告诉别人，这个类是作为功能添加到子类中，而不是作为父类，它的作用同Java中的接口。

**3）使用Mixin类实现多重继承非常注意的是**：

- 首先它必须表示某一种功能，而不是某个物品，如同Java中的Runnable，Callable等；
- 其次它必须责任单一，如果有多个功能，那就写多个Mixin类
- 然后，它不依赖于子类的实现；
- 最后，子类即便没有继承这个Mixin类，也照样可以工作，就是缺少了某个功能。（比如飞机照样可以载客，就是不能飞了）

示例：
```
class Vehicle(object):
    pass
class PlaneMixin(object):
    def fly(self):
        print 'I am flying'
class Airplane(Vehicle, PlaneMixin):
    pass
```
> 扩展：ReactJS也有Mixin功能，且语法很简洁。参考[关于Python的Mixin模式](https://www.cnblogs.com/aademeng/articles/7262520.html)

## python中super用法
在python中引入super()的目的是保证相同的基类只初始化一次.

**使用格式**：

```
super(AudioContainer, self).__init__(**kwargs)
```

**使用的注意点**：

- super()是一个类名而非函数，super(class, self)事实上调用了super类的初始化函数，产生了一个super对象；
- super()机制是用来解决多重继承的，对于直接调用父类名是没有问题的，但根据前人的经验：要么都用类名调用，要么就全部用super()，不要混合的用。混用super类和非绑定的函数是一个危险行为，这可能导致应该调用的父类函数没有调用或者一个父类函数被调用多次。
- super()继承只能用于派生类，用于基类时就会报错。

**super( )小结**：

- B类继承A类，在B类自己的基础上可以调用A类所有的方法。
- A、B同时拥有__init__，B会改写A中的__init__方法，A类的方法失效。
- super函数可以调用A父类中的属性，B类中有同名属性时，覆盖A类中的同名属性。调用函数时总是先查找B自身的定义，如果没有定义，则顺着继承链向上查找，直到在某个父类中找到为止。
- B类__init__参数需大于或等于A父类的__init__方法，因为super初始化了，参数量为父类参数量。
- super函数原理：super().__init__(xxx,xxx)中的xxx参数为类B中输入的参数，但与类A中参数名相对应，即B类输入参数位置会初始化A类中对应位置的参数。详见:[Python super初始化理解过程](https://www.cnblogs.com/HoMe-Lin/p/5745297.html)的小结（5）。
- 单继承时super()和__init__()实现的功能是类似的，使用super()继承时不用显式引用基类。

> 此处设A是基类，B继承A类。super()避免重复调用。super（）的第一个参数可以是继承链中任意一个类的名字

## Python的魔法方法(magic method)：\_\_str\_\_和\_\_repr\_\_

**魔法方法**就是可以给你的类增加魔力的特殊方法，如果你的对象实现了这些方法中的某一个，那么这个方法就会在特殊的情况下被 Python 所调用，你可以定义自己想要的行为，而这一切都是自动发生的。它们经常是两个下划线包围来命名的（比如 __init__/__new__等等），Python的魔法方法是非常强大的。参考：[Python魔法方法指南](https://zhuanlan.zhihu.com/p/26488074?refer=passer)

若要把一个类的实例变成 str，就需要实现特殊方法\_\_str\_\_()。

Python 定义了\_\_str\_\_()和\_\_repr\_\_()两种方法\_\_str\_\_()用于显示给用户，而\_\_repr\_\_()用于显示给开发人员。

```
class Person(object):
    def __init__(self, name, gender):
        self.name = name
        self.gender = gender
class Student(Person):
    def __init__(self, name, gender, score):
        super(Student, self).__init__(name, gender)
        self.score = score
    def __str__(self):
        return '(Student: %s, %s, %s)' % (self.name, self.gender, self.score)
    __repr__ = __str__
s = Student('Bob', 'male', 88)	# 返回字符
print s
```

> 其实\_\_str\_\_相当于是str()方法 而\_\_repr\_\_相当于repr()方法。str是针对于让人更好理解的字符串格式化，而repr是让机器更好理解的字符串格式化。


## 画图

```
import matplotlib.pyplot as plt
plt.plot(t, x)  
# plt.plot(x, s1, 'b', x, s2, 'g', x, s3, 'r')

# 若要新打开一个图窗口，调用figure()函数
plt.figure()
plt.plot(np.real(h), np.imag(h), 'b')

plt.show()
```

参考：[Python--matplotlib绘图可视化知识点整理](https://www.cnblogs.com/zhizhan/p/5615947.html)


## 遍历数组
三种遍历数组的方法

```
array = ["a","b","c"]
# 1
for item in array:
    print(item)
# 2
for index in range(len(array)):
    print(str(index)+".."+array[index])
# 3
for index,val in enumerate(array):
	print(str(index)+"--"+val);
```

## 数组的相关函数

- 求单维数组的长度
	```
	mySeq = [1,2,3,4,5]  
	print len(mySeq)
	```
- 二维数组的大小
	```
	import numpy as np
	x = np.array([[1,2,5],[2,3,5],[3,4,5],[2,3,6]])
	# 输出数组的行和列数
	print x.shape  # (4, 3)
	# 只输出行数
	print x.shape[0] # 4
	# 只输出列数
	print x.shape[1] # 3
	```

- 如何堆叠数组，形成一个矩阵形式的数据
	```
	an = hilbert(x[0, :].T)
	f, time = inst_freq(an, tt, l)
	k = 0
	
	for item in x:
	    if k > 0:
	        an_temp = hilbert(item.T)
	        f_temp, time_temp = inst_freq(an_temp, tt, l)
	        # 按行拼接数组
	        an = np.vstack((an, an_temp))
	        f = np.vstack((f, f_temp))
	    k = k + 1
	```
	输出：
	```
	[[ 1  8  3  9]
	 [ 1  2  3 -2]
	 [ 2  3  4 -1]
	 [ 3  4  5  0]
	 [ 4  5  6  1]]
	[[ 1  1  2  3  4]
	 [ 8  2  3  4  5]
	 [ 3  3  4  5  6]
	 [ 9 -2 -1  0  1]]
	```

- python numpy 数组批量操作

	```
	import numpy as np
	a = np.random.randint(-5, 5, (5, 5))
	# （1）同(2)(3),将小于0的元素赋值为0。
	np.maximum(a, 0)
	# （2）对每个元素进行运算。
	(a + abs(a)) / 2
	# （3）将小于0的元素赋值为0。
	b = a.copy()
	b[b < 0] = 0
	# （4）a元素>0时，a对应元素不变，否则a对应元素赋值为0。
	np.where(a > 0, a, 0)
	```

	输出：
	```
	# a
	array([[-4, -4, -5,  2,  1],
       [-1, -2, -1,  3,  3],
       [-1, -2,  3, -5,  3],
       [ 0, -3, -5,  1, -4],
       [ 0,  3,  1,  3, -4]])
	# （1）
	array([[0, 0, 0, 2, 1],
       [0, 0, 0, 3, 3],
       [0, 0, 3, 0, 3],
       [0, 0, 0, 1, 0],
       [0, 3, 1, 3, 0]])
	# （2）
	array([[0, 0, 0, 2, 1],
       [0, 0, 0, 3, 3],
       [0, 0, 3, 0, 3],
       [0, 0, 0, 1, 0],
       [0, 3, 1, 3, 0]])
	# （3）
	array([[0, 0, 0, 2, 1],
       [0, 0, 0, 3, 3],
       [0, 0, 3, 0, 3],
       [0, 0, 0, 1, 0],
       [0, 3, 1, 3, 0]])
	# （4）
	array([[0, 0, 0, 2, 1],
       [0, 0, 0, 3, 3],
       [0, 0, 3, 0, 3],
       [0, 0, 0, 1, 0],
       [0, 3, 1, 3, 0]])
	```
	> 直接调用函数处理数组的每个元素，避免使用for循环。参考：
[python numpy 数组如何对每个元素进行操作？](https://www.zhihu.com/question/46988087/answer/115228501)

- 对矩阵做转置
	```
	matrix_2_new = transpose(matrix_2)
	# or
	matrix_2_new = matrix_2.T
	```

- 矩阵拼接
	```
	列合并/扩展：np.column_stack()
	行合并/扩展：np.row_stack()
	```
	
	> [python中的矩阵运算](https://www.cnblogs.com/chamie/p/4870078.html)

## 如何获取输入参数数目

```
def test_var_args(*args):
    if len(args) == 2:
        print(args[0]+args[1])
    else:
        print(args[0])
```

## 装饰器（Decorator）

装饰器本身是一个函数，目的是在不改变装饰函数代码的情况下，增加额外的功能，装饰器的返回值是已装饰的函数对象。如下例：

```
def dec(f):
    n=3
    def wrapper(*args, **kw):
        return f(*args, **kw)*n
    return wrapper

@dec
def foo(n):
    return n*2
print(foo(3))	# 18
print(foo(2))	# 12
```

@dec的作用是把元foo函数（待装饰函数）赋值给dec函数（装饰函数），然后返回值wrapper函数（已装饰函数）重新赋值给foo函数，因为实际上foo（x）语句实际执行的是：
```
def wrapper(x)
	return foo(x) * n	# 根据上述代码可知，n=3
```

参考：
1. [如何理解Python装饰器？](https://www.zhihu.com/question/26930016/answer/99243411)
2. [装饰器](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014318435599930270c0381a3b44db991cd6d858064ac0000)


## 文件路径

```
# python中怎样转到指定目录
import os
# 获取当前工作目录
print(os.getcwd())				# D:\py_code\second_py
print(os.path.abspath('.'))		# D:\py_code\second_py

# 获取当前模块的文件名
print(__file__)		# D:/py_code/second_py/test_hht.py

# 获取当前Python命令的可执行文件路径
import sys
print(sys.executable)	# D:\py_code\second_py\venv\Scripts\python.exe
```

拼接路径，防止字符串转义
```
filename = os.getcwd()+r"\ASC\datasets\TUT-acoustic-scenes-2017-development\audio\a001_0_10.wav"
# 输出: D:\py_code\second_py\ASC\datasets\TUT-acoustic-scenes-2017-development\audio\a001_0_10.wav
```

## [Python中join函数和os.path.join用法](https://blog.csdn.net/zmdzbzbhss123/article/details/52279008)
os.path.join()函数，用于合并目录

语法：  
```
os.path.join(path1[,path2[,......]])
```

> 返回值：将多个路径组合后返回。注：第一个绝对路径之前的参数将被忽略

示例：
```
import os
path = os.path.join('system_data', 'model')
print(path)
# 输出是：system_data/model
```

## [Python中有split()和os.path.split()两个函数：](https://blog.csdn.net/sxingming/article/details/51475382)

split()：拆分字符串。通过指定分隔符对字符串进行切片，并返回分割后的字符串列表。语法：
```
str.split(str=" ",num=string.count(str))[n]
```

os.path.split()：将文件名和路径分割开。

## 定义全局变量及常用矩阵函数

- 定义全局变量

	```
	global sply
	```
	> 先要用关键字global将变量定义为全局变量，然后在函数或者条件语句中对变量进行操作。

- repmat()函数

	示例：
	```
	from numpy.matlib import repmat
	A = np.linspace(1, len(t), splx)
	indt = repmat(np.round(A), 3, 1)
	```
	> repmat()函数介绍：[MATLAB repmat函数的使用](https://blog.csdn.net/caichao08/article/details/53725620)

