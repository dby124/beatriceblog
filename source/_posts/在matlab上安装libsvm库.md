---
title: SVM学习——在matlab上安装libsvm库（一）
date: 2017-10-21 11:32:53
tags: [SVM]
categories: [机器学习]
---

# 环境搭建

-  平台：
	- Windows PC
	- MATLAB 软件
	- libsvm库（SVM工具箱）

-  安装步骤
-  准备工作
	- 安装对应的编译器，在网站上查看当前matlab版本支持的编译器版本。本文使用的`matlab 2015a`版本（[支持编译器详情](https://cn.mathworks.com/content/dam/mathworks/mathworks-dot-com/support/sysreq/files/SystemRequirements-Release2015a_SupportedCompilers.pdf)）。
	- 通过查询，发现`Microsoft Windows SDK7.1`支持`matlab 2015a`，所以下面安装这个编译器。
	- 根据安装说明，在安装这个`Windows SDK7.1`之前，1）首先需要安装最新的`Windows service Pack`，用于更新（ps：猜测每个电脑都默认安装，所以在安装过程中直接忽视了这条要求）。2）安装`Microsoft .NET Framework 4`,用于支持安装`Microsoft Windows SDK7.1`。

-  安装libsvm
	- 在[libsvm官网](http://www.csie.ntu.edu.tw/~cjlin/libsvm/)上查找相应的libsvm库，选择`MATLAB and OCTAVE`语言，下载最新版的[`libsvm-3.22.zip`](http://www.csie.ntu.edu.tw/~cjlin/libsvm/#download)
	- 下载后把工具包放在MATLAB安装目录下的toolbox文件夹中，例如， D:\Learn program\matlab2015a\toolbox\libsvm-3.22。
	- 假设你使用的是64位的操作系统和matlab。打开matlab，在matlab目录中添加（set path）：D:\Learn program\matlab2015a\toolbox\libsvm-3.22\windows。因为windows下包含了matlab可执行的二进制文件libsvmread.mexw64/libsvmwrite.mexw64/svmpredict.mexw64/svmtrain.mexw64
	- 在matlab中，进入到D:\Learn program\matlab2015a\toolbox\libsvm-3.22\matlab目录下。运行`mex –setup`，此时按照[LIBSVM在matlab下的使用安装](http://apinetree.blog.51cto.com/714152/1560254/)文中提到的步骤继续操作。
	- 但是由于接口的问题，又出现警告：
	
		MEX 配置为使用 'Microsoft Visual C++ 2012 (C)' 以进行 C 语言编译。

		警告: MATLAB C 和 Fortran API 已更改，现可支持
	        包含 2^32-1 个以上元素的 MATLAB 变量。不久以后，
	         您需要更新代码以利用
	         新的 API。您可以在以下网址找到相关详细信息:
	         http://www.mathworks.com/help/ma ... use-64-bit-api.html。

	- 根据 [关于libsvm工具箱在64位matlab下的安装说明](http://blog.csdn.net/buaasuozi/article/details/50781615/)文中提到的方法，进行下面关键两步：
		- 切换matlab运行目录至：D:\Learn program\matlab2015a\toolbox\libsvm-3.22\matlab，在命令行输入make，也就是该目录下的那个make文件。这时候就像以前安装libsvm一样，会自动make出来很多文件，不用管，主要是会看到make出带有mexm64后缀名的文件，这就成功了。
		- 拷贝第一步中matlab文件夹下所有make出来的文件（.mex64），到libsvm-3.22\windows文件夹下，全部替换。这样就可以了。

-  测试（此处将libsvm-3.22中文`heart_scale`拷贝到libsvm-3.22\matlab目录下，运行下面的语句）
	
	```sh
	 [heart_scale_label,heart_scale_inst]=libsvmread('heart_scale');
	model = svmtrain(heart_scale_label,heart_scale_inst, '-c 1 -g 0.07');
	[predict_label, accuracy, dec_values] =svmpredict(heart_scale_label, heart_scale_inst, model); % test the trainingdata
	```
-  测试结果，出现如下结果表明安装成功
	```sh
	optimization finished, #iter = 134
	nu = 0.433785
	obj = -101.855060, rho = 0.426412
	nSV = 130, nBSV = 107
	Total nSV = 130
	Accuracy = 86.6667% (234/270) (classification)
	```
# SVM参数优化
MATLAB中三种得到最佳g和c参数的方法：

## 遗传算法（GA）选择最佳的SVM参数c&g

- 在运行案例时，报错：` Undefined function or variable 'rep'.`，未找到rep函数，此时，需要安装GA的工具箱，下载英国谢菲尔德大学开发的遗传算法工具箱gatbx，[可从此处下载](http://www.ilovematlab.cn/thread-202921-1-1.html)

> 由于其中的函数扩展名是M，而不是m，需要将其全部改为小写的m，再把文件夹复制到toolbox中，加载至matlab，即可。

- [函数接口介绍](http://www.matlabsky.com/thread-12412-1-1.html?_dsign=42cd2291)

	```sh
	（1）利用GA参数寻优函数(分类问题):gaSVMcgForClass
	[bestCVaccuracy,bestc,bestg,ga_option]= 
	gaSVMcgForClass(train_label,train,ga_option)
	输入：
	train_label:训练集的标签，格式要求与svmtrain相同。
	train:训练集，格式要求与svmtrain相同。
	ga_option:GA中的一些参数设置，可不输入，有默认值，详细请看代码的帮助说明。
	输出：
	bestCVaccuracy:最终CV意义下的最佳分类准确率。
	bestc:最佳的参数c。
	bestg:最佳的参数g。
	ga_option:记录GA中的一些参数。
	\==========================================================
	（2）利用GA参数寻优函数(回归问题):gaSVMcgForRegress
	[bestCVmse,bestc,bestg,ga_option]= 
	gaSVMcgForRegress(train_label,train,ga_option)
	其输入输出与gaSVMcgForClass类似，这里不再赘述。
	```

- 使用案例，加入gaSVMcgForClass.m函数

	```sh
	% GA的参数选项初始化
	ga_option.maxgen = 200;
	ga_option.sizepop = 20; 
	ga_option.cbound = [0,100];
	ga_option.gbound = [0,100];
	ga_option.v = 5;
	ga_option.ggap = 0.9;
	% 得到最佳参数
	[bestacc,bestc,bestg] = gaSVMcgForClass(train_wine_labels,train_wine,ga_option);
	% 利用最佳的参数进行SVM网络训练
	cmd = ['-c ',num2str(bestc),' -g ',num2str(bestg)];
	model = svmtrain(train_wine_labels,train_wine,cmd);
	```

## PSO选择最佳的SVM参数c&g
- 使用案例，加入psoSVMcgForClass.m函数

	```sh
	% 得到最佳参数
	[bestacc,bestc,bestg] = psoSVMcgForClass(train_wine_labels,train_wine);
	% 利用最佳的参数进行SVM网络训练
	cmd = ['-c ',num2str(bestc),' -g ',num2str(bestg)];
	model = svmtrain(train_wine_labels,train_wine,cmd);
	```

## 贪婪算法（Grid）选择最佳的SVM参数c&g
- 使用案例，加入gridSVMcgForClass.m函数

	```sh
	% 首先进行粗略选择: c&g 的变化范围是 2^(-10),2^(-9),...,2^(10)
	[bestacc,bestc,bestg] = gridSVMcgForClass(train_wine_labels,train_wine,-10,10,-10,10);
	% 根据粗略选择的结果图再进行精细选择: c 的变化范围是 2^(-2),2^(-1.5),...,2^(4), g 的变化范围是 2^(-4),2^(-3.5),...,2^(4),
	[bestacc,bestc,bestg] = SVMcgForClass(train_wine_labels,train_wine,-2,4,-4,4,3,0.5,0.5,0.9);
	%% 利用最佳的参数进行SVM网络训练
	cmd = ['-c ',num2str(bestc),' -g ',num2str(bestg)];
	model = svmtrain(train_wine_labels,train_wine,cmd);
	```


参考：

1. [LIBSVM在matlab下的使用安装](http://apinetree.blog.51cto.com/714152/1560254/)
2. [关于libsvm工具箱在64位matlab下的安装说明](http://blog.csdn.net/buaasuozi/article/details/50781615/)