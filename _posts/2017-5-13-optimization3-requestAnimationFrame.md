---
layout:     	notebook
title:     	    动画性能优化之requestAnimationFrame
description:    前端性能优化系列之三
author:     	許健彬
tags:      	    前端性能优化系列
subtitle:     	前端性能优化系列之三
category:     	project
key:            optimization3
---

### 一.前记
在前端性能优化系列第一篇的最后，我稍微介绍了一下`requestAnimationFrame`，刚好今天看到一些比较好的文章，所以再总结了一下`requestAnimationFrame`的一些知识。

### 二.正文
平时对于一些动画，大部分我们都是用`setTimeout`或者`setInterval`来制作的，我们先来看一下这两个方法的缺点：

> #### 1. 使用setTImeout and setInterval 劣势

* setTimeout 不会考虑浏览器发生了什么，例如页面已经被切换到另一个tab，但是还是会占用你的CPU即使现在已经不需要工作了,Chrome将隐藏的选项卡中的`setInterval`和`setTimeout`
设置为1fps(浏览器每秒刷新60fps)，但是这并不是依赖于所有浏览器。
* setTime只在固定的时间内更新，而不是在计算机合适的时候。也就是说，当计算机屏幕重绘的速度跟动画的帧速率不一致时，浏览器就会重新绘制整个屏幕，这无疑会花费计算机的处
理能力，也意味着更高的CPU和风扇的使用率，或者移动设备的电池消耗。

* 另外一种需要考虑的情况是：当一次性多个元素的动画。一种解决方法是通过把所有的动画逻辑放在一个时间间隔内，但这样会带来一个问题即是当前帧不需要的动画也会运行。一种替代方案
便是使用单独的时间间隔。但这同样也会带来每一次你移动屏幕上的一些东西，你的浏览器会重绘，造成浪费的问题。(翻译得不好，所以放上原文，便于读者理解）。
Another consideration is the animation of multiple elements at once. One way to approach this is to place all animation logic in one interval with the understanding that animation calls may be running even though a particular element may not require any animation for the current frame. The alternative is to use separate intervals. The problem with this approach is that each time you move something on screen, your browser has to repaint the screen. This is wasteful! 

> #### 2. requestAnimationFrame 

为了解决效率问题，Mozilla（Firefox的制造商）提出了`requestAnimationFrame`函数，后来被WebKit团队（Chrome和Safari）采用和改进。它提供了一个本机API，用于在浏览器中运行任何类型的动画，无论是使用DOM元素，CSS，画布，WebGL或其他任何内容。
   requestAnimationFrame(draw, element);
  
优点

* 浏览器可以优化它，所以动画将更加流畅
* 非活动选项卡中的动画将停止，优化CPU的使用（提高你的电脑电池寿命）
 
```javascript
//调用一次即可
function draw() {
    requestAnimationFrame(draw);
    // Drawing code goes here
}
draw();

or

(function repeatOften() {
    // Do whatever
    requestAnimationFrame(repeatOften);
})();
```

requestAnimationFrame()函数会返回一个ID，可以给我们取消此操作。

```javascript
var globalID;

function repeatOften() {
  $("<div />").appendTo("body");
  globalID = requestAnimationFrame(repeatOften);
}

$("#start").on("click", function() {
  globalID = requestAnimationFrame(repeatOften);
});

$("#stop").on("click", function() {
  cancelAnimationFrame(globalID);
});
```


这个函数的执行频率取决于您的浏览器和计算机的帧速率，但通常为60fps（因为您的计算机显示器通常以60Hz的速率刷新）这里的主要区别是浏览器会计算一个合适的机会来绘制动画，而不是以预定的间隔。也有说法表示，浏览器可以根据负载，元素可视性（滚动视图）和电池状态来选择优化`requestAnimationFrame`的性能。`requestAnimationFrame`的另一个优点是它将所有的动画组合成一个单一的浏览器重绘。这节省了CPU周期，并使您的设备寿命更长。所以使用`requestAnimationFrame`可以使您的动画更加如丝般顺滑，与您的GPU同步，并减少更少的CPU。如果您浏览到新标签页，浏览器将会将动画调节为抓取动作，从而防止占用太多CPU资源。

因为这个API还比较新，所以在各个浏览器中还需要通过加前缀来达到兼容，具体兼容性可参考[ Can I Use...](http://caniuse.com/#feat=requestanimationframe)不过我们可通过引入下面
这个[polyfill](https://gist.github.com/paulirish/1579671)来简化我们的工作。

```javascript
   (function() {
    var lastTime = 0;
    var vendors = ['ms', 'moz', 'webkit', 'o'];
    for(var x = 0; x < vendors.length && !window.requestAnimationFrame; ++x) {
        window.requestAnimationFrame = window[vendors[x]+'RequestAnimationFrame'];
        window.cancelAnimationFrame = window[vendors[x]+'CancelAnimationFrame'] 
                                   || window[vendors[x]+'CancelRequestAnimationFrame'];
    }
 
    if (!window.requestAnimationFrame)
        window.requestAnimationFrame = function(callback, element) {
            var currTime = new Date().getTime();
            var timeToCall = Math.max(0, 16 - (currTime - lastTime));
            var id = window.setTimeout(function() { callback(currTime + timeToCall); }, 
              timeToCall);
            lastTime = currTime + timeToCall;
            return id;
        };
 
    if (!window.cancelAnimationFrame)
        window.cancelAnimationFrame = function(id) {
            clearTimeout(id);
        };
}());
```

### 三.参考文章
[requestAnimationFrame](http://creativejs.com/resources/requestanimationframe/)

[Using requestAnimationFrame](https://css-tricks.com/using-requestanimationframe/)