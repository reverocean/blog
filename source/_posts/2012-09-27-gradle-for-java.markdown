---
layout: post
title: "Gradle for Java"
date: 2012-09-27 10:15
comments: true
categories: [Java, Gradle]
---
Gradle针对Java开发提供了‘java’插件，非常方便。如果安装约定的目录结构组织项目，几乎不用修改build.gradle。当然也很方便修改build.gradle去适应你的项目结构。

#A basic Java project
{% codeblock 使用Java plugin %}
apply plugin: 'java'
{% endcodeblock %}

*  gradle的Java插件约定的项目结构跟Maven的项目结构一样
*  所有的output文件放在build文件夹下

#Tasks

*  build：编译，测试并创建一个包含main下面的类和资源文件的JAR
*  clean：删除build文件夹，做清理
*  assemble：编译并打一个JAR包，不测试代码。加了War插件之后会打一个war包
*  check：编译，测试。也可以添加其他的插件（Code-quality）来检查你的代码格式

#管理依赖
依赖管理分为两个部分，第一是本项目依赖外部的其他jar，第二是本项目会产生一个jar包，别的项目会依赖当前项目的jar包，所以需要把jar包放置到一个公共的位置。

## 管理外部依赖  

* 添加Maven的Repository
{% codeblock 添加respositories %}
repositories {
    mavenCentral()
}
{% endcodeblock %}
{% codeblock 添加respositories %}
repositories {
    maven {
        url "http://repo.mycompany.com/maven2"
    }
}
{% endcodeblock %}

{% codeblock 添加lvy respositories %}
repositories {
    ivy {
        url "http://repo.mycompany.com/repo"
    }
}
{% endcodeblock %}

{% codeblock 添加lvy local respositories %}
repositories {
    ivy {
        url "../local-repo"
    }
}
{% endcodeblock %}
	
	*  Gradle支持Maven和lvy的Respository
	*  Gradle可以通过local file system或者HTTP访问Respository
	*  Gradle默认不配置respository

* 添加dependencies
{% codeblock 添加dependencies %}
dependencies {
    compile group: 'commons-collections', name: 'commons-collections', version: '3.2'
    testCompile group: 'junit', name: 'junit', version: '4.+'
}
{% endcodeblock %}
{% codeblock 添加dependencies %}
dependencies {
    compile 'org.hibernate:hibernate-core:3.6.7.Final'
}
{% endcodeblock %}

	*  跟Maven一样，依赖分为几个声明周期：
		*  compile
		*  runtime
		*  testCompile
		*  testRuntime
	*  同样跟Maven一样，需要指定依赖包的group，name以及version。也可以通过简单的方法“group:name:version”来指定
	
##Publishing artifacts

{% codeblock 设置publish到lvy %}
uploadArchives {
    repositories {
        ivy {
            credentials {
                username "username"
                password "pw"
            }
            url "http://repo.mycompany.com"
        }
    }
}
{% endcodeblock %}

{% codeblock 设置publish到Maven %}
apply plugin: 'maven'

uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: "file://localhost/tmp/myRepo/")
        }
    }
}
{% endcodeblock %}

{% codeblock 设置publish到文件夹 %}
uploadArchives {
    repositories {
       flatDir {
           dirs 'repos'
       }
    }
}
{% endcodeblock %}


