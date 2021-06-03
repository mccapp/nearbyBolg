---
title: android视频播放总结
date: 2016-10-27 15:06:29
categories: android
tags: [android]
---
> [Android] Android 视频播放总结

```
VideoView
```
VideoView 的使用非常简单，播放视频的步骤：

 1. 在界面布局文件中定义VideoView组件，或在程序中创建VideoView组件
 2. 调用VideoView的如下两个方法来加载指定的视频：
 - setVideoPath(String path);加载path文件代表的视频
 - setVideoURL(Uri uri);加载uri所对应的视频
 3. 调用VideoView的start()、stop()、psuse()方法控制视频的播放
**Exoplayer**
[ExoPlayer Git 地址][1]

**用法**
ExoPlayer开源项目包含了libray和示例：

 - ExoPlayer library - 这部分是核心的库
 - Demo app - 这部分是演示怎么使用ExoPlayer
ExoPlayer库的核心类是ExoPlayer类。该类维护了播放器的全局状态。比如如何获取媒体数据，如何缓冲以及是怎样编码格式。

 ExoPlayer基于MediaCodec和AudioTrack提供了默认的音视频的TrackRenderer实现。所有的renderers都需要SampleSource对象，Exoplayer从SampleSource获得media samples用于播放。下图展示了Exoplayer是如何配置组合这些组件用于播放音视频的。
![此处输入图片的描述][2]
ExoPlayer库提供了一些不同类型的SampleSource实例：
  - ExtractorSampleSource – 用于 MP3，M4A，WebM，MPEG-TS 和 AAC；
  - ChunkSampleSource – 用于 DASH 和平滑流的播放；
  - HlsSampleSource – 用于 HLS 播放；



 在 ExoPlayer 的 Dome 中使用 DemoPlayer 对 ExoPlayer 进行了封装，并提供了使用上述几种 SampleSource 构建 TrackRenderer 的 Builder。
  - SmoothStreamingRendererBuilder
  - DashRendererBuilder
  - ExtractorRendererBuilder
在使用的时候我们根据不同的需求创建对应的 RendererBuilder，然后将 RendererBuilder 传递给 DemoPlayer 然后调用 DemoPlayer 的 setPlayWhenReady 方法。

优缺点

ExoPlayer 相较于 MediaPlayer 有很多很多的优点：

 - 支持动态的自适应流 HTTP (DASH) 和 平滑流，任何目前 MediaPlayer 
 - 支持的视频格式（同时它还支持 HTTP
 - 直播(HLS)，MP4，MP3，WebM，M4A，MPEG-TS 和 AAC）。 支持高级的 HLS 特性，例如正确处理EXT-X-DISCONTINUITY 标签； 
 - 支持自定义和扩治你的使用场景。ExoPlayer 专门为此设计；
 - 便于随着 App的升级而升级。因为 ExoPlayer是一个包含在你的应用中的库，对于你使用哪个版本有完全的控制权，并且你可以简单的跟随应用的升级而升级； 
 - 更少的适配性问题。

ExoPlayer 的缺点：

 - ExoPlayer 的音频和视频组件依赖 Android 的 MediaCodec 接口，该接口发布于 Android4.1（API 等级
   16）。因此它不能工作于之前的Android 版本。

**Vitamio**

[Vitamio demo 地址][3]

**用法**
Vitamio的使用步骤：  
 1. 下载Vitamio库，并作为工程依赖。
 2. 在Activity的onCreate方法中添加如下代码，初始化Vitamio的解码器
```
    @Override public void onCreate(Bundle icicle){
        super.onCreate(icicle)
        if(!LibsChecker.checkVitamioLibs(this))
        return ;
    }
```

3. 在AndroidMainfest.xml中声明InitActivity

```
<activity android:name="io.vov.vitamio.activity.InitActivity" android:configChanges="orientation|screenSize|smallestScreenSize|keyboard|keyboardHidden"
    android:launchMode="singleTop" android:theme="@android:style/Theme.NoTitleBar"
    android:windowSoftInputMode="stateAlwaysHidden"/> 

```
4. 其余步骤和使用 Android 原生的 VideoView 是一样的。

**优点**

 - 强大、支持超多格式视频和网络视频播放
 - 使用简单。调用非常简单，方便使用。
 
**ijkplayer** 【哔哔哩开源】
[ijkplayer 项目地址][4]

**用法**
1.clone ijkplayer 项目到本地

2.编译 ijkplayer

下面是我的编译环境

 - 编译环境 Mac OS X 10.10.5
 - Android
 - NDK — android-ndk-r10e
 - Android Studio 1.3.1
 - Gradle 2.4
编译之前要安装 homebrew, git, yasm。

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
 brew install git
 brew install yasm
 
配置 NDK

```
# add these lines to your ~/.bash_profile or ~/.profile
# export ANDROID_SDK=<your sdk path>
# export ANDROID_NDK=<your ndk path>

clone 项目

git clone https://github.com/Bilibili/ijkplayer.git ijkplayer-android
```

编译 ffmpeg    

```
    cd ijkplayer-android 
    ./init-android.sh 
    cd config
    rm module.sh
    ln -s module-default.sh module.sh
    cd android/contrib
    ./compile-ffmpeg.sh clean
    ./compile-ffmpeg.sh all
```

ffmpeg 编译通过后，编译 ijkplayer 

```
cd ..
./compile-ijk.sh all
```  
编译通过后在 android 目录下有 ijkplayer 文件夹，这个是 ijkplayer 的 demo。导入 Android Studio 即可。ijkplayer 的 demo 中提供了 IjkVideoView 类，它封装了 Android 原生的 MediaPlayer 、ExoPlayer 和 IjkMediaPlayer。可根据需要选择。

**优缺点**

 - ijkplayer 最大的优点就是可以根据需要编译需要的解码器。在编译的时候通过 ln -s module-default.shmodule.sh 选择要编译的解码器。ijkplayer 在 config目录下提供了三种 module.sh 。也可自己修改module.sh 。
 - ijkplayer 的缺点是库太大。加入项目后会大大增加你的 APP 的大小。

    


  [1]: https://github.com/google/ExoPlayer.git
  [2]: http://blog.qiji.tech/wp-content/uploads/2016/03/standard-model.png
  [3]: https://github.com/yixia/VitamioBundle.git
  [4]: https://github.com/Bilibili/ijkplayer.git
