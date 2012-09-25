---
layout: post
title: "Tpyes of test in Android"
date: 2012-09-25 15:34
comments: true
categories: [Android, Test]
---
#Unit Tests
使用JUnit来写Android的Unit Tests。
###Unit Tests组成模块  
*  test fixture：A test fixture is the well known state defined as a baseline to run the tests and isshared by all the test cases, and thus plays a fundamental role in the design of thetests.   
*  setUp：用来初始化fixture  
*  tearDown：释放fixture

###Test preconditions
因为JUnit框架通过反射来运行test的方法，所以没有固定的顺序来运行所有的test。但是我们一般创建一个testPreconditions()方法来测试preconditions。

###真正的测试
在JUnit3里，所有的以test开头的public void方法，都是Unit test。在JUnit4里使用@Test来标注哪些是Unit Tests。  
Android提供了@SmallTest，@MediumTest和@LargeTest来给测试分类，这样你可以使用test runner来跑单独的类别。  
使用assert*  
Android提供了MoreAsserts和ViewAsserts

###Mock objects
Android在android.test.mock包下提供了几个mock objects，在写测试的时候很有帮助：  
* MockApplication  
* MockContentProvider  
* MockContentResolver  
* MockContext  
* MockCursor  
* MockDialogInterface  
* MockPackageManager  
* MockResources  
这些都是stub，不是真正的实现，你需要继承他们并且实现自己的mock objects。
#UI tests
众所周知，在Android里只有主线程可以更改UI元素，所以被@UIThreadTest标注的测试会在主线程上运行，并修改UI。另外，如果你只想一部分测试运行在UI线程上，你可以使用Activity.runOnUiThread方法来实现。  
Android提供了TouchUtils来测试，并提供了以下的方法：  
* click  
* drag  
* long click  
* scroll  
* tap  
* touch  

#Integration tests
#Functional or acceptance tests
FitNesse(http://www.fitnesse.org)  
BDD(http://behaviour-driven.org)  
jbehave(http://jbehave.org)  
#Performance tests
#System tests
包括：  
* GUI tests  
* Smoke tests  
* Performace tests  
* Installation tests   
 