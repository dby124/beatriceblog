---
title: PHP7的变化
date: 2018-04-24 21:19:38
tags: [PHP7]
categories: [PHP]
---

### 引言

PHP7与PHP5版本相比有非常大的变化，尤其是在Zend引擎方面。为了提升性能，PHP7对Zend进行了深度优化，使得PHP的运行速度大大提高，比PHP5.0-5.6快了近5倍，同时还降低了PHP对系统资源的占用。

PHP7比较大的变化有：

### 抽象语法树

PHP是一种解释性语言，通过解析器来执行。

那么首先来看一下编译器与解释器的区别：读入源语言后，解释器和编译器都要进行**词法分析、语法分析和语义分析**，之后，二者开始有所分别。

- 解释器在语义分析后选择了直接执行语句；

- 编译器在语义分析后选择将将语义存储成某一种中间语言，之后通过不同的后端翻译成不同的机器语言（可执行程序）。其存在一个预编译的过程。如下图所示：

    ![解释器与编译器的区别](/images/PHP/解释器与编译器的区别.jpg)

    > [关于编译器与解释器的区别](https://blog.csdn.net/touzani/article/details/1625760)

PHP7之前的版本，代码解释过程：

- PHP代码在语法解析阶段直接生成ZendVM指令，即在`zend_language_parser.y`中直接生成opline指令，使得编译器与执行器耦合在一起。
- 编译生成的指令再供执行引擎使用，该指令是在语法指令直接生成的，若要更换执行引擎，怎需要修改语法解析规则；若PHP语法变化，但没有修改执行引擎，仍需要修改语法解析规则。


### Native TLS

PHP5.x版本扩展中，有`TSRM_CC`、`TSRM_DC`宏，用于线程安全。

PHP中有很多变量需要在不同函数间共享，多线程的环境下不能简单地通过全局变量来实现，为了适应线程的应用环境，PHP提供了一个线程安全资源管理器，将全局资源进行线程隔离，不同的线程之间互不干扰。

使用全局资源需要先获取本线程的资源池，这个过程比较占用时间，因此，PHP5.x通过参数传递的方式将本线程的资源池传递给其他函数，避免重复查找。这种方式需要所有函数接受资源池的参数（`TSRM_DC`宏所加的参数）**，这些参数传递不仅易遗漏参数，还是得代码不优雅**。

PHP7使用Native TLS（线程局部存储）来保存线程的资源池，简单来说就是通过`__thread`标识一个全局变量，这样这个全局变量就是线程独享的了，不同线程的修改不会相互影响。


### 指定函数参数、返回值类型

输入和输出参数必须是指定的数据类型，示例如下：

```
function foo(string $name): array {
    return [];
}
```

### zval结构的变化

Zval是PHP中最重要的数据结构之一（另一个比较重要的数据结构是hash table），它包含了PHP中的变量值和类型的相关信息。它是一个struct，在PHP5.x中，基本结构为：

```
struct _zval_struct {
    zvalue_value value;     /* value，变量的具体值 */
    zend_uint refcount__gc;  /* variable ref count，记录变量的引用计数（自动回收的基础） */
    zend_uchar type;          /* active type ，类型*/
    zend_uchar is_ref__gc;    /* if it is a ref variable，标识变量是否为引用 */
};
typedef struct _zval_struct zval;
```

变量的实际值，具体来说是一个zvalue_value的联合体（union），用来适配不同的变量类型:

```
typedef union _zvalue_value {
    long lval;                  /* long value */
    double dval;                /* double value */
    struct {                    /* string */
        char *val;
        int len;
    } str;
    HashTable *ht;              /* hash table value,used for array */
    zend_object_value obj;      /* object */
} zvalue_value;
```

> 参考：[PHP内核探索之变量（1）变量的容器-Zval](https://blog.csdn.net/ohmygirl/article/details/41542445)

PHP5.x中引用计数是在zval中，而不是在具体的value中，这样一来，导致变量复制时需要复制两个结构，zval，zvalue_value始终是绑定在一起的。

- 变化1：**PHP7中将引用计数转移到具体的value中**，这样更合理，因为zval只是变量的一个载体，可以简单地认为是变量名，而value才是真正的值，这个改变**使得PHP变量之间的复制、传递更加简洁、易懂**。
- 变化2：**zval结构的大小由24byte减少到16byte，这PHP7能够降低系统的资源占用。**


### 异常处理

PHP5.x中很多操作会直接抛出error错误，PHP7中将多数错误改为了异常抛出，这样就可以通过try catch捕捉到异常。这种新的异常处理方式使得错误处理更加可控。

### HashTable的变化

HashTable，即哈希表，也称为散列表，它是PHP强大的array()类型的内部实现结构，也是内核中使用非常频繁的一个结构，函数符号表、类符号表、常量符号表等都是通过HashTable实现的。

PHP7中，HashTable结构的大小由72byte减小到56byte，同时，数组元素Bucket结构也由72byte减小到32byte。

### 执行器

execute_data、opline采用寄存器变量存储，执行器的调度函数为`execute_ex()`，这个函数复制执行PHP代码编译生成ZendVM指令。在执行期间会频繁地用到execute_data、opline两个变量。

PHP5.x中，这两个变量是由`execute_ex()`通过参数传递给各指令handle的。

PHP7中不再采用传参的方式，而是将execute_data、opline通过寄存器来存储，避免传参导致的频繁出入栈操作，同时寄存器相比于内存的访问速度更快。这个优化使得PHP的性能有了5%左右的提升。


### 新的参数解析
PHP5.x通过`zend_parse_parameters()`解析函数的参数，PHP7提供另外一种方式，同时保留原来方式，但是新的解析方式速度更快。


参考：秦朋 《PHP7内和剖析》第1.3节

推荐文章——关于PHP内部实现的文章：
1. [给PHP开发者的PHP源码（1）：源码结构](http://blog.jobbole.com/102175/)
2. [理解 PHP 内部函数的定义](http://blog.jobbole.com/102235/)
3. [PHP 的变量实现](http://blog.jobbole.com/102433/)
4. [理解数组在PHP内部的实现](http://blog.jobbole.com/102494/)