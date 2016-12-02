---
layout:     	notebook
title:     	display:none和visibility:hidden的区别！
description:     测试	
author:     	許健彬
tags:      	 css  
subtitle:     	 希望对你有帮助！
category:     	project
key:          css_none
---

## 本文主要介绍display和visibility的区别,也作为作者的学习笔记，希望对你有帮助！

------
	
我们先看一段代码吧，可复制运行试试看(改代码是实现鼠标放上去把照片上面遮罩层一块一块去掉)，如果不想看可以直接跳过代码段直接看结果。

要求特效：
<img src="{{ "/img/normal.gif " | prepend: site.baseurl }}" style="width:400px;height:200px;">

> * CSS

```css

    <style>

        * {
            margin: 0;
            padding:0;
        }

        #div1 {
            width: 517px;
            height: 600px;
            background: url("pic.jpg") no-repeat center;
            z-index: 1;
        }

        #div2 {
            width: 517px;
            height: 600px;
            position: absolute;
            top:0;
            left: 0;
        }

        #div2 .div3 {
            width: 51.7px;
            height: 60px;
            background-color: #eeeeee;
            float: left;
            z-index: 2;
        }
    </style>
		
```

> * html

```html

   <div id="div1"></div>
   <div id="div2"></div>

```

> * js代码

```javascript

     var arrDiv = div2.getElementsByClassName('div3');
            Array.prototype.forEach.call(arrDiv,function (demo) {
                demo.onmouseover=function (e) {
                    demo.style.display="none";
                }
            });
			
```

	
代码特效：
<img src="{{ "/img/different.gif " | prepend: site.baseurl }}" style="width:400px;height:200px;">

代码乍一看，是没有任何问题的吧，可是为什么是这种效果呢？

^_^O(∩_∩)O哈哈~原来都是那个display:none惹的锅！

> 1. display:none是把一整块div取消了，不占空间，而visibility:是不可见但是占据那块空间的。因为我的遮罩层小div是浮动的，所以当前面的div消失，会自动补上，然后一个个
触发onmouseover事件，自然就出现了上面的那种效果，这种效果挺好的，刚好最近在做一个图片墙，刚好用来增加特效。

> 2. 上述把display:none改为visibility:hidden，或者opacity：0.1就可以达到目的效果了！
