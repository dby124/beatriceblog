---
title: 《高性能PHP》学习笔记
date: 2018-2-5 11:32:53
tags: [PHP]
categories: [PHP]
---

本文主要是阅读PHP做的学习笔记，以便查阅。

#简介
《高性能PHP 7》从PHP所需环境讲起，涉及环境搭建与配置设置等内容，能够帮助有一定计算机基础的读者实现PHP运行环境的搭建及PHP周边软件的安装配置。《高性能PHP 7》亮点内容是介绍PHP 7特性的部分，同时也告诉读者哪些语法将会被废弃，对读者上手PHP 7有极大的帮助。除介绍PHP 7的新特性外，《高性能PHP 7》用大量章节介绍如何使用PHP 7及周边软件来构造高性能的Web应用程序，同时介绍了一些PHP开发的最佳实践，帮助读者更好地理解Web项目与PHP应用程序开发。附录部分为读者介绍了一些不错的工具，讲解了MVC与开发框架等周边知识，能够帮助读者更好地开发PHP项目。

# 环境搭建
- PHP出现的背景

	由于PHP语言自身存在性能问题的瓶颈，面对大规模应用场景存在性能低下问题，即使是小型应用程序在面对大流量场景时，也存在这个问题。

	在此期间出现了很多缓存技术方案，例如APC缓存等，但它们只是治标不治本。所以，PHP社区迫切需要一款能够显著提升PHP应用性能的新版本，此时，PHPNG项目随之而来。

	PHPNG全称PHP Next Generation，是PHP的全新分支，主要目标是提升应用性能。一些人认为PHPNG属于即时编译(Just In Time，JIT)，但实际上，它是基于Zend Engine的依次重构，以针对性能问题的进行专项优化。PHPNG是PHP 7项目的基础，通过PHP官网的wiki可以看到PHPNG项目已被并入PHP 7的开发主干。

