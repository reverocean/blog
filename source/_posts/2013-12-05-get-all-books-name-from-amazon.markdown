---
layout: post
title: "Get all books name from Amazon"
date: 2013-12-05 11:22
comments: true
categories: [Other]
---
到年底了，Training Budget需要报销。在一年里陆陆续续买了些书，但是也有很多书会在最后一起买，因为怕Budget会花在别的地方。  
选了一些书之后，下一个任务就是要把书名拷贝到邮件里，一本书这样做当然无妨，但是要书多的话，这就不是一个程序员该做的事情了。因为我是在Amazon买的书，所以下面提供一个从Amazon订单里提取书名的方法：
#引入JQuery
去年的Amazon还有JQuery在页面上，但是很不幸，今年Amazon上已经没有JQuery的引用了，那么我们需要从外部引入JQuery。最简单的办法当然就是在Chrome的Console里从Google的CDN加载JQueery，在Console里输入如下的代码：  
```
var jq = document.createElement('script');
jq.src = "https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js";
document.getElementsByTagName('head')[0].appendChild(jq);
jQuery.noConflict();
```

#获取书名
有了JQuery之后，我们就可以使用JQuery牛B的selector选择我们的书名了，script如下：  
```
$('.product-title a').each(function(i, e){console.log($(e).text())})
```
大功告成，书名就都输出在Console里了，拷贝出来，放到邮件即可。
modify
