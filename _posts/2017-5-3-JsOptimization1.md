---
layout:     	notebook
title:     	    函数节流、函数防抖实现原理分析
description:    前端性能优化系列之一
author:     	許健彬
tags:      	    前端性能优化系列
subtitle:     	前端性能优化系列之一
category:     	project
key:            optimization1
---

## 一.写在前面

以前都没怎么注意积累性能优化方面的知识，打算写一些文章记录一下，所以开了一个前端性能优化系列。这是系列第一篇，关于函数节流`throttle`与函数防抖`debounce`两个函数原理及作用。

## 二.应用场景

在某些场景下，往往由于事件频繁被触发，因而频繁执行DOM操作，导致UI停顿甚至浏览器崩溃、假死。特别是在智能手机上，这种事件造成的压力更大。有一篇文章介绍过，滚动事件在电脑端触发频率很容易达到每秒30次，而到了移动端可以达到每秒100次以上。所以移动端一般都需要对此类事件进行优化。同时，有的事件伴随着像后台发送请求，由于用户频繁触发，对服务器造成压力（搜索框功能），常见的事件如下

* window对象的`resize`、`scroll`事件等
* 拖拽时的`mousemove`，`mousedown`等鼠标移动事件
* 文字输入、自动完成的`keyup`，`keydown`事件等

对于这种情况下，为了更好的用户体验和减少服务器的压力，一般我们解决方法就是通过事件回调函数的执行频率来达到折中。而相对应就出现了函数节流`Throttling`和函数防抖`Debouncing`这两种技术。本质就是将事件回调函数用debounce或throttle包装，事件触发的频率没有改变，只是我们自定义的回调函数的执行频率变低了。如果你的回调函数是基于DOM操作或者其他花销巨大的操作，那使用这两种技术是非常有必要的。所以如果你的回调函数只是处理一些js的数据，那么用不用防抖和节流处理是一样的。


##  三.Throttling与Debouncing

想象每天上班大厦底下的电梯。把电梯完成一次运送，类比为一次函数的执行和响应。假设电梯有两种运行方案 `throttle` 和 `debounce` 

* throttle 方案的电梯。保证如果电梯第一个人进来后，10秒后准时运送一次，不等待。如果没有人，则待机。
* debounce 方案的电梯。如果电梯里有人进来，等待10秒。如果又有人进来，10秒等待重新计时，直到10秒超时，开始运送。

一般对于window的resize和scroll事件和mousemove事件，实际需求一般是采用throttle减少其回调函数执行频率；而文本输入自动完成等事件一般采用debounce来解决，但说到底还是只要我们理解这两个函数的原理，实际应用时看具体需求，按需使用就行。

## 四.简单实现
两种方法其实都是利用`setTimeout`来实现功能。相信聪明的你肯定能看明白的。

> ### debounce  实现

```javascript
	function debounce(fn,delay){
		var delay=delay || 250;
		var timer; 
		return function(){
			var _this=this;
			var args=arguments;
			if(timer)}{
				clearTimeout(timer);
			}
			timer=setTimeout(function(){
				timer=null;
				fn.apply(_this,args);
			},delay);
		};
	}
```

> ### throttle 实现

```javascript
	function throttle(fn,delay){
		var last,timer;delay || (delay=250);
		return function(){
			var _this=this;
			var args=arguments;
			var now=+ new Date();
			if(last && now-last < delay){
				clearTimeout(timer);
				timer=setTimeout(function(){
					last=now;
					fn.apply(_this,args);
				},delay);
			}else{
				last=now;
				fn.apply(_this,args);
			}
		}
	}
```

> ### 使用方法

```javascript
	window.onscroll=debounce(dosomething,200);
	window.onscroll=throttle(dosomething,200);
```

## 五.其他完整实现
比较简单的实现如上一小节，其实里面还有一些细节，我们可以借鉴一下其他库函数的实现，毕竟是人家几百个工程师实践出来的，肯定比我们想得周到。Jquery和underscore和lodash都实现了这两个方法，他们内部实现有点不一样，但提供的接口基本一样，下面我只介绍一下jq的实现方法和参数意义，其他的差不多，大家选择性使用。可参考[链接:Debouncing and ThrottlingExplained 
Through Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)这篇文章，里面介绍得很清楚。
> ### 1.Jquery

