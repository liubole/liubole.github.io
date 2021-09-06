
---
title: post_get 同一个参数传两次
tags: [post]
categories: [网络,http]
toc: false
date: 2016-12-06 10:35:19
---

最近在做保险业务，遇到一个奇怪（shaojianduoguai）的问题，同一个参数需要传递两次，比如：
```php
period=2016-12-12&period=2017-12-12
```

本来在php里，经常用array来传递、接收post参数，array的特点是同一个键名唯一，所以肯定不能简单地用array传递参数了。 (′д｀ )…彡…彡

--- 
### POST可以传递两个相同名字的参数吗？

可以。下面是测试代码：

```php
//index.php:
function post($url, $para)
{
    $curl = curl_init($url);
    curl_setopt($curl, CURLOPT_HEADER, 0); // 过滤HTTP头
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);// 显示输出结果
    curl_setopt($curl, CURLOPT_POST, true); // post传输数据
    curl_setopt($curl, CURLOPT_POSTFIELDS, $para);// post传输数据
    $responseText = curl_exec($curl);

    curl_close($curl);
    return $responseText;
}
$url = "local.soft.com/sub.php";
$res = post($url, 'period=2016-12-12&period=2017-12-12');
var_dump(unserialize($res));
//string(35) "period=2016-12-12&period=2017-12-12"

//sub.php
echo serialize(file_get_contents("php://input"));
die();
```

既然POST可以传递，那么GET呢？测试发现GET也可以：

```php
//index.php
function get($url)
{
    $curl = curl_init($url);
    curl_setopt($curl, CURLOPT_HEADER, 0);
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
    $responseText = curl_exec($curl);

    curl_close($curl);
    echo $responseText;
}

$url = "local.soft.com/sub.php?period=2016-12-12&period=2017-12-12";
get($url);

//sub.php
var_dump($_SERVER['QUERY_STRING']);
die();
```

### 结论：GET和POST都可以传递两个相同名字的参数