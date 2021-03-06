---
layout:     	notebook
title:     	    JS 闭包问题 与for循环
description:    闭包问题 与for循环
author:     	許健彬
tags:      	    JS
subtitle:     	For loop and Closures
category:     	project
key:            forandClosures
---

## 前记

最近在一些学习群里看到了对闭包问题的讨论，于是乎自己查了一些资料加深对闭包问题的理解，但是越查越蒙，也发现很多答案回答总是不对题，所以想自己写下这篇文章，
一方面可以给让大家快速学习，不用去看那么多不恰当的回答，浪费时间，这也是我写博客分享的初衷。另一方面方便自己以后查阅！以下正文开始？吃瓜群众椅子快拿出来了lalala.....


## 闭包

> 1. 闭包是指那些能够访问独立(自由)变量的函数 (变量在本地使用，但定义在一个封闭的作用域中)。换句话说，这些函数可以“记忆”它被创建时候的环境。这是MDN上的解释。
   
  说下我自己的理解，因为JS存在函数作用域的概念，不可以对作用域进行引用或赋值，因此没有办法在外部访问函数里面变量。简单来讲，可以看成一个父函数里面嵌套一个子函数，
  而父函数是无法访问子函数的变量，而子函数是可以访问父函数的变量。而唯一的途径就是通过上述闭包函数。这时候就可以把子函数当成一个闭包函数，在子函数先打印父函数变量出来，
  然后return 子函数，这时当window执行环境执行父函数时候，就相当于访问到父函数里面的变量，通过父函数()()。这里看不明白？没关系，下面有例子，肯定教到你懂！请继续！
 
> 2.  常见闭包形式
	  
```javascript	 
      function parent(){
        var _super_a = 1;
        var child = function(){
          console.log('_super_a: ' + _super_a);
        }
        return child;
      }
     // parent() 得到的是child，parent()()等于child()
      parent()();  
```
 
   如何理解呢？根据JS作用域概念，最外面的是无法访问的 parent函数里面_super_a变量的，那我们如果要访问呢，这时候就只能使用闭包函数了。而child就是所谓的闭包函数啦，
   通过parent()()，就能在控制台打印出父函数parent()里面的变量。这就是闭包！
   
 
## 常见的for循环与闭包

```javascript
	for(var i=0,arr=[];i<=3;i++) {
		arr.push(function(){console.log(i)});
    } 	
```
 
 这段代码引用于知乎上一个问题，这段代码只会输出4，这是因为push方法总会返回新数组的长度。同时为什么function方法里面的console.log()不会执行输出呢？
  抛开有关大家常说的执行环境作用域链变量引用的解释，我们还可以从一些执行步骤乃至语义的角度来分析以上代码。arr.push()里面的参数虽然是一个函数体，但这个函
  数体在没被执行的时候你可以把它看作是字符串，只有当这串字符串被执行的时候才会被解析为各个单词所对应的操作。因此当你调用arr[0]()、arr[1]()之类的时候，
  程序才开始解析  function () {console.log(i) }  这串字符串，比如此时程序才知道 i 代表着一个储存着数值的变量，这个数值是多少呢？要按照作用域链去查找，当查找到 i 这个变量时，按照执行顺序来看，for循环已经结束了，i的值固定为 4。
 
 
```javascript
    for(var i = 0; i < 10; i++) {
		setTimeout(function() {
		console.log(i);
		}, 1000);
    }
```

   这个程序执行完是输出10个10，这里简单说一下由于JS单线程的影响，详细可看笔者的分析单线程文章，因为浏览器会把setTimeout里面的函数加到任务队列中，等到主线程的任务执行完毕，并且setTimeout延迟时间到了才会执行回调函数，而同样，因为回调函数也是一个闭包函数，自然也保存着对外面的自有变量i的引用，而这时i已经随着for循环（主线程任务）执行完毕变成了10，所以当setTimeout回调执行时，理所当然访问到的i是10，所以才会造成不是按0到9输出，并且是同时输出10个10（说多一句，你可能还会看见一个数字，那个是浏览器返回给你setTimeout的id，可以用来取消定时器等）。
   
   而闭包函数为什么能保持着对父函数的引用呢？原因是子闭包域即当前的子函数的 function scope 会产生一个 closure 对象属性,这个对象属性
   内包含的是子闭包域对父闭包域的所有引用(只要子闭包域(内部函数)还存活,其父闭包域(外部函数)就不能被释放),倘若在父闭包域存活期间对其私有变量内容进行修改,则对这些父
   闭包域的私有变量进行引用的子闭包域中 function scope 的 closure 对象属性的内容也会发生变化,因为这只是引用.所以也会造成上面的结果。这个可以看segmentfault上的
   [链接:用9种办法解决 JS 闭包经典面试题之 for 循环取 i](https://segmentfault.com/a/1190000003818163),这篇文章文章写得很详细，在这里我就不浪费时间再分析一遍了。
 
   综上，此类for循环与闭包问题，我个人观点，并不能当成一个很好的闭包例子。究其根本，是因为在js中for循环并不存在块级作用域，所以变量i是一个全局变量，而循环体内闭包函数里面i的引用保存的是全局那个i，所以最后打印出来都是最后一个i,下面图片证明i是全局变量，全局可访问到
   <img src="{{ "/img/closure_var.jpg" | prepend: site.baseurl }}" style="width:80%;height:300px;">
   
   而我们可以通过ES6新增的let命令来解决这个问题，let命令可以生成一个块级作用域，声明的变量仅在块级作用域内有效。效果如下图：即在全局访问不到循环定义变量
    <img src="{{ "/img/closure_let.jpg" | prepend: site.baseurl }}" style="width:80%;height:300px;">
	
   摘自[阮一峰老师ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/let#let命令)：变量i是let声明的，当前的i只在本轮循环有效，所以每一次循环的i其实都是一个新的变量，
   所以在闭包函数里面访问的便是对应i的引用，你可能 会问，如果每一轮循环的变量i都是重新声明的，那它怎么知道上一轮循环的值，从而计算出本轮循环的值？这是因为 JavaScript 
   引擎内部会记住上一轮循环的值，初始化本轮的 变量i时，就在上一轮循环的基础上进行计算。
   
  
   我看见其他一些文章引用秘密花园的例子，但是分析的不到位，分析成因为setTimeout异步，而for循环的i瞬间执行完，就是比异步快，所以造成最后答案是永远输出10。而且下面
   评论也有的人理解错了，作者也未能指正，说明作者也并没能正确理解。所以上面根据自己理解分析一遍，希望不会误人子弟。最后希望对你有帮助。
   

## 结语

  今天的分享就到这里，我声明一下，因为笔者也不是圣人，不可能没有错误，况且圣人也可能出错，有些知识可能有差错，因为大家都是在彼此学习的过程中，所以希望发现我文章错误
  的朋友能联系我，博客导航可以找到我的个人信息，可以联系我，不会让错误误导更多的读者。不过，我也向大家保证，我的博客，我绝对是用心写的，这些知识，如果我自己没有经过大量
  资料验证，我是不敢发出来给大家的。所以大家可以放心。并且我也会持续更新。
   
   
   
   
   
   
   
   
   
   
   
   
   
   
   
   