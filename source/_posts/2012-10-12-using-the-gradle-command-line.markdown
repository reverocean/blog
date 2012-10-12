---
layout: post
title: "Using the Gradle Command-Line"
date: 2012-10-12 14:21
comments: true
categories: [Gradle]
---
执行Gradle命令一些用法

* 一次运行多个task  
	***gradle dist test***
* 使用-x参数可以指定哪些task不执行   
	***gradle dist -x test***
* 使用--continue当有错误时继续执行未执行的task  
	***gradle dist test --continue***
* 使用task的缩写执行task   
	***gradle cT***   
	执行compileTest task
* 使用-b指定build脚本文件
* gradle projects
* gradle tasks
* gradle dependencies
* gradle properties
* gradle -m clean compile列出要执行哪些task