- 搭建windows环境 

	在Windows系统中，已经有很多PHP集成环境工具，这些工具打包了Apache、PHP、MySQL等PHP常用的软件，是的这些集成安装和使用非常的简单。这些工具大部分采用Apache搭配PHP 7作为WebServer，这样的集成环境有**XAMPP、WAMPP、EasyPHP**。其中，只有EasyPHP同样也可以采用Nginx搭配PHP 7使用的方式作为WebServer，并支持WebServer在Nginx、Apache之间的轻松转换。

	> XAMPP软件还可以运行在Linux与MacOS上，而WAMPP与EasyPHP只能运行在Windows环境中，相比之下，更推荐支持Nginx的EasyPHP。关于WebServer软件，本文推荐使用Nginx。

	> Nginx的Windows版本可以从[http://nginx,org/en/download.html](http://nginx,org/en/download.html) 下载，虽然用其他主线版本没有什么问题，但是更推荐使用稳定版本。PHP的Windows版本可以从[http://windows.php.net/download/](http://windows.php.net/download/)下载，下载与系统匹配的32位或者64位的非线程安全版本。

	我们可以根据自己的实际情况选择集成环境，但是我们应该更深入掌握WebServer的每一个细节，所以我们独立安装Nginx（或者Apache）、PHP 7、MySQL，并设置好他们之间的连接，手动将它们配置到一起。

	此处以Nginx为例：
	- 根据上面提供的地址下载Nginx、PHP的二进制程序，复制Nginx到一个自定义的目录中,再将PHP复制到Nginx目录下，或是找一个固定的目录存放PHP。
	- 在PHP目录下，有两个`.ini`文件，分别是`php.ini-development`与`php.ini-production`，选择其中之一并改名为`php.ini`，之后PHP会以这个文件作为配置文件。
	- 按住`shift`键并用鼠标右击PHP目录，打开命令行窗口，输入以下命令：`php-cgi -b 127.0.0.1:9000`
		> 此处参数`-b`用于告知PHP我们希望启动FastCGI服务，执行上述命令会将绑定PHP到127.0.0.1这个IP地址的9000端口上，至此PHP便运行起来了。
	- 配置Nginx，打开`nginx_folder/conf/nginx.conf`文件，首先在配置文件的服务器配置信息中添加根目录和默认首页文件，具体如下：`server { root html; index index.php index.html index.htm;`
	- 现在配置Nginx，让它在启动时通过FastCGI模式与PHP通信。在`nginx.conf`中配置，去掉下面这块配置的注释即可启用：`location ~ \.php$ { fastcgi_pass 127.0.0.1:9000; fastcgi_param SCRIPT_FILENAME complete_path_webroot_folder$fastcgi_script_name;include fastcgi_params;`
		> 注意：`complete_path_webroot_folder$fastcgi_script_name`路径的默认值是nginx目录下的一个HTML的目录。另外fastcgi_param配置中的符号“\”需要用符号“/”加以替换。
	- 在Nginx目录下，通过以下命令重启Nginx：`nginx -s restart`，重启之后，打开浏览器，输入Windows服务器，输入Windows服务器、设备的IP地址或主机名，即可看到Nginx欢迎页面。
	- 到这一步，就只需验证PHP的安装情况以及它与Nginx的工作情况，在Webroot目录下创建一个info.php文件，并且编辑代码，在浏览器其中访问对应的服务器，验证是否能够正确解析这个文件。
	

# PHP 7新特性

　　PHP 7具有很多编写高性能、高效代码的新特性，同时也移除了一些历史版本中过时的特性，这些过时特性如果在PHP 7中使用会触发一个Error错误。目前大多数的Fatal错误都可以异常捕获，所以PHP不再显示一些不标准的Fatal错误，取而代之的是抛出一个携带着很多可用信息的异常。PHP 7提供了一些OOP新特性。

- 类型声明

	老版的PHP在函数和类之间传递参数时不必声明变量类型，同样返回数据时也不必声明变量类型，任何数据都可以被传递。这样会造成PHP不清楚传递的是什么类型的变量，函数和方法接收的变量数据类型也是未知的。因为PHP 7引入形参和返回值的类型声明。
	> 自己的理解：其实最开始PHP不需要类型声明就是为了简化编程，那么处理过程就会更复杂，这可能就是一个性能与编程简便的一个权衡。类型声明是针对大规模应用场景的一个升级，这就是一个语言成长的必经之路吧。
	
	- 形参声明：PHP 7支持的形参类型声明的类型有整型、浮点型、字符串型、布尔类型，可用于函数形参及对象的方法形参上。例如：`public function age(int $age) { // 函数体}`
	
	- 返回值声明：类似于形参声明，注意类型也可以是某个类的对象,例如：`public function age(int $age) : string { // 函数体}`
	
- 命名空间与use关键字批量声明
	当代码量规模变大时，很多类会放在命名空间下，这样方便维护和管理。然而当出现一个命名空间下有很多类且我们要一个性使用多个类的情况下，我们不得不直接将它们声明在代码的顶部。在命名空间下声明类如下所示：

	```sh
	$book = new Publichers\Packt\Book();
	$ebook = new Publichers\Packt\Ebook();
	$video = new Publichers\Packt\Video();
	$Presentation = new Publichers\Packt\Presentation();
	```
	
	**加载类的方式**有两种：
	- 通过`include`声明显示要加载的类。
	- 通过`_autoload`函数来加载所有的类文件。

	现在换一种方法，可以增加代码的可读性，PHP 7引入批量的**use声明**，其中包括三种声明模式：
	- 非混合模式的use声明
	 
		```sh
		use Publichers\Packt\{ Book, Ebook, Video, Presentation };
		use function Publichers\Packt\{ getBook ,savebook };
		use const Publichers\Packt\{ COUNT,KEY };
		```

	- 混合模式的use声明
		 
		```sh
		use Publichers\Packt\{ 
			Book, 
			Ebook, 
			Video, 
			Presentation, 
			function getBook;
			function saveBook;
			const COUNT,
			const KEY
		};
		```
	- 复合模式的use声明
		 
		```sh
		use Publichers\Packt\{ 
			Paper\Book, 
			Electronic\Ebook, 
			Media\Video, 
			Media\Presentation
		};
		```

	**匿名类**的声明与使用同时进行，它具备其他类所具备的所有功能，差别在于匿名类没有类名。匿名类的一次性小任务代码流程对性能提升帮助很大，你不必将整个类写完后再使用它。匿名类的语法如下：

	```sh
	new class(argument) { definition };
	//示例如下
	$name = new class() {
		public function _construct() {	// 此处为构造函数
			echo 'Altaf Huassain';
		}
	};
	```
	> 虽然匿名类是没有命名的，但是PHP内部，会在内存的引用地址表中为其分配一个全局唯一的名称。匿名类在继承方面与命名类相同，一样可以继承父类及父类的方法和接口，方式与继承命名类相同。


- 老式构造函数的摒弃

	从PHP 4开始，构造函数便可以通过命名保持与类名一致的方式来声明自己是构造函数，这种方式一种被沿用至PHP 5.6.大师在PHP 7中，这种构造函数的声明方式不推荐使用。

	老式构造方法：
	```sh
	class Packt {
		protected $number;
		public function packt() {	// 此处为构造函数
			echo 'Altaf Huassain';
		}
	};
	```

	新的构造方法：
	```sh
	class Packt {
		protected $number;
		public function _construct() {	// 此处为构造函数
			echo 'Altaf Huassain';
		}
	};
	```

	> 注意：老式的构造函数声明方式在PHP 7中被使用，只是会产生不推荐的信息，但是一般这类不推荐的方式在接下来的版本中会被移除，所以不建议使用老版构造方法。在PHP 7中同时出现老版构造函数和新方法，新方法的构造函数会被调用，而老版构造函数不会被调用。
	
- Throwable接口

	PHP 7提供一种全局的接口，使得所有的类都可以基于此使用`throw`关键字。在PHP 7之前异常可以被截获，但是错误不能被截获。从PHP 7之后，任何完整程序或者一部分中的Fatal错误可以被截获。为了更好地截获诸多的错误（大多数Fatal错误），**PHP 7提供了`throwable接口`，异常与错误都继承这个接口**。

	> 自定义的PHP类是不能继承throwable接口的，如果希望继承throwable接口，需要继承某个异常类。

- 太空飞船操作符（<=>）

	太空飞船操作符在**比较变量（包括字符串型、整型、浮点型、数组、对象等）**时比较有用，太空飞船操作符将（<、==、>）

	具体的使用规则如下：
	- 当符号两边相等时返回0
	- 当符号右边大于符号左边时返回-1
	- 当符号右边小于符号左边时返回1

- null合并运算符（??）

	在PHP 7中推荐使用合并运算符，在第一操作数存在时可被直接返回第二操作数，具体使用方法如下：
	```sh
	$post = $_POST['title'] ?? NULL;
	// 等效于老版的PHP的如下语句：
	$post = ($_POST['title']) ? $_POST['title'] : NULL;
	```

	合并运算符**可以连续使用**：
	```sh
	$post = $_POST['title'] ?? $_GET['title'] ？？ 'No POST or GET';
	```

- 统一变量语法

	通过花括号来规定变量解析的优先级，针对以下情况：
	```sh
	$first = ['name' => 'second'];
	$second = 'Howdy';
	echo $$first['name']; //PHP 5.X版本，这种方式与从左到右的变量解析的原则（$$first会优先被解析）产生不一致。这样的方法在PHP版本中会产生错误。
	// 统一变量语法：
	echo ${$first['name']};
	```

- 其他特性与变更

	PHP 7还更新了很多其他特性，例如常量数组、switch循环中的多个默认值、session_start中的选项数组等。

	- 常量数组：
		- PHP 5.6开始，常量数组可以使用`const`关键字来声明，方法如下：
	
			```sh
			const STORES = ['en', 'fr', 'ar' ];
			```
		
		- 在PHP 7中，常量数组可以通过`define`函数初始化，如：
		
			```sh
			define('STORES', ['en', 'fr', 'ar' ];
			```

	- switch中的多个default默认值:PHP 7开始，**switch循环语句中不允许出现多个default默认值**，否则会出现Fatal错误：`Fatal error: Switch statements may only contain one default clause in ...`
		
	- Session\_start函数中的选项数组
		在PHP 7之前，使用session时，必须先调用session_start()函数，这个函数并没有参数要传递，所有session相关的配置都在php,ini文件中。从PHP 7开始，可以在调用函数时传递参数选项数组，这些设置信息将覆盖php.ini中的session配置，且实参部分传递的选项数组将优先于php.ini中的session配置而被使用，代码示例如下：
		
		```sh
		session_start([
			'cookie_lifetime' => 3600,
			'read_and_close' => true
		]);
		```
	- Unserialize函数引入过滤器
		通常使用serialize和unserialize两个方法分别对对象进行序列化和反序列化。然而，unserialize()函数并不安全，因为没有任何过滤项，并且可以反序列化任何类型的对象。因此PHP 7引入过滤器，默认情况下允许反序列化所有类型的对象。使用方法如下：
		
		```sh
		$result = unserilize($object, ['allowed_classes' => ['Packt', 'Books', 'Ebooks']]);
		```
	

# PHP 7应用性能提升

　　为了提升性能，PHP 7已经完全基于PHPNG进行重写。不过依然有很多其他的方法可以用来进一步提升PHP 7的性能，譬如高性能的代码、采用最佳实践、WebServer调优、缓存等。

- Nginx与Apache
	
	目前有很多HHTP Server软件可供使用，目前最流行的是Nginx与Apache。

	- Apache
		Apache具有**足够灵活、广泛支持、能力强化、以模块方式集成大多数语言**（如PHP）的优点。因为Apache是在进程内部解析大多数脚本语言的，所以没有软件间通信的开销。Apache处理请求的模式有三种：prefork模式（线程创建进程）、worker模式（进程创建线程）、事件驱动模式（与worker模式相似，但这种模式会为连接保持创建专用线程，活动请求使用另外创建的线程），因此它能提供更高的灵活度。

		由于每个请求都会由一个进程或者线程处理，所有Apache在处理请求时开销很大。当它应用于高并发场景时，其性能低下的问题就凸显出来了。
	
	- Nginx

		Nginx提供的**异步、事件驱动、非阻塞请求处理**。Nginx不必等待每个请求完成，避免了锁住资源。Nginx创建许多工作进程，每个工作进程可以处理数千个链接，因此可以使用很少的进程来**承载高并发流量**。

		Nginx没有内置任何解释性语言，这样Nginx就可以专注处理请求的接收和响应，而具体的解析脚本语言的进程则在Nginx之外。

		通常认为Nginx快于Apache，但是在如静态资源（图片、css、js等文件）场景下，Apache有自己的优势。在构建高性能服务器时，Apache并不是问题所在PHP才是真正的瓶颈。

- HTTP服务器性能优化

	每款HTTP Server程序提供的功能，都可以实现优化请求处理和服务内容。优化HTTP Server程序的性能和可扩展性的技术包括：

	- 缓存静态文件

		将静态文件（如图片、css文件、js文件、字体文件等变更不频繁的文件）缓存在用户设备上。要实现这样的效果，Web Server程序需要添加特殊的响应头信息，以便让用户在浏览内容的同时将静态内容缓存到用户设备上。

	- HTTP持久链接（HTTP Keep-alive技术）
		
		HTTP持久链接表示一条TCP/IP链接上承载着过多个上下行请求。相比于传统的单链接模式（一次请求需要创建一条单独的BS链接的模式）而言，HTTP Keep-alive技术有大幅度性能提升。优点如下：

		- CPU和内存的负载会减轻。因为同一时刻的打开的TCP链接数变少了，后续请求和响应无需打开新链接，可以继续基于这些TCP链接发送上下行请求。
		- 当TCP链接建立后，请求的等待时间就会减少。
		- 网络阻塞情况减轻。因为在同一时刻，只会有少数的链接保持着。

		不足：许多服务器有并发数限制，当并发数上升到一定程度时，程序的性能将大大降低。为了解决这个问题，设置链接超时时间便非常必要。设置之后，**超过设定时间的链接将会自动关闭**。

	- GZIP压缩

	将网络中传输的内容进行压缩后再传递，可以有效减轻传输负担，从而提升HTTP请求的响应速度。Apache和Nginx都支持GZIP压缩，大部分的浏览器也都支持GZIP压缩数据的解析。

	- PHP独立部署服务

	Apache是以mod_php模块的方式加载PHP的，在这种方式，PHP与Apache耦合的很紧，所有的请求都会由Apache模块处理，这会非常消耗机器的硬件资源。我们可以通过让PHP-FPM与Apache结合，它们都独立部署，通过FastCGI协议互相传递数据。这样Apache只需关注HTTP请求链接即可，PHP进程则由PHP-FPM创建与维护。

	Nginx本身就是与PHP相互独立的。

	当PHP独立服务时，Web服务器不必对动态内容进行处理，仅仅将请求转发给另外一个服务即可，这大大减轻Web服务器的负载能力。

	- 关闭不用的模块
		
		Apache与逐个Nginx都有许多默认携带的模块，大多数情况下是用不到的，因此最好的方式是关闭不用的模块，方法：1）先将所有的模块禁用并重启服务器，然后逐个开启并检查应用程序是否运行正常；2）将所有模块默认全部开启。然后逐个关闭并检查应用程序是否运行正常，剔除不用的模块。通过这个方法可以做一个列表标识 启用或禁用的模块。

	- Web服务器资源

		每个Web服务器都会默认一些全局配置以供使用，然而这些设置可能并没有完美匹配服务器硬件情况，最大的硬件方面的问题是RAM内存，内存越多，服务器可处理的请求就越多。

- 内容分布网络（CDN）

	CDN网络通常服务于媒体文件，例如图片文件、css文件、js文件和音视频文件，这些文件会被缓存在各地的服务器上，这些服务器在地域上足够分散，当收到请求时，CDN网络会选择最适合用户的最近节点，将内容下发给用户。

	CDN网络的特性：
	- 由于内容是静态的，不频繁更改，因此CDN会将他们缓存在内存中。当某个文件的请求到达时，CDN直接从缓存中发送文件，这比从磁盘中加载文件并将其发送到浏览器更快。
	- CDN服务器位于不同的位置。
	- 每个浏览器都具有向域发送同时请求的限制。
	- 通常，存在对动态内容的少量请求和对静态内容的更多请求。
	
	CDN根据客户的位置决定了内容的地理可用性，缓存广泛，优化网络中的文件传输。

- CSS与JavaScript优化

	每个Web应用程序都会有CSS和JavaScript文件，现在大多数应用程序都包含很多css和js文件，用来增强应用的粘度和互动效果。css和js文件越大，浏览器需要发送的请求就越多，从而容易影响其性能。针对css和js文件的优化方法是：

	- 合并：将所有的css和js文件分别合并为一个文件。
	- 缩小：删除css和js文件中所有空行、注释和格外空格，这样能够减小文件迟勋，提高文件加载速度。
	> Minify是一组完全使用PHP编写的库，支持CSS与JavaScript文件的合并与压缩，代码完全面对对象和命名空间，可以将其嵌入开发框架中。[下载地址](https://github.com/matthiasmullie/minify)，注意minify依赖同一作者写的[路径转换库](https://github.com/matthiasmullie/path-converter)，

	Grunt:是一个JavaScript任务运行器，它能够将某些重复的任务自动化，避免反复工作。注意Grunt的安装需要Node.js和npm。

- 全页缓存技术

	网站的完整页面存储在缓存正，为下一次请求提供此缓存页面。如果网站的内容不经常更改，全页缓存的效果更好。

- Varnish：开源Web应用程序加速器，在Web服务器软件之前运行，但是它必须配置在端口80上，这样才能使每个请求到达。

- 基础设施：

	在应用程序流量一次性增加到上千万的用户，若在单个服务器上运行，性能受到很大的影响。针对程序的可扩展性和可用性，可在多个服务器上部署程序。构成部分：
	
	- 负载均衡（LB）：根据每个Web服务器上的负载情况，将外网流量以一定的规则分发给Web服务器。
	- Web服务器：可以根据需要尽可能多地部署Web服务器，它们可以很轻易地连接到LB。
	- 数据库服务器：用于安装数据库。

# 提升数据库性能

　　数据库在动态网站中扮演着一个关键的角色，所有流入流出数据杜辉与数据库进行交互。因此，如果PHP应用数据库没有进行较好的设计或优化，其性能将会受到非常大的影响。

- MySQL数据库

- 查询缓存（Query Caching）

- 存储引擎

	存储引擎（又称表类型）是MySQL的核心部分，负责处理表的操作。MySQL提供9个存储引擎，使用最广泛的是MyISAM和InnoDB。

	- MyISAM
		- 为速度而设计，和Select搭配起来使用更好
		- 如果表的数据偏向于**静态**，使用MyISAM最好。
		- MyISAM支持**表级锁**。
		- MyISAM支持全文搜索。
		- MyISAM数据压缩、自我复制、查询缓存、数据加密。
		- MyISAM不支持外键。
		- MyISAM不支持事务。
		- MyISAM支持集群数据库。
		
	- InnoDB(默认存储引擎)
		- InnoDB是为高可靠性和高性能而设计的，适合处理大量数据。
		- InnoDB支持行级锁。
		- InnoDB支持外键。
		- InnoDB支持事务。
		- InnoDB数据压缩、自我复制、查询缓存、数据加密。
		- InnoDB可以用在集群环境下，但是并没有完全支持，不过InnoDB表可以完全转换为NDB存储引擎，这样既可用于集群环境。

- Percona数据库和Percona XtraDB存储引擎

	- Percona是免费、开源的数据库，对于MySQL完全兼容且提供加强功能，可完全代替MySQL并能提供更好的文档、性能、扩展性。
	- Percona由MySQL衍生，支持MySQL的所有特性，并在此基础上提供更多、更好的性能。Percona使用一种改进的存储引擎——XtraDB，它是InnoDB的加强版，有更多的特性和更快的速度，在现代硬件上有有着更好的扩展性，Percona XtraDB在高负载环境下使用内存的效率更高。
	> Percona只能在Linux系统上使用，目前不能在Windows系统上使用。具体安装可参考[Percona安装手册](http://www.percona.com/doc/percona-server/5.5/installation.html)
	 
- MySQL性能监控数据：PHPMyAdmin的status展示MySQL服务器的相关信息和一些统计。

- Redis键值缓存存储

- Memcached键值缓存存储


# 调试和分析

调试过程中，需要知道一个脚本程序消耗了多少资源，包括内存消耗、CPU以及执行时间。Xdebug是一种PHP扩展，为PHP脚本提供调试和分析信息，能够得到错误的	全部追踪信息。同时Xdebug提供与IDE的交互式调试脚本的能力。

与Subline text 3，可安装Xdebug client插件


# PHP编程最佳实践

- Composer：PHP依赖管理工具。

- Git：代码托管和版本控制。

- Grunt watch：JavaScript任务运行器。

参考：

1. 源码[下载地址](http://www.broadview.com.cn/book/4912)