---
layout:     	notebook
title:     	    什么是JS为单线程及单线程如何运作？
description:    JS单线程原理
author:     	許健彬
tags:      	    JS single thread 
subtitle:     	JS单线程原理
category:     	project
key:            jssinglethread
---


###本文概要

* 1 线程与进程
* 2 为什么JS是单线程呢？
* 3 一个程序理解JS单线程
    *JavaScript是单线程的，同一时刻只能执行特定的任务。而浏览器是多线程的。一个浏览器至少实现三个常驻线程：1 javascript引擎线程，2GUI渲染线程 3浏览器事件触发线程 4Http请求线程。异步任务（各种浏览器事件、定时器等）都是先添加到“任务队列”（定时器则到达其指定参数时）。当Stack栈（JS主线程）为空时，就会读取Queue队列（任务队列）的
     第一个任务（队首），然后执行。
* 4 setTimeout知识扩展
* 5 参考文章


### 一 线程与进程
      进程和线程都是操作系统的概念。进程是应用程序的执行实例，每一个进程都是由私有的虚拟地址空间、代码、数据和其它系统资源所组成；进程在运行过程中能够申请创建和使用系统资源（如独立的内存区域等），这些资源也会随着进程的终止而被销毁。而线程则是进程内的一个独立执行单元，在不同的线程之间是可以共享进程资源的，所以在多线程的情况下，需要特别注意对临界资源的访问控制。在系统创建进程之后就开始启动执行进程的主线程，而进程的生命周期和这个主线程的生命周期一致，主线程的退出也就意味着进程的终止和销毁。主线程是由系统进程所创建的，同时用户也可以自主创建其它线程，这一系列的线程都会并发地运行于同一个进程中。

### 二 为什么JS是单线程呢？
      显然，在多线程操作下可以实现应用的并行处理，从而以更高的CPU利用率提高整个应用程序的性能和吞吐量。特别是现在很多语言都支持多核并行处理技术，然而JavaScript却以单线程执行，为什么呢？

      其实这与它的用途有关。作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。若以多线程的方式操作这些DOM，则可能出现操作的冲突。假设有两个线程同时操作一个DOM元素，线程1要求浏览器删除DOM，而线程2却要求修改DOM样式，这时浏览器就无法决定采用哪个线程的操作。当然，我们可以为浏览器引入“锁”的机制来解决这些冲突，但这会大大提高复杂性，所以 JavaScript 从诞生开始就选择了单线程执行。

      JavaScript是单线程的，在某一时刻内只能执行特定的一个任务，并且会阻塞其它任务执行。那么对于类似I/O等耗时的任务，就没必要等待他们执行完后才继续后面的操作。
	  在这些任务完成前，JavaScript完全可以往下执行其他操作，当这些耗时的任务完成后则以回调的方式执行相应处理。这些就是JavaScript与生俱来的特性：异步与回调。

      当然对于不可避免的耗时操作（如：繁重的运算，多重循环），HTML5提出了WebWorker，它会在当前JavaScript的执行主线程中利用Worker类新开辟一个额外的线程来加载和运行特定
	  的JavaScript文件，这个新的线程和JavaScript的主线程之间并不会互相影响和阻塞执行，而且在WebWorker中提供了这个新线程和JavaScript主线程之间数据交换的接口：postMessage
	  和onMessage事件。但在HTML5 Web Worker中是不能操作DOM的，任何需要操作DOM的任务都需要委托给JavaScript主线程来执行，所以虽然引入HTML5WebWorker，但仍然没有改线
	  JavaScript单线程的本质。	
	  
### 三 分析程序

```javascript
       function foo() {
           console.log("first");
           setTimeout(( function(){
               console.log( 'second' );
           }),5);
       }
        
       for (var i = 0; i < 1000000; i++) {
           foo();
       }
```
执行结果会首先全部输出first，然后全部输出second；尽管中间的执行会超过5ms。

这是为什么呢？？？
  JS运行在浏览器中，是单线程的，每个window一个JS线程，既然是单线程的，在某个特定的时刻只有主线程的执行栈的代码能够被执行，并阻塞其它的代码。浏览器中很多行为是
  异步（Asynchronized）的，如onclick,setTimeout,ajax等。这种会由浏览器相应模块处理并放入任务队列中。只有当主线程中执行栈为空的时候（即同步代码执行完后），
  才会进行事件循环（event loop）来观察任务队列(很多童鞋搞不清，如果js是单线程的，那么谁去轮询大的Event loop事件队列？答案是浏览器会有
  单独的线程去处理这个队列。)，当事件循环检测到任务队列中有需要执行的任务就取出相关回调放入执行栈中由主线程执行。javascript引擎是单线程处理它的任务队列。
  所以当多个事件触发时，会依次放入队列,然后一个一个响应。

	  	  
### setTimeout知识扩展

扩展小知识：setTimeout()方法即使时间设置为0，也会有16毫秒的延迟，在火狐下貌似会有0毫秒的延迟，大部分还是16毫秒，对于JavaScript来说，由于new Date()只需要得到毫秒值，
并不需要过高的精度，因此最合理的方法当然是调用GetTickCount()来取值，而不是采QueryPerformanceCounter() 配合 QueryPerformanceFrequency()来得到高精度的时间值。
由于GetTickCount()自身的限制，JavaScript的new Date()得到的时间隔是16ms精度的。——对于win98或其它系统来说，这个值可能是58ms/10ms。对于定时器来说，JavaScript应该会是
使用timeSetEvent()来做定时器。因为如果用线程＋sleep()会存在较大的开销（事实上我出观察到线程计数是没有变化的），而采用SetTimer()会存在消息队列的问题。所以采用timeSetEvent()是最实际的方案。——至于16个timeSetEvent()的限制，可以通过同一个timeSetEvent()中处理多个例程来规避。因此，由于timeSetEvent()自身在缺省状态下的限制，因此导致了16m时间间隔。
详细深层次的原因可以看以下文章
1[JavaScript时钟间隔的问题～](http://blog.csdn.net/aimingoo/article/details/1449258)
2[再谈JavaScript时钟中的16ms精度问题．](http://blog.csdn.net/aimingoo/article/details/1451556)



###本文参与文章

1 [细说JavaScript单线程的一些事](http://www.codeceo.com/article/javascript-threaded.html)此文下方参考文章也有借鉴意义
2 [阮一峰老师：JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)
3  [Javascript是单线程的深入分析](http://www.cnblogs.com/Mainz/p/3552717.html)



















