
---
title: composer中的autoload
tags: [composer,PHP自动加载]
categories: [PHP,工具]
toc: false
date: 2016-08-26 11:24:36
---

composer作为一款PHP的包管理器，类似于Java中的maven。当我们用composer把需要使用的包下载下来后，借助__autoload() 我们就能直接在项目中使用第三方库了。

composer提供了四种自动加载机制：

* classmap：
* psr-0:
* psr-4:
* files:

