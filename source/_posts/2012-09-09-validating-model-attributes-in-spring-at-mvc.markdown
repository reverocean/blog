---
layout: post
title: "Validating Model Attributes in Spring @MVC"
date: 2012-09-09 14:18
comments: true
categories: [Java, Spring MVC]
---
##Spring的Validator接口 
Spring的Validation主要接口是：*org.springframework.validation.Validator*
{% codeblock Validator接口 lang:java %}
package org.springframework.validation; 

public interface Validator {    boolean supports(Class<?> clazz);    void validate(Object target, Errors errors);}
{% endcodeblock %}  
* supports方法： 用来判断该validator是否可以验证对象。如果supports返回**true**，框架会调用validate方法去验证。  
* validate方法： 实现验证
在Spring @MVC里有两种方式触发验证：  
    1.  把Validator注入到Controller，我们自己手工调validate方法。  
    2.  在方法上添加*javax.validation.Valid*或者*org.springframework.validation.annotation.Validated*annotation。Spring的annotation要比javax的强大些，可以指定hints和validation groups（如果和JSR-303validator联合使用）

**需要注意的是，validation结果要是有错误的话，validator会返回message code，需要配置MessageSource来让错误消息显示的更有意义些。**    
     
***

##实现一个Validator
**需求**
```
实现一个验证Account的Validator
username, password和email必填
email是正确的email地址
地址，城市和国家是必填的
```
{% codeblock AccountValidator lang:java%}

package com.apress.prospringmvc.bookstore.validation; 

import java.util.regex.Pattern;  import org.springframework.validation.Errors;  import org.springframework.validation.ValidationUtils;  import org.springframework.validation.Validator;  import com.apress.prospringmvc.bookstore.domain.Account;public class AccountValidator implements Validator {    private static final String EMAIL_PATTERN = "^[_A-Za-z0-9-]+(\\.[_A-Za-z0-9-]+)*@"    											+"[A-Za-z0-9]+(\\.[A-Za-z0-9]+)*(\\.[A-Za-z]{2,})$";    @Override    public boolean supports(Class<?> clazz) {        return (Account.class).isAssignableFrom(clazz);    }    @Override    public void validate(Object target, Errors errors) {        ValidationUtils.rejectIfEmpty(errors, "username",                                  "required", new Object[] {"Username"});        ValidationUtils.rejectIfEmpty(errors, "password",
              "required", new Object[] {"Password"});        ValidationUtils.rejectIfEmpty(errors, "emailAddress",                                  "required", new Object[] {"Email Address"});        ValidationUtils.rejectIfEmpty(errors, "address.street",                                  "required", new Object[] {"Street"});        ValidationUtils.rejectIfEmpty(errors, "address.city",                                  "required", new Object[] {"City"});        ValidationUtils.rejectIfEmpty(errors, "address.country",                                  "required", new Object[] {"Country"});        if (!errors.hasFieldErrors("emailAddress")) {            Account account = (Account) target;            String email = account.getEmailAddress();            if (!emai.matches(EMAIL_PATTERN)) {                errors.rejectValue("emailAddress", "invalid");            }		} 	}}
{% endcodeblock %}
* 第14行的isAssignableFrom方法用于判断类Class1和另一个类Class2是否相同或是另一个类的超类或接口  
* *ValidationUtils.rejectIfEmpty*方法用来判断对象的属性是否为空，还有*ValidationUtils.rejectIfEmptyOrWhiteSpace*方法。但是判断属性是否为空还可以在*org.springframework.web.bind.WebDataBinder*里加以验证  
* *errors.hasFieldErrors("emailAddress")* 方法用来判断emailAddress是否有错误  
* *errors.rejectValue*用于手动往errors里添加一个错误  
* 这里的*required*都是Error Message的Key，需要Spring的框架处理  
* *address.street*是嵌套属性

***
##配置并使用自定义Validator
{% codeblock AccountValidator lang:java%}
package com.apress.prospringmvc.bookstore.web.controller;import com.apress.prospringmvc.bookstore.domain.AccountValidator;import javax.validation.Valid;// Other imports omitted@Controller@RequestMapping("/customer/register")public class RegistrationController {    @InitBinder    public void initBinder(WebDataBinder binder) {        binder.setDisallowedFields("id");        binder.setValidator(new AccountValidator());}@RequestMapping(method = { RequestMethod.POST, RequestMethod.PUT })public String handleRegistration(@Valid @ModelAttribute Account account, BindingResult result) {        if (result.hasErrors()) {            return "customer/register";        }        this.accountService.save(account);        return "redirect:/customer/account/" + account.getId();    }    // Other methods omitted}
{% endcodeblock %}

* *binder.setValidator(new AccountValidator())*在Controller中将我们自定义的Validator注册一下
* *@Valid @ModelAttribute Account account*绑定到Account上，这样当页面提交之后就会触发验证
* *BindingResult result*在binding和validation中的错误信息都会放到BindingResult里去
* 使用*result.hasErrors()*判断是否有错误（包括binding和validation）

***
##使用JSR-303 Validation
JSR-303提供了一种方便的方式来进行validation，不需要我们自己去实现接口，也不需要在init-binder方法中注册，只需要在要验证的类上加上响应的annotation就可以了。
{% codeblock AccountValidator lang:java%}
package com.apress.prospringmvc.bookstore.domain;
import java.util.Date;import java.util.List;import javax.validation.Valid;import org.hibernate.validator.constraints.Email;import org.hibernate.validator.constraints.NotEmpty;public class Account {    private Long id;    private String firstName;    private String lastName;    private Date dateOfBirth;    @Embedded    @Valid    private Address address = new Address();    @NotEmpty    @Email    private String emailAddress;    @NotEmpty    private String username;    @NotEmpty    private String password;    // getters and setters omitted}
{% endcodeblock %}

* *@Embedded*表示嵌套对象
* *@NotEmpty*验证不能为空
* *@Email*验证Email地址的合法性  
**这样看起来JSR-303简单很多**
***
> 本文是基于Pro Spring MVC - With Web Flow的笔记，非原创