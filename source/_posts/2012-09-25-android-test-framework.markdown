---
layout: post
title: "Android test framework"
date: 2012-09-25 17:58
comments: true
categories: [Android, Test]
---
Android提供了一套基于JUnit的测试框架--Instrumentation，key features：  
*  方便访问Android的系统对象  
*  Instrumentation框架方便用于测试控制和检测应用  
*  Mock Objects  
*  可以单独跑一个测试或者test suite
*  提供ADT插件支持管理测试和测试项目
 
Instrumentation是Android测试框架的基础，在测试时注入application需要的的mock组件来隔离依赖。

一般需要建立一个和项目名+Test的测试项目，在项目里的AndroidManifest.xml文件里声明Instrumentation。
{% codeblock 被测试project的AndroidManifest.xml lang:xml %}
<?xml version="1.0" encoding="utf-8"?><manifest xmlns:android="http://schemas.android.com/apk/res/android"	package="com.example.aatg.sample"	android:versionCode="1"	android:versionName="1.0">	<application android:icon="@drawable/icon" android:label="@string/app_name">		<activity android:name=".SampleActivity" android:label="@string/app_name">			<intent-filter>				<action android:name="android.intent.action.MAIN" />				<category android:name="android.intent.category.LAUNCHER" />			</intent-filter>		</activity>	</application>	<uses-sdk android:minSdkVersion="7" /></manifest>
{% endcodeblock %}
{% codeblock 测试project的AndroidManifest.xml lang:xml %}
<?xml version="1.0" encoding="utf-8"?><manifest xmlns:android="http://schemas.android.com/apk/res/android"	package="com.example.aatg.sample.test"	android:versionCode="1" 
	android:versionName="1.0">	<application android:icon="@drawable/icon" android:label="@string/app_name">		<uses-library android:name="android.test.runner" />	</application>	<uses-sdk android:minSdkVersion="7" />	<instrumentation android:targetPackage="com.example.aatg.sample"		android:name="android.test.InstrumentationTestRunner"		android:label="Sample Tests" />	<uses-permission android:name=" android.permission.INJECT_EVENTS" /></manifest>
{% endcodeblock %}

注意：
* Test项目的package是被测项目的package后面加上*.test*  
* 指定**android.test.InstrumentationTestRunner**为Test runner  
* 被测项目和测试项目都是Android应用，他们自己对应的APK都会被安装到设备上。他们会共享同样的进程，因此他们具有同样的特征。  
* 运行测试应用的时候，Activity Manager使用Instrumentation开始和控制test runner  
* 在Instrumentation声明里指定了**targetPackage**
