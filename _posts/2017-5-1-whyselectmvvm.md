---
layout:     	notebook
title:     	    传统Jquery VS Vue
description:     Vue这么火，我要入手吗？
author:     	許健彬
tags:      	     Vue
subtitle:     	 Vue这么火，我要入手吗？
category:     	project
key:             Vue
---


## 一.写在前面

最近几个月，因为周围同学都开始准备实习了，自己也心痒痒的。闲着没事的时候，也上拉勾网看了一番。看完后，惊呆了！我的心情是~~~~(>_<)~~~~WTF?居然好多实习都写着熟悉一门MVVM框架，这就有点尴尬了！对于平时都是用的Jquery+Template+BootStrap这一套开发的我，感觉有点虚呀！但是冷静想想，这也没啥，毕竟去到人家公司，都是人家用的什么就学什么，也不可能什么都学齐了再去工作把。所以，这不开始接触Vue了。其实早些时候就开始看了一些Vue的语法，也做了一点简单的demo，但是，没有深层次去了解这类MVVM框架的优点，有几次同学问到为什么要用Vue?他的好处是什么？都不怎么会答，只是能说出他是双向绑定，组件化开发等浅显的优点，但是对比Jqery，他究竟好在哪里呢？这几天查了一些资料，试着总结一下，但毕竟没有实际的项目支持，所以只能流于资料层面，理解也不深刻，也算是给自己留个坑，等以后项目中有了深层次的体会再回来填坑(*^__^*) 嘻嘻……


## 二.何为MVVM呢？

先来看下MVC模式与MVVM模式的对比
> #### MVC模式

  * 视图（View）              --- 用户界面
  * 控制器（Controller）      --- 业务逻辑
  * 模型（Model）             --- 数据保存
  
  各部分之间的通信方式如下：
<img src="{{ "/img/MVC.png " | prepend: site.baseurl }}" style="width:400px;height:350px;">

  * View --- 用户在View上进行操作——比如在文本框输入数值或是点击按钮，传送指令到 Controller
  * Controller  --- Controller处理这个动作，完成业务逻辑后，要求 Model 改变状态
  * Model --- 将新的数据发送到 View，用户得到反馈
  
-----------------------------------------------------------------------------

> #### MVVM模式

  * Model          --- 数据访问层
  * View           --- UI界面
  * ViewModel      --- 它是View的抽象，负责View与Model之间信息转换，将View的Command传送到Model
  
  各部分之间的通信方式如下：
<img src="{{ "/img/MVVM.png " | prepend: site.baseurl }}" style="width:100%;height:200px;">

  View与ViewModel连接的方式：
  * Binding Data      -----实现数据的传递
  * Command           -----实现操作的调用
  * AttachBehavior    -----实现控件加载过程中的操作
  
-----------------------------------------------------------------------------
  
简单来说，MVVM的核心就是提供对View和ViewModal的双向数据绑定，使得ViewModal状态改变可以自动传给View,即所谓的数据双向绑定。 

在以前的年代，MVC 作为Web 应用的最佳实践是OK 的，这是因为 Web 应用的View 层相对来说比较简单，前端所需要的数据在后端基本上都可以处理好，View 层主要是做一下展示，所以View
层相对来说比较轻量。但是随着HTML5等前端技术的发展，前端应用的复杂程度已不同往日，今非昔比。

> #### 这时前端开发就暴露出了三个痛点问题：

