---
title: View的技术传递
date: 2014-12-08 13:52:03
categories: android
tags: [view]
---

 **1. 基础知识**
     (1)  所有Touch事件都被封装成MotionEvent对象，包括Touch的位置、时间、历史记录以及第几个手指(多手指触摸灯)。
    (2)  事件类型分类为ACTION_DOWN，ACTION_UP，ACTION_MOVE，ACTION_POINTER_DOWN，ACTION_POINTER_UP，ACTION_CANCEL，每个事件都以ACTION_DOWN开始ACTION_UP结束。
    (3)  对事件的处理包括三类，分别为传递---dispatchTouchEvent()函数、拦截---oninterceptTouchEvent函数、消费----onTouchEvent函数和OnTouchListener
**2.传递流程**
    (1)  事件从Activity.dispatchTouchEvent开始传递,只要没有被停止或拦截,从最上层的View(ViewGroup)开始一直往下(子View)传递。子View可以通过OnTouchEvent()对事件进行处理。
    (2)  事件由父View(ViewGroup)传递给子View,ViewGroup可以通过oniterceptTouchEvent对事件做拦截,停止其往下传递。
    (3)  如果事件从上往下传递过程中一直没有被停止，且最底层子View没有消费事件，事件会反向往上传递，这时父View(Group)可以进行消费，如果还是没有被消费的话，最后会到Activity的OnTouchEven()函数。
    (4)如果View没有对ACTION_DOUWN进行消费,之后的其他事件不会传递过来
    (5)  OnTouchListener优于OnTouchEvent()对事件进行消费。
    上面的消费即表示相应函数返回值为true
    (1)View不处理事件流程图
    ![此处输入图片的描述][1]
    (2)View处理事件流程图
    ![此处输入图片的描述][2]


  [1]: https://raw.githubusercontent.com/android-cn/android-open-project-analysis/master/tech/touch-event/image/ignorant-view-example.jpg
  [2]: https://raw.githubusercontent.com/android-cn/android-open-project-analysis/master/tech/touch-event/image/interested-view-example.jpg