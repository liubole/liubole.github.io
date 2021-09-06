
---
title: PHP二维数组排序
tags: [排序]
categories: [PHP]
toc: false
date: 2016-09-08 17:37:53
---

思路：将 array (key => array) 转换为 array (key => array中的待排序字段)，降维为一维数组排序，然后将一维数组的value替换为原始数组对应key的value。

二维数组排序的时间复杂度等同一维数组排序的时间复杂度。同理推测，n维数组排序的时间复杂度等同一维数组排序的时间复杂度。

----------
