---
title: MATLAB一些自定义函数
date: 2017-08-20 16:37:06
tags: [MATLAB]
categories: [科研]
---

# 分帧和分窗处理：

- 对信号x加窗分帧处理

	```sh
	wlen=50;      % 帧长                      
	inc=18;            % 帧移                      
	win=hanning(wlen);  % 窗函数                     
	fn=floor(((N-wlen)/inc))+1;   % 计算帧数                 
	time=(0:N-1)/Fs;                        
	frameTime=(((1:fn)-1)*inc+wlen/2)/Fs;    
	X=enframe(x,win,inc)';     %分窗处理
	```

# 时域特征：
-  求信号signal的时域特征
	- 时域函数	

	```sh
	function [ rms,peak,factor] = timechara(signal)
	% timechara:作用，求输入信号的自相关序列的相关参数函数
	% 输入：signal：一帧信号的序列
	% 输出：rms：每帧信号的有效值（对时间的均值）
	%       peak：每帧信号的峰值
	%       factor(1):峰值因子，反映波形的形状特征
	%       factor(2)：脉冲因子
	%       factor(3)：裕度因子
	%       factor(4)：波形因子
	%       factor(5)：K因子
	%       kurtosis:峭度
	    wlen = length(signal(:,1));     % 每帧信号的采样点个数
	    s_ave = mean(signal);      % 每帧信号的均值
	    rms = sqrt(sum((signal-s_ave).^2)/wlen); % 每帧信号的自相关序列的有效值（对时间的均值）
	    peak = (max(signal)-min(signal))/2;     % 每帧信号的峰值
	    factor(1) = peak/rms;         % crestfactor：每帧信号的峰值因子，反映波形的形状特征;
	    factor(2) =  peak/(sum(abs(signal))/wlen);      % impulsefactor:每帧信号的脉冲因子
	    factor(3) = peak/((sum(sqrt(abs(signal)))/wlen).^2);     % marginfactor:每帧信号的裕度因子
	    factor(4) = rms/(sum(abs(signal))/wlen);      % shapefactor:每帧信号的波形因子
	    factor(5) = peak*rms;     % Kfactor:每帧信号的K因子
	    factor(6) = kurtosis(signal); % kurtosis:峭度
	end
	```

	- 例子

	```sh
	x=0:0.1:2*pi;
	y=sin(x); %信号
	ma = max(y); %最大值
	mi = min(y); %最小值
	me = mean(y); %平均值
	pk = ma-mi; %峰-峰值
	av = mean(abs(y)) %绝对值的平均值(整流平均值)
	va = var(y); %方差
	st = std(y); %标准差
	ku = kurtosis(y); %峭度
	rm = rms(y); %均方根
	S = rm/av %波形因子
	C = pk/rm; %峰值因子
	Kr = sum(y.^4)/sqrt(sum(y.^2)) %峭度因子
	I = pk/av %脉冲因子
	xr = mean(sqrt(abs(y)))^2;
	L = pk/xr; %裕度因子
	```

- 求信号的短时平均能量的函数。

		```sh
		function [ E ] = energy( frame )
		% energy:对于一帧信号，求出短时能量
		% 输入参数：frame:一帧信号
		% 输出参数：E:信号的短时能量
		    u=frame;
		    u2=u.*u;    
		    E=sum(u2);  %幅度的平方和
		end
	```

- 短时平均过零率 

		```sh
		function [ zcr ] = zcr( frame )
		% zcr:求一帧信号的短时平均过零数
		% 输入参数：
		   zcr = 0;
		   wlen = length(frame);
		   frame = frame-mean(frame);             % 消除直流分量,但是此处处理后与在整个信号出消除直流分量求得的zrc有差距
		   for i=1: (wlen-1)     % 在一帧内寻找过零点
		       %if条件处可加门限
		       if frame(i)* frame(i+1)< 0     % 判断是否为过零点
		           zcr = zcr+1;         % 是过零点，记录1次
		       end
		   end
		end
		```

