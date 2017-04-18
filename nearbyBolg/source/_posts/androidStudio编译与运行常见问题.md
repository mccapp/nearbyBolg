---
title: androidStudio编译与运行常见问题
date: 2017-03-30 13:50:25
categories: android
tags: [android,bug]
---
最近开发公司一个项目，环信客服SDK的demo接入。android5.0的手机大部分可以正确使用，
其他之下的手机会报java.lang.VerifyError.。之前以为环信客服SDK的兼容问题，后来把融云屏蔽掉，
后面又接连报错，才意思到是我项目出问题，百度了一些资料，才发现java.lang.VerifyError有几种情况会报错;
google的官方说法是：java.lang.VerifyError是adt与jdk的不同版本的不同，进入架包之类的会报错各种不同的错误，是无法机器无法解决的。
解决方法一
![此处输入图片的描述][1]

但是，不要忘记，这只是让你app的编辑文件分成2分，你软件运行有可能只运行第一个软件，它是默认运行的，有可能会报VerifyError。
Apk在运行的时候，有一个dexpathlist，而Multidex的源码中，会根据你的系统版本号对dexpathlist做修改，将所有的dex都添加到dexpathlist中.
接下来集成有两个步骤:
一. 从sdk\extras\android\support\multidex\library\libs 目录将android-support-multidex.jar导入工程中
二. 如果你的工程中已经含有Application类,那么让它继承android.support.multidex.MultiDexApplication类,
     如果你的Application已经继承了其他类并且不想做改动，那么还有另外一种使用方式,覆写attachBaseContext()方法:
     
![此处输入图片的描述][2]     

网上的一些解释是：app项目过大，必须分成2个Dex文件，Android studio才会运行正常。

这样才能让你的两个dex文件合并在一起，不会报异常了。
解决方法二:
我只说使用android studio的情况，有可能是你的升级android studio2.0出现的问题 Android Studio 2.0带来了很多新功能和优化，其中最让我兴奋的是Instant Run和Gradle编译速度的提升，虽然预览版bug多，但是程序员怕这个？
网上处理的方法是：解决方法就是把Gradle版本改回去，改成1.5版本比较稳定，不会出现问题
解决方法三:
最后一种是，我也没有碰见过，但是我看着比较靠谱，所有写下来，哈哈  我的记性比较差，请原谅我这个智障哦/(ㄒoㄒ)/~~。
你的LibRARY架包包含的.class源文件与你自己module内的一个架包一个源文件.class文件冲突，所有你可以通过ctrl+N,可以在android studio中遍历你的.class源文件，删除其中一个，运行是ok的。


由于是第一次写，有点没头没脑，只要是想给那些遇见跟我一样问题的人  一些解决方案 O(∩_∩)O哈哈~，不过这个问题我难了一个月，头疼死了。


  [1]: http://wx1.sinaimg.cn/mw690/005zrb37gy1fe4ts34rolj30fy08v0ta.jpg
  [2]: http://wx4.sinaimg.cn/mw690/005zrb37gy1fe4ttmdlavj30jq06iwen.jpg