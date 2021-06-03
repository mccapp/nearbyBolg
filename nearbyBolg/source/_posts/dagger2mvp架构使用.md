---
title: dagger2mvp架构使用
date: 2016-10-31 16:02:34
categories: mvp
tags: [android,dagger2,mvp]
---

了解请点击[dagger2基础了解][1]
    
dagger2使用教程：
    根目录的build文件加入android-apt支持
```
    环境配置：
    buildscript{
        repositories{
            jcenter()
        }
    }
    dependencies{
        classpath 'com.android.tools.build:gradle:x.x.x'
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.4'
    }
    
    在app目录的build文件加入 apt plugin的支持
    
    plugin : 'com.neenbeankt.android-apt'
     
     加入dagger2 retrofit okhttp依赖
     
     compile 'com.google.dagger:dagger:2.0'
     apt 'com.google.dagger:dagger-compiler:2.0'
     provided 'org.glassfish:javax.annotation:10.0-b28'
     
     在Application中实践Dagger2
     现在依赖已经加入，开始动手使用Dagger2，第一步考虑我们Application。
     先创建AppApplication，并在AndroidMainfest.xml 注册好。
 

    public class AppApplication extends Application{
        public void onCreate(){
            super.onCreate();
        }
    }
    
    <application
       android:name=".AppApplication"
       android:allowBackuo="true"
       android:icon="@mipmap/ic_lacuncher"
       android:label="@string/app_name"
       android:label="@string/app_name"
       />
```
     
     

  [1]: http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0519/2892.html