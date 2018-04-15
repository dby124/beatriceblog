---
title: PHP 过滤器（Filter）
date: 2018-04-13 22:14:38
tags: [cookie&session]
categories: [PHP]
---
## 简介

PHP 过滤器用于**验证和过滤来自非安全来源的数据**。验证和过滤用户输入或自定义数据是任何 Web 应用程序的重要组成部分。设计 PHP 的过滤器扩展的目的是使数据过滤更轻松快捷。

由于大部分Web应用程序都依赖于外部数据的输入，这些数据的来源包括：

- 来自表单的输入数据
- Cookies
- 服务器变量
- 数据库查询结果

> 通过使用过滤器，您能够确保应有程序获得正确的输入类型。

## 过滤函数

对于需要过滤的变量，使用下列函数：

函数 | 描述 | PHP
---|---|---
filter_has_var() | 检查是否存在指定输入类型的变量。 | 5
filter_id() | 返回指定过滤器的 ID 号。 | 5
filter_input() | 从脚本外部获取输入，并进行过滤。 | 5
filter_input_array() | 从脚本外部获取多项输入，并进行过滤。 | 5
filter_list() | 返回包含所有得到支持的过滤器的一个数组。 | 5
filter_var_array() | 获取多项变量，并进行过滤。 | 5
filter_var() | 获取一个变量，并进行过滤。 | 5

> PHP：指示支持该函数的最早的 PHP 版本。

## 过滤器

### FILTER_CALLBACK
调用用户自定义函数来过滤数据。把自定义的函数作为一个过滤器来使用。这样，就拥有了数据过滤的完全控制权。指定的函数必须存入名为 "options" 的关联数组中。

> 注意：既可以创建自己的自定义函数，也可以使用已有的 PHP 函数。规定准备用到过滤器函数的方法，与规定选项的方法相同。

在下面的例子中，使用了一个自定义的函数把所有 "\_" 转换为空格：

```
<?php
function convertSpace($string) {
	return str_replace("_", " ", $string);
}
$string = "Peter_is_a_great_guy!";
echo filter_var($string, FILTER_CALLBACK, array("options"=>"convertSpace"));
?>
// 结果：Peter is a great guy!
```


### validating过滤器

- **作用**：

	1. 用于验证用户输入。 
	2. 严格的格式规则。（比如 URL 或 E-Mail 验证） 
	3. 如果成功则返回预期的类型，失败则返回 false。 

- **效果**：Validation 用于**校验数据**。例如，在filter\_var($variable)中传入FILTER\_VALIDATE\_EMAIL，会校验$variable是否为合法的email地址。

- **返回结果**：校验通过返回数据，校验失败返回false。

```
<?php
$var=300;

$int_options = array("options"=>
array("min_range"=>0, "max_range"=>256));

var_dump(filter_var($var, FILTER_VALIDATE_INT, $int_options));
?>
```

## sanitizing过滤器
- **作用**：

	1. 用于允许或禁止字符串中指定的字符。 
	2. 无数据格式规则。 
	3. 始终返回字符串。

- **效果**：Sanitization 用于**修正数据，不校验数据**。例如，在filter_var($variable)中传入FILTER_SANITIZE_EMAIL，$variable变量中不符合email地址规则的字符将会被修剪。

- **返回结果**：返回修正后的数据，无论修正是否起作用。

- **示例**：

```
<?php
$url="http://www.w3school.com.cn";
var_dump(filter_var($url,FILTER_SANITIZE_ENCODED));
?>
```