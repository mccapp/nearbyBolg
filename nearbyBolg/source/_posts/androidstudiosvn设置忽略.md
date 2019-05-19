---
title: androidstudiosvn设置忽略
date: 2016-09-21 12:13:18
categories: git
tags: [git]
---

一、Android Studio创建的Android项目一般需要忽略
1、.idea文件夹
2、.gradle文件夹
3、所有的build文件夹
4、所有的.iml文件
5、local.properties文件。
方法一、在项目的.idea/workspace.xml的文件里设置
```xml
<?xml version="1.0" encoding="UTF-8"?>    
<project>    
    <component name="ChangeListManager">    
        ...    
    <ignored path=".gradle/" />     
    <ignored path=".idea/" />     
    <ignored path="gradle/" />   
    <ignored path="项目名/build/" />    
    <ignored path="gradlew" />    
    <ignored path="gradlew.bat" />    
    <ignored mask="*.iml" />     
    <ignored path="local.properties" />    
    <ignored path="build/" />   
    <ignored mask="*.apk" />  
    </component>    
    ...    
</project>    
```
[![](http://a1.eoeandroid.com/attachment/forum/201607/18/172122qdxadmdcidnzabmb.png)](http://a1.eoeandroid.com/attachment/forum/201607/18/172122qdxadmdcidnzabmb.png)
