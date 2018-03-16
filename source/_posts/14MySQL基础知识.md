---
title: 数据库设计笔记——MySQL基础知识（四）
date: 2017-08-11 10:35:12
tags: [数据库]
categories: [数据库]
---
## 概述

-  关系型数据库——由表来存储相关的数据，MySQL，SQL Server，Oracle等都是关系型数据库；
-  **元数据**：用于集成并管理数据。
-  MySQL数据库
	- 用户数据库：用户根据需求创建数据库
	- 系统数据库
		- **information_schema**：主要存储系统中的一些数据库对象信息，如用户信息、列信息、权限信息、字符集信息和分区信息等
		- **performance_schema**：存储数据可以服务器性能参数
		- **mysql**：系统的用户权限信息
 		- **test** 

-  数据对象
	- 表
		- 栏位：该表的列（也叫属性或者字段）的名字和数据类型
		- 索引：根据指定的数据库表列建立起来的顺序，提供快速访问的路径且可以监督表的数据。
	- 视图
	- 存储过程（手动调用，无返回值）
	- 函数（手动调用，必须有返回值）
	- 触发器（自动调用）
	- 事件
-  数据库的命名规则
	- 由字母、数字、下划线、@、#、$符号组成，首字母不能是数字和$符号，标识符不允许是MySQL的保留字，不允许有空格和特殊字符，长度小于128位。

## 存储引擎
-  存储引擎决定数据库对象表的类型，即如何如何存储、索引数据、是否支持事务，也决定了表在计算机中的存储方式。
-  数据类型，决定数据库对象表可以存储数据的类型。
-  存储引擎类别(9)
	- InnoDB（默认）
	> 支持事务，支持具有提交、回滚、崩溃恢复能力的事务安装，比MyISAM占磁盘空间大。
	- MyISAM
	> 不支持事务，也不支持外检，访问速度较快
	- MEMORY
	> 使用内存来存储数据，访问速度快，但安全上无保障
	- FEDERATED
	- MRG_MYISAM：
	- BLACKHOLE
	- CSV
	- ARCHIVE
	- PEFORMANCE_SCHEMA
-  相关命令
	- 查看DBNS版本

		```sh
		SELECT version();	
		```

	- 查询默认存储引擎
	
		```sh
		SHOW　VARIABLE LIKE 'storage _engine%';　
		```
-  修改默认存储引擎通过修改`my.ini`文件中的`"default_storage-engine"`参数

## 数据类型(每个字节8为)

-  数值
	- 整型
		- TINYINT（1字节）
		- SMALLINT（2字节）
		- MEDIUMINT（3字节）
		- INT/INTEGER（4字节）
		- BIGINT（8字节）
	- 浮点型
		- FLOAT（4字节）
		- DOUBLE（8字节）
	- 定点型：DEC/DECIMAL(M/D)
	> 其中M是小数点左右数字之和，D是右边数字个数，M<=38


-  字符串型
	- CHAR（0~255字节）     ：固定字长
	- VARCHAR（0~65535字节）：可变长度

-  日期时间类型
	- YEAR：年份，1字节
	- TIME：时分秒，3个字节
	- TIMESTAMP：经常插入或者更新日期为当前系统时间，4字节
	- DATATIME：年月日时分秒，8字节
	- DATE：年月日，4字节

## 查看表结构
-  表的操作
	```sh
	DESCRIBE table_name  //查看定义
	SHOW CREATE TABLE table_name;  //查看表详细定义
	```

-  操作表的约束
	- 通过表的约束来完成对表中所存储的数据是否合法、是否具有完整性，进行检查。

- 约束
	- 单列约束：每个约束只约束一列数据。
	- 多列约束：每个约束约束多列数据。