* 1、开发者在代码中大量调用相同的 DOM API, 处理繁琐 ，操作冗余，使得代码难以维护。 
* 2、大量的DOM 操作使页面渲染性能降低，加载速度变慢，影响用户体验。[高频dom操作和页面性能优化探索](https://feclub.cn/post/content/dom)
* 3、当 Model 频繁发生变化，开发者需要主动更新到View ；当用户的操作导致 Model 发生变化，开发者同样需要将变化的数据同步到Model 中，这样的工作不仅繁琐，而且很难维护复杂多变的数据状态。

其实，早期jquery 的出现就是为了前端能更简洁的操作DOM 而设计的，但它只解决了第一个问题，另外两个问题始终伴随着前端一直存在。

> #### MVVM 的出现，完美解决了以上三个问题

MVVM 由 Model,View,ViewModel 三部分构成，Model 层代表数据模型，也可以在Model中定义数据修改和操作的业务逻辑；View 代表UI 组件，它负责将数据模型转化成UI 展现出来，ViewModel 是一个同步View 和 Model的对象。

在MVVM架构下，View 和 Model 之间并没有直接的联系，而是通过ViewModel进行交互，Model 和 ViewModel 之间的交互是双向的， 因此View 数据的变化会同步到Model中，而Model 数据的变化也会立即反应到View 上。

ViewModel 通过双向数据绑定把 View 层和 Model 层连接了起来，而View 和 Model 之间的同步工作完全是自动的，无需人为干涉，因此开发者只需关注业务逻辑，不需要手动操作DOM, 不需要关注数据状态的同步问题，复杂的数据状态维护完全由 MVVM 来统一管理。
 
## 三.MVVM 之 Vue

> #### Vue的细节

Vue可以说是MVVM 架构的最佳实践，专注于 MVVM 中的 ViewModel，不仅做到了数据双向绑定，而且也是一款相对比较轻量级的JS 库，API 简洁，很容易上手。

Vue.js 是采用 Object.defineProperty 的 getter 和 setter，并结合观察者模式来实现数据绑定的。当把一个普通 Javascript 对象传给 Vue 实例来作为它的 data 选项时，Vue 将遍历它的属性，用 Object.defineProperty 将它们转为 getter/setter。用户看不到 getter/setter，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化。

* Observer 数据监听器，能够对数据对象的所有属性进行监听，如有变动可拿到最新值并通知订阅者，内部采用Object.defineProperty的getter和setter来实现。
* Compile 指令解析器，它的作用对每个元素节点的指令进行扫描和解析，根据指令模板替换数据，以及绑定相应的更新函数。
* Watcher 订阅者， 作为连接 Observer 和 Compile 的桥梁，能够订阅并收到每个属性变动的通知，执行指令绑定的相应回调函数。
* Dep 消息订阅器，内部维护了一个数组，用来收集订阅者（Watcher），数据变动触发notify 函数，再调用订阅者的 update 方法。

<img src="{{ "/img/Vueyuanli.png " | prepend: site.baseurl }}" style="width:100%;height:400px;">

从图中可以看出，当执行 new Vue() 时，Vue 就进入了初始化阶段，一方面Vue 会遍历 data 选项中的属性，并用 Object.defineProperty 将它们转为 getter/setter，实现数据变化监听功能；另一方面，Vue 的指令编译器Compile 对元素节点的指令进行扫描和解析，初始化视图，并订阅Watcher 来更新视图， 此时Wather 会将自己添加到消息订阅器中(Dep),初始化完毕。

当数据发生变化时，Observer 中的 setter 方法被触发，setter 会立即调用Dep.notify()，Dep 开始遍历所有的订阅者，并调用订阅者的 update 方法，订阅者收到通知后对视图进行相应的更新。

以上分析借鉴[@by 一像素](http://www.cnblogs.com/onepixel/p/6034307.html)

## 四.Jquery VS Vue

> #### Jquery优点
* 1 jQuery 良好地屏蔽了各浏览器之间的差异,解决了多浏览器兼容问题，优化JS
* 2 支持链式调用
* 3 封装了DOM操作和网络请求
* 4 插件生态庞大，插件书写简单开放
* 5 非常稳定，简洁优雅API很少变动
* 6 选择器方便易用，方便快捷操作DOM

> #### Vue优点

* 1.数据双向绑定

通过数据绑定链接View和Model，让数据的变化自动映射为视图的更新。
* 2.组件系统

在大型的应用中，为了分工、复用和可维护性，我们不可避免地需要将应用抽象为多个相对独立的模块。而应用类UI可以看作是全部由组件树构成的。
<img src="{{ "/img/zujian.jpg" | prepend: site.baseurl }}" style="width:100%;height:200px;">

* 3.基于构建工具的单文件组件格式

作者在基于webpack的loader API基础上开发了vue-loader插件，从而让我们可以用单文件格式（.Vue）来书写Vue组件。
通过单文件组件格式，把一个组件的模板，样式，逻辑三要素整合在同一个元素中，即方便开发，也方便复用和维护。另外，Vue本身支持对组件的异步加载，配合webpack的分块打包功能，可以极其轻松的实现组件的异步按需加载功能。
* 4.异步批量DOM更新

异步批量DOM更新：当大量数据变动时，所有受到影响的watcher会被推送到一个队列中，并且每个watcher只会推进队列一次。这个队列会在进程的下一个 “tick” 异步执行。这个机制可以避免同一个数据多次变动产生的多余DOM操作，也可以保证所有的DOM写操作在一起执行，避免DOM读写切换可能导致的layout。
* 5动画系统：

Vue.js提供了简单却强大的动画系统，当一个元素的可见性变化时，用户不仅可以很简单地定义对应的CSS Transition或Animation效果，还可以利用丰富的JavaScript钩子函数进行更底层的
动画处
理。
* 6.可扩展性：

除了自定义指令、过滤器和组件，Vue.js还提供了灵活的mixin机制，让用户可以在多个组件中复用共同的特性。

-----------------------------------------------------------------------------

在MVVM中，数据是核心。而jQuery则以DOM为核心。而DOM只是HTML在JS的世界的抽象，是一个很易变的东西。因此如果业务代码遍历选择器表达式会非常难维护。但不可否认，jQuery是操作DOM的王者，让我们操作DOM顺手拈来。但如果不让你操作DOM，不是更好吗？就像jQuery不让你用getElementById，getElementsByTagName, querySelecterAll，大家都不知道里面有多少坑，短短
几个字母$(expr)是背后sizzle选择器引擎1700行的实现！！！！jQuery其实是在用户代码与原生API中提供一层厚厚的粘合层，因此摸起来光溜溜。在MVVM中，DOM操作基本是水下运作了，mvvm把大量的dom封装在了内部，并进行了优化。由于VM与V之间的双向绑定，操作了VM中的数据（当然只能是监控属性），就会同步到DOM，我们透过DOM事件监控用户对DOM的改动，也会同步到VM。DOM隐形了。 


## 四.总结

其实，总的来说，如果项目比较简单，还是可以用Jquery+template那一套开发方式，但是项目如果比较复杂了， 涉及状态改变较多，这时候用MVVM绝对能少写很多代码，逻辑比较清晰，后期
维护比较容易。不然光对着Jquery那一大串选择器，你就会很烦的。而且用这种MVVM框架还有模块化和组件的复用的优点。其实看完那么多资料，还不如撸上一个demo去体会体会比较实际，印象也比较深刻。





















