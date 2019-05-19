---
title: android studio升级之后很多jar无法下载 解决
date: 2018-9-08 16:03:12
categories: android studio
tags: [mac]
---



解决方案
```
buildscript {
    
    repositories {
        maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.2.1'
    }
}
 
allprojects {
    repositories {
        maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    }
}
 
task clean(type: Delete) {
    delete rootProject.buildDir
}
```