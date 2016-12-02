---
layout:     	notebook
title:     	bootstrap在微信浏览器的小坑
description:     	overflow:hidden失效！
author:     	許健彬
tags:      	bootsrtap tip
subtitle:     	overflow:hidden失效！
category:     	project
key:          JS_bootstrap
---

## 记录一个小bug

    今天后台胖林同学叫我改一个bug，具体是一个移动端的页面，用了bootstrap的模态框，然后当模态框弹出来滑动模态框里面的内容，居然外面body会出现滚动条，看到这个，
我想着给他body加个overflow:hidden属性不就是啦，然后就去bootstrap官网查了一下这个模态框，发现Bootstrap源码是有给Body加这个属性（.modal-open类）的，那为什么还会
这样,接着测试了小米和魅族的本机自带浏览器和UC等等，均出现这个问题，最后百度了一下，知乎上朋友说了在body加了position:fixed;最后bug解决。原来是因为移动端是基于
touch事件。而overflow是滚动事件，所以overflow失效了。还有另外一个方法就是：把滚动的div的position设置成fixed，然后top:0,或者top设为$(window).scrollTop()。此方法
没试验，大家可以试试。

