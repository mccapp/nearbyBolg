---
title: android断点调试
date: 2016/10/31 09:47:42
categories: android
tags: [android,mvp]
---
 Android Studio包含一个debugger程序,可以帮助你在模拟器和真机上调试你的android应用.通过Android Studio的debugger,你可以:

1.选择你想调试的设备.
2.在代码上设置断点.
3.在运行时去检查变量和表达式的值.
可能平时大家调试代码都是通过打印log的形式,今天带大家走进断点调试的世界.


断点调试-基础篇
设置断点
设置断点的方法: 左键在需要调试的代码所处的侧边栏处单击.如下图所示: 

![Alt text](../../../../images/20160628204032573.png)
开始调试
点击红色箭头指向的按钮,即可进行代码调试,如下图所示: 
![Alt text](../../../../images/20160628204300199.png)
调试界面如下所示: 
![Alt text](../../../../images/20160628204824333.png)
单步调试
Step Over(F6)
代表程序直接执行当前行代码.(ps:如果该行是函数调用,则直接执行完函数的全部代码) 
![Alt text](../../../../images/20160628205544518.png)
Step Into(F5)
代表程序执行当前行代码(ps:如果该行有自定义方法,则运行进入自定义方法,不会进入官方类库的方法)
![Alt text](../../../../images/20160628210031943.png)
具体效果如下: 
![Alt text](../../../../images/20160628210222255.png)
Step Out(F7)
跳出Step Into进入的方法.例如我们感觉进入的方法没有问题,不需要执行后续代码,就可以通过Step Out跳出当前进入的代码.
![Alt text](../../../../images/20160628210902909.png)