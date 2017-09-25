---
layout:     	notebook
title:     	    秋招第一站
description:    广州凡科互联网科技股份有限公司面试总结
author:     	許健彬
tags:      	    Interview
subtitle:     	广州凡科互联网科技股份有限公司面试总结
category:     	project
key:            Interview1
---

　　今天是我们学校的大型招聘会，在消息通知下来的时候就查了一下，总共三家前端招聘公司，从拉勾网上的招聘信息来看，是招javaweb方向的，所以就没打算去投简历。然后今天听
到消息说不同部门貌似要求不同，而且招聘现场也没给出招聘具体技能要求，遂去凡科公司面试了一下。听说凡科公司挺热闹的，所以想等晚一点再过去，最后11点左右过去，等到12点
35分那招聘方说叫我们先去吃饭了，当时已经站到头昏脑胀了，因为早餐都没吃。

　　这里我想吐槽一下一些同学，本来人就多，而且大家都是大学生，最起码应该懂得排队的规则吧？最搞
笑的是排着排着队，就在我前面无缘无故多了两位没见过的插在前面了.......这素质，虽然后面一位朋友说叫我跟他们说一下，但是我性格不喜欢这样子，既然他们做出这样子行为了，
肯定不管别人说什么。
 
　　不说那些了，还是聊聊面试吧，本来以为只是HR面试，没想到是技术面试，而且一点多的时候，感觉很困（平常都不会，考前综合症(●'?'●)，没想到这么怂，哈哈,，然后面试的时
候很热，又紧张，果不其然，自己感觉是个表现得极差的面试，不过还是从面试官那里学到了一些知识，所以记录下来，给大家分享分享，也不是很难的东西，哎，一紧张头脑一片空白。


### 面试题总结(凭记忆重述，面试的时候是看着简历的点问的)



> #### 1 , 如何开启`GPU`渲染

为什么要开启：

  * 1 GPU上的复合页面图层可以在涉及大量像素的绘图和合成操作中实现比CPU（无论是在速度和功耗方面）还要好的效率;
  * 2 GPU上的页面不需要进行昂贵的回流操作
  * 3 CPU和GPU之间的并行性，可以同时运行以创建高效的图形管道。

