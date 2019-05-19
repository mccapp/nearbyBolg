---
title: androidstudio项目目录结构解析
date: 2016-4-10 10:35:56
categories: android
tags: [android]
---
首先先上图
![此处输入图片的描述][1]

大家看到的是android studio 创建的项目清单【图方便，就用完整的项目来做示例 - - 】
app/build app模块build编译输出的目录( 关于上传svn或github需要忽略的文件待会说明)
app/build.gradle (主要配置 编译sdk版本、编译工具	、包名、sdk最低支持版本、目标sdk版本、app版本号、app版本名称、ndk等等)
app/app.iml app模块的配置文件(Intellij模块文件，很重要但是基本不用管，大家都知道Intellij是AndroidStudio的亲妈的嘛，所以有这个文件是正常的)
app/proguard-rules.pro app模块proguard文件(混淆配置文件,主要用于打包签名apk配置混淆代码[是否混淆是可选的])
build.gradle 项目的build.gradle编译文件，主要就是申明仓库地址和版本号
settings.gradle定义项目包含哪些模块[指引用了哪些外库]
local.properties 配置SDK/NDK
nearby.iml 项目的配置文件
External Libraries 项目依赖的Lib, 编译时自动下载的

> AndroidStudio自动忽略

    现在的AndroidStudio已经很智能了，当创建项目的时候自动给我们创建了一个
    上传svn或git需要忽略的文件如下：
    .gitignore 并给我们忽略了一些文件
    
    *.iml
    .gradle
    /local.properties
    /.idea/workspace.xml
    /.idea/libraries
    .DS_Store
    /build
    /captures
    
    手动忽略
    1、.idea文件
    2、.gradle文件夹
    3、所有的build文件夹
    4、所有的.iml文件
    5、local.properties文件


  [1]: http://ww1.sinaimg.cn/mw690/005zrb37gw1f9mtxo22gnj30bj0kngo7.jpg