
---
title: 妈妈购 Shop_com解析
tags: [妈妈购]
categories: [其他]
toc: false
date: 2016-05-28 22:35:17
---

use HuNanZai\Component\Log\Service as Logger;
use Api\ShopCom\Model\UncheckLog;
use Api\ShopCom\Model\CheckedLog;
use Api\ShopCom\Model\CheckProof;
use Api\ShopCom\Model\PayLog;

**use HuNanZai\Component\Log\Service as Logger;**
去HuNanZai\Component\Log\Service看源码。涉及到composer。
>composer:提供依赖管理+自动加载功能。
>phpstorm是否支持composer：支持，在windows上安装composer，进入composer.json所在目录，运行composer install -vvv
>composer.json:
>>composer中的autoload：自动加载，基于spl_autoload_register() 函数

