---
layout: post
title: "Advance of task in gradle"
date: 2012-09-29 09:19
comments: true
categories: [Gradle]
---
Gradle在执行的时候有三个生命周期：

1. initialization
2. configuration
3. execution

#Configuration
configuration一般用于设置Task在运行时所需的变量和数据结构，每次运行Gradle build文件的时候，都会运行一次configuration代码。
{% codeblock Configuration of task %}
task initializeDatabase
initializeDatabase { print 'configuring ' } 
initializeDatabase { println 'database connection' }
{% endcodeblock %}
注意这里的configuration块和定义task的块是一样的，不一样的地方就是在task后面没有"<<"活着doLast等。

#Task是对象
Gradle为每一个Task都会创建一个具有属性，方法的对象。我们可以控制每个Task对象的访问顺序，类型以及其功能。一般的每个新的Task都是DefaultTask类型（就像Java里的java.lang.Object一样）。DefaultTask没有实际的功能，只提供一些Gradle需要的接口。
##DefaultTask的方法

* dependsOn
{% codeblock 不同的方式调用dependsOn lang:ruby%}
// Declare that world depends on hello  
task loadTestData {	
}
task loadTestData {	
}
task loadTestData {	
}

task loadTestData
task loadTestData(dependsOn: createSchema)
{% endcodeblock %}

* doFirst  
可以添加一段在执行task的之前执行的代码，doFirst可以在已经存在的Task上添加代码。当多次定义doFirst的时候，每个代码块都会被累加到doFirst里去，运行的时候是安装先定义的后执行的顺序。

* doLast  
doLast除了在执行完Task之后执行和doFirst不一样之外，其他都一样。

* onlyIf  
onlyIf会判断你传入的闭包是否满足条件，如果满足条件就执行Task，否则就不执行。使用onlyIf方法，你可以使用System property来切换Task是否可以被调用。也可以读文件、调用web services、检查安全等来控制。
{% codeblock onlyIf的例子 lang:ruby %}
task createSchema << {
	println 'load test data'
	System.properties['load.data'] == 'true'
{% endcodeblock %}

{% codeblock 不传人property执行结果 lang:ruby%}
gradle loadTestData
:loadTestData SKIPPED
{% endcodeblock %}

{% codeblock 传人property执行结果 lang:ruby %}
gradle -Dload.data=true loadTestData
{% endcodeblock %}

##DefaultTask的属性

* didWork  
表示Task是否执行完毕的布尔值。不时所有的Task都有didWork这个属性，但是一些内置的Task都有，比如Compile，Copy以及Delete都有didWork这个Property，用于表示是否成功执行完成。

{% codeblock 利用compileJava的didWork lang:ruby%}
apply plugin: 'java'
	if(tasks.compileJava.didWork) {
	}
{% endcodeblock %}

* enabled  
用于表示Task是否会被执行的布尔值。当被设置为false的时候，task就不会被执行。

{% codeblock 设置task的enabled lang:ruby%}
task templates << {
	println 'send emails'
{% endcodeblock %}
这个build在执行gradle sendEmails的时候，因为sendEmails的enabled设置成了false，所以他不会被执行，但是他依赖的templates会被执行。

* path
表示Task全路径的字符串。一般的该值就是task的name属性，但是当被其他的build文件调用时，就不一样了。

* logger
Gradle内部logger的引用，它实现了org.slf4j.Logger接口，但是添加了更多额外的logging level（DEBUG, INFO
, LIFECYCLE, WARN, QUIET, ERROR）。

{% codeblock logger的level lang:ruby%}
task logLevel << {
					'DEBUG',
					'LIFECYCLE', 
					'QUIET', 
					'WARN', 
					'ERROR']
		logger.error logMessage
 }
{% endcodeblock %}

* logging  
logging用于访问logger的日志级别，参见上面的例子。

* description  

{% codeblock description的不同设置方式 lang:ruby%}
task helloWorld(description: 'Says hello to the world') << { 
	println 'hello, world'

	println 'hello, world'
}
{% endcodeblock %}

* temporaryDir  
返回零时目录File的引用

* 动态Property

#Task类型
除了默认的DefaultTask，Gradle也定义了一些通用的task类型。

##自定义Task类型
当Gradle提供的Task类型不够用的时候，我们可以自己定义一些我们的Task类型。
###Custom Tasks Types in the Build file

{% codeblock description的不同设置方式 lang:ruby%}
task createDatabase(type: MySqlTask) {

	def hostname = 'localhost'
		if(database) {
		} else {
		}
}
{% endcodeblock %}
	
	* 自定义Task需要继承自DefaultTask 
	* hostname，port等都是Task自定义的属性
	* 使用@TaskAction标示一个方法为Task的Action
	
###Custom Tasks in the Source Tree
在project的根目录下建一个buildSrc的文件夹，然后在文件夹里创建groove的源文件，在Gradle运行task的时候，会找该目录下的文件。 