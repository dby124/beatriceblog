---
title: MATLAB工具常用函数
date: 2017-08-20 15:16:26
tags: [MATLAB]
categories: [科研]
---
# 简介
-  MATLAB 是一种用于**算法开发、数据可视化、数据分析以及数值计算的高级技术计算语言和交互式环境**。使用 MATLAB，可以较使用传统的编程语言（如 C、C++ 和 Fortran）更快地解决技术计算问题。

-  **应用范围**：包括信号和图像处理、通讯、控制系统设计、测试和测量、财务建模和分析以及计算生物学等众多应用领域。附加的工具箱（单独提供的专用 MATLAB 函数集）扩展了 MATLAB 环境，以解决这些应用领域内特定类型的问题。MATLAB 提供了很多用于记录和分享工作成果的功能。可以将您的 MATLAB 代码与其他语言和应用程序集成，来分发您的 MATLAB 算法和应用。

-  主要功能：
	-  此高级语言可用于技术计算
	    - 此开发环境可对代码、文件和数据进行管理
	
	    - 交互式工具可以按迭代的方式探查、设计及求解问题
	
	    - 数学函数可用于线性代数、统计、傅立叶分析、筛选、优化以及数值积分等
	
	    - 二维和三维图形函数可用于可视化数据
	
	    - 各种工具可用于构建自定义的图形用户界面
	
	-  各种函数可将基于 MATLAB 的算法与外部应用程序和语言（如 C、C++、Fortran、Java、COM 以及 Microsoft Excel）集成。 


-  版本说明
	- 从MATLAB 4.2c开始，每个版本增加了一个建造编号；

	- 例如MATLAB7.6的建造标号是R2008a。说明MATLAB7.6与MATLAB2008a是等同的；

	- 对于建造编号，愈来愈正规化以后，每年出两个版本，例如2011a和2011b。一般来说。a是测试版，b是正式版。从出版时间上看，a是前半年出，b是后半年出。


# 安装工具包

