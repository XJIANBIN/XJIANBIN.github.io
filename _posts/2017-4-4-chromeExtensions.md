---
layout:     	notebook
title:     	    chrome extensions
description:    chrome extensions for Guangzhou driver's license exam	
author:     	許健彬
tags:      	    chrome
subtitle:     	chrome extensions 小尝试
category:     	project
key:            chrome
---


## 一.前记

清明期间，闲着无事，正好要报名驾照考试的约课，受到一位同学启发，所以就尝试了一把chrome extensions的开发。写下这篇文章，记录一下学习所用资料与遇到几个小问题，方便以后
查阅。这是项目[源码](https://github.com/XJIANBIN/Lesson-assistant)，有需要的朋友可以下载查看交流。

## 二.小弯路
 
> 1 . 如何把一个url变成bsse64等文件格式 
  * (http://jsfiddle.net/handtrix/YvQ5y/) 有的慢
  * (http://stackoverflow.com/questions/6150289/how-to-convert-image-into-base64-string-using-javascript)

> 2 . var confirm = document.getElementsByClassName("l-btn-icon-left") 拿到的是一个数组，要操作点击事件必须要confirm[0];

> 3 . 比较两个字符串，要先去除掉前后的空格，否则consosle.log出来是一样，但是还是不相等，返回false。

> 4 . 同一个url不同时间访问的是不同图片。因为该网站需要填写验证码，所以我调用了聚合数据验证码API。API需要base64，我用canvas转换成base64，因为canvas回去请求一次图片，所以
后台保存的验证码会变了，但是页面的验证码不会变，但是聚合数据API返回的验证码是当前（canvas请求）的图片的答案，所以是没问题的。

> 5 . 很多提供API的网站要求参数为base64图片，但是他们格式是去除了“data:image/png;base64,”这个头部，当时还为这个傻傻懵逼了好一会。

> 6 . 异步

```javascript
     chrome.storage.sync.get("data", function (retVal) {
            if(retVal.data!=null){
			   var data=retVal.data.carAdministrationData_area||"";
			   data=data.split(",");
			   data.forEach(function(elements,index){
				   Area[index]=elements.replace(/(^\s*)|(\s*$)/g, "");
			   });
	        }
         });
		 console.log( Area);
```

因为插件中需要保存一些数据，而我选择保存在storage中，而且采用异步获取的方式，而且下面需要用到此异步操作生成的数组。以下就是获取到的数组了：
<img src="{{ "/img/googleExtentsion.png " | prepend: site.baseurl }}" style="width:400px;height:200px;">
有没有觉得这个数组很奇怪，为什么外面显示长度为0，里面却有数据而且长度为4？
问题出在这个代码上，因为这个代码是异步去获取chrome.storage，而下面console出来的时候，还没获取到，里面的数据是异步加进去的，所以才会显示成这样。

> 7 . [JSCompress ](https://jscompress.com/) 最后安利一个使用UglifyJS2压缩和最小化的在线网站，可以把所有JS打包成一个js文件，我们项目中用来打包Handlebars模板文件（因为
handlebars提供的功能在项目中有点问题）

## 三.总结
   
清明假期就这么过了。虽然没有去玩（单身狗玩什么？还是学习好，学习使我快乐！(*^__^*)），但是过得很充实。还是蛮有成就感的。

参考文章

* [谷歌插件API文档](https://developer.chrome.com/extensions/api_index)
* [Chrome扩展及应用开发（首发版）] (http://www.ituring.com.cn/book/1421)   
* [Chrome扩展及应用开发](http://www.ituring.com.cn/minibook/950)
* [360翻译文章](http://open.chrome.360.cn/extension_dev/overview.html)
* [chrome.storage中文文档](https://crxdoc-zh.appspot.com/apps/storage)
