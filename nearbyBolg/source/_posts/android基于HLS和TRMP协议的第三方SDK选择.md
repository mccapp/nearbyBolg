---
title: android基于HLS和TRMP协议的第三方SDK选择
date: 2016-10-28 09:29:49
categories: android
tags: [android,直播]
---
协议的详解网上资料都太多了，我就不赘述了。Android上3.0开始支持hls，但在4.0以后就支持的不太好了。而对rtmp来说，Android是默认不支持的。网上有一些第三方库支持这两种协议，下面就我找到的第三方库列出并做出比较。

 - 提到流播放就不得不提到 Vitamio .协议支持全面，嵌入项目简单，对个人开发者免费。项目地址:
   https://github.com/yixia/VitamioBundle.
 - B站开源出来的 ijkplayer
   .项目维护及时，许多大公司都在用。网易云音乐，斗鱼TV，火猫TV等等，影响力可见一斑。支持RTMP，HLS。项目地址:https://github.com/Bilibili/ijkplayer.
   七牛开源出来的 PLDroidPlayer. 这个是基于B站的 ijkplayer
   进行二次开发的。项目维护也很及时，你去上边提issue，很快就会有人回复你。同样支持RTMP,HLS。继承步骤简单，有wiki，我现在用的就是这个;)
   ,但以后收不收费就不一定了。项目地址:https://github.com/pili-engineering/PLDroidPlayer.
 - 大牛直播出品的 SmarterStreaming.
   个人使用免费，项目地址:https://github.com/daniulive/SmarterStreaming.
 - 新浪开源出来的 sinavideo_playersdk.
   完全免费，你甚至可以修改备注。不过只支持hls协议，定制简单。项目地址:https://github.com/SinaVDDeveloper/sinavideo_playersdk.

  [原文地址][1]


  [1]: http://isunxu.xyz/android/between-rtmp-and-hls-third-party-choice/