这里我使用的是[链接:JQ的jquery.ba-throttle-debounce.js插件](http://www.bootcdn.cn/jquery-throttle-debounce/)可自行下载
源码封装了jq的写法，这里我将其去除了，也加了一些注释。

#### 参数作用

* no_trailing(boolean):表示最后一次节流函数调用后是否再执行一次回调函数，默认是false,即再调用一次。如下图：

<img src="{{ "/img/no_trailing.png" | prepend: site.baseurl }}" style="width:100%;height:500px;">

* debounce_mode(boolean):决定debounce执行在延迟时间间隔前后，默认是false，即延迟规定时间后再执行回调函数。如下图：

<img src="{{ "/img/debounce_mode.png" | prepend: site.baseurl }}" style="width:100%;height:500px;">

直接使用函数名throttle与debounce就可使用

#### 正确使用方法 ：

```javascript
   $(window).on('scroll', _.debounce(doSomething, 200));
   
   OR
   
   var debounced_version = _.debounce(doSomething, 200);
   $(window).on('scroll', debounced_version);
```
 
#### 错误使用方法：

```javascript
$(window).on('scroll', function() {
   _.debounce(doSomething, 300); 
});
```


#### 源码：
```javascript
(function(window, undefined) {
        throttle  = function(delay, no_trailing, callback, debounce_mode) {
        	//这里参数运行方法时候是一直存在的，因为在子方法里面保存着对父函数的引用（闭包概念），所以这片内存不会被销毁。
            var timeout_id,   //设置定时器
                last_exec = 0;  //上一次调用时间
            if (typeof no_trailing !== 'boolean') {
                debounce_mode = callback;  
                callback = no_trailing;
                no_trailing = undefined;
            }
            function wrapper() {
                var that = this,
                    elapsed = +new Date() - last_exec, //间隔时间
                    args = arguments; //我们绑定事件的时候，事件执行时会传入事件对象，这里参数指事件对象
                console.log(args);

                function exec() {
                    last_exec = +new Date();  //指的是最后一次调用时间
                    callback.apply(that, args);  //执行原来的函数，并把作用域及参数传进去
                };

                function clear() {
                    timeout_id = undefined;
                };
                if (debounce_mode && !timeout_id) {
                    exec();
                }
                timeout_id && clearTimeout(timeout_id);  //如果定时器不为空就清空定时器
                if (debounce_mode === undefined && elapsed > delay) {
                	  exec();
                } else if (no_trailing !== true) {
                    timeout_id = setTimeout(debounce_mode ? clear : exec, debounce_mode === undefined ? delay - elapsed : delay);
                }
            };
            // if ($.guid) {
            //     wrapper.guid = callback.guid = callback.guid || $.guid++;
            // }
            // 整数guid是一个内部的jQuery参数，这将有助于以后解除绑定的功能。但在lodash/underscore 中并没有使用，所以暂时不知道不使用这个参数会有什么后果。
            return wrapper;  //返回函数 
        };
        //第二个参数决定回调函数的执行时间，任何非flase表示回调在一开始执行。false表示在时间间隔最后调用
        //这里参考coodpen上面两个示例动画，很形象（科学上网环境）
        //http://codepen.io/dcorb/pen/GZWqNV （任何非flase）
        //http://codepen.io/dcorb/pen/KVxGqN   (false)
        debounce = function(delay, at_begin, callback) {
            return callback === undefined ? throttle(delay, at_begin, false) : throttle(delay, callback, at_begin !== false);
        };
    })(this);
```

> ### 2 underscore V1.7.0 

这里提一下[链接:underscore1.7.0中文文档](http://learningcn.com/underscore/#debounce)这份中文文档里面关于`debounce`的immediate 参数的描述与英文文档不符，如下

<img src="{{ "/img/immediate .jpg" | prepend: site.baseurl }}" style="width:100%;height:350px;">

正确应该是传参 immediate 为 true， `debounce`会在 wait 时间间隔的开始调用这个函数 。并且在 waite 的时间之内，不会再次调用。（实测通过）
还有两个其他网站的中文文档是正确的，但是上面那个是错误的，我在网站上也找不到联系他们的方式，所以也无法提醒他们更改。大家看到注意一下就行。

### 六.扩展requestAnimationFrame 

翻译自[链接:Debouncing and ThrottlingExplained Through Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)
requestAnimationFrame是限制功能执行的另一种方式。它可以认为是_.throttle（dosomething，16）。但是具有更高的保真度，因为它是一种浏览器本机API，旨在提高准确性。

> #### 优点

* 它安排延迟60fps（16 ms的帧）的时间，但内部将决定渲染的最佳时机。
* 是一个简单和标准的API，将来不会改变。维护少。

> #### 缺点

* rAF的开始/取消得我们手动调用，不像`.debounce`或`.throttle`，它在内部进行管理。
* 如果浏览器选项卡未激活，则不会执行。虽然在滚动，鼠标或键盘事件不存在这种可能性。
* 虽然所有现代浏览器都提供rAF，但IE9，Opera Mini和旧版Android仍然不支持(仍需polyfill,可查看[链接:requestAnimationFrame for Smart Animating](https://www.paulirish.com/2011/requestanimationframe-for-smart-animating/),这个解决方案现在还需要)。
* node.js不支持rAF，因此您无法在服务器上使用它来限制文件系统事件。

根据经验，如果您的JavaScript函数是“绘画”，涉及重新计算元素位置的所有内容中使用`requestAnimationFrame`，能让你的动画更加顺滑不卡顿。

### 七.总结
`debounce`, `throttle`和`requestAnimationFrame`三种技术都略有不同，但他们都是非常有用而且互补的。


### 八.参考文章
[链接:Debounce and Throttle: a visual explanation](http://drupalmotion.com/article/debounce-and-throttle-visual-explanation)                                 

[链接:jQuery throttle / debounce: Sometimes, less is more!](http://benalman.com/projects/jquery-throttle-debounce-plugin/)













