---
title: AD制图相关问题总结
date: 2017-08-09 10:09:00
tags:
categories: [知识扩展]
---
## AD制图总体步骤
### 新建一个工程文件
	File ->New->Project->PCB.Project->save

### 新建原理图
在此工程目录下新建原理图与PCB并保存

	  File ->New->Schematic->save

### 生成PCB（前提是原理图已完成且没有错误）
-  `File->New-> PCB->save`，建成PCB文件 显示PCB界面

-  `Design->(第一项)Import changes From xxPrjPcb->Execute change ->Validate charges`

-  `Design->(第一项)Update Schematics in xxPrjPcb->Execute change ->Validate charges`

-  将封装摆好位置，对PCB中的元器件封装布线

	> **自动布线**：`Auto Route->all` ,注意尽量将全部的线放在bottom layer层，自动布线后可以手动微调。

	> **取消布线**：`tool->un-route`。

-  覆铜（覆铜效果不好的话，如铜面部平滑，就不要覆铜因为这样反而效果不好，覆铜主要用于信号处理电路屏蔽干扰），`place polygo plane` 属性社会之为`GND` ，可设置其安全距离及铜的密度。

-  覆铜设置安全距离：`Design->Rules->clearance`; 覆铜时，使铜接地，注意铜被分离，形成死铜的情况。

### 生成钻孔文件
-  点击PCB文件，是界面显示PCB图，另存为2.8版本，该版本文件即为钻孔文件

### 生成打印文件
-  同样也是显示在PCB界面，选择
`File->Fabrication Output->gerber file`

-  设置

	
		 General -> ( `milimeters` （4:4）; `inches` （2:5）)
		
		 layers -> ( `plot layers` (use on) ；`mirror layers` (all off) )
		
		 Drill Drawing -> ( `Graphic symbols` ) 
		
		 Apertures -> ( `Embeded apertures`(RS274x) )
		
		 Advanced ->
			`Suppress leading zeroes` (中间)；
			`Refrence to relative origin` (中间)；
			`Separate file per layer/Unsorted`(raster)；
			`optimize change location commands`；
			`Generate DRC Rules export file` (RUL).

注意：其中括号表示选中的值，没有值的选项表示打钩

-  点击OK

## 注意的问题

-  top layer层放元器件

-  Bottom layer层方引脚、焊盘、贴片

-  keep out layer层是最外边的框

-  最后，记得给PCB设置原点，用以定位：`Edit( 编辑)->Origin->set`

-  焊盘直径可尽量设置的大些，方便焊接，线宽也要设置的宽，线不能是直角，否则形成天线，干扰特别大。

-  PCB界面，点击小写字母q，可将单位mil与mm相互转换。

-  PCB检查，DRC ：tool->Design Rule check。

-  加泪滴 ：tool->teardrop ，删除泪滴选remove。

-  画元件时，注意管脚左右两端的标号保持一致，否则封装会出问题，引脚的电气属性也会错乱。

-  封装RAD-0.1表示间距2.58mm, RAD-0.2表示间距5.16mm,RAD-0.3表示间距7.62mm。
