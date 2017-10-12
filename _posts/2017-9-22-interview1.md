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

  * 1, 获取Dom并将其分割为多个渲染层RenderLayer(负责 DOM 子树)，GraphicsLayer(负责 RenderLayer 的子树）;
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

* [实现达到 60FPS 的高性能交互动画](https://zhuanlan.zhihu.com/p/29729996?group_id=896857362130960384) 

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
    
  * 1, 官网给的例子是通过新的Vue实例（在main.js），来`eventBus`来传递数据,eventBus.`$emit`,然后父组件eventBus.`$on`去接收。
     [其他资料](https://cn.vuejs.org/v2/guide/migration.html?#dispatch-和-broadcast-替换)
	 
  * 2, 我的做法是：因为在每个子组件中都会生成一个VueComponent实例，并且里面的`$root`存的是主的Vue实例，也就是
     main.js里面实例化的实例，所以我就通过主实例来传递数据，this.`$root`.`$emit`('cartAdd', event.target);在调用的组件的created
     里面this.`$root`.`$on`('cartAdd', this._drop)，_drop是用来调用另外一个子组件的方法。
	 
  * 3, 另外一种方法是直接利用子组件实例派发this.`$emit`('cartAdd', event.target);然后在组件标签  <cartcontrol :food="food" @cartAdd='_drop'></cartcontrol> 
	 里面声明即可在对应方法拿到数据


> #### 6 , base64如何从16位存储变为8位存储（这道题不会，当时也没听多大清楚）

  当时面试官问题的场景是问我怎么进行图片预览，我说H5 File API 或者canvas都可以转换成base64，然后他就问了编码问题，好像是base64上传后台问题，
  
  一般上传给后台都是表单上传的，直接就可以把文件对象上传给后台了，如果想把base64给后台，也可以通过API转成blob或者File对象，然后用formData上传后台。
  
  > [base转为File](https://stackoverflow.com/questions/35940290/how-to-convert-base64-string-to-javascript-file-object-like-as-from-file-input-f)


### 文末寄语
　　好了，总结一下这次的面试真的表现得非常烂，应该也是挂了的！不过还是感谢面试官让我看到自身的一些问题，让我能查漏补缺。

　　是时候重新出发了，希望下次能给大家带来好消息，也祝大家周末愉快！

### 2017.9.27 续更
　　昨天收到了凡科公司的面试邀请，今天就到了公司面试了，总的来说感觉挺好的，面试官也好客气的一说，因为我笔试题比另外一个同学先交，而那个同学先面试了，面试官
我一进去就跟我say不好意思，哈哈，真的是太可爱！我面试完出来还跟我说了无数句不好意思，我跟他解释说，没关系，另外一个是我同学，是很小的事，我也不知道有这个顺序
，而且技术人哪有那么小气（哈哈 ，我很善良的，原谅我吹一波，那是嘛，我觉得这没关系的），但是面试官的态度还是让我很钦佩，很喜欢。现在回想一下，当时我进去面试官
看着手机，不会是在看我博客吧？然后不会看到这篇文章开头？以为我很介意排队问题吧！(●'◡'●)，哇塞，细思极恐！哈哈开玩笑的！
　　
  > #### 1 ,XSS
  
   三种类型
   * 1, 存储型XSS,数据库中存有存在XSS攻击的代码，返回给客户端，若客户端不进行转义直接输出给浏览器渲染，就有可能导致XSS攻击
   * 2, 反射型XSS，关键点仍然是在通过url控制了页面的输出，需要欺骗用户自己去点击链接才能触发XSS代码（服务器中没有这样的页面和内容），一般容易出现在搜索页面。（反射型xss实际上是包括了dom - xss了，只因为输出地点不同而导致结果不一致）
   * 3, DOM-XSS, 例如 
   
```javascript
	<img src="0" onerror="alert(0)">
```
	 注：反射型xss和dom-xss都需要在url加入js代码才能够触发。
	 
详细案列可看
    [跨站的艺术 - XSS Fuzzing 的技巧](https://juejin.im/post/58ddcf70ac502e006399d75d)
  
[如何让前端更安全？——XSS攻击和防御详解](https://mp.weixin.qq.com/s/6ChuUdOm7vej8vQ3dbC8fw)
  
[避免XSS攻击](https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/09.3.md)
	
[浅谈WEB安全性（前端向）](http://www.cnblogs.com/vajoy/p/4176908.html)
	
[HtmlEncode和JavaScriptEncode（预防XSS）](http://www.cnblogs.com/lovesong/p/5211667.html)
	
[一个前端DOMXSS过滤器](https://www.leavesongs.com/PENETRATION/javascript-domxss-filter.html)
	
[Web前端攻防，一不小心就中招了](https://juejin.im/post/5912740344d904007b010252)
	
[浅谈XSS攻击的那些事（附常用绕过姿势）](https://zhuanlan.zhihu.com/p/26177815?utm_source=weibo&utm_medium=social)
	
[过滤库](https://github.com/cure53/DOMPurify)
	
[转义库](https://github.com/sindresorhus/escape-goat)
	
  > #### 2，CSS兼容性
  
  css hack:利用浏览器自身的bug来实现特定浏览器的样式
  
  * 1, 图片间隙 
   在div中插入图片，图片会将div下方撑大3px。hack1：将<div>与<img>写在同一行。hack2：给<img>添加display：block；dt li 中的图片间隙。hack：给<img>添加display：block；hack3:给div加上font-size:0;	
  * 2, IE hack
   ```html
     .ie6_7_8{
       color:blue; /*所有浏览器*/
       color:red\9; /*IE8以及以下版本浏览器*/
       *color:green; /*IE7及其以下版本浏览器*/
       _color:purple; /*IE6浏览器*/
   }
   ```
  参考资料
  
  > [也谈兼容性——通用hack方法/CSS兼容方案/js兼容方案全推送](https://zhuanlan.zhihu.com/p/25123086)
  
  > [CSS常见兼容性问题总结](http://www.cnblogs.com/imwtr/p/4340010.html)
   
### 2017.9.28 
　　今天和我一同面试的同学收到了offer了，我等到了下午，没有任何消息。说实话，非常伤心和失望，面试的时候自我感觉良好，面试官也和蔼，给了我极大的信心，最终结果如此
，心里难免有点难以接受。想了一下，面试过程中答得不好的点有css与js位置为何要一个头部和一个尾部，我干脆地答了一个减少页面白屏时间，但是我当时脑子奔溃，想了好久还是
想不出为什么，可能面试官从这里就对我没了好感吧，然后面试官旁敲侧击了几下，最终我回答出了js线程与UI线程互斥的概念。然后还有一个XSS问题也回答得比较不好，因为这个平
时没关注，虽然看了一下文章，但是印象还是不是很深刻，毕竟没有实践过，最后面试官说他们团队是搞H5的，我随口说了没关系，我能接受，因为我以前做过一个校庆的H5的，在我的
概念中可能H5就是翻页加上CSS3动画。而且面试官问了平时有用chrome控制台做什么性能方面的事情吗，我说了我懂得看performance，我觉得这是一个加分点，我确实花过时间在这上
，而且一般的新手应该也很少会这个。以上就是我总结的面试过程中的不足。我感觉面试官对我还是蛮满意的，不知道是不是我的错觉。最后结果是这样子的。心里有点难受。可能就是
期望过大，伤心也就多大吧！心里还是很遗憾，因为不知道自己到底是哪里出问题了，好想能跟面试官聊一聊，让我知道自己哪里的不足，不过这应该是不可能的。人生有时候就是这么
遗憾。感觉面试都是说刷经验，但是一般面试完不过就不理你了（我很讨厌这种面试流程），这样对大家帮助只是停留在知识的补充而已，并不知道自己的问题!

### 2017.9.29
　　今天又等了一天，昨天晚上朋友说就算不过也会通知我的，叫我再等等看，我还是怀着希望等了一天，就算等来的是不适合的通知！可惜最后什么也没等到。这种感觉确实很难受。
现在应该说是彻底失望了。对这家公司的感到失望，因为给我感觉对面试者不友好，因为这家公司算是比较大了，上市公司，从我同学的经历貌似其他公司（不大的）是会给回复的即
使不过。这样的公司，能赢得我的尊重！至少为别人着想！我觉得，至少给个面试结果没那么难吧？群发个邮件很难吗？或者你们确实是很忙吧，但是如果你们能站在面试者的角度考
虑一下，哎，算了，没关系，这不怪你们！是我们没缘分，只能祝福贵司越来越大，越来越好！

　　平时总是安慰别人，最近自己的心情却没办法很快的调节，感觉自己还是太弱了。文章应该到这里就告一段落了，给我了一个极大的反省，同时也给我极大的动力。总结问题，继续
前进，我肯定不会倒下的，这是给我自己的激励！加油！

