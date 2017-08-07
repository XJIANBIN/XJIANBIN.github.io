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

本文是学习Vue源码的笔记，如果想要详细了解，可查阅参考资料或自行阅读源码。

### 二 模板渲染

Vue 2.0 中模板渲染与 Vue 1.0 完全不同，1.0 中采用的 DocumentFragment，而 2.0 中借鉴 React 的 Virtual DOM。基于 Virtual DOM

render function 顾名思义就是渲染函数，这个函数是通过编译模板文件得到的，其运行结果是 VNode。

> 渲染过程

* 1, `new Vue()`：实例化Vue   

* 2, `$mount()`: 获取模板,并且在这过程中通过调用相关方法_count（new Watcher()实现数据响应式，当Watcher监听到数据变化,就会执行reder函数输出一个新的 VNode 树形结构的数据（VNode对象即Virtual DOM）[`_mount方法源码`](https://github.com/vuejs/vue/blob/v2.1.10/src/core/instance/lifecycle.js#L64-L66)?

* 3, `compileToFunction()`： 将 template 编译成 render 函数。首先读缓存（在compileToFunction()中,会创建一个对象，把complice编译完后的对象的render 和 staticRenderFns 两个属性分别转换成函数缓存在对象中，然后把对象存进缓存（但是笔者有一点迷惑就是根据官网[`模板编译`](https://cn.vuejs.org/v2/guide/render-function.html#模板编译)compile编译完的对象属性已经是方法了，不是字符串，但是源码中确实返回的是字符串，根据[`generate`](https://github.com/vuejs/vue/blob/v2.1.10/src/compiler/codegen/index.js#L22-L47)可看出），没有缓存就调用 compile 方法拿到 render function 的字符串形式，在通过new Function 的方式生成真正的渲染函数

* 4, `compile`：将 template 编译成 render 函数的字符串形式,这个函数主要有三个步骤组成：parse，optimize 和 generate，最终输出一个包含 ast，render 和
staticRenderFns的对象。compile 函数主要是将 template 转换为 AST，优化 AST，再将 AST 转换为 render function字符串,render function 与数据通过 Watcher 产生关联。

* 5,  `update()`：update判断是否首次渲染，是则直接创建真实DOM,否则调用patch(),并且进行触发钩子和更新引用等其他操作。[`参考`](https://github.com/vuejs/vue/blob/v2.1.10/src/core/instance/lifecycle.js#L77-L114)

* 6,  `patch()`：新旧 VNode 对比的 diff 函数,对两个树结构进行完整的diff和patch的过程，最终只有发生了变化的节点才会被更新到真实 DOM 树上。

* 7,  `destroy()`：完全销毁一个实例。清理它与其它实例的连接，解绑它的全部指令及事件监听器。触发 beforeDestroy 和 destroyed 的钩子。在大多数场景中你不应该调用这个方法。最好使用 v-if 和 v-for 指令以数据驱动的方式控制子组件的生命周期。 [`查看源码`](https://github.com/vuejs/vue/blob/v2.1.10/src/core/instance/lifecycle.js#L167)

> 当数据发生变化，会执行Watcher中的_update函数,_update会执行这个渲染函数，输出一个新的VNode对象，然后调用patch函数，拿这个新的 VNode 与旧的 VNode 进行对比(diff，只有发生了变化的节点才会被更新到真实 DOM 树上。

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

 * 2 [Vue源码探究二 从 $mount 讲起，一起探究Vue的渲染机制](https://segmentfault.com/a/1190000009467029)
