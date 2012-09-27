---
layout: post
title: "Gradle Task"
date: 2012-09-26 17:18
comments: true
categories: [Gradle]
---
Gradle里的task用来描述构建中不可分割的原子任务，例如编译，打包JAR，生存javadoc等，都是task。  

#定义Task
{% codeblock 定义Task %}
	task hello {
 	   doLast {
    	    println "Hello World!"
       }
	}
{% endcodeblock %}

{% codeblock 简单定义Task %}
	task upper << {
    	String someString = 'mY_nAmE'
    	println "Original: " + someString 
    	println "Upper case: " + someString.toUpperCase()
	}
{% endcodeblock %}
注意：  

* 这里直接使用“<<”来定义Task，“<<”是doLast的别名
* 定义Task的时候就是写代码，这里先定义了一个变量，然后调用toUpperCase方法

{% codeblock 另外一个Task %}
	task count << {
    	4.times { print "$it " }
	}
{% endcodeblock %}
#运行Task
```
gradle -q hello
Hello World!
```
其中：  

*  -q是gradle的命令行参数，用于阻止gradle的log输出，这样就只输出gradle脚本里输出的内容。
*  Hello World！是我们定义的task的输出。

#Task依赖
{% codeblock Task依赖 %}
	task hello << {
    	println 'Hello world!'
	}
	task intro(dependsOn: hello) << {
    	println "I'm Gradle"
	}
{% endcodeblock %}

*  使用dependsOn来定义当前task依赖的task
*  依赖的Task可以在当前的Task之后定义

#动态Task
{% codeblock Task依赖 %}
	4.times { counter ->
    	task "task$counter" << {
        	println "I'm task number $counter"
    	}
	}
	task0.dependsOn task2, task3
{% endcodeblock %}

*  使用counter作为变量，在定义task的时候使用“**task$counter**”会动态定义出task0，task1，task2，task3
*  **task0.dependsOn task2, task3**通过调用task0的dependsOn的方法，也可以定义Task的依赖

#Task的API
{% codeblock Task API %}
	task hello << {
    	println 'Hello Earth'
	}
	hello.doFirst {
   	 	println 'Hello Venus'
	}
	hello.doLast {
    	println 'Hello Mars'
	}
	hello << {
    	println 'Hello Jupiter'
	}
{% endcodeblock %}

*  doFirst和doLast可以被执行多次
*  定义完task之后，就可以调用doLast，doFirst和<<

#Task的属性

{% codeblock Task API %}
	task myTask {
    	ext.myProperty = "myValue"
	}
	task printTaskProperties << {
    	println myTask.myProperty
	}
{% endcodeblock %}

*  在定义Task的时候，使用**ext.myProperty**为Task添加属性
*  在别的Task里，直接使用**myTask.myProperty**来访问Task的属性

#定义函数

{% codeblock Task API %}
	task checksum << {
    	fileList('../antLoadfileResources').each {File file ->
        	ant.checksum(file: file, property: "cs_$file.name")
        	println "$file.name Checksum: ${ant.properties["cs_$file.name"]}"
    	}
	}
	task loadfile << {
    	fileList('../antLoadfileResources').each {File file ->
    	    ant.loadfile(srcFile: file, property: file.name)
        	println "I'm fond of $file.name"
    	}
	}
	File[] fileList(String dir) {
    	file(dir).listFiles({file -> file.isFile() } as 		FileFilter).sort()
	}
{% endcodeblock %}

* 在后面定义了fileList函数
* 在定义Task的时候，调用fileList函数

#默认Task

{% codeblock Task API %}
	defaultTasks 'clean', 'run'
	task clean << {
    	println 'Default Cleaning!'
	}
	task run << {
    	println 'Default Running!'
	}
	task other << {
    	println "I'm not a default task!"
	}
{% endcodeblock %}

* 使用**defaultTasks 'clean', 'run'**定义哪些task是默认的
* 可以定义多个默认的Task


