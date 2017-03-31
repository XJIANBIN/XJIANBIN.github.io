---
layout:     	notebook
title:     	    高兼容性的清空input file方案
description:    项目点滴
author:     	許健彬
tags:      	    JS  input file
subtitle:     	项目点滴
category:     	project
key:            inputfile
---

### 前记
今天项目中有一个小需求，就是清空inputfile里面的值，我第一反应就写了$("input[type=file]").val("");但是我觉得应该会有更好的方法，耐不住好奇心的我就google了一下（最后还被同组的小伙伴喷了一脸，项目bug还没改完，你就敢逛stackoverflow？O(∩_∩)O），其实还有好些其他方法，但是有的兼容性不好，在stackoverflow看了一些方法，最后有一个比较好的方法，就想在博客写下来，方便以后查阅，也有印象。下面把看到的方法记下来。

###  一. 引用脚本之家的

```javascript	
    > 1 .【√】$("#file")[0].value = "";
    > 2 .【√】$("#file")[0].value = null;
    > 3 .【×】$("#file").attr("value","");
    > 4 .【×】$("#file").attr("value",null);
    > 5 .【√】$("#file").val("");
    > 6 .【√】$("#file").val(null);
```
 
### 二.引用stack overflow
 
```javascript	
     input.replaceWith(input.val('').clone(true));
```
 
 但这个方法兼容不了IE8，因为IE8说为了保护用户安全，把文件上传区设置为只读，所以设置val()会失效，详细可看[IE官方博客](https://blogs.msdn.microsoft.com/ie/2008/07/02/ie8-security-part-v-comprehensive-protection/);
 
 可以用之前先判断一下是否为IE
 
```javascript
        if ($.browser.msie) {
            $('#file').replaceWith($('#file').clone());
       } else {
            $('#file').val('');
       }
```
 
### 三.引用stack overflow

```javascript
      function resetFormElement(e) {
       e.wrap('<form>').closest('form').get(0).reset();
       e.unwrap();
     
       // Prevent form submission
       e.stopPropagation();
       e.preventDefault();
     }
```

这个方法是先在Input 外面包上一个form表单，然后利用表单重置，最后再删掉表单这个不像clone（）方法,这个不会改变元素属性。这个方法可以在IE10, FF, Chrome & Opera 执行。但IE11
会有问题。
