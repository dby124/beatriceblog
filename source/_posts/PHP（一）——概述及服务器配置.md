---
title: PHP（一）——概述及服务器配置
date: 2018-04-14 9:22:38
tags: [PHP]
categories: [PHP]
---

#  PHP概述
## PHP定义
PHP是Hypertext Preprocessor（超文本预处理器）的缩写，是服务器端的、跨平台的、HTML嵌入式的脚本语言，尤其适合Web开发。

## 优势
PHP是一种很有前景的Web语言，其具有以下优势：

- 开放源代码。
- 易于学习。
- 数据库连接。
- 面向对象编程。
- 可扩展性。

## 发展历史

- 1994年，PHP最初由Rasmus Lerdorf发起，之后以Personal Home Page Tools (PHP Tools)对外发布第一个版本，在早期版本，提供了访客留言、访客计数器等简答功能。
- 1995年，**PHP第二个版本**问世，定名为PHP/FI（From Interpret），是一个专为个人主页/表单提供解释程序的程序，它有着Perl样式变量，能自动解释表单变量，加入了对MySQL的支持，并可以嵌入到HTML中，已经包含了今天PHP的一些基本功能。
- 1998年6月，正式发布**PHP第三版本**，命名为PHP3，它类似于当今的PHP语法结构的第一个版本，它最强大的功能是其扩展性，能够给最终用户提供数据库、协议和API等基础结构。Zeev Suraski 和 Andi Gutmans 在 PHP 3 发布后开始改写PHP 的核心，这个在1999年发布的剖析器称为 Zend Engine，他们也在以色列的 Ramat Gan 成立了 Zend Technologies 来管理 PHP 的开发。
- 2000年5月，**PHP4.0正式版本**发布。PHP4.0除了更高的性能之后，还包含了其他一些功能，比如：支持更多的Web服务器，输出缓存，更安全的处理用户输出的方法等。
- 2004年7月，**PHP5**问世。**PHP5是里程碑式的版本，采用ZendII引擎**，完备对象模型，改进了语法设计，最终使得PHP成为设计完备、具有面向对象能力的脚本语言。
- 2015年6月，**PHP7**第一版的alpha版本正式公开发布。PHP7是PHP编程语言的一个主要版本，并号称是开发Web应用程序的一次革命，可开发和交付移动企业和云应用。此版本被认为是PHP在2004年发布PHP5后最重要的变化。到2017年已经到了PHP7.1的版本。其中PHP7使用新的 **Zend Engine 3.0** 提高应用程序性能两倍，比PHP5.6 低了 50％ 的内存消耗。 它可以服务于更多的并发用户，无需任何额外的硬件。考虑到当今的工作量 PHP7设计并重构。

## PHP、ASP和JSP对比
PHP、ASP和JSP是三种常用的网络开发语言，他们之间有着各自的特点。

ASP、PHP和JSP的共同点：
- 都能嵌入到HTML代码中；
- 都是面向Web服务器的技术，客户端浏览器不需要任何附加的软件支持；
- HTML代码主要负责信息的显示样式，而程序代码则是描述处理逻辑。

ASP、PHP和JSP的对比：

特性 | PHP | ASP | JSP
---|---|---|---
提出方 | Rasmus Lerdorf | 微软 | Sun公司
相似的语言 | perl | Visual Basic | Java
代码解析方式 | 语言引擎解释执行 | 语言引擎解释执行 | **被编译成Servlet并由Java虚拟机执行**
操作系统 | 均可 | Win32 | 均可
Web服务器 | 多种 | IIS | 多种
执行效率 | 快 | 快 | 极快
**稳定性** | 佳 | 中等 | 佳
**学习门槛** | 低 | 低 | 高
函数支持 | 多 | 较多 | 多
**系统安全** | 好 | 差 | 好
**升级速度** | 快 | 慢 | 中等
**网页结合** | 好 | 好 | 差
开发时间 | 短 | 短 | 较长

> 注意：JSP代码解析时，编译操作仅在JSP页面的第一次请求时发生。

## 常用开发工具

- PHP代码开发工具（具有调试功能）
    - Zend studio
    - PHP Edit
- 网页设计工具
    - Frontpage
    - Dreamweaver
- 文本编辑器
    - UItraEdit
    - EditPlus
    - sublime text
    - Windows自带记事本

# 服务器配置

## 准备
想要配置一台支持PHP语言的Web服务器，需要从硬件和软件两方面进行需求分析。

### 选择硬件
配置一台Web服务器，从硬件角度可分为两条路线：
- 专业级。从构建一台真正的Web服务器出发，按服务器标准卑职专业级硬件。也就是说需要支持24小时/365天的服器级CPU、主板和散热机箱，以及RAID的冗余备份系统，软件和硬件防火墙，标准空调托管环境等标配。
- 业余调试级。以目前主流的个人电脑配置就可以满足其硬件要求，即至少256MB内存，奔腾级CPU、10Mb/s网卡。

### 选择操作系统
要配置Web服务器，确定硬件需求后，还需要选取操作系统：
- Windows，需要硬件平台上装有Windows操作系统，这就意味着花费一笔钱想Microsoft购买该软件、
- Linux，开源免费的。
> 综合上述比较，一般选用Linux作为服务器的操作系统。

