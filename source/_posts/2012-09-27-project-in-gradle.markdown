---
layout: post
title: "Project in gradle"
date: 2012-09-27 14:07
comments: true
categories: [Gradle]
---
针对每一个项目的build.gradle，在运行的时候Gradle都会创建一个**Project**对象：

*  在build脚本里调用没有定义的方法都会被代理到project对象
*  在build脚本里没有定义的属性也会被代理到project对象

{% codeblock %}
println name
println project.name
{% endcodeblock %}

#Project的属性
Name		| Type			| Default Value
:----------	| :-----------:	| :-----------
project		| Project	 	| The Project instance
name		| String	 	| The name of the project directory.
path		| String	 	| The absolute path of the project.
description	| String	 	| A description for the project.
projectDir	| File	 		| The directory containing the build script.
buildDir	| File	 		| projectDir/build
group		| Object		| unspecified
version		| Object		| unspecified
ant			| AntBuilder	| An AntBuilder instance

