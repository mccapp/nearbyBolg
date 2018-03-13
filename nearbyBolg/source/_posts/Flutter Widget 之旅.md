---
title: Flutter Widget 之旅
date: 2018-3-05 16:30:12
categories: flutter
tags: [android,ios]
---

flutter 程序入口 ：
import 'package:flutter/material.dart';

void main() {
  runApp(
    new Center(
      child: new Text(
        'Hello, world!',
        textDirection: TextDirection.ltr,
      ),
    ),
  );
}

基本的小部件：
	1.Text：在应用程序中可以构建一个文本信息展示
	2.Row、Cloumn：水平布局(Row)与垂直布局(Column)方向上创建灵活的布局。其设计基于网络的Flexbox布局模型。
	3.Stack：绘制顺序上堆叠小部件(类似 android RelativeLayout),您可以根据Positioned在子布局使用该小部件 , Stack相对于堆栈的顶部 , 右侧 , 底部或左侧边缘定位他们。堆栈基于Web的绝对定位布局模型。
	4.Container：创建矩形视觉元素。一个容器可以装饰一个 BoxDecoration , 如背景 ,边框或阴影。也可以具有边距 , 填充 和 应用于其大小的约束。也可以矩阵在三维空间进行变换
	5.Scaffold：实现了基本的纸墨设计布局结构。MaterialApp的child是Scaffold Widget。支持 左边栏(Drawers) , snack bars , 以及bottom sheets。
	Scaffold 有下面几个主要属性：
	appBar：显示在界面顶部的一个AppBar,也就是android中的ActionBar、Toolbar
	body：当前界面所显示的主要内容Widget
	floatingActionButton：纸墨设计中所定义的FAB,界面的主要功能按钮
	persistentFooterButtons：固定在下方显示的按钮,比如对话框下方的确定、取消按钮
	drawer：侧边栏控件
	backgroundColor：内容的背景颜色,模拟热使用的是ThemeData.scaffoldBackgroundColor的值
	bottomNavigationBar：类似于android重的android:windowSoftInputMode=”adjustResize”控制界面内容 body 是否重新布局来避免底部被覆盖了，比如当键盘显示的时候，重新布局避免被键盘盖住内容。默认值为 true。
显示 snackbar 或者 bottom sheet 的时候，需要使用当前的 BuildContext 参数调用 Scaffold.of 函数来获取 ScaffoldState 对象，然后使用 ScaffoldState.showSnackBar 和 ScaffoldState.showBottomSheet 函数来显示。
http://blog.chengyunfeng.com/?p=1042

主要的小部件：

	1.StatelessWidget[无状态]
	2.StatefulWidget[有状态]