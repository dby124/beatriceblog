---
title: PHP的表单获取与HHTP请求方式
date: 2017-08-08 11:58:38
tags: [PHP]
categories: [PHP]
---
## PHP获取表单数据的方法

PHP获取表单数据的方法主要有GET、POST和REQUEST三种方式。

> 注意，提交表单的方法只有POST和GET两种。POST和GET都可以向Web服务器传输数据。

GET与POST对比

对比 | GET | POST
---|---|---
附加形式 | URL之后 | HTML HEADER
参数处理方式 | URL编码 | 无URL编码
传输数据的大小 | 小于2kB | 理论上无限制
安全性 | 用户可见，不安全 | 用户不可见，安全
适用场景 | 数据查询 | 数据添加、修改和删除

### GET

- **附加形式**：GET方法的数据会附在URL之后（即把数据放置在HTTP协议头中），以“？”分割URL和传递数据，参数之间以“&”连接。使用GET方式时，URL较长，数据可见。

- **参数处理方式 **：GET方法传输参数，如果数据是英文字母/数字，则原样，**若数据中包含空格或者汉字，则需要进行编码处理**。相关的例子可以看这里提到的问题：[URL 编码](http://twei.site/2018/03/25/URL%E7%BC%96%E7%A0%81/)

- **传输数据的大小**：GET方法传输的数据量较小，不能大于2kB。

> 提交表单时，method的默认值是"GET".

### POST

- **附加形式**：通过HTTP POST机制，将表单内各个字段与其内容放置在HTML HEADER内一些传送到ACTION属性所指的URL地址，**用户看不见过程，也不能直观地看到传输过程**。

- **参数处理方式 **：POST把提交的数据防止在HTTP包中的包体中，不会对汉字进行URL编码处理。POST 提交的数据必须放在消息主体（entity-body）中，但协议并没有规定数据必须使用什么编码方式。但是，数据发送出去，还要服务端解析。POST 提交数据方案，包含了 Content-Type 和消息主体编码方式两部分。

- **传输数据的大小**：POST方法的传输数据量较大，理论上没有大小限制。

- **[Http POST 提交数据的四种方式](https://www.jianshu.com/p/e47abb91465d?utm_campaign=haruki&utm_content=note&utm_medium=reader_share&utm_source=weixin)**
	- application/x-www-form-urlencoded （默认常用的）
	- multipart/form-data
	- application/json
	- text/xml，现在几乎不用
> HTTP 协议规定的 HTTP 请求方法有 OPTIONS、GET、HEAD、POST、PUT、DELETE、TRACE、CONNECT 这几种。其中 POST 一般用来向服务端提交数据

### REQUEST

`$_REQUEST`变量包含`$_GET`, `$_POST`和`$_COOKIE`的内容。

`$_REQUEST`变量可以用来获取通过“GET”和“POST”这两种方法发送的表单数据。


## PHP发送HHTP请求的几种方式

### cURL
- **cURL** 是一个用来传输数据的工具，支持多种协议，如在 Linux 下用 curl 命令行可以发送各种 HTTP 请求。PHP 的 cURL 是一个底层的库，它能根据不同协议跟各种服务器通讯，HTTP 协议是其中一种。

- 现代化的 PHP 开发框架中经常会用到一个包，叫做 GuzzleHttp，它是一个 HTTP 客户端，也可以用来发送各种 HTTP 请求，GuzzleHttp 并不依赖 cURL 库，而支持多种发送 HTTP 请求的方式。


### stream流的方式
- [关于 PHP stream 的介绍文章](https://www.oschina.net/translate/understanding-streams-in-php)
- stream_context_create 作用：创建并返回一个文本数据流并应用各种选项，可用于 fopen(), file_get_contents() 等过程的超时设置、代理服务器、请求方式、头信息设置的特殊过程。
- 以一个post为例

	```sh
	<?php
	/**
	 \* Created by PhpStorm.
	 \* User: tanteng
	 \* Date: 2017/7/22
	 \* Time: 13:48
	 \*/
	function post($url, $data)
	{
	    $postdata = http_build_query(
	        $data
	    );
	    $opts = array('http' =>
	                      array(
	                          'method' => 'POST',
	                          'header' => 'Content-type: application/x-www-form-urlencoded',
	                          'content' => $postdata
	                      )
	    );
	    $context = stream_context_create($opts);
	    $result = file_get_contents($url, false, $context);
	    return $result;
	}
	```

### socket方式

- 使用套接字建立连接，拼接 HTTP 报文发送数据进行 HTTP 请求。

- 一个 GET 方式的例子：

	```sh
	<?php
	$fp = fsockopen("www.example.com", 80, $errno, $errstr, 30);
	if (!$fp) {
	    echo "$errstr ($errno)<br />\n";
	} else {
	    $out = "GET / HTTP/1.1\r\n";
	    $out .= "Host: www.example.com\r\n";
	    $out .= "Connection: Close\r\n\r\n";
	    fwrite($fp, $out);
	    while (!feof($fp)) {
	        echo fgets($fp, 128);
	    }
	    fclose($fp);
	}
	?>
	```

## 参考资料
[1] [HTTPS 与 SSL 证书概要](http://www.runoob.com/w3cnote/https-ssl-intro.html)

[2] [15 张思维导图来快速学习 PHP 语言基础](http://www.runoob.com/w3cnote/15-php-mind-mapping.html)