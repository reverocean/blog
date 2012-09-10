---
layout: post
title: "Internationalization in Spring @MVC"
date: 2012-09-09 20:05
comments: true
categories: [Java, Spring @MVC]
---

##Spring中i18n的组件
1. *org.springframework.context.MessageSource*基于message code和Locale转换成相应的message
2. *org.springframework.web.servlet.LocaleResolver*从session或者cookie里取的locale
3. *org.springframework.web.servlet.i18n.LocaleChangeInterceptor*改变locale

#MessageSource
###MessageSource的实现
Spring在*org.springframework.context.support*包下提供了两个MessageSource的实现：  
1. *ResourceBundleMessageSource*：使用JVM提供的ResourceBundle实现，只能在classpath里加载资源  
2. *ReloadableResourceBundleMessageSource*：和*ResourceBundleMessageSource*非常类似，但是提供了**自动重新加载**和**缓存**能力，以及可以从文件系统读取资源文件

###配置MessageSource
{% codeblock 配置MessageSource lang:java %}
package com.apress.prospringmvc.bookstore.web.config;import org.springframework.context.support.ReloadableResourceBundleMessageSource; // Other imports omitted@Configuration@EnableWebMvc@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {    @Bean    public MessageSource messageSource() {        ReloadableResourceBundleMessageSource messageSource;        messageSource = new ReloadableResourceBundleMessageSource();        messageSource.setBasename("classpath:/messages");        messageSource.setUseCodeAsDefaultMessage(true);        return messageSource;} }
{% endcodeblock %}

*  配置一个name是**messageSource**的Bean
*  该配置会从classpath加载所有的messages.properties和messages_[locale].properties文件

#LocaleResolver
LocaleResolver用来判断使用什么Locale，Spring在*org.springframework.web.servlet.i18n*包里具有如下实现：
*  FixedLocaleResolver：固定Locale，不支持改变locale
*  SessionLocaleResolver
*  AcceptHeaderLocaleResolver：从Http头的accept里取的locale，一般和用户的操作系统一样，因此也不支持改变locale。是默认**LocaleResolver**
*  CookieLocaleResolver

#LocaleChangeInterceptor
**LocaleChangeInterceptor**检查当前的request中是否有locale的参数，如果有，interceptor会使用配置的LocaleResolver去改变当前用户的locale。参数的名子（locale）是可配置的。
#配置Spring的i18n
{% codeblock Spring的i18n配置 lang:java %}
package com.apress.prospringmvc.bookstore.web.config;import org.springframework.context.MessageSource;import org.springframework.context.support.ReloadableResourceBundleMessageSource;import org.springframework.web.servlet.HandlerInterceptor;import org.springframework.web.servlet.LocaleResolver;import org.springframework.web.servlet.config.annotation.InterceptorRegistry;import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;import org.springframework.web.servlet.i18n.CookieLocaleResolver;import org.springframework.web.servlet.i18n.LocaleChangeInterceptor;// Other imports omitted@Configuration@EnableWebMvc@ComponentScan(basePackages = { "com.apress.prospringmvc.bookstore.web" })public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {    @Override    public void addInterceptors(InterceptorRegistry registry) {        registry.addInterceptor(localeChangeInterceptor());    }    @Bean    public HandlerInterceptor localeChangeInterceptor() {        LocaleChangeInterceptor localeChangeInterceptor;        localeChangeInterceptor = new LocaleChangeInterceptor();        localeChangeInterceptor.setParamName("lang");        return localeChangeInterceptor;}    @Bean    public LocaleResolver localeResolver() {        return new CookieLocaleResolver();    }    @Bean    public MessageSource messageSource() {        ReloadableResourceBundleMessageSource messageSource;        messageSource = new ReloadableResourceBundleMessageSource();        messageSource.setBasename("classpath:/messages");        messageSource.setUseCodeAsDefaultMessage(true);        return messageSource;} }
{% endcodeblock %}

注意：  
*  23行的代码**localeChangeInterceptor.setParamName("lang")**用来设置request中的参数名  
*  一般把LocaleChangeInterceptor配置为第一个interceptor，这样即使出现什么错误，任然能够改变用户的language