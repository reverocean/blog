---
layout: post
title: "Writing Building Script"
date: 2012-10-12 14:43
comments: true
categories: [Gradle]
---
在Gradle运行的时候，会创建一个Project的实例。同时每个Script文件也会被编译为一个实现了**Script**的类，也就意味着在**Script**接口里声明的属性和方法在你的Script文件里都是有效的。
#定义变量
##局部变量
{% codeblock Local variables lang:groovy %}
	def dest = "dest"
	task copy(type: Copy) {
		from "source"
		into dest
	}
{% endcodeblock %}
## Extra properties
{% codeblock Extra properties lang:groovy %}
apply plugin: "java"

ext {
    springVersion = "3.1.0.RELEASE"
    emailNotification = "build@master.org"
}

sourceSets.all { ext.purpose = null }

sourceSets {
    main {
        purpose = "production"
    }
    test {
        purpose = "test"
    }
    plugin {
        purpose = "production"
    }
}

task printProperties << {
    println springVersion
    println emailNotification
    sourceSets.matching { it.purpose == "production" }.each { println it.name }
}
{% endcodeblock %}

* Gradle的每个对象都可以通过ext来定义属性
* *sourceSets.all { ext.purpose = null }*为**sourceSets**包含的每个对象设置一个*ext.purpose*属性(相当于初始化)，不要这句的话，build也能正常运行，但是会有Warning
* **sourceSets**是Configures the source sets of this project
* *sourceSets.matching { it.purpose == "production" }.each { println it.name }*是Groovy语法，匹配上“production”的再遍历

#Groovy语法
##Property accessor
Groovy自动为属性添加getter和setter方法
{% codeblock Property accessor lang:groovy %}
// Using a getter method
println project.buildDir
println getProject().getBuildDir()

// Using a setter method
project.buildDir = 'target'
getProject().setBuildDir('target')
{% endcodeblock %}
##Optional parentheses on method calls
方法调用的时候括号是可选的
{% codeblock Optional parentheses lang:groovy %}
test.systemProperty 'some.prop', 'value'
test.systemProperty('some.prop', 'value')
{% endcodeblock %}
##List
{% codeblock List lang:groovy %}
// List literal
test.includes = ['org/gradle/api/**', 'org/gradle/internal/**']

List<String> list = new ArrayList<String>()
list.add('org/gradle/api/**')
list.add('org/gradle/internal/**')
test.includes = list
{% endcodeblock %}
##Map
{% codeblock Map lang:groovy %}
// Map literal
apply plugin: 'java'

Map<String, String> map = new HashMap<String, String>()
map.put('plugin', 'java')
apply(map)
{% endcodeblock %}

* 这里**apply plugin: 'java'**相当于Ruby里的**apply plugin=>'java'**
##Closures as the last parameter in a method
{% codeblock Closure lang:groovy %}
repositories {
    println "in a closure"
}
repositories() { println "in a closure" }
repositories({ println "in a closure" })
{% endcodeblock %}


