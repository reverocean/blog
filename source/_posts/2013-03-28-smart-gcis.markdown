---
layout: post
title: "Smart GCIS"
date: 2013-03-28 20:14
comments: true
categories: [Java, DropWizard, Gradle] 
---
在大数据如此活跃的今天，我们这两天也跟大数据来了一次亲密接触。我们六个人的小组在一天时间里完成了一个简单的产品智能推荐系统，我们称为[Smart GCIS](https://github.com/reverocean/Stuart)。  
#项目结构
我们决定提供Micro-Service，所以在项目里面我们分了好如下几个模块：

* smart-persistence
* smart-match
* smart-recommendation
* smart-web  
其中smart-persistence提供持久层服务，我们使用Hibernate来访问Mysql；smart-match提供match功能，通过Spring-Batch调用Match引擎来匹配相同的用户；smart-recommendation为客户推荐可能要购买的产品；smart-web提供一个界面用于市场人员查看客户以及其推荐产品，同事smart-web还提供RESTd的Micro-Service。

#构建脚本
针对这样的一个多模块项目，我们使用[Gradle](http://www.gradle.org/)作为我们项目的构建脚本。
##问题
在项目的初期，搭建项目的时候，我犯了一个致命的疏忽。就是在配置多模块的项目时需要一个settings.gradle的文件，我少写了一个**s**，导致子模块在Idea里不是Java项目。这个废了我不少时间，最后还是[dreamhead](http://dreamhead.blogbus.com/)帮助才发现问题。  
**我要跟s做斗争**  
##进步
我们在项目里面使用[DropWizard](http://dropwizard.codahale.com/)作为Micro-Service的web开发，而Dropwizard的一个特点就是它通过一个main函数启动并提供web服务。在DropWizard官方文档里是要让打成jar包，然后通过Java -jar来运行。这样是很方便，但是在开发过程中就不方便了，因为要打Jar包之后运行，才能看见结果。  
所以我引入了Gradle的[application](http://www.gradle.org/docs/current/userguide/application_plugin.html)插件，在smart-web的build.gradle文件中apply这个插件，并且指定要运行的main之后就可以在命令行里运行：
```groovy
gradle :smart-web:run
```
来运行DropWizard了。不过这里还有个问题就是在运行DropWizard的时候，需要为main函数提供一个server和configuration.yml的参数，但是在gradle :smart-web:run后面加上这两个参数有会报错。好在Gradle的appliation插件提供了相应的配置：  
```groovy
run {
    args 'server', 'src/main/resources/stuart.yaml'
}
```
这样就解决问题了。
 <!--more-->
#DropWizard
在[ThoughtWorks2012年的技术雷达](http://www.thoughtworks.com/cn/articles/technology-radar-october-2012-0)中，提到了一个趋势，就是embedded servlet containers。[DropWizard](http://dropwizard.codahale.com/)就是这样一个embedded servlet containers。  
Dropwizard的文档很详细，我就不赘述了，但是在这个项目中，我们使用Dropwizard提供web页面（Freemarker），就必然要访问css和javascript。目前，Dropwizard还不支持提供assets，好在我们碰见的问题都不时问题，已经有人已经提供好了[扩展](https://github.com/bazaarvoice/dropwizard-configurable-assets-bundle)，我们只需要include进来就可以了。  

* 首先，需要在build.gradle中加入"com.bazaarvoice.dropwizard:dropwizard-configurable-assets-bundle:0.1.9"的依赖
* 其次按照扩展的说明文档，很容易就能配好
* 但是有一个坑，就是assets的目录需要映射的，在文档中是dashboard，需要改成自己需要的就可以了

#其他
这次活动一共6个人，一个人准备数据，一个人完成持久层，一个人完成匹配引擎，一个人完成推荐系统，两个人pair做界面，感觉配合的非常好。

 