-  完整性约束
	- NOT NULL ：非空
	- DEFLAULT ：设置字段的默认值
	- UNIQUE KEY：设置字段的值是唯一
	- PRIMARY KEY（PK）：约束字段为表的主键，只作为表的唯一标识
	- AUTO_INCREMENT ：约束字段自动增加
	- FOREIGN KEY（FK）：约束字段为表的外键

-  注意
	- **多字段主键**：主键有多个字段组合而成。
	- **字段自动增加**（AUTO_INCREMENT）：MySQL唯一扩展的完整性约束，当为数据库表中插入新记录时，字段上的值会自动生成唯一的ID。一个数据表中只能有一个字段使用该约束，且字段数据类型一定是整型，由于其唯一的ID，该字段经常会设置为PK主键。
	- 设**置外键约束（FK）**：外键约束保证多个表（通常两个）之间参照完整性，即构建两个字段之间的参照关系。设置外键的两个表之间具有父子关系，即子表中某个字段的取值范围由父表所决定。如全年级的成绩表，以学号作为总表的主键，而班级作为另一个表的主键，则班级为总表的外键，也就是总表的子表。

## 索引的操作

-  **表**：包含数据库的所有数据的数据库对象，数据库对象表是存储和操作数据的**逻辑结构**。
-  **索引**：一种有效组合数据的方式。通过索引对象，可以快速查询到数据库对象表中的特殊记录，是一种提高性能的常用方式，一个索引会包含表中安排一定顺序排序的一列或多列字段。
-  根据索引的存储类型，可分为：
	- B型树索引（BTREE）<- InnoDB支持
	- 哈希索引（HASH）<- MEMORY支持
-  索引作用
	- 提高查找数据的速度
	- 实现数据库对象表的完整性

-  MySQL支持6种索引
	- **普通索引**： 在创建索引时，不附加任何限制条件（如唯一、非空等），可创立在任何数据类型的字段上。
	- **唯一索引**：在创建索引时，限制索引的值必须是唯一的，可更快查询某记录。
	- **全文索引**
		- 主要是关联在数据类型为CHAR、VARCHAR、TEXT的字段上，以便能够更加快速地查询数据量较大的字符串类型字段。
		- 全文索引只能在存储引擎为**MYISAM**的数据表上创建。
	- **单列索引**
	- **多列索引**
	- **空间索引**

-  适用于创建索引的情况
	- 经常查询的字段，即在where子句中出现的字段。
	- 在分组的字段，即group By子句中出现的字段。
	- 存在依赖关系的子表和父表之间的联合查询，即关键/外键字段。
	- 设置唯一完整性约束的字段。
	
-  不适用于创建索引的情况
	- 在查询中很少被使用的字段
	- 拥有许多重复值的字段

> 注意，过多的创建索引则会占据许多磁盘空间。

## 视图的操作

- **视图**：通过视图的操作不仅可以**实现查询的简化**，而且**可以提高安全性**。所谓的视图，本质上是一种**虚拟表**，其内容与真实表相似包含一系列带有名称的列和行数据，但是视图并不在数据库中以存储的数据值形式存在。行和列数据来自定义视图的查询所引用基本表，并且在**具体引用视图时动态生成**。
-  **视图的特点**
	- 视图的列可以来自不同的表，是表的抽象和在逻辑意义上建立的新关系。
	- 视图时由基本表（实表）产生的虚表。
	- 视图的建立和删除不影响基本表。
	- 对视图内容的更新（添加、删除和修改）直接影响基本表。（相当于对基本表操作的工具）
	- 当视图来自多个基本表时，不允许添加和删除数据。

-  创建视图语法		
	```sh
	CREATE VIWE 'database_name'.'VIWE_name' AS (SELECT * FROM ...);
	```
	> database_name为当前的数据库名称，VIWE_name为新建视图名称，“*”处填写要查询的列，用“,”隔开，“...”处写表名称。

