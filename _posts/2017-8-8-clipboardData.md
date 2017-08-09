---
layout:     	notebook
title:     	    知乎版权声明与复制粘贴图片 实现原理解析
description:    ClipboardEvent.clipboardData介绍
author:     	許健彬
tags:      	    JS
subtitle:     	ClipboardEvent.clipboardData介绍
category:     	project
key:            clipboardData
---

### 前记
经常逛知乎的同学就知道，每一次在知乎复制东西时或者一些网站复制其他东西的时候,总会被打上了版权信息，对这个功能的实现原理一直感兴趣，但是一直没有下定决心搞一搞，最近趁着复习很无聊（哈哈，当然也是好久没更新博客，不好意思了），就想玩一玩，放松一下，看看有没有可以分享给大家的，这不，就给大家生产出这篇文章了。请继续往下看！

### 一 H5 Drag 和 drop
先给大家看一个好玩的东西 =》Drag 和 drop ,是H5标准组成部分，用于拖放。具体请点[我](http://www.w3school.com.cn/html5/html_5_draganddrop.asp)
<p data-height="265" data-theme-id="0" data-slug-hash="xLqjqN" data-default-tab="js,result" data-user="XJIANBIN" data-embed-version="2" data-pen-title="H5 drop demo" class="codepen">See the Pen <a href="https://codepen.io/XJIANBIN/pen/xLqjqN/">H5 drop demo</a> by XUJIANBIN (<a href="https://codepen.io/XJIANBIN">@XJIANBIN</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

### 二 如何打上版权？

> 这里我们先来了解一下以下剪贴板事件：

  * 1, `beforecopy`：在发生复制操作前触发；
  * 2, `copy`：在发生复制操作的时候触发；
  * 3, `beforecut`：在发生剪切操作前触发；
  * 4, `cut`：在发生剪切操作的时候触发；
  * 5, `beforepaste`：在发生粘贴操作前触发;(这个事件在google浏览器56.0.2924.87上不会触发)
  * 6, `paste`：在发生粘贴操作的时候触发。

> 接着我们又得认识一个新朋友：clipboardData对象,操作剪贴板数据全靠它

  * 1,在IE中，clipboardData对象是window对象的属性
  * 2,而在Chrome、Safari和Firefox 4+中，clipboardData对象是相应event对象的属性。

但是，在Chrome、Safari和Firefox 4+中，只有在处理剪贴板事件期，clipboardData对象才有效，这是为了防止对剪贴板的未授权访问；在IE中，则可随时访问clipboardData对象。为了确保跨浏览器兼容，最好只在发生剪贴板事件期使用clipboardData对象。

> 为了完成我们的目的，我们还得认识clipboardData对象的两个方法:

* 1,`getData()`方法用于从剪贴板中获取数据，它接收一个参数，即要取得的数据格式。在IE中，有两种数据格式：”text”和”URL”。而在Chrome、Safari和Firefox 4+中，这个参数是一种MIME类型。不过，可以用”text”代表”text/plain”。

```javascript
  //获取剪贴板数据方法
  function getClipboardText(event){
      var clipboardData = event.clipboardData || window.clipboardData;
      return clipboardData.getData("text");
  };
```

* 2,`setData()`setData()方法的第一个参数也是数据类型，第二个参数是要放在剪贴板中的文字。对于第一个参数，IE照样是支持”text”和”URL”，而在Chrome、Safari中，仍然支持MIME类型。但是与getData()方法不同的是，在Chrome、Safari中的setData()方法不能识别”text”类型。

```javascript
  //设置剪贴板数据
  function setClipboardText(event, value){
      if(event.clipboardData){
          return event.clipboardData.setData("text/plain", value);
      }else if(window.clipboardData){
          return window.clipboardData.setData("text", value);
      }
  };
```

> 接着我们再认识一个API Window.getSelection()

表示用户选择的文本范围或插入符号的当前位置。 如果想要将 selection 转换为字符串，可通过连接一个空字符串（“”）或使用 String.toString() 方法。

至此，我们可以来完成这个任务了：

```javascript
   document.addEventListener('copy', function(event) {
      if (window.getSelection()){
        var clipboardData = event.clipboardData || window.clipboardData;
      //  console.log(clipboardData);//注意这里如果要在控制台查看，最好下断点单步执行查看，因为程序执行完了这个对象会清空，console出来的是一个空对象。
         clipboardData.setData('text/plain',window.getSelection().toString   () + '-- BY XUJIANBIN');
      }
      event.preventDefault();// 切记这个 不然浏览器默认的复制操作会替换自定义字符串
   });
```

有同学肯定会觉得奇怪，为什么上面介绍了`setDada()`方法,但是这里偏偏用上了另外一个API来获取剪贴板数据呢？那是因为：

* 在IE浏览器中，cut和copy事件中的getData()方法始终返回null；而其他浏览器始终返回空字符串''。但如果和setDada()方法配合，就可以正常使用。
* 在paste事件中，只有IE浏览器可以正常使用setData()方法，chrome浏览器会静默失败，而firefox浏览器会报错

所以采用以上实现方法实现,能兼容到IE9，因为getSelection()IE9以下不支持，详见[浏览器兼容性](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/getSelection)

> 你肯定见到过这样的场景：点击某个按钮，就能复制某段字符串下来，例如github上面的复制链接，那这个我们可以怎么实现呢？

这个我们就得来认识一个新的API `document.execCommand(aCommandName, aShowDefaultUI, aValueArgument)`,有以下三个参数：
* 1 aCommandName：命令的名称，如”cut”、”copy”、”paste”等命令。
* 2 aShowDefaultU：否展示用户界面，默认为false。Mozilla没有实现。
* 3 aValueArgument： 一些命令需要一些额外的参数值（如insertimage需要提供这个image的url）。默认为null。

示例一：
```javascript
   <input type="text" id="inputText" value="测试文本"/>
   <input type="button" id="btn" value="复制"/>
   <textarea rows="4"></textarea>
   <script>
   var btn = document.getElementById('btn');
   btn.addEventListener('click', function(){
     var inputText = document.getElementById('inputText');
     inputText.focus();
     inputText.setSelectionRange(0, inputText.value.length);
     document.execCommand('copy', true);
   });
   </script>
```
示例二：

```javascript
    <div id="show" style="width:100px;background:rgb(50,167,144)">XJIANBIN</div>
    var myEle = document.getElementById('show'),
        range = document.createRange();
    range.selectNode(myEle);
    window.getSelection().addRange(range);
    myEle.onclick = function() {
      try {
        if (document.execCommand) {
          document.execCommand("copy", "false", null);
        }
      } catch (e) {
        alert('不支持复制!');
      }
    }
```

不过，这个API兼容性不是很好,只能兼容到IE9,详情可查看[execCommand兼容性](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/execCommand)

### 三 如果复制图片到页面上

接下来我们继续介绍`clipboardData`对象的其他知识点

> clipboardData对象的属性：

   |   属性 |   类型   |   说明   |
   |------- |---------|----------|
   |dropEffect|String|默认是 none。|
   |effectAllowed|String|默认是 uninitialized。|
   |Files|FileList|粘贴操作为空List。|
   |items|DataTransferItemList|剪切板中的各项数据。|
   |types|Array |剪切板中的数据类型。|

items属性：是一个DataTransferItemList对象，里面是DataTransferItem类型的数据。
DataTransferItem有两个属性分别为kind(一般为string或者file)和type(具体数据类型,即MIME-Type)和两个比较重要的方法:

* 1,`getAsFile()`,如果如果kind是file，可以用该方法获取到文件
* 2,`getAsString()` 如果kind是string，可以用该方法获取到字符串，字符串需要用`回调函数`得到，回调函数的第一个参数就是剪切板中的字符串的节点字符串DOMString;类似下面：

<img src="{{ "/img/clipboardData.jpg" | prepend: site.baseurl }}" style="width:100%;height:200px;text-align:center;">

[getAsString参考资料：](https://developer.mozilla.org/en-US/docs/Web/API/DataTransferItem/getAsString)

接着就来动手实现：

> 注意1：js通过剪切板能获取到的图片是怎么来的，它必须是用QQ截图或者系统截图功能截下来的图片，或者是网页上某个图片单击右键复制图片等。用ctrl+c 是获取不到的。即使复制多张图片，也只有最后一张图片放入剪贴板

> 注意2：[MAC上复制图片问题](https://segmentfault.com/a/1190000004288686#articleHeader9),参考此文章。

示例一：使用window.URL.createObjectURL，兼容到IE10,具体查看[兼容性](https://developer.mozilla.org/zh-CN/docs/Web/API/URL/createObjectURL)

```javascript
   <input type="text" id="imgInput" placeholder="请粘贴图片到文本框中">
   <div id="log-box"></div>
   <button id="clear-logs" type="button">Clear images</button>
   <script>
          window.addEventListener('load', function (e) {

          document.getElementById('imgInput').onpaste = function (e) {
              var items = e.clipboardData.items;
              for (var i = 0; i < items.length; ++i) {

                  if (items[i].kind == 'file' &&
                      items[i].type.indexOf('image/') !== -1) {

                      var blob = items[i].getAsFile();
                      window.URL = window.URL || window.webkitURL;
                      var blobUrl = window.URL.createObjectURL(blob);

                      var img = document.createElement('img');
                      img.src = blobUrl;
                      var logBox = document.getElementById('log-box');
                      logBox.appendChild(img);
                  } else if(items[i].kind == 'string' ){
                     items[i].getAsString(function(str){
                         console.log(str);
                     });
                  }

              }
          }

          var btn = document.getElementById('clear-logs')
             btn.addEventListener('click', function (e) {
              clearLog();
             });
          });

          function clearLog() {
             var node = document.getElementById('log-box');
             while (node.firstChild) {
              node.removeChild(node.firstChild);
             }
          }  
   </script>
```

示例二:使用File API获取图片

```javascript
        <input type="text" id="input" placeholder="请粘贴图片到文本框中">
        // 将图片资源显示到页面中
     function showImage(imageData) {
         var reader = new FileReader();
         reader.onload = function(e){
             var img = new Image();
             img.src = e.target.result;
             document.body.appendChild(img);
         };
         // 读取图片文件
         reader.readAsDataURL(imageData);
     }

     document.querySelector("#input").addEventListener("paste", function(e){
         var clipboardData = e.clipboardData,
             items,
             item,
             types;

         if(clipboardData){
             items = clipboardData.items;
             if(!items){
                 return;
             }
             item = items[0];
             // 保存在剪贴板中的数据类型
             types = clipboardData.types || [];
             for(var i = 0; i < types.length; i++ ){
                 if(types[i] === "Files"){
                     item = items[i];
                     break;
                 }
             }
             // 判断是否为图片数据
             if( item && item.kind === 'file' && item.type.match(/^image\//i) ){
                 var blob = item.getAsFile();
                 showImage(blob);
             }
         }
     });
```

[clipboardData对象的兼容性](https://caniuse.com/#search=clipboardData)

> 因为IE 与google safari等 操作方式等等有差别，所以做以下简要封装 [参考文章](http://blog.csdn.net/allen_hdh/article/details/20746315)

```javascript
        var evenUtil = {
          addHandler: function(element, type, handler) {
            if (element.addEventListener) {
              element.addEventListener(type, handler, false);
            } else if (element.attachEvent) {
              element.attachEvent('on' + type, handler);
            } else {
              element['on' + type] = handler;
            }
          },
          getEvent: function(event) {
            return event ? event : window.event;
          },
          getClipboardText: function(event) {
            var clipboardData = (event.clipboardData || window.clipboardData);
            return clipboardData.getData('text');
            //在IE中，有两种数据格式：”text”和”URL”。
            // 在Chrome、Safari和Firefox 4+中，这个参数是一种MIME类型。不过，可以用”text”代表”text/plain”。
          },
          setClipboardText: function(event, value) {
            if (event.clipboardData) {
              return event.clipboardData.setData('text/plain', value);
            } else if (window.clipboardData) {
              return window.clipboardData.setData('text', value);
            }
          },
          preventDefault: function(event) {
            if (event.preventDefault) {
              event.preventDefault();
            } else {
              event.returnValue = false;
            }
          }
        };
        //  var textbox = document.form[0].elements['textbox1'],
        //  eventUtil.addHandler(textbox,'copy',function(event){
        //    event = eventUtil.getEvent(event);
        //    eventUtil.preventDefault(event);
        //    evenUtil.setClipboardText(event,'XUJIANBIN');
        //  })

```

### 四 知乎编辑框能复制粘贴图片原理

从知乎源码中可看到,知乎也是利用Clipboard API 可以在用户粘贴时获知粘贴的内容,然后把二进制数据传到服务器，生成一个url返回，再生成一个img插入到编辑框中。
我使用Node做了一个后台接收demo,想试试的小伙伴可以拷贝自行测试（应该懂得要先下载里面的依赖吧(*^__^*) 嘻嘻……)

前端部分代码：

```javascript
    <input type="text" id="input" placeholder="请粘贴图片到文本框中">

    document.querySelector("#input").addEventListener("paste", function(e){
     var clipboardData = e.clipboardData,
         items,
         item,
         types;

     if(clipboardData){
         items = clipboardData.items;
         if(!items){
             return;
         }
         item = items[0];
         // 保存在剪贴板中的数据类型
         types = clipboardData.types || [];
         for(var i = 0; i < types.length; i++ ){
             if(types[i] === "Files"){
                 item = items[i];
                 break;
             }
         }
         // 判断是否为图片数据
         if( item && item.kind === 'file' && item.type.match(/^image\//i) ){
             var blob = item.getAsFile();
             var xhr = new XMLHttpRequest();
             var data = new FormData();
             data.append("webmasterfile", blob);
             xhr.open('POST','http://localhost:9096',true);
             xhr.onreadystatechange = function(){
               if(xhr.readyState == 4) {
                 var img = new Image();
                 console.log(xhr.responseText);
                 img.src = xhr.responseText;
                 document.body.appendChild(img);
               }
             }
             xhr.send(data);
         }
     }
    });
```

Node 部分代码：
```javascript
     const express = require('express');
     const bodyParser = require('body-parser');
     const multer = require('multer');
     const pathLib = require('path');
     const fs = require('fs');
     var server = express();
     // 因为我是在本地测试，所以会有跨域问题，所以设置Cros
     server.all('*', function(req, res, next) {
         res.header("Access-Control-Allow-Origin", "*");
         res.header("Access-Control-Allow-Headers", "X-Requested-With");
         res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
         res.header("X-Powered-By",' 3.2.1')
         res.header("Content-Type", "application/json;charset=utf-8");
         next();
     });
     // 解析post方法提交文件与数据
     server.use(bodyParser.urlencoded({
       extended: false
     }));
     server.use(multer({
       dest: './www/upload'
     }).any());

     server.post('/', function(req, res) {
       // 因为使用multer来保存文件，这个中间件保存的文件没有后缀名，要根据接收到的文件来判断，但因为前台代码传来的是blob(如果表单上传会有一个文件名),所以这里统一手动加上png后缀（经测试，知乎也是无论什么图片都是返回png格式图片）
       var newName = req.files[0].path + '.png' //pathLib.parse(req.files[0].originalname).ext;
       // 2 重命名文件
       fs.rename(req.files[0].path, newName, function(err) {
         if (err) {
           res.send('上传失败');
         } else {
           res.send("http://" + req.headers.host +'/' + pathLib.parse(newName).base);
         }
       });
     });
     server.use(express.static('./www/upload'));
     server.listen(9096);
```

### 五 总结

一般项目中应该都有兼容性要求，这时候推荐一个[轻量JS框架](https://clipboardjs.com/),因为此篇文章只是作为学习总结笔记,作者也没用过，所以没法对此框架做出评价，等以后有需求用到了，有坑再来补充！哦，还有,express配置了托管静态文件，但是如果在浏览器直接访问图片url会输出乱码，这个问题最后没有解决，先记在这里，等有空再查查（或者如果你懂得可以在评论中告诉我哦！十分感谢！）

### 六 参考资料

* [Clipboard API and events包括详细事件触发过程](https://www.w3.org/TR/clipboard-apis/)
* [原生JavaScript实现复制/粘贴](http://www.dengzhr.com/js/1056)
* [Pasting image from clipboardData object](https://codebits.glennjones.net/copypaste/pasteimagedata.htm)
