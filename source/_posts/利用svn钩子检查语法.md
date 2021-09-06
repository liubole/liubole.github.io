
---
title: 利用svn钩子检查语法
tags: []
categories: [仓库,svn]
toc: false
date: 2016-08-31 10:14:32
---

使用 register_shutdown_function 捕获代码异常的时候，语法错误是无法捕获的，本着及时预防、发现错误的原则，利用 svn 的 hooks 对提交的代码进行语法检查很有必要。

svn常见的钩子有：
>post-commit：文件提交之后执行
>post-lock：文件加锁之后执行
>post-revprop-change：revision 属性修改之后执行
>post-unlock：文件解锁之后执行
>pre-commit：文件提交之前执行
>pre-lock：文件加锁之前执行
>pre-revprop-change：revision 属性修改之前执行
>pre-unlock：文件解锁之前执行
>start-commit：未建立 Subversion transaction 之前执行

这里使用 pre-commit 这个钩子。

**PS：这里遇到一个问题就是不知道svn安装在哪里的。。。最后求助于命令 whereis svn. Yeah!**  [更多linux命令][1]



[1]:






