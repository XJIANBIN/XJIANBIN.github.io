---
layout:     	notebook
title:     	handlebars预编译
description:     至今在国内没见过正确的预编译写法！	
author:     	許健彬
tags:      	   template
subtitle:     	 担保你不会白来这一趟！
category:     	project
key:          handlebars
---
## 关于handlebars
> handlebars是一个前端开发模板，这里不过多介绍，
	[基础入门请点击](http://www.cnblogs.com/iyangyuan/archive/2013/12/12/3471227.html)
	请务必先了解这个模板再看下面，(*^__^*) 
	
## 关于预编译走过的弯路！	
	
> 国内的文章对于预编译的介绍如[此文](http://www.gbtags.com/gb/share/5764.htm)，[handlebars官网](http://handlebarsjs.com/precompilation.html)对
于handlebars预编译的调用写法都是

	
```javascript	
	Handlebars.templates.预编译命令得到的JS文件名(contex,options);
```	

	
> 实测这种写法是不通过的（如果有小伙伴测试通过的话可告知！），会报找不到Handlebars方法的错误，通过google查到国外朋友的另外一种写法：


```javascript	
	var logo=Handlebars.templates[“预编译命令得到的JS文件名”](数据); //切记加上引号
	 $('#logo').html(foo);  
```	

	
这种写法实测通过！！为了查这个调用，真是╮(╯▽╰)╭
	

## 就这么点干货吗？
> NONONO!有的小伙伴肯定有疑问，一个模板文件就是一个JS，而页面一般都是5个以上的handlebars模板吧，那这样我的html头部不得都是JS的引入吗？
O(∩_∩)O哈哈~我当然也不允许这么LOW啦♪(^∇^*)，最后翻开得到的那个JS文件看，里面是有个文件名同样的对象的，一开始我们也以为是通过文件名来
调用的，这下好啦，只要我们把其他生成的JS文件手动复制到同一个JS文件就行啦，也就是如下代码，

	
```javascript
templates['demoa'] = template({"1":function(container,depth0,helpers,partials,data) {
.....
},"useData":true});
```	

	
> 记得使用预编译命令的时候最好不要 -m哦，这是压缩的命令，压缩完会比较难看，一般项目上线才进行压缩！
	

> 1. 到此这篇文章本来就可以结束了，但是，我们上面引用的是handlebars.js这个文件，这个文件未压缩的情况下有140K，官方给出了另外一个可以进一步节约带宽，而且更加适合
移动端加载的方案，那就是使用体积更小的runtime文件，也就是handlebar.runtime.js这个文件,这个文件在未压缩的情况下也只有24K，[handlebars.js Bootstrap中文网开源
项目免费 CDN 服务](http://www.bootcdn.cn/handlebars.js/)这个网址上面可以下载到runtime版本的JS，


## 后续
其实handelbars还有更方便的用法，就是结合gulp来使用，这回就先写到这里，以后有机会再更吧！(*^__^*) 嘻嘻…..
	
## 12.4更新
上面第一种方式今天居然可以了，反倒是外国友人那种不行了，真是奇怪，大家用到的时候看看吧。