### 选择服务器软件
IIS是Windows XP 系统自带的服务器，但是该服务器只支持ASP语言，对于PHP是不能解析的，需要另外配置才可以。由于IIS不是支持PHP语言的主流，其对于PHP支持的高级功能是大打折扣的。

目前有很多HHTP Server软件可供使用，目前最流行的是Nginx与Apache。

- Apache具有**足够灵活、广泛支持、能力强化、以模块方式集成大多数语言**（如PHP）的优点。因为Apache是在进程内部解析大多数脚本语言的，所以没有软件间通信的开销。

    > 由于每个请求都会由一个进程或者线程处理，所有Apache在处理请求时开销很大。当它应用于**高并发场景**时，其性能低下的问题就凸显出来了。

- Nginx提供的**异步、事件驱动、非阻塞请求处理**。Nginx不必等待每个请求完成，避免了锁住资源。Nginx创建许多工作进程，每个工作进程可以处理数千个链接，因此可以使用很少的进程来**承载高并发流量**。
 
    > Nginx没有内置任何解释性语言，这样Nginx就可以专注处理请求的接收和响应，而具体的解析脚本语言的进程则在Nginx之外。

通常认为Nginx快于Apache，但是在如静态资源（图片、css、js等文件）场景下，Apache有自己的优势。在构建高性能服务器时，Apache并不是问题所在PHP才是真正的瓶颈。

在实际的项目开发中,Apache是使用最广泛的服务器软件。

## Apache安装
[官方下载地址：http://httpd.apache.org/download.cgi](http://httpd.apache.org/download.cgi)
。关于在不同平台上的安装可以百度获知。

Apache配置：网站能够解析静态网页之后，所有的程序必须放在Apache的htdocs文件夹下才能被解析，如果用户想自己建立网站根目录，则需要对Apache的http.conf文件进行如下配置，具体步骤为：
- 查找DocumentRoot，下面有这样一行`DocumentRoot"C:/Program Files/Apache Group/Apache2/htdocs"`，这就是网站默认的根目录，可以修改为希望设置的目录。
- 查找“This should changed whatever you set DocumentRoot to”，在其下面有这样一行：`<Directory "C/Program Files/Apache Group/Apache2/htdocs">`，将C/Program Files/Apache Group/Apache2/htdocs改成要设置的目录，如C:\Web。

## PHP安装
[官方下载地址：http://php.net/downloads.php](http://php.net/downloads.php)
。关于在不同平台上的安装可以百度获知。

PHP配置：对于不同的环境，需根据具体需要进行配置。

## MySQL安装
[官方下载地址：https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)

根据不同的需求下载MySQL对应的版本，之后按网上相关教程安装即可。

对于MySQL数据操作，除了命令行之后，还可以通过图形化操作软件进行操作，如phpMyAdmin、SQLyog和Navicat等。可参考：[五种常用的MySQL图形化管理工具介绍使用](https://www.2cto.com/database/201710/693934.html)

# PHP开源框架

-  **Laravel（较重要）**是一个现代化的PHP开发框架，代码优雅，使用 composer 方式扩展功能，社区活跃，缺点是比较重，比较适合做后台管理或者应用型WEB系统。

- **Zend Framework（较重要）**：是完全基于PHP5的开源PHP开发框架，可用于开发Web程序和服务。

-  **ThinkPHP（较重要）**国内使用比较多，优点是文档非常多，各种问题解决方案比较多，缺点是代码不够规范，理念落后。

- **Pop PHP**：一种强壮的框架，而且易于使用的WEB应用开发语言，在PHP框架中排名前十。它提供了一系列可管理的学习曲线，同时又提供了健壮和强大的功能。

- **Kohana**是一款基于MVC模式开发的纯PHP5的框架。 它的特点就是高安全性，轻量级代码，容易使用。

- **PHPixie**是一个性能卓越并且功能丰富的轻量级PHP框架。 PHPixie能使web开发更简单、更快速。

- **Medoo**是一款超轻量级的PHP、SQL数据库开发框架，可提升开发Web应用的效率与性能。

- **Flight**是一个快速、简单、可扩展的微型PHP框架。

- **Phalcon**是高性能PHP应用程序框架。它执行速度快，消耗资源少。根据官方测试，Phalcon是目前世界上速度最快的PHP框架。

- **Yii**是一个基于组件、用于开发大型 Web 应用的高性能PHP框架，是目前最有效率的PHP框架之一。通过一个简单的命令行工具它可以快速创建一个web应用程序的代码框架，开发者可以在生成的代码框架基础上添加业务逻辑，以快速完成应用程序的开发。

- **Webasyst**是一个PHP框架，用来创建时尚多用户的 web 应用和网站。

- **Symfony**是一个完整的框架结构，可用来帮助并加速网络应用的开发。

- **CodeIgniter**是一套给PHP网站开发者使用的应用程序开发框架和工具包。 它为组织提供了足够的自由支持，允许开发人员更迅速地工作。

-  **CI**非常轻量级，是一个简单的MVC框架，性能也很快。