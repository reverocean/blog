---
layout: post
title: "How to Use Your Custom Gradle Plugin"
date: 2013-12-10 14:50
comments: true
categories: [Gradle]
---
在[Gradle Plugin for MyBatis Generator](http://reverocean.github.io/blog/2013/12/09/gradle-plugin-for-mybatis-generator/)一文中描述了如何自定义一个插件，下面我们就应该考虑该如何使用我们的插件了。相信大家都知道通过**apply plugin:插件名**来使用插件，但是要使用自定义插件的话，有一个问题就是Gradle脚本去哪里找我们自定义的插件。我们可以通过下面三种方式来使用自定义插件：  
1. 将插件和Gradle脚本放在同一个文件里  
2. 上传到Maven Repository里去  
3. 上传到Github上  
讲插件的定义代码放在build.gradle文件里，这个没有什么好讲的，在插件里直接通过apply就可以。但是我们自定义插件的目的就是为了让插件能够被其他项目使用到，要是放在build.gradle文件里就不能被其他项目使用了，也就失去了我们自定义插件的意义了，所以我们着重讲下后面两种情况。

#上传到Maven Repository
Gradle天然和Maven融合的很好，除了可以使用Maven Repository里的依赖，还可以在Gradle里使用在Maven Repository的插件。说到Maven Repository，想必大家都知道有本地和远程两种Maven Repository之分。在上传时上没有什么不一样的配置，只是使用的时候，本地的Maven Repository只能在本机上使用，而远程的可以给整个公司乃至全球的人使用。  
要上传到Maven Repository，我们首先要在自定义插件项目的build.gradl文件里使用*maven*插件：  
```
apply plugin: 'maven'
```  
这样我们执行*gradle install*就能把插件安装到Local Maven Repository的默认目录（一般在home下的.m2文件下），在使用插件的Gradle脚本里，通过如下的方式引用：  
{% codeblock Local Maven Repository插件的使用 lang:groovy %}
buildscript {
    repositories {
        mavenLocal()
    }
    dependencies {
        classpath('com.rever:mybatis:1.0-SNAPSHOT')
    }
}
apply plugin: 'mybatis'
{% endcodeblock %} 
 <!--more--> 
这里是放到默认目录，如果我们想放到特定目录的话，我们就要使用Maven插件提供的uploadArchives任务了，我们通过为uploadArchives指定要上传的路径即可：  
{% codeblock 指定uploadArchives路径 lang:groovy %}
apply plugin: 'maven'

uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: "file://${projectDir}/repo")
        }
    }
}
{% endcodeblock %}  
这样就上传到当前plugin项目的根目录下的repo目录下，同样这里的url如果指定为远程Maven Repository的话，就会上传到远程。那么在使用的时候也就需要调整了：
{% codeblock 上传到指定的文件夹 lang:groovy %}
buildscript {
    repositories {
        maven {
            url uri('../repo')
        }
    }
    dependencies {
        classpath('com.rever:mybatis:1.0-SNAPSHOT')
    }
}
apply plugin: 'mybatis'
{% endcodeblock %}
按照这两种方式就可以将插件传到Maven Repository，之后在项目的Gradle脚本里使用了。
#上传到Github
上传到Maven Repository一般来说就能解决问题了，但是如果公司没有Maven Repository私服的话要去sonatype注册用户还是很麻烦，作为我们用来管理代码的Github要是也能提供插件就完美了。  
其实，将插件上传到Github，然后再使用也很简单。首先还是要像上面上传到Local Maven Repository上传到当前项目的repo文件夹下：
{% codeblock 上传到指定的文件夹 lang:groovy %}
apply plugin: 'maven'

uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: "file://${projectDir}/repo")
        }
    }
}
{% endcodeblock %}
然后将repo添加到Github上去，但是这样还不能使用。为了能够使用上传到Github上的插件，需要提供一个gradle文件：
{% codeblock mybatis.gradle lang:groovy %}
buildscript {
    def pathParts = sourceURI.path.split('/')
    def version = pathParts[-2]
    def basePath = pathParts[0..(pathParts.size()-6)].join('/')
    if (sourceURI.scheme != 'file') {
        def uri = new java.net.URI(sourceURI.scheme, sourceURI.userInfo, sourceURI.host, sourceURI.port, basePath, null, null)
        basePath = "${uri.toString()}"
    }

    repositories {
        mavenCentral()
        mavenRepo url: basePath
    }
    dependencies {
        classpath("com.rever:mybatisPlugin:${version}")
    }
}

repositories {
    mavenCentral()
}
apply plugin: com.rever.MyBatisGeneratorPlugin
{% endcodeblock %}
该文件也就是根据当前路径解析出版本号，然后再使用该插件。将该文件放到对应的版本文件夹下，我这里存放的路径是：**repo/com/rever/mybatisPlugin/1.0-SNAPSHOT/**。有了这个文件之后，我们通过如下简单的引用就能使用插件了：
{% codeblock 使用Github上的插件 lang:groovy %}
buildscript {
    apply from: 'https://raw.github.com/reverocean/MyBatisGenerator/master/repo/com/rever/mybatisPlugin/1.0-SNAPSHOT/mybatis.gradle'
}
{% endcodeblock %}

***
本文讲解了两种方式上传Gradle自定义插件，大体上来说如果插件就开发者自己使用，那么可以上传到Local Maven Repository，如果要整个公司使用，那么我推荐放到公司的私有Maven Repository或者其他Maven Repository上去。如果你的代码在Github上管理，那么我推荐上传到Github上去。


