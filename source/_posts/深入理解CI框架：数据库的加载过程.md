
---
title: 深入理解CI框架：数据库的加载过程
tags: [codeigniter]
categories: [PHP,codeigniter]
toc: false
date: 2017-02-15 16:48:30
---

说明：没特别指明的情况下，本文涉及的Codeigniter均为3.1.2版。

---

要在 CI 框架里使用数据库，你可以显示地加载它：
```php
$this->load->database(db_to_load);
```
也可以在加载 model 的时候顺带加载数据库：
```php
// 第三个参数
$this->load->model(model_to_load, name, db_to_load);
```
当我们只有一个数据库的时候，上面两种方法都行得通。**默认情况下，CI 把数据库连接赋值给一个叫 db 的属性**。但如果有多个数据库(>=2)，并且要经常切换，就只能使用 `database()` 并且第二个参数传 true，例如：

```php
$this->dblink = $this->load->database(db_to_load, true);//return ?
```

我们看一下 `database()` 方法的部分实现细节：

```php
// database() 方法开头会判断 db 是否已被赋值
if ($return === FALSE && $query_builder === NULL && isset($CI->db) && is_object($CI->db) && ! empty($CI->db->conn_id))
{
    return FALSE;
}
...
// 返回数据库连接
if ($return === TRUE)
{
	return DB($params, $query_builder);
}
...
// 赋值给 db
$CI->db =& DB($params, $query_builder);
```

看得出来，**每次执行 database(db_to_load, true) 的时候都会连接一次数据库，所以在必要时才 load model。**

>注：1. 上面指的是调用 database(db_to_load, true)；不是 database(db_to_load)；
> 2. 读者事先看过 DB() 方法，知道 DB() 会去连数据库 o(￣▽￣)o 。


下面是 `DB()` 方法的部分实现细节：

```php
...
//此处有很多 include 和 require_once
...
// $params['dbdriver'] 在配置数据库时指定，比如 mysql
$driver = 'CI_DB_'.$params['dbdriver'].'_driver';
$DB = new $driver($params);
...
// $driver 继承的某个抽象类有 initialize() 方法
$DB->initialize();
```

下面是 `initialize()` 方法的部分实现细节：

```php
/*
 * 这是 CI_DB_driver 类
 */
// 防止重复连接数据库，但是只在这样调用时才起作用： $this->load->database(db); 注意第二个参数。
if ($this->conn_id)
{
    return TRUE;
}
// db_connect 由继承该类的派生类实现
$this->conn_id = $this->db_connect($this->pconnect);
...
// 最后设置mysql(客户端)编码
return $this->db_set_charset($this->char_set);
```

如果你再深入源码了解 CI_DB_driver 类，你会发现下面的关系链：

> 
> $driver extends CI_DB { } // drivers 举例： CI_DB_mysql_driver
>
> class CI_DB extends CI_DB_query_builder { } // CI_DB 是个空类
> 
> // or： class CI_DB extends CI_DB_driver { }
> 
> abstract class CI_DB_query_builder extends CI_DB_driver { }
> 
> abstract class CI_DB_driver { }
>

上面出现了两个抽象类，它们一个偏向于 Query，一个偏向于 DB，下面是两个类的注释：
>CI_DB_query_builder : This is the platform-independent base Query Builder implementation class.
>CI_DB_driver: This is the platform-independent base DB implementation class.

`CI_DB` 是个空类，没有任何实现，但是所有数据库驱动类（例如 `CI_DB_mysql_driver`）都继承 `CI_DB`。
注意到 `&DB(params, query_builder_override)` 方法的第二个参数，它决定了 `CI_DB` 继承 `CI_DB_query_builder` 还是 `CI_DB_driver` : 
```php 
if (! isset($query_builder) OR $query_builder === TRUE) 
{
    if ( ! class_exists('CI_DB', FALSE))
	{
        class CI_DB extends CI_DB_query_builder { }
    }
} 
elseif ( ! class_exists('CI_DB', FALSE))
{
	class CI_DB extends CI_DB_driver { }
}
```

可以看出来，`CI_DB` 起到了**“适配器”**的作用。


完。