- 求输入信号的自相关序列的相关参数函数

		```sh
		function [ rms,peak,crestfactor] = fcorr(signal)
		% fcorr:作用，求输入信号的自相关序列的相关参数函数
		% 输入：signal一个信号的序列
		% 输出：rms：每个信号的自相关序列的有效值（对时间的均值）
		%       peak：自相关函数序列的峰值
		%       crestfactor：自相关函数序列的峰值因子，反映波形的形状特征
		    wlen = length(signal(:,1));
		    ss = xcorr(signal);
		    ss = ss(1:wlen,:);      % 因为相关函数是对称的，取前一半
		    ss_ave = mean(ss);      % 自相关函数序列的均值
		%     rms = sqrt(sum((signal-ss_ave).^2)/wlen); % 这是rms的错误求法，但是测试的训练效果较好
		    rms = sqrt(sum((ss-ss_ave).^2)/wlen); % 每个信号的自相关序列的有效值（对时间的均值）
		    peak = (max(ss)-min(ss))/2;     % 峰值
		    crestfactor = peak/rms;         % 峰值因子，反映波形的形状特征;
		end
		```

# 时频域特征
- 求信号的小波的特性

	```sh
	function [ energy,sqr ] = wave( frame )
	% 输入参数：frame:一帧信号
	% 输出参数：
	%---------energy:小波系数的能量
	%---------sqr:小波系数的均方差
		T = wpdec(frame,2,'db2');       %db8小波做两层分解
	    %重构第二层的所有节点
	    for i = 1:4
	        y = wprcoef(T,[2,i-1]);
	        energy(:,i) = sum(y.*y);     %重构信号的能量
	        sqr(:,i) = var(y);          %重构信号的均方差
	    end
	end
	```

- 小波变换的改进函数

	```sh
	function [ energy,sqr] = wave( frame,N )
	% wave:求信号的小波的特性
	% 输入参数：frame:一帧信号
	% 输出参数：energy:小波系数的能量；sqr:小波系数的均方差;coef:波峰系数
	    T = wpdec(frame,N,'db8');       %db8小波做N层分解
	    %重构第N层的指定节点
	    for i = 1:(2^N)
	        y = wprcoef(T,[N,i-1]);
	        % y = wprcoef(T,[N,mum(i)]);
	        energy(:,i) = sum(y.*y);     %重构信号的能量
	        sqr(:,i) = var(y);          %重构信号的均方差
	        % coef(:,i) = max(y)/mean(y);  %重构信号的波峰系数，效果不好
	    end
	end
	```