浏览器渲染的流程:

  * 1, 获取Dom并将其分割为多个渲染层（RenderLayer(负责 DOM 子树)，GraphicsLayer(负责 RenderLayer 的子树）;
  * 2, 将每个层栅格化，并独立的绘制进位图中;
  * 3, 将这些位图作为纹理（从主存储器移动到图像存储器的位图图像,上传GPU）;
  * 4, 复合多个层来生成最终的屏幕图像;

translate3d就是给元素创建自己的layer，这样就可以避免更改样式时间造成的重绘，并且能通过GPU渲染这也是css动画原理。

创建自己layer的元素或属性:

	  * 1, 3D 或透视变换(perspective transform) CSS 属性
	  * 2, 使用加速视频解码的 <video> 元素
	  * 3, 拥有 3D (context-mode为webgl) 上下文或加速的 2D 上下文的 <canvas> 元素(未查到相关资料)
		  [concept-canvas-context-mode](https://html.spec.whatwg.org/multipage/canvas.html#concept-canvas-context-mode)
	  * 4, 混合插件(如 Flash) <embed>
	  * 5, 对自己的 opacity 做 CSS 动画或transition和transform进行动画变换的元素
	  * 6, 拥有accelerated CSS filters的元素(测试chrome 61.0.3163.100并无效)
	  * 7, 元素有一个包含复合层的后代节点(换句话说，就是一个元素拥有一个子元素，该子元素在自己的层里)(测试chrome 61.0.3163.100并无效)
	  * 8, 元素有一个 z-index 较低且包含一个复合层的兄弟元素(换句话说就是该元素在复合层上面渲染),(测试chrome 61.0.3163.100并无效)


  虽然层是有用的，但创建过多的层会为整个图形堆栈(graphics stack)增加额外的开销。时刻记得提醒自己！

参考资料:

* [Javascript高性能动画与页面渲染](http://www.infoq.com/cn/articles/javascript-high-performance-animation-and-page-rendering)

* [https://www.html5rocks.com/zh/tutorials/speed/layers/](https://www.html5rocks.com/zh/tutorials/speed/layers/)

* [GPU Accelerated Compositing in Chrome](https://www.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome)



> #### 2 , `SEO` 优化

从重构方向谈SEO: 

  * 1, TDK优化(title,description,keywords)
  * 2, 注意结构清晰，如果条件允许（如移动端，兼容ie9+，如果ie8+就针对ie8引入html5.js,,实用HTML5语义化标签，如header,footer,section,aside,nav,articles;
  * 3，优化url，越短越好，避免过多参数，减少目录层次，目录层次具有描述性，url包含关键词（中文除外,，字母小写，连词符用-；
  * 4，img 设置alt属性，超链接加上title，指向其他网站的连接要加上rel="nofollow" 避免小蜘蛛去爬取一些无意义的链接或者垃圾连接。表格加上标题caption;
  * 5，注意标签权重问题，strong em 与 b i标签对应效果一致,但是权重不同。strong b加粗，em i 斜体效果,如果只是需要效果就用后者,不然会影响seo，
       因为加重的关键字并不是你所需要的

从建站方向谈SEO(扩展):

  * 1, 使用robots.txt，网络爬虫排除标准”（Robots Exclusion Protocol,，网站通过Robots协议告诉搜索引擎哪些页面可以抓取，哪些页面不能抓取。
  * 2，sitemap站点地图

> #### 3 , canvas鼠标移动画S

    ···javascript
	    <!DOCTYPE html>
		<html lang="en">

		<head>
			<meta charset="UTF-8">
			<title>鼠标拖拽画S</title>
			<style>
			html,
			body {
				margin: 0;
				padding: 0;
				width: 100%;
				height: 100%;
			}

			#myCanvas {
				display: block;
				border: 1px solid #ea0773;
				margin: 20px auto;
			}
			</style>
		</head>

		<body>
			<canvas id="myCanvas"  width="400px" height="400px">
				您的浏览器不支持H5 canvas,请更换或更新浏览器以体验!
			</canvas>
			<script>
			// window.addEventListener('load',canvasDraw,false);

			function canvasSupport(e) {
				return !!e.getContext;
			}

			window.onload = function() {
				var myCanva = document.getElementById('myCanvas'),
					myCanvasLeft = myCanva.offsetLeft,
					myCanvasTop = myCanva.offsetTop;
				if (!canvasSupport(myCanva)) {
					return;
				}
				var ctx = myCanva.getContext('2d');

				myCanva.onmousedown = function(event) {
					var e = event || window.event;
					ctx.moveTo(e.clientX - myCanvasLeft, e.clientY - myCanvasTop);
					document.onmousemove = function(event) {
						var e =  event || window.event;
						ctx.lineTo(e.clientX - myCanvasLeft, e.clientY - myCanvasTop);
						ctx.stroke();
					}
					myCanva.onmouseup = function(event) {
						document.onmousemove = null;
						document.onmouseup = null;
					}
				}
			}
			</script>
		</body>

		</html>
    ```


   > 注意canvas 宽高设置的问题，必须写在canvas标签内，并且按指定格式书写。因为canvas标签里的宽高是相当于定义画布的大小（默认宽300px，高150px）。
     在定义了画布之后，canvas就相当于一张图片了，类似于img，所以这个时候再设置宽高，就会把canvas拉伸成style里设置的宽高了。
	
> #### 4 , 如何优化`Dom`性能

   * 1, 缓存Dom查询完成后使用变量缓存DOM（查询Dom非常耗时)

		HTMLCollection与NodeList区别
		HTMLCollextion是动态的，页面节点变化后，引用即更新 （getElementsByTagName)
		NodeList跟HTMLCollextion 一样也是动态的 （childNodes)
		特例 通过querySelectorAll取到的NodeLis为静态的

   * 2, 批量操作Dom
	    * 方法：先用字符串拼接完成，再用innerHTML更新Dom
	    * 原因： 操作DOM非常耗时，而且可能发生重复渲染

   * 3, 在内存中操作DOM 
        * 方法: 使用DocumentFragment对象 （测试中利用这个对象在chrome中动态添加dom元素所有的时间比一般直接添加更耗时，而在火狐中略微比一般直接添加好一点)
        * 原因 让DOM操作发生在内存中，而不是页面上，避免频繁访问DOM

   * 4, DOM读写分离
        * 方法：修改DOM动作与访问DOM分开批量进行
        * 原因：浏览器具有惰性渲染机制，读取DOM会触发浏览器的一次渲染

   * 5, 使用事件代理
        * 方法：将事件监听注册在父级元素上
        * 原因：减少内存占用，能捕获到动态添加的节点事件

> #### 5 , Vue组件间通信

子组件传递数据给父组件
    
  * 1, 官网给的例子是通过新的Vue实例（在main.js），来`eventBus`来传递数据,eventBus.$emit,然后父组件eventBus.$on去接收。
     [其他资料](https://cn.vuejs.org/v2/guide/migration.html?#dispatch-和-broadcast-替换)
	 
  * 2, 我的做法是：因为在每个子组件中都会生成一个VueComponent实例，并且里面的`$root`存的是主的Vue实例，也就是
     main.js里面实例化的实例，所以我就通过主实例来传递数据，this.$root.$emit('cartAdd', event.target);在调用的组件的created
     里面this.$root.$on('cartAdd', this._drop)，_drop是用来调用另外一个子组件的方法。
	 
  * 3, 另外一种方法是直接利用子组件实例派发this.$emit('cartAdd', event.target);然后在组件标签  <cartcontrol :food="food" @cartAdd='_drop'></cartcontrol> 
	 里面声明即可在对应方法拿到数据


> #### 6 , base64如何从16位存储变为8位存储（这道题不会，当时也没听多大清楚）

  当时面试官问题的场景是问我怎么进行图片预览，我说H5 File API 或者canvas都可以转换成base64，然后他就问了编码问题，好像是base64上传后台问题，
  
  一般上传给后台都是表单上传的，直接就可以把文件对象上传给后台了，如果想把base64给后台，也可以通过API转成blob或者File对象，然后用formData上传后台。
  
  > [base转为File](https://stackoverflow.com/questions/35940290/how-to-convert-base64-string-to-javascript-file-object-like-as-from-file-input-f)


### 文末寄语
　　好了，总结一下这次的面试真的表现得非常烂，应该也是挂了的！不过还是感谢面试官让我看到自身的一些问题，让我能查漏补缺。

　　是时候重新出发了，希望下次能给大家带来好消息，也祝大家周末愉快！