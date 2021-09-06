
---
title: 如何同时遍历js中的数组和对象
tags: [js]
categories: [Javascript]
toc: false
date: 2016-09-21 09:54:43
---

同样的数据从服务器取得并解码后（json数据），在js端呈现两种不同的结果，一为数组，一为对象，暂且不讨论问题的原因（**待解决**），这里讨论解决办法。

开始以为只会遍历数组，没考虑到会遍历对象，所以用了 for（对象无length属性所以失败）。

forEach 只能用于数组，不能用于对象；
for 需要长度作为循环终止条件，对象没有length属性，也不考虑；
for in 可以同时遍历数组和对象，符合条件；

```php
var searches = {state:1,page:2};
for (var key in params) {
    searches[key] = params[key];
}
```

Object.keys() 先把对象或属性取出来得到数组a，然后遍历a，相当于在遍历数组和对象，也符合条件；

var p = data[i] || data.i;//不论data是数组还是对象，都可以取到值


