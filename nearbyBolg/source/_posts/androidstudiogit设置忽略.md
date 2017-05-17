---
title: androidstudiogit设置忽略
date: 2015-09-21 12:13:18
categories: git
tags: [git]
---
要忽略的文件
-.idea文件夹
-.gradle文件夹
-所有文的build文件夹
-所有的.iml文件
-loca.properties文件

所以：
跟目录的
.gitignore
.gradle
/local.properties
/.idea/workspace.xml
/.idea/libraries
.DS_Store
/build
/captures
.idea
*.iml

module目录下的.gitignore
/build
*.iml