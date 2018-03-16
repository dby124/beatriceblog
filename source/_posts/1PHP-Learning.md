---
title: PHP Learning
date: 2017-08-08 11:58:38
tags: [PHP]
categories: [PHP]
---

## PHP发送HHTP请求的几种方式

## cURL
- **cURL** 是一个用来传输数据的工具，支持多种协议，如在 Linux 下用 curl 命令行可以发送各种 HTTP 请求。PHP 的 cURL 是一个底层的库，它能根据不同协议跟各种服务器通讯，HTTP 协议是其中一种。

- 现代化的 PHP 开发框架中经常会用到一个包，叫做 GuzzleHttp，它是一个 HTTP 客户端，也可以用来发送各种 HTTP 请求，GuzzleHttp 并不依赖 cURL 库，而支持多种发送 HTTP 请求的方式。


## stream流的方式
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

## socket方式

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

## PHP开源框架

-  **CI**非常轻量级，是一个简单的MVC框架，性能也很快。

-  **ThinkPHP3.2**国内使用比较多，优点是文档非常多，各种问题解决方案比较多，缺点是代码不够规范，理念落后。

-  **Laravel** 是一个现代化的PHP开发框架，代码优雅，使用 composer 方式扩展功能，社区活跃，缺点是比较重，比较适合做后台管理或者应用型WEB系统。


## 参考资料
[1] [15 张思维导图来快速学习 PHP 语言基础](http://www.runoob.com/w3cnote/15-php-mind-mapping.html)

[2] [HTTPS 与 SSL 证书概要](http://www.runoob.com/w3cnote/https-ssl-intro.html)