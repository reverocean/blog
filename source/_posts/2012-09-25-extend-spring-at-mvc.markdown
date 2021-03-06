---
layout: post
title: "Extend Spring @MVC"
date: 2012-09-25 09:21
comments: true
categories: [Java, Spring @MVC]
---
#Extending RequestMappingHandlerMapping
Spring @MVC通过在方法上使用RequestMapping来确认应该使用哪个方法来响应相应的请求，而RequestMapping又通过各种                                                              RequestCondition的实现来完成各种过滤（比如：consumes，headers，methods，params，produces以及value等）。在Spring @MVC框架中使用RequestConditionHolder和RequestMappingInfo这两个实现。  

##自定义RequestCondition
* 实现                                RequestCondition接口
{% codeblock RequestCondition接口 lang:java %}
package org.springframework.web.servlet.mvc.condition;

import javax.servlet.http.HttpServletRequest;
import org.springframework.web.bind.annotation.RequestMapping;

public interface RequestCondition<T> {
	T combine(T other);
	T getMatchingCondition(HttpServletRequest request);
	int compareTo(T other, HttpServletRequest request);
}
{% endcodeblock %}

* 继承RequestMappingHandlerMapping
	*  getCustomTypeCondition方法根据对应的Handler**类**返回类级别的condition
	*  getCustomMethodCondition方法根据对应的Handler**方法**返回方法级别的condition
	
#扩展RequestMappingHandlerAdapter
在Reques™appingHandlerAdapterli里，Spring @MVC通过各种HandlerMethodArgumentResolver的实现来决定传什么参数给Handler的方法。

***To be continue …***
