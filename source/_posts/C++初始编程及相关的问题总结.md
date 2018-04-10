---
title: C++初始编程及相关的问题总结
date: 2017-09-27 09:16:53
tags: [C++]
categories: [知识扩展]
---
这篇文章是根据之前完成的“数字信号处理器”这门课的作业

## 运行环境及建工程步骤

- 创建工程项目：

	Visual Studio（VS）后，点击菜单栏第一个键，`文件` -> `新建`-> `项目`-> `设置项目名称以及存储位置`-> `选择Win32控制台应用程序`-> 勾选“`为解决方案创建目录`”-> `下一步`-> `下一步`-> 勾选“`空项目`”-> `完成`。

- 更新VS2010，或者卸载VS2012安装2010后，建立Win32 Console Project/MFC项目时会出现"LINK : fatal error LNK1123: 转换到 COFF 期间失败: 文件无效或损坏"的错误。
解决方案：

	（1）：将 `项目`——`项目属性`——`配置属性`——`连接器`——`清单文件`——`嵌入清单` “`是`”改为“`否`”。若还不能解决问题进入第二步。
	
	（2）：查看计算机是否为64bit操作系统，如是，继续如下操作。
	查找是否有两个cvtres.exe。
	C:\Program Files(x86)\Microsoft Visual Studio 10.0\vc\bin\cvtres.exe
	C:\Windows\Microsoft.NET\Framework\v4.0.30319\cvtres.exe
	右键`属性`—`详细信息`， 查看两者版本号，删除/重命名较旧的版本，或者重新设置Path变量。
	治本的办法是第二步，删除旧版本的`cvtres.exe`后，就不需要每次都设置配置了。

