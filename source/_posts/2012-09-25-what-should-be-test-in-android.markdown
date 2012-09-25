---
layout: post
title: "What should be tested in Android"
date: 2012-09-25 12:31
comments: true
categories: [Android]
---
一般不需要测试那些不会失败的代码，例如getter和setter方法。
#Activity lifecycle events
我们应该测试Activity是否正确处理了lifecycle events。比如：  
*  如果Activity在onPause或者onDestory事件里保存了Activity的状态，在onCreate里restore了保存的状态，那么你就应该测试这些状态是否被正确的保存和恢复了。  
*  Configuration-changed事件也需要被测试

#Database和filesystem操作
我们应该测试数据库和文件系统操作是否正确处理。我们需要在隔离的低级别，高级别的ContentProviders以及从application本身上测试这些操作。
Android在android.test.mock包下提供了一些mock对象，用于隔离测试。

#Physical characteristics of the device
需要测试应用可以在不同的设备上都能运行，包括如下方面：  
*  Network capabilities  
*  Screen desities  
*  Screen resolutions  
*  Screen sizes  
*  Availability of sensors  
*  Keyboard and other input devices  
*  GPS  
*  External storage  
可以配置不同的AVD来进行各种设备上的测试。
