---
layout: post
title: "Inteceptor in Spring @MVC"
date: 2012-09-24 14:48
comments: true
categories: [Java, Spring @MVC]
---

# Interceptors
Interceptor和Filter的功能一样，都是用于拦截incoming HTTP requests。但是Filter要比interceptor更强大些，因为Filter能够修改incoming request/response。

##Interceptor的回调接口
*  preHandler： Called before the handler is invoked
*  postHandler: 在Handler调用之后，View渲染之前调用。可以用于替换model里的共享对象
*  afterCompletion：在request处理完成之后。该方法无论preHandler方法调用成功与否都会被调用。可以用于清除一些资源。

##Interceptor接口
*  org.springframework.web.servlet.HandlerInterceptor
*  org.springframework.web.context.request.WebRequestInterceptor
这两个Interceptor的最大区别在与WebRequestInterceptor跟底层技术独立，可以用于JSF或者Servlet，而HandlerIntercepotr只能用于Servlet。HandlerInterceptor的preHandler方法返回false可以用于阻止handler的调用。

#Configuring
配置Interceptor分为两步：  
*  配置Interceptor  
*  关联Interceptor和Handler：有两个方法来完成关联：  
	1. 显示的把interceptors添加到handler mapping的配置里去  
	2. 使用org.springframework.web.servlet .config.annotation.InterceptorRegistry添加（推荐这种方式）
{% codeblock 显示关联HandlerMapping和Interceptor lang:java %}
package com.apress.prospringmvc.bookstore.web.config;
{% endcodeblock %}  


{% codeblock 使用InterceptorRegistry关联HandlerMapping和Interceptor lang:java %}
package com.apress.prospringmvc.bookstore.web.config;
{% endcodeblock %}  


{% codeblock Limiting an Interceptor to Certain URLs lang:java %}
package com.apress.prospringmvc.bookstore.web.config;
{% endcodeblock %}  
