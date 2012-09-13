---
layout: post
title: "Using Scoped Beans"
date: 2012-09-11 09:22
comments: true
categories: [Java, Spring @MVC]
---
#Bean的Scope
Spring支持的Scope有：
*  singleton: 默认scope  
*  prototype  
*  thread  
*  request  
*  session  
*  globalSession  
*  application   
 
在定义Bean的地方添加*org.springframework.context.annotation.Scope*annotation，为定义的Bean指定不同的Scope。该annotation可以用在type-level和method-level上。  
*  type-level: 该类型的所有的Bean都是指定的Scope  
*  method-level： 当前方法所定义的Bean是指定的Scope  

Scope Annotation的属性：  
*  value： 指定Bean的scope类型  
*  proxyModel： 指定是否需要proxy以及proxy的机制。**ScopedProxyModel.TARGET_CLASS**用于指定该Bean所对应的类型没有接口，需要默认值是NO

#Example of session scope
{% codeblock 配置session scope的Bean lang:java %}
	package com.apress.prospringmvc.bookstore.web.config; 
	//Other imports omitted	import org.springframework.context.annotation.Scope;	import org.springframework.context.annotation.ScopedProxyMode;	import com.apress.prospringmvc.bookstore.domain.Cart;	@Configuration	@EnableWebMvc	@ComponentScan(basePackages = 		{ "com.apress.prospringmvc.bookstore.web" })public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {//Other methods omitted@Bean@Scope(value = "session", proxyMode = ScopedProxyMode.TARGET_CLASS) 	public Cart cart() {        return new Cart();    }}
{% endcodeblock %}  

#使用SessionStatus管理session scope bean的状态
{% codeblock SessionStatus实例 lang:java%}
	@RequestMapping(method = RequestMethod.POST, params = "order")  	 public String checkout(SessionStatus status,@Validated @ModelAttribute Order order,                           BindingResult errors) {          if (errors.hasErrors()) {            return "cart/checkout";        } else {            this.bookstoreService.store(order);            status.setComplete(); //remove order from session            this.cart.clear(); // clear the cart            return "redirect:/index.htm";		} }
{% endcodeblock %}