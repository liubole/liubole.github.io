
---
title: 深入理解CI框架：框架如何运转起来（实践篇）
tags: [codeigniter,源码]
categories: [PHP,codeigniter]
toc: false
date: 2016-12-30 14:24:52
---

说明：没特别指明的情况下，本文涉及的Codeigniter均为3.1.2版。

---

本篇文章所展示的代码就是：浓（阉）缩（割）后的codeigniter框架，目的就是让大家看明白ci框架的构成和运行始末。有些地方和CI不一样，比如我这里用了controller基类来加载model和view，实际上CI是通过CI_Loader来做的，希望我这样做不会对你们理解CI框架造成影响。

###准备工作：
>1、apache rewrite
>```php
>RewriteEngine On
RewriteBase /
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php?/$1 [L]
>```
>2、目录结构（强烈建议把下面的文件放到域名根目录下 =￣ω￣= ，这关系到index.php的路径解析）：
>
- index.php
- core
    controller.php
- controllers
    my_controller.php
- models
    my_model.php
- views
    my_view.php
    footer.php
>
>3、访问路径（域名按实际情况修改）：
>`http://www.myphp.com/index.php/my_controller/doTest/p1/p2?p=mm`
>

###Step1：index.php 入口文件
```php
//index.php
//记得设置url rewrite，所有的请求都要走 index.php
$uri = parse_url('http://dummy' . $_SERVER['REQUEST_URI']);
$query = isset($uri['query']) ? $uri['query'] : '';
$path = isset($uri['path']) ? $uri['path'] : '';
$real_path = trim(substr($path, strlen($_SERVER['SCRIPT_NAME'])), '/');

//提取 controllers 和 method
list($class, $method, $args_string) = explode('/', $real_path, 3);
$args = explode('/', $args_string);

//调用方法
require_once( __DIR__ . '/core/controller.php');
require_once(__DIR__ . '/controllers/' . $class . '.php');
$_class = new $class();
call_user_func_array(array($_class, $method), $args);
$_class->output();
```
###Step2：核心 controller
```php
//core/controller.php
//所有控制器均继承该基类，model()和view()方法的作用类似CI_Loder里的model()和view()
class controller
{
    private $output = '';

    public function model($name, $_ = NULL)
    {
        $file_path = dirname(__DIR__) . '/models/' . $name . '.php';
        require_once($file_path);
        return $_ ?
            new $name() :
            new $name(array_shift(func_get_args()));
    }

    public function view($name, $data = array())
    {
        $file_path = dirname(__DIR__) . '/views/' . $name . '.php';
        ob_start();
        extract($data);
        require($file_path);
        $output = ob_get_contents();
        ob_clean();
        @ob_end_clean();
        @ob_end_flush();
        $this->output .= $output;
    }

    public function output()
    {
        echo $this->output;
    }
}
```
###Step3：controllers 控制器
```php
//controllers/my_controller.php
class my_controller extends controller
{
    //访问该方法
    public function doTest()
    {
        //加载模型
        $this->my_model = $this->model('my_model');

        //将值赋值给$this->data数组
        $this->data['title'] = '深入理解CI框架';
        $this->data['args'] = array($arg1, $arg2);
        $this->data['words'] = $this->my_model->upper('hello world!');

        //把值传入到view里面
        $this->view('header', $this->data);
        $this->view('my_view', $this->data);
        $this->view('footer', $this->data);
    }
}
```

###Step4：models 模型
```php
//models/my_model.php
class my_model
{
    //只实现了一个方法，用来演示
    public function upper($word)
    {
        return strtoupper($word);
    }
}
```

###Step5：views 视图
```php
//views/my_view.php
<div><?php echo $words; ?></div>
<div>args: <?php print_r($args);?></div>
```

```php
//views/header.php
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <title><?php echo $title;?></title>
    <meta name="renderer" content="webkit">
    <meta http-equiv="X-UA-Compatible" content="IE=Edge">
    <meta name="keyword" content="">
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
    <meta name="format-detection" content="telephone=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black">
    </head>
<body>
```

```php
//views/footer.php
</body>
</html>
```

###运行结果
>运行后的结果是：
>HELLO WORLD!
>args: Array ( [0] => p1 [1] => p2 )


完。