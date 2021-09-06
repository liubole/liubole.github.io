
---
title: 深入理解CI框架：框架如何运转起来（原理篇）
tags: [codeigniter,源码]
categories: [PHP,codeigniter]
toc: false
date: 2016-12-19 14:22:04
---

说明：没特别指明的情况下，本文涉及的Codeigniter均为3.1.2版。

---

##一、核心
###index.php：入口文件
入口文件**主要定义路径和环境**，包括``BASEPATH``、``FCPATH``、``APPPATH``等常量表示的路径和``application``、``system``等变量表示的路径；
    
>``index.php``通过``$_SERVER['CI_ENV']``来分辨当前的执行环境，目前支持``development``、``testing``、``production``三种（默认``development``），如果你在``apache``里配置``CI_ENV``为其他值（比如mytest），项目会输出错误信息并停止执行；
    
###core/CodeIgniter.php：分派请求
``CodeIgniter.php``加载了各种各样的类和方法，它解析用户的请求（``URI类``），判断请求对应哪个控制器的哪个方法（``Router类``），创建控制器对象，执行方法，得到结果并输出（``Output类``），期间还会加载并执行预先设置好的各种钩子（``Hooks类``）。

>1. 很显然，``CodeIgniter.php``是CI框架最重要的文件之一，它根据用户请求调用不同类处理并输出结果。
2. CI框架解析用户请求，判断对应哪个控制器哪个方法时，主要依据``$_SERVER``这个变量（``$_SERVER['REQUEST_METHOD']``等）
3. 除了各种类，``CodeIgniter.php``还加载了很多公共方法，详见``core/Common.php``和``core/compat``

##二、提取核心代码
```php
$RTR =& load_class('Router', 'core');
$class = ucfirst($RTR->class);
require_once BASEPATH.'core/Controller.php';
$CI = new $class();
```

##三、细枝末节
###core/Common.php：公共方法
``Common Functions``——这是``Common.php``定位，用来存放各种公共方法，比如``is_php``、``is_https``、``is_cli``、``show_404``、``function_usable``等等。
>``core/compat``目录下也存放有很多公共方法文件，比如``hash.php``、``mbstring.php``、``password.php``、``standard.php``，我们经常用的``array_column``函数就是在``standard.php``里定义的。

##四、黑科技
###extract：从数组中将变量导入到当前的符号表
```php
//index.php
$vars = array('name' => 'jane', 'age' => 20);
extract($vars);
var_dump($name);
```
###ob_xxx函数：把输出放到缓冲区、从缓冲区获取内容
>ob_start：打开输出控制缓冲
ob_end_flush：冲刷出（送出）输出缓冲区内容并关闭缓冲
ob_end_clean：清空（擦除）缓冲区并关闭输出缓冲
```php
//index2.php
//将一个php文件保存到文本文件里
//load->view 如何实现的
ob_start();
include('./index.php');
$buffer = ob_get_contents();
file_put_contents('index.php.txt', $buffer);
@ob_end_clean();
ob_end_flush();
```

###ReflectionMethod：反射类
```php
//检查方法是否可以访问
//如何在访问一个private方法或不存在的方法的时候显示404
$reflection = new ReflectionMethod($class, $method);
if ( ! $reflection->isPublic() OR $reflection->isConstructor())
{
    $e404 = TRUE;
}
```

完。