-  由于视图的功能实际上是**封装查询语句**，分为以下几类
	- (1）封装实现**查询常量**语句的视图，即所谓的常量视图
		
		```sh
		CREATE VIWE view_test1(新建视图的名称) AS SELECT 3.1415926;
		```
		> 可将上面的语句输入到MySQL5.7 command line client 中。

	- (2)封装使用**聚合函数**(SUM、MIN、MAX、COUNT等)，查询语句的视图。

		```sh
		CREATE VIWE view_test2 AS SELECT COUNT(col_name) FROM table_name;
		```

	- (3)封装了实现**排列功能**（ORDER BY）查询语句的视图

		```sh
		CREATE VIWE view_test3 AS SELECT col_name FROM table_name ORDER BY id DESC;   //排列方式
		```

	- (4)封装了实现**表内连接**查询语句的视图

		```sh
		CREATE VIWE view_test4 AS SELECT col_name FROM table_name as s.t_group as g WHERE s.group_id=g.id AND g.id=2;
		```

	- (5)封装了实现**表外连接**（LEFT JOIN和RIGHT JION）查询语句的视图

		```sh
		CREATE VIWE view_test5 AS SELECT col_name FROM table_name as s LEFT/RIGHT/ALL JION t_group as ON s.group=g.id WHERE g.id=2;
		```

	- (6)封装实现**子查询相关**查询语句的视图

		```sh
		CREATE VIWE view_test6 AS SELECT col_name FROM table_name AS s WHERE s.group_id=g,id IN (SELECT id FROM t_group);
		```

	- (7)封装了实现**记录联合**（UNION和UNION ALL）查询语句的视图

		```sh
		CREATE VIWE view_test7 AS SELECT id,name FROM table_name UNION ALL SELECT id,name FROM　t_group;
		```

-  查看视图

	```sh
	USE database_name;
	SHOW TABLES;
	```

-  查看视图详细信息

	```sh
	SHOW TABLE STATUS [FROM db_name]|[LIKE 'pattern']
	SHOW　TABLE STATUS FROM db_name \G
	```

-  查看视图定义信息

	```sh
	SHOW CREATE VIWE view_name;
	```

-  查看视图设计信息

	```sh
	DESCRIBE/DESC view_name;
	```

-  通过系统表查看视图信息

	```sh
	USE infoemation_schema;
	SELECT * FROM views WHERE table_name='view_selectproduct' \G
	```

	> SQLyog软件中直接“工具”—>“信息”

## 触发器的操作
-  触发器用来实现由一些**表事件触发的某种操作**，是与数据库对象表关联最紧密的数据库对象之一。在DBS中，当执事件间时，则会激活触发器，从而执行其包含的操作。触发器类似于编程语言中的函数。总的来说就**是表发生更改时，自动进行一些处理**。

-  MySQL的触发器语句（事件引导）：
	- DELETE语句：删除
	- INSERT语句：添加
	- UPDATE语句：修改
	> 其他SQL语句不会激发触发器，会经常使用触发器数据库对象，是由于该对象能够加强数据库表中数据的完整性约束和业务规则等。

-  创建触发器

	```sh
	DELIMITER $  //固有格式，当有多个触发器语句时使用
	CREATE TRIGGER t_afterinsert_on_doorlog AFTER INSERT 
	ON t_doorlog FOR EACH ROW 
	BEGIN
	  DECLARE jobno VARCHAR(30);
	  DECLARE time1 VARCHAR(20);
	  SET jobno = (SELECT job_num FROM tb_park_user_base WHERE card_num = new.cardNo);
	  SET time1 = DATE_FORMAT(new.operateTime,'%Y%m%d%H%i%s');
	  INSERT INTO t_attendance (job_no, operateTime) 
	  VALUES
	    (jobno,time1);
	END$
	```
![创建触发器](/images/创建触发器.jpg)


>- DELIMITER $   意思是告诉mysql下文执行语句以“$”结束，如果没有这句，下面语句会出错，mysql无法判断哪句结束。

> - CREATE TRIGGER t_afterinsert_on_doorlog AFTER INSERT ON t_doorlog FOR EACH ROW   创建触发器的语法 其中**AFTER**同级关键字有**BEFORE**，而** INSERT**同级关键字还有**DELETE**，**UPDATE**

> - `BEGIN`和`END`之间的是触发器执行语句。DECLARE jobno VARCHAR(30)   意思是声明触发器SQL中的局部变量，SET jobno=....　　意思是给局部变量赋值，=右边可为查询语句或函数方法

## 单表数据记录查询

-  四则运算：+(加)、-(减)、*(乘)、/(DIV除)、%(取余)。->字段间运算

	```sh
	SELECT ename(字段) sal*12 FROM table_name;  //查询年薪
	```

-  对运算结果/某字段修改字段名，使显示更直观

	```sh
	SELECT ename(字段),sal*12 yearsalary FROM table_name;
	SELECT ename(字段),sal*12 AS yearsalary FROM table_name;
	```

-  设置数据显示格式，以便用户浏览所查询到的数据. 在MySQL软件中提供函数CONCAT()来连接字符串，从而实现设置显示数据的格式，语句如下：

	```sh
	SELECT CONCAT|ename ,'雇员的年薪为：', sal*12) yearsalary FROM table_name;
	```


> 结果：SMITH雇员的年薪为：9600.00

## 多表数据记录查询

-  多表数据记录查询
	- 内连接查询：笛卡尔积内连接表后查询

	- 外连接查询：笛卡尔积外连接表后查询

	- 子查询

	> 进行连接查询中，首先要对两张或两张以上表进行连接操作。多表数据记录查询是关系数据操作中专门用于数据库操作的关系运算。有**并、笛卡尔积、连接（UNION）**。
	
-  查询语法

![多表数据记录查询](/images/多表数据记录查询1.jpg)

![多表数据记录查询](/images/多表数据记录查询2.jpg)

![多表数据记录查询](/images/多表数据记录查询3.jpg)

![多表数据记录查询](/images/多表数据记录查询4.jpg)


## 实现特殊功能比较运算符

-  正则表达式匹配

	- `^`：匹配字符串**开始**部分，如`REGEXP '^c'`

	- `$`:匹配字符串**结束**部分，如`REGEXP 'g$'`

	- `.`：匹配字符集合**中**任意一个字符，如`REGEXP '^c···g$'`

	- `[字符集合]`：匹配字符集合**中**任意一个字符，如`REGEXP '[abc]'`

	- `[^字符集合]`：匹配字符集合**外**任意一个字符，如`REGEXP '^[abc]'`

	- `str1|str2|str3`：匹配str1、str2、str3中任意一个字符

	- `*`：匹配字符，包括0个或1个，如`REGEXP 'a*g'`

	- `+`：匹配字符，包括1个，如`REGEXP 'a+g'`

	- `字符串{N}`：字符串出现N次

	- `字符串{M,N}`：字符串出现至少M次，至多N次

-  位运算符

	- &：按位与

	- |：按位或

	- ~：按位取反

	- ^：按位异或

	- <<：按位左移

	- \>>：按位右移
	
	- 例子：

	```sh 
	SELECT 5&6, BIN (5&6) 二进制；
	SELECT 4&5&6, BIN (4&5&6) 二进制；
	```
	
	结果：

![按位运算符计算结果](/images/按位运算符计算结果.png)

## MySQL常用函数

![MySQL常用函数1](/images/MySQL常用函数1.jpg)

![MySQL常用函数2](/images/MySQL常用函数2.jpg)

![MySQL常用函数3](/images/MySQL常用函数3.jpg)

![MySQL常用函数4](/images/MySQL常用函数4.jpg)

![MySQL常用函数5](/images/MySQL常用函数5.jpg)

## 存储过程和函数的操作


## MySQL事务


## MySQL安全性机制




