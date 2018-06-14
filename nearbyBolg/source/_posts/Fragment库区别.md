---
title: Fragment库区别
date: 2017-09-22 14:48:20
categories: android
tags: [android]
---

在做项目时，同事把基础fragment类中的Android.support.v4.app.Fragment改成了android.app.Fragment，导致了一些问题，今天特别来标注一下：

强烈推荐使用android.support.v4.app.Fragment，不过依然要了解为什么？？



1.最低支持版本不同

android.app.Fragment 兼容的最低版本是android:minSdkVersion="11" 即3.0版

android.support.v4.app.Fragment 兼容的最低版本是android:minSdkVersion="4" 即1.6版

 

2.需要导jar包

fragment android.support.v4.app.Fragment 需要引入包android-support-v4.jar



3.获取manager的方法不同

android.app.Fragment使用getFragmentManager()

android.support.v4.app.Fragment使用getSupportFragmentManager()



注意:

android.support.v4.app.Fragment不能使用<fragment>标签

而且想用fragment+viewpager只能使用android.support.v4.app.Fragment，因为FragmentPagerAdapter就是android.support.v4.app.Fragment才有。

切记！！ android.support.v4.app.Fragment与android.app.Fragment不要混用。