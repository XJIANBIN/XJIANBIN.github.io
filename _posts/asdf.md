---
layout:     	notebook
title:     	    google extension
description:    my first google extension	
author:     	許健彬
tags:      	    google extension
subtitle:     	my first google extension
category:     	project
key:            googleExtension
---


   

## 踩过的坑
   1函数异步
```javascript
     chrome.storage.sync.get("data", function (retVal) {
            if(retVal.data!=null){
			   var data=retVal.data.carAdministrationData_area||"";
			   data=data.split(",");
			   data.forEach(function(elements,index){
				   Area[index]=elements.replace(/(^\s*)|(\s*$)/g, "");
			   });
	        }
         });
		 console.log( Area);
```
	因为插件中需要保存一些数据，而我选择保存在storage中，而且采用异步获取的方式，而且下面需要用到此异步操作生成的数组。以下就是获取到的数组了：
	<img src="{{ "/img/googleExtentsion.png " | prepend: site.baseurl }}" style="width:400px;height:200px;">
	有没有觉得这个数组很奇怪，为什么外面显示长度为0，里面却有数据而且长度为4？
	问题出在这个代码上，因为这个代码是异步去获取chrome.storage，而下面console出来的时候，还没获取到，里面的数据是异步加进去的
	
	
	
F:\ImageMagick-7.0.1-Q16;F:\Ruby23-x64\bin;C:\ProgramData\Oracle\Java\javapath;D:\app\112\product\11.2.0\client_2\bin;D:\app\112\product\11.2.0\client_1\bin;%SystemRoot%\system32;%SystemRoot%;%SystemRoot%\System32\Wbem;%SYSTEMROOT%\System32\WindowsPowerShell\v1.0\;C:\Program Files (x86)\Microsoft SQL Server\100\Tools\Binn\;C:\Program Files\Microsoft SQL Server\100\Tools\Binn\;C:\Program Files\Microsoft SQL Server\100\DTS\Binn\;C:\android-sdk-windows\tools\;C:\android-sdk-windows\plarform-tools;\C:\android-sdk-windows\ant\bin;C:\Program Files (x86)\nodejs;C:\Program Files\Java\jdk1.8.0_51\bin;F:\xunleixiazai\eclipse\plugins\org.apache.ant_1.9.4.v201504302020\bin;C:\Users\Administrator\android-sdks\platform-tools;C:\Users\Administrator\android-sdks\tools;F:\xunleixiazai\phonegap-2.9.0\lib\android\bin\create-android;C:\Program Files (x86)\Microsoft SQL Server\100\Tools\Binn\VSShell\Common7\IDE\;C:\Program Files (x86)\Microsoft Visual Studio 9.0\Common7\IDE\PrivateAssemblies\;C:\Program Files (x86)\Microsoft SQL Server\100\DTS\Binn\;H:\软件\java32\jdk32\bin;C:\Program Files\nodejs\;F:\Git\cmd	

### 前记
今天，在阅读JS高级程序设计这本书时，书中的一句话不是很理解，所以查了些资料，看了一下别人的分析，大致明白了意思，所以把学习心得记录下来。

> 原话是：ECMAScript中的所有参数传递的都是值，不可能通过引用传递参数。

这段话是看到函数参数部分时的一句提示说明。这句话让我不解的是第二部分，不可能通过引用传递参数。

  哼哼o(￣ヘ￣o#)，这里先插播一则小“广告”！
* 按值传递，指的是在方法调用时，传递的参数是按值的拷贝
* 按引用传递，指的是传递的参数是按引用进行传递的，传递引用的地址，也就是变量所对应的内存空间的地址。


### 要讲清楚这个问题，得先温习下两个知识点

> 1. ECMAScript变量包含两种不同数据类型的值,基本类型值与引用类型值。5种基本类型值分别是Number,Boolean,String
,Undefined,Null引用类型值是保存在内存中的对象。并且是按引用访问的。因为JS不允许直接访问内存中的位置，也就
是不能直接操作对象的内存空间。操作对象时，实际操作的时对象的引用而不是实际的对象。
  2. 变量的复制

*  基本类型变量的复制,变量对象上创建一个新值，然后把该值复制到为新变量分配的位置上。
<img src="{{ "/img/function1.png " | prepend: site.baseurl }}" style="width:400px;height:300px;">

*  引用类型的复制,同样也会将存储在变量对象中的值复制到新变量分配的空间中。不同的是这个值的副本是一个指针，而这个指针指向
存储在堆中的一个对象。复制操作结束后，两个变量实际上将引用同一个对象，因此改变其中一个变量，就会影响另一个变量。
<img src="{{ "/img/function2.png " | prepend: site.baseurl }}" style="width:400px;height:200px;">

### 好了，接下来就是说道为何参数传递只能是按值传递？

先看JS高级程序设计的一个下面一个例子

```javascript
	function setName(obj){
	obj.name="xujianbin";
	}
	var person=new Object();
	setName(person);
	alert(person.name);   //"xujianbin"
```

> 以上代码创建了一个对象，保存在person中，然后被传递到函数中复制给obj，在这个函数内部，obj和person引用
同一个对象。因为即使这个变量是按值传递的，obj也会按引用来访问同一个对象。所以在函数内部更改对象属性，
函数外部也有反映。这就会让我们误以为这是按引用传递

```javascript
	function setName(obj){
	obj.name="xujianbin"
	obj=new Object();
	obj.name="howard";
	}
	var person= new Object();
	setName(person);
	alert(person.name);  //"xujianbin"
```

> 按照按引用传递的定义，传递的是引用的地址，在函数内部改变了地址，外部变量相应会改变，即原始的引用会改变
。可是结果并没有改变name属性，证明并不是按引用传递。那么真相究竟是什么呢？

> 上面知识点说到，JS不能直接操作对象的内存空间，并且操作对象实际上是操作对象的引用而不是实际的对象。
但这个并不意味着传递参数是按引用传递参数，参数可以是对象引用，而JS是按值传递对象引用的。
当传递给函数不是引用时，传递的都是该值的一个副本（按值传递）。区别在于引用。在C++中当传递给函数的参数
时引用时，传递的就是这个引用，或者内存地址（按引用传递），JS中，当对象引用是传递给方法的一个参数时，
你传递的是该引用的一个副本（按值传递）而不是引用本身。



