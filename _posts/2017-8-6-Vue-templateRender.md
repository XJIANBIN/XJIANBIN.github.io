---
layout:     	notebook
title:     	    Vue源码：模板渲染
description:    基于Vue2.0源码
author:     	許健彬
tags:      	    Vue
subtitle:     	基于Vue2.0源码
category:     	project
key:            Vue_templateRender
---
 
### 一 写在前面

本文是学习Vue源码的笔记，如果想要详细了解，可查阅参考资料或源码自信阅读。
	
### 二 模板渲染
  
Vue 2.0 中模板渲染与 Vue 1.0 完全不同，1.0 中采用的 DocumentFragment，而 2.0 中借鉴 React 的 Virtual DOM。基于 Virtual DOM
  
render function 顾名思义就是渲染函数，这个函数是通过编译模板文件得到的，其运行结果是 VNode。
 
> 渲染过程

  ------------ | -------------
  1  new Vue()                 |          
  2  $mount()                  |      获取模板,并且在这过程中通过调用相关方法_count（new Watcher()实现数据响应式）
  3  compileToFunction()       |      将 template 编译成 render 函数。首先读缓存，没有缓存就调用 compile 方法拿到 render function 的字符串形式，在通过new 
                                       Function 的方式生成真正的渲染函数
  4  compile                   |      将 template 编译成 render 函数的字符串形式,这个函数主要有三个步骤组成：parse，optimize 和 generate，最终输出一个包含 ast，render 
                                       和staticRenderFns 的对象。compile 函数主要是将 template 转换为 AST，优化 AST，再将 AST 转换为 render function；
                                       render function 与数据通过 Watcher 产生关联；                              
  5  new Watcher()              |       这一步 
  6  update()                   |       _update 函数会执行这个渲染函数，输出一个新的 VNode 树形结构的数据（VNode对象即Virtual DOM）。 
  7  patch()                    |      新旧 VNode 对比的 diff 函数,对两个树结构进行完整的duff和patch的过程，最终只有发生了变化的节点才会被更新到真实 DOM 树上。
  8  destroy()                  |     销毁对象
  
  
  当数据发生变化，会执行Watcher中的_update函数,_update会执行这个渲染函数，输出一个新的VNode对象，然后调用patch函数，拿这个新的 VNode 与旧的 VNode 进行对比(diff)，只有发生了变化的节点才会被更新到真实 DOM 树上。
  
### 三 独立构建 与 运行时构建

  区别在于前者包含模板编译器而后者不包含。
  模板编译器的职责是将模板字符串编译为纯JS的渲染函数，如果你想要在组建中使用temolate选项，你就需要编译器。
  
  * 1 独立构建包含模板编译器并支持template选项，它也依赖浏览器的接口的存在，所以你不能用来为服务端渲染
  
  * 2运行时构建不包含模板编译器，因此不支持template选项，只能用render选项，但即使使用运行时构建，在单文件组建中也依然可以写模板，因为单位件组件的模板会在构建时预编译
    为render函数，运行时构建比独立构建要轻量30%。
  
  > 在$mount的不同：
  
  运行时构建的$mount函数:
  
```javascript
  
     Vue.prototype.$mount = function (
     el?: string | Element,
     hydrating?: boolean
     ): Component {
     el = el && inBrowser ? query(el) : undefined
     return this._mount(el, hydrating)
     }
	 
```
  
  而独立构建的 $mount函数，会先用一个临时变量mount保存上面的$mount方法然后重写$mount函数，这时，调用$mount就会包括模板编译功能了
  
```javascript
        var mount = Vue$2.prototype.$mount;
        Vue$2.prototype.$mount = function (el, hydrating) {
       ...省略代码（里面为模板编译器入口）...
       return mount.call(this, el, hydrating)
       };   
```
   
  不管独立构建还是运行时构建，都会调用 vm._mount方法：此方法内会调用 new Watcher()主要是将模板与数据建立联系,是模板渲染和数据之间的纽带。
  
  > 注意:参考资料中运行时构建$mount函数可在[这里](https://github.com/vuejs/vue/blob/v2.1.7/src/entries/web-runtime.js)查看，里面指向的是独立构建函数。
  
### 四 参考资料

 * 1 [Vue2.0 源码阅读：模板渲染](https://zhouweicsu.github.io/blog/2017/04/21/vue-2-0-template/)
 
 * 2 [【Vue源码探究二】从 $mount 讲起，一起探究Vue的渲染机制](https://segmentfault.com/a/1190000009467029)
 