
---
title: spl_autoload_register 入门级讲解
tags: [PHP自动加载]
categories: [PHP]
toc: false
date: 2016-08-25 15:35:38
---


<h2 id="1">__autoload()</h2>

了解 spl_autoload_register() 前先了解一下 __autoload()，或者直接 [跳过此节](#2)：
```php
function __autoload($c)
{
    var_dump($c);
}
new Say();
```
``` php
//输出
string(3) "Say"
Fatal error: Class 'Say' not found in E:\Dropbox\liubole\htdocs\test\index.php on line xx
```

当Say类不存在的时候，自动执行了 __autoload() 函数，并将类名当做参数传入了 __autoload()。

如果在 __autoload() 里面 include 一下包含 Say 类的文件，可以“亡羊补牢”，代码继续正常执行：

```php
function __autoload($c)
{
    echo "not fund class \"{$c}\", auto loading...\n";
    
    //libs下的say.php，大小写无影响
    include_once __DIR__ . "/libs/{$c}.php";
}

$say = new Say();
$say->hello();
```
包含 Say 类（libs/say.php）的文件内容：
```php
class Say
{
	function __construct()
	{
		echo "init class \"Say\"...\n";
	}

	function hello()
	{
		echo "say: hello~\n";
	}
}
```
再次执行，结果如下：
```php
not fund class "Say", auto loading...
init class "Say"...
say: hello~
```

至此我们大概了解了 __autoload() 的实际执行情况。


----------


<h2 id="2">spl_autoload_register()</h2>

PS：前缀 SPL 的全称是 Standard PHP Library。

当PHP找不到类文件会调用 __autoload() 方法，当注册了自己的函数或方法时，PHP不会调用 __autoload() 函数，而会调用自定义的函数。

>如果在你的程序中已经实现了__autoload函数，它必须显式注册到__autoload栈中。因为spl_autoload_register()函数会将Zend Engine中的__autoload函数取代为spl_autoload()或spl_autoload_call()。

上面的话来自 [php.net][1]。OK，那怎样算 **显式注册到__autoload栈中** 呢？
 
 ◔ ‸◔ ... ◔ ‸◔ ..... ◔ ‸◔  ...... ◔ ‸◔  ........◔ ‸◔
 
答案是：spl_autoload_register("__autoload") 。以下是我找来的一点摘要，描述了为何需要这样做：

* autoload机制的主要执行过程为：
(1) 检查执行器全局变量函数指针autoload_func是否为NULL。
(2) 如果autoload_func==NULL，则查找系统中是否定义有__autoload()函数，如果没有，则报告错误并退出。
(3) 如果定义了__autoload()函数，则执行__autoload()尝试加载类，并返回加载结果。
(4) 如果autoload_func不为NULL，则直接执行autoload_func指针指向的函数用来加载类。注意此时并不检查__autoload()函数是否定义。

* 如果既实现了__autoload()函数，又实现了autoload_func（将autoload_func指向某一PHP函数），那么只执行autoload_func函数。

* SPL autoload机制的实现是通过将函数指针autoload_func指向自己实现的具有自动装载功能的函数来实现的。SPL有两个不同的函数spl_autoload， spl_autoload_call，通过将autoload_func指向这两个不同的函数地址来实现不同的自动加载机制。spl_autoload的功能比较简单，而且它是在SPL扩展中实现的，我们无法扩充它的功能。所以我们需要spl_autoload_register将autoload_func指向spl_autoload_call的这个功能。

* 在SPL模块内部，有一个全局变量autoload_functions，它本质上是一个HashTable，不过我们可以将其简单的看作一个链表，链表中的每一个元素都是一个函数指针，指向一个具有自动加载类功能的函数。spl_autoload_call按顺序执行这个链表中每个函数，在每个函数执行完成后都判断一次需要的类是否已经加载，如果加载成功就直接返回，不再继续执行链表中的其它函数。

* 自动加载函数链表autoload_functions由spl_autoload_register函数维护，它可以将用户定义的自动加载函数注册到这个链表中，并将autoload_func函数指针指向spl_autoload_call函数。


欲知详情请移步 [__autoload机制详解以及与spl_autoload_register的区别][2]， 文章把原因讲的很详细，时间匆忙我仅囫囵吞枣瞄了眼。


----------


本文涉及资料：
[PHP: spl_autoload_register - Manual ][1]
[__autoload机制详解以及与spl_autoload_register的区别][2]


----------


[1]:http://php.net/manual/zh/function.spl-autoload-register.php
[2]:http://www.cnblogs.com/eric-gao/articles/3368719.html