- HHT变换的相关特征求取函数

	```sh
	% 对一个信号做HHT变换，求得相关的特征
	function [peaks,bjpvar,aveA,mseA,rmsA,msize,ratio1,msee] = infreqfeature(x,fs)
	% 输入：x 是一个信号，是一个列向量
	% 输出： 
	%        peaks 边际谱的前4个峰值
	%        bjpvar 边际谱的方差
	%        aveA  各分量瞬时幅度的均值
	%        rmsA  各分量瞬时幅度的有效值
	%        mseeA 各分量瞬时幅度的方差和
	%        msize(1)、msize(2)、msize(3)、msize(4):瞬时频率第三个分量的极大值、极小值点数和第四个分量的极大值、极小值点的个数
	%        ratio 各分量瞬时频率的方差贡献率
	%        
	%        
	% 调用的函数：
	% hht工具箱函数：      emd()、hhspectrum()、toimage()
	% 自定义函数：         findmainfreq()
	    N = length(x);
	    imp=emd(x);                  % 对信号进行EMD分解
	    [m,n]=size(imp);             % 求取EMD分解成几个分量
	    % 对IMF分量求取瞬时频率与振幅：A：是每个IMF的振幅向量,f:每个IMF对应的归一化瞬时频率，t：时间序列号
	    [A,f,t] = hhspectrum(imp);
	    % 求瞬时频率
	    infreq = fs * f;
	    %求信号的边界谱bjp，E：对应的振幅值
	    [E,tt1]=toimage(A,f,t,length(t));
	    enery = E;
	    E=flipud(E);
	    for k=1:size(E,1)
	        bjp(k)=sum(E(k,:))*1/fs;
	    end
	    % 求边界谱的峰值和对应的采样点的位置
	    % peaks表示前5个峰值，如果需要求这些峰值对应的位置，直接在peaks参数后面加一个参数即可，详见该函数的定义
	    [peaks] = findmainfreq(bjp);   
	    bjpvar =  var(bjp);      %边际谱的方差
	    % 瞬时频率第三个和第四个分量的极值点的个数
	    [indmin3, indmax3, indzer] = extr(infreq(3,:),1/fs);
	    msize(1) = length(indmin3);       % 第三个分量极大值点数
	    msize(2) = length(indmax3);       % 第三个分量极小值点数
	    [indmin4, indmax4, indzer] = extr(infreq(4,:),1/fs);
	    msize(3) = length(indmin4);       % 第四个分量极大值点数
	    msize(4) = length(indmax4);       % 第四个分量极大值点数
		% 非有效特征，瞬时频率的均值、方差和有效值的特征对于好坏瓶子的区分性很差，所以不需要使用
	    % 所有分量瞬时频率的均值、方差和有效值
	    for k = 1:m-1
		% ave(k) = mean(infreq(k,:));     % 瞬时频率的均值
	        mse(k) = var(infreq(k,:));      % 各分量的瞬时频率的方差
		% rms(k) = sqrt(sum((infreq(k,:) - ave(k)).^2)/N);% 各分量的瞬时频率的有效值
	    end
	    % 所有分量瞬时幅度的均值、方差和有效值
	    for k = 1:m-1
	        aveA(k) = mean(A(k,:));     % 瞬时幅度的均值
	        mseA(k) = var(A(k,:));      % 各分量的瞬时幅度的方差
	        rmsA(k) = sqrt(sum((A(k,:) - aveA(k)).^2)/N); % 各分量的瞬时幅度的有效值
	    end
	    % 所有分量的方差和
	    msee = sum(mse);
	    % 各分量的方差贡献率,此处只求前6个分量的方差贡献率
	    for j = 1:6
	        ratio(j) = (100*mse(j))/msee;       %获取瞬时频率的方差贡献率
	    end
	    ratio1 = (100*mse(1))/msee;     % 第一个分量的瞬时频率方差贡献率
	end
	```

# 频域特征
- 求出信号的频域特征参数

	```sh
	function [ area,peaks,loc ] = spectrum( frame )
	% spectrum:对一帧信号取频谱特征
	% 输入参数：frame:一帧信号，长度为256
	% 输出参数：area:频谱面积；peaks：频谱曲线峰值系数(最大5个)：col:最大5个峰值系数对应的位置
	    N = length(frame);
	   [f amp] = FFT(frame,N);
	   temp = amp(1:N/2)';
	   area = polyarea(1:N/2,temp);       %求频谱面积
	   [K,V] = findpeaks(temp);         %找出频谱峰值
	   peaks = sort(K,'descend');       %降序排列峰值
	   peaks = peaks(1:5);      %取出前5大的峰值
	   for i = 1:5
	       mark = find(K == peaks(i));
	       loc(i) = V(mark);
	   end
	end
	```

- 直接求频域特征

	```sh
	function [ FC,MSF,VF,RMSF,RVF ] = frequency( frame )
	% frequency:求出一帧信号的频域特征参数
	% 输入参数：frame:一帧信号
	% 输出参数： FC:重心频率
	%           MSF:均方频率
	%           VF:频率方差
	%           RMSF:均方根频率
	%           RVF:频率标准差
	    delta = 1/48000;     % 采样间隔
	    N = length(frame(:,1)); % 每帧信号的点数
	    for i=2:N
	        s(i,:) = (frame(i,:)-frame(i-1,:))/delta;
	    end
	    FC = sum(s(2:N,:).*frame(2:N,:))/(2*pi*sum(frame(:,:).^2));
	    MSF = sum(s(2:N,:).^2)/(4*(pi^2)*sum(frame(:,:).^2));
	    VF = MSF-(FC^2);
	    RMSF = sqrt(MSF);
	    RVF = sqrt(VF);
	end
	```