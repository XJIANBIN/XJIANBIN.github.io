---
layout:     	notebook
title:     	    Jquery性能优化小技巧
description:    前端性能优化系列之二
author:     	許健彬
tags:      	    前端性能优化系列
subtitle:     	前端性能优化系列之二
category:     	project
key:            optimization2
---

### 前记
今天朋友分享了[Jquery性能优化](http://learn.jquery.com/performance/)的几篇文章，我学习了一下，顺便记录下来。还有阮一峰老师也整理了一篇[JQuery的最佳实践](http://www.ruanyifeng.com/blog/2011/08/jquery_best_practices.html)，大家也可以去读一下，也可以学到不少东西。

### 正文

> 1 .把appen元素操作放在循环外面

操作DOM是会损耗性能的。如果您需要在DOM中附加很多元素，您应该一次性附加它们，而不是每个单独一次添加。

* 第一种方法

```javascript
var frag = document.createDocumentFragment();
 
$.each( myArray, function( i, item ) {
 
    var newListItem = document.createElement( "li" );
    var itemText = document.createTextNode( item );
 
    newListItem.appendChild( itemText );
 
    frag.appendChild( newListItem );
 
});
 
$( "#ballers" )[ 0 ].appendChild( frag );
```

* 第二种方法

```javascript
var myHtml = "";
 
$.each( myArray, function( i, item ) {
 
    myHtml += "<li>" + item + "</li>";
 
});
 
$( "#ballers" ).html( myHtml );

```

> 2 . 在循环中缓存数组的长度

```javascript
   var myLength = myArray.length;
 
   for ( var i = 0; i < myLength; i++ ) {
 
    // do stuff
 
   }
```


> 3 . 分离元素再进行操作

DOM操作是很慢的，你应该尽量避免操作它， jQuery 1.4 介绍了一个方法，让你移除需要操作的元素，然后再进行操作。
如果你要对一个DOM元素进行大量处理，应该先用.detach()方法，把这个元素从DOM中取出来，处理完毕以后，再重新插回文档。根据测试，使用.detach()方法比不使用时，快了60%。
   
```javascript
   var table = $( "#myTable" );
   var parent = table.parent();
 
   table.detach();
 
   //添加很多行和列到table中
 
   parent.append( table );
```

> 4 . 不要在选择元素为空的选择器上进行操作

JQuery还是会进行执行代码，即使选择元素为空，你最好在操作之前验证一下你的选择器元素是否包含元素

```javascript
   // 这里即使选择元素为空，也会调用内部三个方法
   $( "#nosuchthing" ).slideUp();
 
   // 更好的做法:
   var elem = $( "#nosuchthing" );
 
   if ( elem.length ) {
 
       elem.slideUp();
 
   }
 
   // 最好的做法，封装成扩展方法
   jQuery.fn.doOnce = function( func ) {
 
       this.length && func.apply( this );
 
       return this;
 
   }
 
   $( "li.cartitems" ).doOnce(function() {?
 
       // make it ajax! \o/?
 
   });
```

> 5 . 优化选择器

* 尽少或者避免使用JQuery的选择器扩展

```javascript
   // 慢 (:even 选择器是JQery的扩展选择器)
   $( "#my-table tr:even" );
 
   // 快 
   $( "#my-table tr:nth-child(odd)" );
```

* 避免过度的特异性

```javascript
  $( ".data table.attendees td.gonzalez" );
 
  $( ".data td.gonzalez" );
```

更清晰的选择器有助于提高选择器的性能，因为选择器引擎在查找元素时不需要遍历过多的层

* 以ID选择器为开头

```javascript
   // 快
   $( "#container div.robotarm" );
 
   // 非常快
   $( "#container" ).find( "div.robotarm" );
```

第一种方法，JQuery使用document.querySelectorAll()遍历DOM，而第二种方法使用document.getElementById()，即使find()函数会导致速度变慢，但还是比第一种快

* 关于对IE8及以下的优化

  * 在选择器右边使用比较特殊的指向，而左边可以比较模糊指向
  
  ```javascript
    // 没优化:
    $( "div.data .gonzalez" );
 
    // 已优化:
    $( ".data td.gonzalez" );
  ```
  
  *避免通用选择器
  
  ```javascript
  $( ".buttons > *" ); // 性能损耗非常大.
  $( ".buttons" ).children(); // 这种方式更好.
 
  $( ":radio" ); // 隐含的通用选择
  $( "*:radio" ); // 与上面一样，更明显
  $( "input:radio" ); // 比较好的方式.
  ```

> 6 . 当改变非常多的元素样式时使用样式表

如果你需要改变页面超过20个元素的样式，增加一个<style>标签到页面将提速超过60%

```javascript
   // 选择元素超过20个，将会非常慢
   $( "a.swedberg" ).css( "color", "#0769ad" );
 
   //更快的方式
   $( "<style type=\"text/css\">a.swedberg { color: #0769ad }</style>")
    .appendTo( "head" );
```

> 7 . 多读源码，你将会学到更多的优化方式。









