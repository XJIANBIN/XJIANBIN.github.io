---
layout:     	notebook
title:     	    Vuex-dispatch源码分析
description:    dispatch 与 action
author:     	許健彬
tags:      	    Vue
subtitle:     	dispatch 与 action 
category:     	project
key:            Vuex-dispatch
---

### 写在前面

昨天，朋友在用Vuex遇到一个问题，所以和他搞到了凌晨快两点还没解决，今天早上他解决了，但是我们都对原理不清楚，我也很想知道，所以花了些时间去查，最后弄明白了，记录下来，方便其他小伙伴。

### 问题描述

场景是Vuex通过dispatch 派发一个登陆事件，调用Login()方法去后台请求登陆数据，然后在dispatch的then里面进行后续操作，而登陆方法即(actions)是异步的，问题就出在dispatch 没有等Login执行完再执行then回调，而是直接执行then方法。代码如下

<img src="{{ "/img/Vuex-1.png" | prepend: site.baseurl }}" style="width:100%;height:300px;">

解决方法如下图，在Login方法里面返回Promise对象，并且异步方法成功之后改变状态，一开始的代码是既没有返回Promise对象，也没有改变状态。（其实官方文档写法是有返回promise对象的，我也不知道他为什么就忘记写这一句.......）
<img src="{{ "/img/Vuex-2.png" | prepend: site.baseurl }}" style="width:100%;height:500px;">

通过上面那种写法，问题是解决了，但是为什么不返回Promise ，dispatch也能执行then呢？肯定是源码中帮我们封装好了呀！但我还是想确定一下，就去github查了Vuex 的源码，如下：

<img src="{{ "/img/Vuex-6.png" | prepend: site.baseurl }}" style="width:100%;height:700px;">

大概做法就是通过dispatch 方法传进去的key，找到this._action[type]对象对应的action方法，赋值给entry常量，然后判断它的长度，大于1，即表示action事件等于或大于2件，就通过Promise.all方法封装成一个大的Promise对象。如果传进去的是一个action，就执行该方法。

这时，我又想到了，那为什么一开始我们没return一个Promise对象，dispatch方法可以执行then回调呢？因为Then回调是定义在Promise对象的原型上的，即如果不是Promise对象，是不可能有then方法，即调用会报错，但是现在并没有报错，应该是哪里生成了一个Promise对象。这时想起一些文档资料中说到，Vuex 2.0以上版本action是会返回Promise对象的，这时我又看了一下源码，

<img src="{{ "/img/Vuex-5.png" | prepend: site.baseurl }}" style="width:100%;height:700px;">

可以看到，里面有一个判断，即如果不是Promise对象，就通过Promise.resolve()方法转换为Promise对象（当时，和另外一个小伙伴一起研究这个问题的，因为我知道resovle方法是改变状态的，当时我在阮一峰老师学习教程那里，还感到奇怪，为什么把Promise.resolve()这些原型上的方法拿出来讲，不是只是改变状态的作用吗?，我还信誓旦旦的跟那位小伙伴说，这个只是改变状态的作用而已嘛)，然后去查了一下

<img src="{{ "/img/Vuex-3.png" | prepend: site.baseurl }}" style="width:100%;height:200px;">

果真，学到的不一定印象深刻，只有踩过坑才记得住.....

这里多说一句，你知道吗？.then()方法也是会返回一个Promise对象的。具体可看[阮老师的Promise教程](http://es6.ruanyifeng.com/#docs/promise#Promise-prototype-then)







