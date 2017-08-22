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
-  求信号x的时域特征
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
- 求出一帧信号的小波的特性

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