- `#include<***.h>`和`#include"***.h"`的区别

	- `#include<***.h>`，用于**系统头文件**，直接从编译器自带的函数库中寻找文件。在#include指令里，系统头文件的文件名要放在尖括号里给出，这是告诉编译器：应该到“标准的”地点寻找这个文件：#include <stdio.h>
	- `#include"***.h"`，用于**自定义头文件**，是先从自定义的文件中找，如果找不到在从函数库中寻找文件。

	> 头文件是一些以.h作为扩展名的标准文本文件。一般情况下，都应该把自定义的头文件和其余的程序文件放在同一个子目录里，或者在主程序目录下专门创建一个子文件夹来集中存放它们。参考：[C++中“头文件”的定义和使用](http://blog.csdn.net/duan19920101/article/details/50991794)

- 自定义头文件

	```sh
	\#ifndef CIRCLE_H
	\#define CIRCLE_H
	//你的代码写在这里
	\#endif
	```
	> CIRCLE_H这个名字实际上是无所谓的，命名为什么都行，只要符合规范都行。原则上来说，建议把它写成这种形式，因为比较容易和头文件的名字对应。

   源文件（.cpp）：主要写实现头文件中已经声明的那些函数的具体代码。需要注意的是，开头必须#include一下实现的头文件，以及要用到的头文件。那么当你需要用到自己写的头文件中的类时，只需要#include进来就行了。**工程中只能有一个main()函数，是应用程序的入口**。

	> 虽说头文件可以用来保存任意代码片段，但典型的做法是只用它们来保存函数声明、用户自定义类型数据（结构和类）、模板和全局性的常量。

	如果你有一个程序需要多次调用一个或一组函数，或是你有一个或一组函数需要在多个程序里调用，就应该把它们的声明拿出来放到一个头文件里。
	
	头文件应该只包含最必要的代码，比如只声明一个类或只包含一组彼此相关的函数。把接口（函数的原型）和实现（函数体的定义）分开是对代码进行模块化的基本原则之一。

## C++编程中遇到的一些问题

- menset()：char型初始化函数
 
	- 所在头文件`<string.h>`或 `<memory.h>`，有人通过测试，发现使用memset似乎不需要使用额外的头文件。。
	- 函数原型：`void *memset(void *s , int ch , size_t  n )`
	- 函数解释：将s中的前n个字节用ch替换并且返回s。
	- 函数作用：在一段内存块中填充某一个给定的值，常用于较大的对结构体和数组的清零操作。
	 
	> 参考：[C++中memset函数的用法](http://blog.csdn.net/qq_22122811/article/details/52738029)一般用于清零。

- 在<float.h>中定义了浮点类型的范围：
	```sh
	#define DBL_MAX 1.7976931348623158e+308 /* max value */
	#define DBL_MIN 2.2250738585072014e-308 /* min positive value */
	```

- 给C++的一维数组赋初值，只有在定义的时候可以整体初始化，
错误的赋值方法:
	```sh
       int *temp = new int[2];
       temp[0] = arr1[0];
       temp[1] = arr1[2];  
	```
	应该为：
	```sh
       int temp[2];
       temp[0] = arr1[0];
       temp[1] = arr1[2];
	```

对于new创建的数组，之间使用创建时的大小。new数组只能用无参的构造函数或者所有参数都有默认值的构造函数。


- **函数调用的三种参数传递方式**：
	- （1）传值调用：实参被拷贝了一份，然后在函数体内使用，函数体内修改参数变量时修改的只是实参的一份拷贝，而实参本身没有改变，所以如果想要在调用的函数中修改实参的值，使用值传递不能达到其目的，这时只能使用引用或者指针传递。

	- （2）传址调用（即是传指针）:传递的是一个地址值,如果改变被调函数中的指针地址，它将应用不到主调函数的相关变量。指针可以改变其指向的对象（指针变量中的值可以改）。

	- （3）传引用：任何对于引用参数的处理都会通过一个间接寻址的方式操作到主调函数中的相关变量，引用对象不能修改。。
	> - [C++中引用传递与指针传递的区别](https://www.cnblogs.com/CheeseZH/p/5163200.html)
	> - [C语言中memset函数详解](http://blog.csdn.net/kevinhg/article/details/45390809)

- **指针的使用**：如果使用下面的方法对一个数组做初始化，`double** q = Pxy;`，此处直接使用Pxy构建一个二维数组,但是在此之后的语句，对数组q操作，Pxy的值也会相应地改变。

- 计算静态数组的长度函数
	```sh
	template<typename T>
	int count(T& x)
	{
	       int s1 = sizeof(x);
	       int s2 = sizeof(x[0]);
	       int result = s1 / s2;
	       return result;
	}
	```
> 对于动态数组，上面的函数不能得到二维数组的长度，sizeof(动态数组)结果是单个元素的大小，相当远`sizeof(x[0])`。


- 随机数的产生
    （1） c语言中rand()函数的用法笔记：http://blog.csdn.net/chikey/article/details/66970397
    （2）C++中rand()函数的用法，可根据这个来产生随机数

    （3）示例，使用下面的语句，产生的是一个0-1的随机数，且精度为1/32767，`rand() / (double)RAND_MAX`;

- C++动态内存分配：

	`delete[] mi;`// 由于mi是静态存储分配，不需要手动释放内存，如果加这两句可能会陷入程序不运行的状态。
	
	```sh
	// 使用new构建并初始化二维数组
	float**  create_array(int row, int col){
	       float** p = new float*[row];
	       for (int i = 0; i < row; ++i) {
	              p[i] = new float[col];
	              for (int j = 0; j < col; ++j) {
	                     p[i][j] = 0;
	              }
	       }
	       return p;
	}
	// 初始化二维数组
	template<typename T>
	void init_array(T&& arr, int row, int col){
	       for (int i = 0; i < row; ++i) {
	              for (int j = 0; j < col; ++j) {
	                     arr[i][j] = 0;
	              }
	       }
	}
	```
> 参考：（1）[C++ 动态内存分配（6种情况，好几个例子）](http://www.cnblogs.com/findumars/p/5272691.html)（2）[C++ 内存分配秘籍—new，malloc，GlobalAlloc详解](https://blog.csdn.net/feidongyizhong/article/details/6325779)（分析详细易懂）

- 调试
（1）数据类型与输入或输出的类型不匹配
`fscanf_s(fp, "%lf,", &temp);   //此处temp定义的是float类型，但是输入确实double型`
报错如下：
`Run-Time Check Failure #2 - Stack around the variable 'temp' was corrupted`.

- 将变量加入到字符数组中：
```sh
sprintf_s(t, "result/FJ_%d_%d_%d_%d.txt", FEATURE_NUM, FEATURE_SUB_NUM,1,5);
```

- 输入字符串，转化为数组，并统计字符个数：

	```
	#include<iostream>
	#include<string>
	using namespace std;
	int main() {
		// 输入数据
		string s1;
		char temp;
		while((temp=cin.get())!='\n') {
			s1 +=temp;
		}
		const int LEN =s1.length();
		char* s = new char[LEN];//根据输入字符串的长度，创建字符数组 
		for(int i = 0;i<LEN;i++){  //将字符串保存到字符数组中 
			s[i]=s1[i];
		}
	    cout<<LEN<<endl;
		return 0;
	}
	```