-  MATLAB安装voicebox的步骤
	-  下载安装包

		- [官方主页](http://www.ee.ic.ac.uk/hp/staff/dmb/voicebox/voicebox.html)
	
		- [官方下载](http://www.ee.ic.ac.uk/hp/staff/dmb/voicebox/voicebox.zip)
	 
	- 解压voicebox.zip，将整个目录voicebox复制到MATLAB的安装目录的TOOLBOX目录下，如D:\MATLAB7\toolbox\

	- 打开Matlab，在MATLAB命令窗口中输入以下命令：
		- 将TOOLBOX下新加的voicebox工具箱加到MATLAB的搜索路径中去：
	
			```sh
			cd D:\MATLAB7\toolbox\voicebox 
			```
		- 添加voicebox工具箱的MATLAB的搜索路径也可采用如下指令：
	
			```sh
			 >> addpath(genpath('D:\MATLAB7\toolbox\voicebox')) 
			% 或者
			 >>path('D:\MATLAB7\toolbox\voicebox',path) 
			```
	- 检验是否成功设置的方法：
		- 在命令窗口中输入以下命令：which activlev.m（可以为所加工具箱的任一个M
		文件名称），如果显示正确，就说明上面的设置成功。
	
			```sh
			>> which activlev.m 
			```
		- 结果：
			`D:\MATLAB7\toolbox\voicebox\activlev.m `

-  添加工具包：
	- 将下载的安装包复制matlab安装目录下面的`toolbox`文件夹中；

	- 打开软件；file -> setpath -> Add with subfolders；

	- 将要导入的工具箱所在的文件夹（该文件夹必须在MATLAB的toolbox文件夹中）添加；保存即可。

	- 验证：在命令行中输入改行代码：' which [此处为该工具箱中任意一个函数]'；输出结果若不报错，则说明导入成功。

# matlab调试注意点

-  注释一段代码，使用如下方式：

		 Ctrl+R     注释选定的整段代码；相当于text->Comment；
		 Ctrl+T     取消注释的整段代码；相当于text->Uncomment；
-  报错

		Subscripted assignment dimension mismatch.
		表示矩阵的下标不匹配，可能是给矩阵赋值的过程中没有对齐下标
-  代码调试

	     Ctrl+C    停止运行代码
	     F5     运行代码
-  乘和点乘的区别：

		a*b就是矩阵乘法
		a.*b就是a,b的对应元素相乘
-  MATLAB查看小波基,在命令窗口输入：

    ```sh
	wavemngr('read',1)
	```

-  运行程序时，想要在在命令窗口显示程序中一个变量的值：
	- 只需要在求取这个变量时，后面不加分号，就表示将这个变量在命令行窗口中显示其值

	- 只需要在求取这个变量时，后面不加分号，就表示将这个变量在命令行窗口中显示其值

	- 例如：`[easy_a,easy_p,easy_r,easy_f] = mea_badsig(easy_out)`

-  计算程序的运行时间
	- 在开头加上`tic`，结尾加上`t=toc`；t就是代码运行时间。(不用加分号)

-  转义符：

		''单引号
		%%字符百分比
		\\反斜杠
		\ a报警
		\ b退格
		\ f换页
		\ n新行
		\ r回车
		\ t水平制表符
		\ v垂直选项卡
		\ xN十六进制数，
		N\ N八进制数，



# 常用函数

## MATLAB中一些判断函数

- `isscalar`：判断A是否是标量，即一行一列的矩阵。
- `isnumeric`：判断输入参数是否是数字类型（包括浮点型和整型）。
- `isnan`：判断数组中的元素是否为无穷大。
- `iscell`：判断给定数组是否是元胞数组。
- `isfield`：判断输入是否是结构体数组的域（成员）。
- `isvector`:判断是否为单行或者单列（也可以是单个元素），若是二维及以上的数据，则返回0。
> 如果为真，则返回1（true，否则0（false）。 

## 取整

- `fix(x)` : 截尾取整.

		fix( [3.12 -3.12])
		结果 3 -3

- `floor(x)`:不超过x 的最大整数.(高斯取整)

		floor( [3.12 -3.12])
		结果 3 -4

- `ceil(x)` : 大于x 的最小整数

		ceil( [3.12 -3.12])
		结果 4 -3

- `round(x)`：取最近的整数，相当于四舍五入取整

		round([3.12 -3.12])
		结果 3 -3


## 取余数

- `rem(n,m)`或`mod(n,m)`

	- mod 模数求余

	- rem 求余数

##  求平方根

`sqrt(a)`
          
## 取FFT变换：

```sh	
Y = fft(S,N); %做FFT变换，S为信号的序列，N变换的点数
```

## 取均值 :
- mean函数的作用主要是求列或行的平均数：

> % 下面参数说明：A是矩阵、函数原型：mean(A , dim)，若dim缺省，则默认dim=1，求列平均
	
- 对列求平均数：`mean(A , 1)  = mean(A)    % dim = 1可以简写`

- 对行求平均数：`mean(A , 2)`

- mean2():相当于对整一个矩阵求像素平均值:相当于`mean( mean( A ) )`

## 标准偏差函数std

- s = `std(X)`
	> 此处X是一个矢量，该函数返回标准偏差（注意其分母为n-1，而不是n） 。结果s是一个X各样本偏差无偏估计的平方根(X包含独立的、同分布样本)。如果X是一个矩阵，该函数返回一个行矢量，它包含了X每列元素的标准偏差。

- s =` std(X,flag)`
	> 若flag = 0,该函数与std(X)相同；若flag = 1, std(X,1) 返回的标准偏差其分母为n而不是n-1。

- s = `std(X,flag,dim)`
	> 该函数计算沿着dim指定的维度计算标准偏差。


## 求一维曲线以下面积

```sh
area = polyarea(1:length(x),x);       %求曲线以下的面积
```
## 反转

- `fliplr(x)`：将时间序列x反转，使得x的顺序与原来的完全相反。

##  矩阵
- 空矩阵定义：    a=[ ];

- 矩阵的初始化：

	- `zeros(m,n)`     产生m*n的全0矩阵

	- `ones(m,n)`      产生m*n的全1矩阵

	- `rand(m,n)`      产生均匀分布的随机矩阵，元素取值范围：0.0-1.0

	- `randn(m,n)`     产生正态分布的随机矩阵

	- `magic(N)`       产生N阶魔方矩阵（矩阵的行、列和对角线上元素的和相等）

	- `eye(m,n)`       产生m*n的单位矩阵

- 矩阵的合并：

	- 按行合并（即行数相同）：用将要合并的矩阵放在中括号中，以空格或者逗号隔开

	- 按列合并（即列数相同）：用将要合并的矩阵放在中括号中，以分号隔开

- 获得矩阵行数或列数的函数如下：

	- `ndims(A)`：          返回A的维数

	- `size(A)`：           返回A各个维的最大元素个数

	- `length(A)`：         返回max(size(A))

	- `[m,n]=size(A)`：     如果A是二维数组，返回行数和列数

	- `size(A,1)`：			返回矩阵的行数

	- `size(A,2)`：			返回矩阵的列数

	- `nnz(A)`：            返回A中非0元素的个数
- 对矩阵行和列的操作
    (A,2)表示对行操作，(A,1)表示对列操作

	![求行和列的操作示例](image/get_juzhen_num.png)


- matlab中求矩阵的平方和：`sumsqr`函数 

	- `nnz(A)`：            返回A中非0元素的个数

- matlab中求矩阵的平方和：`sumsqr`函数 

- 矩阵转置函数，`transpose` 函数，示例：`A = transpose(B);`

- 通过复制小矩阵得到较大的矩阵：`repmat`函数
	
	```
	% 语法：
	B = repmat(A,M,N)
	% 示例，初始是2*2的矩阵
	A = [1,3;4,2]
	B = repmat(A,1,3)
	% 得到的结果，生成2*6的矩阵 B = [1,3,1,3,1,3;4,2,4,2,4,2]
	```

- 返回位置信息min函数，输入为一个矩阵，则反馈回来的是每列的最小值和对应的位置。

	```sh	
	[minx, ind] = min(x);
		```
## 求距离
-  求欧氏距离
```sh
    b = sqrt(norm(A-B)^2);
	或：
    for i = 1: 4       % 数组的长度
    	dist_temp(i) = sqrt(sum((A(i)-B(i)).*(A(i)-B(i))));  %欧氏距离的函数。
    end
```

## 判断元素与数组的从属关系
 
```sh
sum(ismember(a,X))          % 如果a在数组X中，则返回为1，否则返回0
a = (ismember(m:n,indzer));         % 返回的是一个元素为0或1的一个数组，对应每个元素是否在数组中
all(ismember(indmax(1):indmin(1),indzer));          % 对判断的结果做或运算
```

## 两个集合(数组)的运算
- 取交集

	```sh
	[c, ia, ib] = intersect(A, B);   %这个函数是c返回A B的交集，ia，ib返回的是交集所在数组的指标(索引)
	```

	比如A=[2 3 4];   B=[3]; 则[c, ia, ib] = intersect(A, B);得到 c=[3]    c在数组A的指标为ia=2  同理 c在数组B的指标为ib=1。

- 取差集

	```sh
	c=setxor(A,B);   % 取两个集合交集的非（异或），c返回A与B的异或，即属于A且不属于B的元素和属于B且不属于A的元素。
	setdiff(A,B);  % 这个的意思就是A里面有的元素,而B里面没有,没有的数据顺序:从大到小(重复的数据不重复出现)
	[c ia]= setdiff(A,B);  %  ia 的意思是 A里面的第几个元素B里面没有 这里面值得注意的是：如果A里面相同的元素B里面都没有 那么就显示最后一次的出现位置
	[C,ia] = setdiff(A,B,'rows');  %这里是第几行不同，C表示出不同行的各个数，ia写出第几行是不同（而且特别注意的地方也是和上面那个一样） 
	```
	
	> [参考文档](https://wenku.baidu.com/view/9adcff6f10a6f524ccbf85ab.html)

## 求序列的自相关和互相关。

- `c = xcorr(x,y)` 返回矢量长度为2*N-1互相关函数序列，其中x和y的矢量长度均为N，如果x和y的长度不一样，则在短的序列后补零直到两者长度相等。

- 求取代码

	```sh
	c = xcorr(x)  %为矢量x的自相关估计。
	c = xcorr(x,y,'option')  %为有正规化选项的互相关计算；
	```

- 其他相关函数
	- "biased"为有偏的互相关函数估计；
	- "unbiased"为无偏的互相关函数估计；
	- "coeff"为0延时的正规化序列的自相关计算；
	- "none"为原始的互相关计算。

	> 在Matalb中，求解xcorr的过程事实上是利用Fourier变换中的卷积定理进行的。

- 相关程度与相关函数的之间的联系

	> - 在概率论和统计学中，相关（Correlation，或称相关系数或关联系数），显示两个随机变量之间线性关系的强度和方向。在统计学中，相关的意义是用来衡量两个变量相对于其相互独立的距离。最常用的是皮尔逊积矩相关系数。其定义是两个变量协方差除以两个变量的标准差（方差的平方根）。
	
	> - 在概率论和统计学中，相关（Correlation，或称相关系数或关联系数），显示两个随机变量之间线性关系的强度和方向。在统计学中，相关的意义是用来衡量两个变量相对于其相互独立的距离。最常用的是皮尔逊积矩相关系数。其定义是两个变量协方差除以两个变量的标准差（方差的平方根）。

	> - 相关系数只是一个比率，不是等单位量度，无什么单位名称，也不是相关的百分数，一般取小数点后两位来表示。相关系数的正负号只表示相关的方向，绝对值表示相关的程度。因为不是等单位的度量，因而不能说相关系数0.7是0.35两倍，只能说相关系数为0.7的二列变量相关程度比相关系数为0.35的二列变量相关程度更为密切和更高。也不能说相关系数从0.70到0.80与相关系数从0.30到0.40增加的程度一样大。

- 相关系数就用命令corrcoef

	```sh
 	z = min(min(corrcoef(x1, x2)))    % 就是x1,x2之间的相关系数。
 	z = abs(z);                       % 取绝对值
	```

	> 取绝对值后，0-0.09为没有相关性，0.3-弱，0.1-0.3为弱相关，0.3-0.5为中等相关，0.5-1.0为强相关

	- 这个函数等价于：

	```sh
	z = sum((x1-mean(x1)).*(x2-mean(x2)))/(sqrt(sum((x1-mean(x1)).^2)*sum((x2-mean(x2)).^2)));
	```

## 产生随机数
-  `linspace(x1,x2,N)`

- 功能：linspace是Matlab中的一个指令，用于产生x1,x2之间的N点行矢量。其中x1、x2、N分别为起始值、中止值、元素个数。若缺省N，默认点数为100。在matlab的命令窗口下输入help linspace或者doc linspace可以获得该函数的帮助信息。

- 例一，在matlab的命令窗口输入：

	```sh
	X=linspace(1,100)
	```

- 将产生从1到100步长为1的数组。类似于在命令窗口中输入：

	```sh
	X=[1:1:100];
	```

- 随机打乱一个数字序列

	```sh
	y = randperm(n);	%y是把1到n这些数随机打乱得到的一个数字序列。
	rowrank = randperm(size(A, 1)); % 随机打乱矩阵的行数
	```
## 用户输入
- 在代码运行过程中，需要用户手动输入某个变量的值通过input函数来实现。

```sh
格式：A=input（提示信息，选项）;
例如：
input_value = input ('请输入初始特征集个数m：');

```


## MATLAB求峭度
- 函数法：

```sh	
x=[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32];
y=kurtosis(x)    %峭度      
```

- 公式法：

```sh
a=mean(x);
b=(x-a).*(x-a).*(x-a).*(x-a);
f=sum(b);
c=f/32;
e=std(x,1).*std(x,1).*std(x,1).*std(x,1);
d=c/e          %峭度
```

> 实验证明，两种方式获取的结果是相等的　　

## 利用feedforwardnet函数创建神经网络

```sh
hide_ele_num = 2*rowNum+1;% 隐藏层神经元个数
%% 建网
net = feedforwardnet(hide_ele_num,'traingdm');     % traingdm为训练函数的设置，此处表示动量梯度下降算法
%% 训练参数设置
net.trainParam.epochs = 10000;     % 最多训练多少次，默认为1000
net.trainParam.goal = 0.001;     % 训练到什么程度才算成功
net.trainParam.lr=0.01;     %学习率设置,应设置为较少值，太大虽然会在开始加快收敛速度，但临近最佳点时，会产生动荡，而致使无法收敛
net.layers{1}.transferFcn = 'tansig';     % 第一层网络的传递函数设置为S函数
net.layers{2}.transferFcn = 'purelin';
%% 开始训练
[net,tr] = train(net,v,p);
```


## Logsig  传递函数为S型的对数函数。

- 调用格式为： A=logsig（N）  
	- N：Q个S维的输入列向量；
	- A：函数返回值，位于区间(0,1) 中

- 波形


![logsig](/images/logsig.png)

# 画图

-  plot画图

	```sh
	plot(x,y); %x y 为相应点集
	```

	```sh
	plot(x,y1,x,y2); % 在一个窗口下绘制多条曲线之方法一
	```

	```sh
	hold on  %在一个窗口下绘制多条曲线之方法二
	plot(x,y1);
	plot(x,y2);
	hold off
	```

- plot后的相关设置

	```sh
	set(gca,'yscale','log'); %画对数坐标的,将y轴的尺度设置为对数，如果要设置横坐标，改为“xscale”即可.
	axis([0 N -2 2]); %设置x，y轴的取值范围，前面两个是x轴的，后面两个是y轴的
	xlabel('x_axis_name'); %设置x y轴名称
	ylabel('x_axis_name'); 
	title('name'); %设置图名称
	legend('曲线1','曲线2');  %加图例（就是加了线型后，右上角的注释）
	grid on;  %加网格
	```

-  线型和颜色
	- 线型（线方式）： - 实线 :点线 -. 虚点线 - - 波折线
	- 线型（点方式）：. 圆点 +加号 * 星号 x x形 o 小圆
	- 线条粗细：plot(x,y,'r','linewidth',4); 
	- 参数为数据点的形状：'.'为实心黑点；'\*'为八线符；'<'为左三角；'d'为菱形；'o'为空心圆圈；'s'为方块符；'+'为十字符号；'^'为上三角符；'>'为右三角符；'h'为六角星；'p'为五角星；'x'为叉字符。
	- 颜色： r红； g绿； b蓝； c青  m紫； k黑； w白；y黄；
	- 例子：

		```sh
		plot(x,y1,’b:+’,x,y2,’g-.*’);
		```
-  加图例legend

	```sh
	legend(字符串1，字符串2，字符串3，…，参数);  %其中字符串为画图顺序依次标注
	```
	- 参数说明如下：
	
参数字符串 | 含  义
-------- | ---
0    | 尽量不与数据冲突，自动放置在最佳位置 
1    | 放置在图形的右上角
2    | 放置在图形的左上角
3    | 放置在图形的左下角
4    | 放置在图形的右下角
-1  | 图形窗外

-  设置背景色

	```sh
	set(gcf,'color','none'); %无背景
	set(gcf,'color',[0,0,0]); %背景色为黑
	set(gcf,'color',[1,1,1]); %背景色为白
	```


- subplot
	- 功能:分割figure，创建子坐标系
	- 语法

		```sh
		figure
		h = subplot(m,n,p) or subplot(mnp)
        subplot(m,n,p,'replace')
        subplot(m,n,P)
        subplot(h)
        subplot('Position',[left bottom width height])
        subplot(..., prop1, value1, prop2, value2, ...)
        h = subplot(...) 
		```

	- 描述：`h = subplot(m,n,p)`或者`subplot(mnp)`将figure划分为`m×n`块，在第p块创建坐标系，并返回它的句柄。当m,n,p<10时，可以简化为subplot(mnp)或者subplot mnp 
 
	> （注：subplot（m,n,p）或者subplot（mnp）此函数最常用：subplot是将多个图画到一个平面上的工具。其中，m表示是图排成m行，n表示图排成n列，也就是整个figure中有n个图是排成一行的，一共m行，如果第一个数字是2就是表示2行图。p是指你现在要把曲线画到figure中哪个图上，最后一个如果是1表示是从左到右第一个位置。 ），[参考博客](http://blog.csdn.net/steelbasalt/article/details/48918001)。

# matlab读取音频文件

## wavread读取音频文件

```sh
filedir = [];			% 设置路径
filename = 'bluesky3.wav';	% 设置文件名
fle = [filedir filename];
[x,fs] = wavread(fle);		% 读入数据文件
x = x(2000:4048,:);
% 采用上面的方法得到的
```


> 在matlab R2015a版本中运行wavread()函数，出现警告：Warning: WAVREAD will be removed in a future release. Use AUDIOREAD instead. 

## audioread读取音频文件


- **语法**

```
[y,Fs] = audioread(filename) % 从名为 filename 的文件中读取数据，并返回样本数据 y 以及该数据的采样率 Fs。

[y,Fs] = audioread(filename,samples) % 读取文件中所选范围的音频样本，其中 samples 是 [start,finish] 格式的向量。

[y,Fs] = audioread(___,dataType) % 返回数据范围内与 dataType（'native' 或 'double'）对应的采样数据，可以包含先前语法中的任何输入参数
```

示例：
```
[x,fs] = audioread('data\a001_0_10.wav');
[M,N] = size(x);
```

- **局限性**
	- 对于 Windows 7 或更高版本和 Linux 平台上的 MP3、MPEG-4 AAC 和 AVI 音频文件，**audioread 读取的样本数量可能比预期的少**。在 Windows 7 平台上，这是由于基础 Media Foundation 框架所致。在 Linux 平台上，这是由于基础 GStreamer 框架的局限性所致。如果需要精确的样本读取，请使用 WAV 或 FLAC 文件。
	- 在 Linux 平台上，**audioread 读取包含用作立体声数据的单通道数据的 MPEG-4 AAC 文件**。

> audioread在 R2012b 中推出。

参考：[matlab文档](https://ww2.mathworks.cn/help/matlab/index.html)（里面包括matlab比较系统的介绍）
