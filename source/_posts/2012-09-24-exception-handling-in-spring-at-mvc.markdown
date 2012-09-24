---
layout: post
title: "Exception Handling in Spring @MVC"
date: 2012-09-24 16:34
comments: true
categories: [Java, Spring @MVC]
---

#Exception Handler
{% codeblock Exception Handler接口 lang:java %}
package org.springframeowork.web.servlet;import javax.servlet.http.HttpServletRequest;import javax.servlet.http.HttpServletResponse;public interface HandlerExceptionResolver {  ModelAndView resolveException(HttpServletRequest request,                                HttpServletResponse response,                                Object handler,                                Exception ex);}
{% endcodeblock %}

* Dispatcher servlet会查找application context里的所有实现了HandlerExceptionResolver接口的Bean。
* 如果有多个ExceptionResolver实现，在有异常出现时，Dispatcher Servlet会一次调用，直到viewname被返回或者response被写入。
* 如果异常没有被处理，那么异常会重新抛出。

##Spring提供的HandlerExceptionResolver实现
* AnnotationMethodHandlerExceptionResolver
* ExceptionHandlerExceptionResolver
* DefaultHandlerExceptionResolver
* ResponseStatusExceptionResolver
* SimpleMappingExceptionResolver
* HandlerExceptionResolverComposite

￼￼