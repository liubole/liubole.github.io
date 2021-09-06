
---
title: ueditor图片显示顺序和上传顺序不一致
tags: [ueditor]
categories: [其他]
toc: false
date: 2016-10-10 11:38:07
---

问题如题所示，运营觉得这个bug很严重必须改，作为好员工我去看了ueditor的源码。
注：ueditor版本是【UEditor1.4.3】

首先问题可以细分为两个：
1、从上传图片到把图片存到数组，这个过程中图片的顺序有没有改变；
2、把保存的图片显示出来，这个过程中图片的顺序有没有改变；

先看第一个问题，单步调试，发现关键代码如下（在image.js中）：
``` js
_this.imageList.push(json);
```
上面保存图片的方式不会改变图片的顺序，问题一无影响的可能性很高；

再看第二个问题，在ueditor.all.js（or ueditor.all.min.js，看具体情况）中，有个 insertimage 方法，这个方法会把图片显示到富文本框，发现一段代码是设置图片顺序的（在最新的ueditor中未见这段代码，应该是修复bug时去掉了）：
```
for (var i=0; ci=opt[i];i++){
	var name = ci.src;
	var tmp = new Array();
	tmp = name.split("?");
	ci.src = tmp[0];
	var tmp2 = tmp[1];
	tmp = tmp2.split("=");
	tmp = tmp[1].split(".");+-
	ci.pos = parseInt(tmp[0]);
	if(!ci.pos){
		tmp = tmp[0].split("_");
		if(tmp[1]){
			ci.pos = parseInt(tmp[1]);
		}else{
			ci.pos=100;
		}
	}
	ci.pos = i + 1;
	opt[i] = ci;
}
```
从上面的代码可以发现，如果图片链接包含参数，图片的排列顺序就会受到参数的影响，而我这里的图片链接刚好有参数，参数还是以数字结尾的 ；

/(ㄒoㄒ)/~~

删掉代码，需求搞定。
    


