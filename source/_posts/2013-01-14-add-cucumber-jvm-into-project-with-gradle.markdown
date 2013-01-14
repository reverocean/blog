---
layout: post
title: "Add cucumber-JVM into project with gradle"
date: 2013-01-14 17:19
comments: true
categories: [Gradle, Cucumber]
---

众所周知，Cucumber是Ruby的一个用来BDD的测试框架。[Cucumber-Java](http://)则是Java版的Cucumber，它模拟Ruby里的DSL，使用Annotation创建了一套Java的BDD测试框架。Cucumber-JVM项目中只有通过ant和maven来使用Cucumber-JVM的例子，这里我记录一下Cucumber-JVM如何在Gradle里使用。
##为项目引入Cucumber-JVM
首先在build.gradle文件中加入对于Cucumber-JVM的依赖：  
```groovy
testCompile(
            'info.cukes:cucumber-java:1.1.1',
            'info.cukes:cucumber-junit:1.1.1',
            'junit:junit:4.10'
)
```  
##添加task运行Cucumber
添加完stories和steps之后，就可以运行Cucumber-JVM了，看看我们的测试是否通过。虽然在Gradle里文档里说了运行**gradle test**时，会扫描classpath路径下具有@RunWith annotation的类，并作为JUnit的测试去运行。但是，我在使用的时候（gradlew1.3），运行**gradle test**找不到标记了@RunWith的JUnit Runner（这个是gradle的一个bug，已经有人报上去了）。  
在研究了Cucumber-JVM自带的例子里的ant脚本之后，我通过添加一个task来运行Cucumber-JVM的测试：  
```groovy cucumber task
task cucumber() {
    dependsOn classes
    doLast {
        javaexec {
            main = "cucumber.api.cli.Main"
            classpath configurations.cucumberRuntime
            classpath sourceSets.main.output.classesDir
            classpath sourceSets.test.output.classesDir
            args = ['-f', 'pretty', '--glue', 'cucumber.examples.java.helloworld', 'src/test/resources']

        }
    }
}
```  

  * 这里实际上就是运行Cucumber-JVM提供的Java类cucumber.api.cli.Main去运行@RunWith的JUnit Runner。
  * 需要给cucumber.api.cli.Main类指定classpath：*sourceSets.test.output.classesDir*，*sourceSets.main.output.classesDir*以及*configurations.cucumberRuntime*。configurations.cucumberRuntime的配置如下：  
```groovy
configurations {
    cucumberRuntime {
        extendsFrom testCompile
    }
}
```  
 * 通过args指定steps的包（cucumber.examples.java.helloworld）和stories的目录（src/test/resources）  
 
 <!--more-->
 ##完整build.gradle  
 
{% codeblock build.gradle lang:groovy %}
apply plugin: 'java'
apply plugin: 'idea'

repositories {
    mavenCentral()
}

configurations {
    cucumberRuntime {
        extendsFrom testRuntime
    }
}

dependencies {

    testCompile(
            'info.cukes:cucumber-java:1.1.1',
            'info.cukes:cucumber-junit:1.1.1',
            'junit:junit:4.10'
    )
}

task cucumber() {
    dependsOn classes
    doLast {
        javaexec {
            main = "cucumber.api.cli.Main"
            classpath configurations.cucumberRuntime
            classpath sourceSets.main.output.classesDir
            classpath sourceSets.test.output.classesDir
            args = ['-f', 'pretty', '--glue', 'cucumber.examples.java.helloworld', 'src/test/resources']

        }
    }
}
 {% endcodeblock %}
  