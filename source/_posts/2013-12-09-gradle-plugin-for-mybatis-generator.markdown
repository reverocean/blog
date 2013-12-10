---
layout: post
title: "Gradle Plugin For MyBatis Generator"
date: 2013-12-09 14:31
comments: true
categories: [Gradle, MyBatis]
---
因为项目原因，最近又重新看了一下MyBatis，发现MyBatis只有Ant和Maven的Generator，没有Gradle的插件。而现在Gradle已经越来越流行，没有Gradle插件怎么行。  
编写Gradle插件，可以从头自己实现，也可以调用现有的Ant Task。MyBatis已经有Ant的Generator了，所以我决定不重造轮子，调用Ant的Task就行了。
##定义插件Task的类型
编写插件就是为了重用一些Task，比如Gradle的Java插件，就提供了编译、测试以及打包等Task，这样在构建脚本里使用：  
```
apply: 'java'
```  
就可以使用这些Task。所以我们自定义插件，就难免要为插件定义Task。Gradle中的Task也是有类型的，比如[Copy](http://www.gradle.org/docs/current/dsl/org.gradle.api.tasks.Copy.html)的Task。自定义Task也可以继承已经有的Task，但是我们这里要使用的Task是Ant提供的，所以我们需要继承ConventionTask。  
{% codeblock Task的定义 lang:groovy %}
class MyBatisGeneratorTask extends ConventionTask {
    //Define some properties
    @TaskAction
    void executeCargoAction() {
	//Implement the task action
    }
}
{% endcodeblock %}
申明了Task之后，就需要实现Task的Action了，就是该Task都做哪些事情。我们这里要调用MyBatis的Ant任务，所以需要使用IsolatedAntBuilder。代码如下：  
{% codeblock Task的Action实现 lang:groovy %}
services.get(IsolatedAntBuilder).withClasspath(getMyBatisGeneratorClasspath()).execute {
       ant.taskdef(name: 'mbgenerator', 
            			 classname: 'org.mybatis.generator.ant.GeneratorAntTask')

       ant.properties['generated.source.dir'] = getTargetDir()
       ant.mbgenerator(overwrite: getOverwrite(), 
            				configfile: getConfigFile(), 
            				verbose: getVerbose()) {}
}
{% endcodeblock %}
 <!--more-->
跟Ant里一样，要使用自定义的Ant任务，就必须先通过Ant的Taskdef定义一个Task，之后才能在Ant脚本里使用。同样在Gradle里也通过**ant.taskdef**来定义新的Ant任务。要定义Task，我们就需要制定从哪个classpath下去加载指定的类名*org.mybatis.generator.ant.GeneratorAntTask*。这里我们通过getMyBatisGeneratorClasspath()获得，这就需要我们在使用该Gradle Task的时候讲classpath传到Task里去，所以我们就需要定义Task的属性了。同时，大家还应该注意到这里不止一个getMyBatisGeneratorClasspath()方法，还有其他的Get方法，这些Get方法都是从Task的属性取值。我们的Task属性定义如下：  
{% codeblock Task的属性 lang:groovy %}
def overwrite
def configFile
def verbose
def targetDir
FileCollection myBatisGeneratorClasspath
{% endcodeblock %}

##编写Plugin
有了Task类型之后，我们就可以编写Plugin了。自定义Plugin需要实现Plugin接口：   
{% codeblock Plugin lang:groovy %}

class MyBatisGeneratorPlugin implements Plugin<ProjectInternal> {
    @Override
    void apply(ProjectInternal project) {
        project.logger.info "Configuring MyBatis Generator for project: $project.name"
        MyBatisGeneratorTask task = project.tasks.create("mbGenerator", MyBatisGeneratorTask);
        project.configurations.create('mybatis').with {
            description = 'The MyBatis Generator to be used for this project.'
        }
        project.extensions.create("mybatis", MyBatisGeneratorPluginExtension)

        task.conventionMapping.with {
            myBatisGeneratorClasspath = {
                def config = project.configurations['mybatis']
                if (config.dependencies.empty) {
                    project.dependencies {
                        mybatis('org.mybatis.generator:mybatis-generator-core:1.3.2')
                    }
                }
                config
            }
            overwrite = { project.mybatis.overwrite }
            configFile = { project.mybatis.configFile }
            verbose = { project.mybatis.verbose }
            targetDir = { project.mybatis.targetDir }
        }

    }
}
{% endcodeblock %}

在这里我们通过*MyBatisGeneratorTask task = project.tasks.create("mbGenerator", MyBatisGeneratorTask);*创建一个Task并给该Task指定相应的属性。

##插件的配置
要编写插件，就难免需要在Gradle里配置该插件。在Gradle的插件里，通常都提供默认配置值，但是有时在Gradle构建脚本里也需要让用户自定义。在Gradle里使用*extension objects*方法，通过Project的[ExtensionContainer](http://www.gradle.org/docs/current/javadoc/org/gradle/api/plugins/ExtensionContainer.html)对象，把构建脚本里的配置信息传到插件里。Extension很简单，就是简单的Groovy对象：  
{% codeblock Extension的定义 lang:groovy %}
@ToString(includeNames = true)
class MyBatisGeneratorPluginExtension {
    def overwrite = true
    def configFile = "generatorConfig.xml"
    def verbose = false
    def targetDir = "."
}
{% endcodeblock %}   
接下来就是在插件里使用这个Extension：  
{% codeblock Extension的使用 lang:groovy %}
project.extensions.create("mybatis", MyBatisGeneratorPluginExtension)
{% endcodeblock %}
这里就会把构建脚本里的mybatis的配置，合并到我们提供的Extension里去，比如在构建脚本里提供如下的配置：  
{% codeblock 配置插件 lang:groovy %}
mybatis{
    configFile = '../generatorConfig.xml'
}
{% endcodeblock %}  
就会覆盖默认的configFile配置，默认会从当前目录里去找generatorConfig.xml的文件，但是经过我们配置之后，就会从上级目录去找该文件。

##Summary
至此，Gradle的MyBatis插件编写完毕，下面一个问题就是如何在项目中使用。该插件已经上传到Github:[https://github.com/reverocean/MyBatisGenerator](https://github.com/reverocean/MyBatisGenerator)。

