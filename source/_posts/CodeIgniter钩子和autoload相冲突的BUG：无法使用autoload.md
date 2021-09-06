
---
title: CodeIgniter钩子和autoload相冲突的BUG：无法使用autoload
tags: [autoload,codeigniter,hooks]
categories: [PHP,codeigniter]
toc: false
date: 2017-05-17 09:50:09
---

BUG出现的原因是未正确使用钩子——至少没有按Codeigniter的要求来使用：我们的钩子类继承了CI_Controller，但阅读源码后发现不能这么干。

当我们 new 一个自己定义的 Controller 的时候（发生在 `system/core/Codeigniter.php`），CI_Controller 的构造方法会被执行，存储于静态变量`$_is_loaded`（可以通过is_loaded()调用）里的类都会被传入`load_class()`加载一遍，而 load_class 方法在引入文件后，不会直接 new 一个传入的类，它会先给传入的类名加上 CI_ 前缀再创建对象。即 new "CI_{$class}"。问题就出现在这里。

    load_class给传入的类名自动加 CI_ 前缀是写死在代码里的，不能通过传入其他参数改变它的行为。个人猜测这样做是为了防止开发者滥用 load_class：框架维护人员通过 load_class 加载框架核心类，开发者使用 Loader 来加载业务相关的类。

解决问题的方法就是，保证每次用户访问最多执行一次 CI_Controller 的构造方法。如果要在钩子里使用 Loader（例如 load->model()、load->database()），可以使用全局的 &get_instance() 得到的 CI_Controller全局静态实例来加载。

```php
//使用CI_Controller的全局静态实例
$this->ci =& get_instance();
$this->ci->load =& load_class('Loader', 'core');
$this->ci->load->model('api_model');
$this->ci->api_model->setCaller($this->appid);
//model里正常连接数据库
```

记得修改config/hooks里的filepath，指向 hooks 目录（默认在APPPATH下）

