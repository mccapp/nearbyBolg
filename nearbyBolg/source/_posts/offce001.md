---
title: offce 001
date: 2015-12-14 12:12:55
categories: offce
tags: [offce]
---
View的绘制流程、Activity、Window、View的关系
Activity启动时创建Window、ViewRoot并建立关联，流程如下：
```
class ActivityThread {
  // startActivity最终会调用到这里
  fun handleLaunchActivity(){
	// 1. performLaunchActivity() 创建activity
    // 2. activity.attach() 内部创建了PhoneWindow
    // 3. activity.onCreate() -> setContentView，实际调用window的对应方法，创建DecorView 
  }
  
  fun handleResumeActivity(){
    // 1. activity.onResume()
    // 2. 获取activity的window对象，添加DecorView到WindowManagerGlobal中
    // 3. WindowManagerGlobal.addView(DecorView)时，创建了ViewRootImpl，所有view绘制的工作都是
    //		ViewRootImpl来调度，在这里才建立了ViewRootImpl和View的关联
  }
}

class ViewRoot {
// ViewRoot添加view后，会执行ViewRoot.requestLayout
// scheduleTraversals -> 在消息队列中插入一个同步消息屏障,保证UI优先绘制 
//  -> 通过choreographer提交绘制任务，同时向底层请求sync信号，
//  -> 在下一次信号到来时JNI回调doTraversal，并移除屏障消息
//  -> doTraversal中调用了performMeasure、performLayout和performDraw进行测量、布局和绘制流程
}

```
MeasureSpec是一个32位int值，高2位表示测量模式，后30位表示在该模式下的测量值，一个view的MeasureSpec由自己的LayoutParams和父View的MeasureSpec共同决定。测量过程实际是递归的测量子view后再设置自己的尺寸。

#onCreate、onResume中能否获取到View的宽高，为什么？
由于View的measure过程和Activity的生命周期方法不是同步执行的，如果View还没有测量完毕，那么获得的宽/高就是0。所以在onCreate、onStart、onResume中均无法正确得到某个View的宽高信息。
解决方式如下：

1.view.post(runnable)，注意这里handler.post(runnable)是不行的，View.post会先判断attachInfo是否为空，如果为空就放到一个等待执行的队列中，等待View被添加（dispatchAttachedToWindow）之后才执行，这时测量已经完毕了，如果不为空，表示View已经被添加，就调用attachInfo中的Handler来post任务，所以是一定能获取到的。注意api23以下和以上的逻辑不一样：

Api23以下：调用的ViewRootImpl.getRunQueue().post()，执行时机是doTraversa()中，这个方法又是在下一个同步信号来的时候调用的，参考屏幕刷新机制。




Api23以上：调用的是getRunQueue().post() ，它的执行时机是View被添加之后执行，如果View只是创建出来没有被添加，那将一直得不到执行。


2.使用ViewTreeObserver

#卡顿原理、屏幕刷新机制、卡顿监控
#卡顿的根本原因：view在16ms内不能完成绘制造成掉帧，常见造成掉帧的原因又有：

1.主线程有耗时操作（合理使用线程来执行耗时任务）
2.View本身太复杂、嵌套过多导致绘制超过16ms（优化View的层级、合理使用include、ViewStub标签）
3.内存抖动造成频繁GC，例如循环内部创建对象，onDraw中创建对象等。（优化内存泄漏、对一些需要频繁创建的对象采用对象池技术）

#屏幕刷新机制：

1.基于handler消息队列，如BlockCanary，handler分发消息前后都会打印日志，可以自定义Printer，计算Looper两次获取消息的时间差，如果时间太长就说明Handler处理时间过长，直接把堆栈信息打印出来，就可以定位到耗时代码
2.代码插桩，在方法的前后插入计时代码来监控执行时间，缺点是包增大，无法监控系统方法，并且需要过滤简单方法
3.循环插入空消息到消息队列，监控这个消息的处理时间，例如每隔1秒插入一条空消息，如果这条消息处理时间间隔大于一定时间，则认为发生了卡顿

#RxJava的原理
Rxjava每个操作符会生成一个新的Observable,同时持有上游事件源和下游Observer，最终在subscribeActual中实现自己的操作逻辑，并连接上下游。
Rxjava有点像观察者模式和责任链模式的结合，普通的观察者模式一般是被观察者通知多个观察者，而Rxjava则是被观察者通知第一个Obsever,接下来Observer依次通知下一个节点的Observer，形成一个“观察链”，将观察者模式进行了一种类似链式的变换，每个节点又会执行它不同的“职责”。

1.SubscribeOn节点在订阅的时候，将它的上游节点的subscribe操作，以runnable的形式交给调度器在执行，在io调度器就是一个线程池，他影响的是事件源的发射行为，如果多次subscribeOn相当后一次subscribeOn把上一次subscribeOn行为在线程池里执行了一次，最终就只有最上边的一个起作用；
2.observeOn会将它下游的Observer放到切换的线程中执行，因此observeOn影响的是它的下游，多次调用影响的是这次到下一次observeOn之间的代码；

#Rxjava中调度器
Schedulers.io()：无边界线程池作为支撑的一个Scheduler，线程可以无限增长，它适用于非CPU密集的I/O工作，比如访问文件系统、执行网络调用、访问数据库等
Schedulers.computation()：用于执行CPU密集的工作，比如处理大规模的数据集、图像处理等等。它由一个有界的线程池作为支撑，线程的最大数量就是可用的处理器数量
Schedulers.newThread()：这个Scheduler 每次都会创建一个全新的线程来完成一组工作
**Schedulers.single()：**只有一个线程作为支撑，只能按照有序的方式执行任务
**Schedulers.from(Executor executor)**我们可以使用它创建自定义的Scheduler
AndroidSchedulers.mainThread()：Android主线程调度器

#事件分发机制
事件传递的顺序：Activity->Window->DecorView

dispatchTouchEvent中：

1.判断是否需要拦截事件的标记intercepted
```
// 如果是down事件或者mFirstTouchTarget不为空
if (actionMasked == MotionEvent.ACTION_DOWN || mFirstTouchTarget != null) {
  final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
  if (!disallowIntercept) {
    // 判断disallowIntercept标记
    // 如果允许拦截则调用onInterceptTouchEvent
    intercepted = onInterceptTouchEvent(ev);
  } else {
    // 有disallowIntercept标记，不拦截
    intercepted = false;
  }
} else {
  // 已有mFirstTouchTarget或者不是down时间，直接拦截
  intercepted = true;
}


```
2.尝试分发事件：如果第一步不需要拦截，并且不是cancel状态，分发给子view

```
if (!canceled && !intercepted) {
	// 遍历子view，判断坐标是否在view范围内并且view没有处于动画状态
  // 满足条件则交给子view的dispatchTouchEvent来处理
  // 如果子view处理了事件，则把子view赋值给mFirstTouchTarget
}

```
3.再次分发事件：判断firstTouchTarget是否为空，如果为空表示没有子view处理事件，则间接的交给自己的onTouchEvent来处理，为空则直接交给firstTouchTarget处理
```
if (mFirstTouchTarget == null) {
  // 没有子view处理事件，交给自己的onTouchEvent处理
} else {
  // 直接交给mFirstTouchTarget处理后续事件
}
```
4.**onTouchListener ：**如果有onTouchListener则优先交给onTouchListener处理，onTouchListener返回true则onTouchEvent将不会调用
5.cancel事件：父视图先不拦截，然后在 MOVE 事件中重新拦截，此时子 View 会接收到一个 CANCEL 事件，例如move出子view范围，或者scrollView中事件首先传递给子view，如果滑动则会被拦截。

#LeakCanary原理

首先java的四种引用，强、软、弱、虚四种引用，配合ReferenceQueue使用，在构造弱引用时传入ReferenceQueue，在垃圾回收之前，会将引用放入队列中，可以通过队列中是否有对象的引用来判断对象是否被回收；

具体就是在Application中注册ActivityLifecycleCallbacks监听activity的生命周期，在onDestory的时候，新建一个弱引用传入队列，在线程空闲的时候，会尝试清除队列的弱引用，如果成功则没有发生泄漏，如果失败，则尝试GC，GC之后再
次尝试清除弱引用，如果失败则发生了内存泄漏

Fragment也类似，在Activity创建时获取到FragmentManager注册一个fragmentLifecycleCallbacks，然后观察fragment；

#Retrofit原理
核心原理：在createService时，创建一个Api接口的动态代理，在loadServiceMethod方法先查找缓存，没有找到则解析Api接口，解析包括两部分，一个是方法上的注解，包括url、Header、请求参数等，第二个是方法的返回类型和参数类型，也就是CallAdapter和ConverAdapter

CallAdapter用于把结果适配成Rxjava、kotlin协程等返回类型

ConverAdapter用于把参数转化为json或者其他格式传输。

loadServiceMethod完毕后，实际上是把请求组装成一个OkHttpCall，用okhttp来进行具体的请求。

#OkHttp原理

优点：连接池技术复用连接，可以降低延迟，无缝支持gzip减少数据量，支持http2以及SPDY多路复用技术

责任链设计模式：用来处理相关事务责任的一条执行链，执行链上有多个节点，每个节点都有机会（条件匹配）处理请求事务，如果某个节点处理完了就可以根据实际业务需求传递给下一个节点继续处理或者返回处理完毕

#请求流程：
1.构建request
2.通过dispatcher执行请求，dispatcher内部包含三个队列：同步请求队列、异步等待队列和异步执行队列，对于同步请求，直接添加到同步请求队列执行，异步请求则添加到等待队列中，然后判断请求数是否大于最大请求数、以及同一个主机的最大请求数，如果可以执行，则提升到执行队列进行执行
3.无论是同步请求还是异步请求，最终都调用到getResponseWithInterceptorChain方法，核心就是拦截器链，包含了7大拦截器，分别负责不同的功能，每一个拦截器都可以自己处理请求，然后直接返回或者交给下一节点来处理。

	自定义拦截器
	RetryAndFollowUpInterceptor：重试和重定向拦截器
	BridgeInterceptor：用来设置一些必要的header
	CacheInterceptor：处理缓存
	ConnectInterceptor：负责建立服务器连接，优先从连接池中找到可用连接(soket连接是否可用，是否超时等，如不可用则会从连接池中移除)，否则打开一个新的连接
	自定义网络拦截器
	CallServerInterceptor：最后一个拦截器，用于真正发送网络请求，返回response

#为什么要组件化，怎么实现？了解的路由框架

痛点：在业务开发中，各个业务模块依赖关系复杂，耦合严重，造成app编译缓慢、不能并行开发、组件复用性不高的问题。通过组件化改造，可以对各个模块进行了业务隔离，使模块可以单独编译运行，降低耦合度，提高了开发和调试效率。

##组件拆分：

应用入口：一个空壳app，提供应用的启动页面

支撑业务组件：网络请求、图片加载等一些支撑服务

通用业务模块：例如支付、推送、地图等sdk，封装后对外提供调用接口

应用业务组件：具体的业务，可以提出来作为一个单独应用的功能模块，两两之间不互相依赖

##模块间的通信，需要引入路由框架，主流路由框架大概分为两种

ARouter：路由表+接口下沉的方式，在编译期间通过注解处理器扫描目标类，然后解析参数生成路由表，
模块服务通过接口下沉的方式，把接口定义在公共库中，具体实现类在业务模块中，在编译时通过扫描字节码发现实现类，然后通过ASM等字节码修改技术完成服务的注册；

CC：组件总线的方式，类似于EventBus发送消息一样，直接找到目标组件，发送一条指令执行对应操作，
通过gradle的Transform API:在编译时(dex/proguard之前)扫描当前要打包到apk中的所有类 ，然后通过ASM技术修改字节码，生成代码完成组件的注册；

#android优化的数据结构：SparseArray、ArrayMap

SparseArray的key只能为int，双列数组结构，优化了自动装箱的过程，并且会自动排序，查找采用二分查找，在数据量不大的情况下效率高

ArrayMap和SparseArray类似，但是key可以是对象，存储的时候存储通过key的hash值排序，也采用二分查找；

#进程间通信的方式

1.广播

2.Messenger，封装的Binder，可以执行简单的串行通信。

3.ContentProvider，适用于进程间的数据共享，可以进行操作权限的控制，如数据库的读写等。

4.Socket，适用于网络通信，用于传输原始的字节流，两次拷贝、效率低于binder

5.Binder，适用于复杂的进程通信，功能强大，CS架构，底层基于内存映射，只进行一次拷贝，所以效率高，可以验证通信的进程id，所以比socket更安全。

#AIDL和Binder机制

AIDL实际上是一套快速实现Binder通信的工具，通过定义AIDL接口，系统会自动生成一个类实现IInterface接口，内部包含Stub和Proxy两个内部类。服务端继承Stub提供服务。

##关键类和方法

Stub：服务端通过继承Stub，实现定义的接口来提供服务

Proxy：服务端对象的本地代理，客户端通过它来间接调用服务端接口的方法

asInterface：客户端调用，将binder对象转化为AIDL接口类型对象，如果和服务端在同一进程，返回Stub对象本身，否则返回Stub.proxy代理对象

asBinder：返回binder对象

onTransact：运行在服务端binder线程池，客户端请求时，远程请求会被系统封装后交给此方法处理

transact：运行在客户端，客户端发起请求时线程挂起，调用到服务端onTransact，等待其返回后才继续执行

#Binder机制

基于C/S架构，包括Server、Client、ServiceManager和Binder驱动，其中Binder驱动运行在内核空间，其他的运行在用户空间

ServiceManager负责服务管理，服务端向ServiceManager注册后，客户端可以向ServiceManager查询获取到目标服务的引用

Binder驱动：负责进程之间的Binder通信的建立，基于内存映射技术，传统的通信方式需要将数据从用户空间拷贝到内核空间，然后从内核空间拷贝到目标进程的用户空间，需要两次拷贝，Binder驱动可以建立用户空间内存和内核空间内存的一个映射，

只需要一次拷贝，非常高效。

#kotlin的扩展方法是怎么实现的，inline关键字的作用

扩展方法编译成java后，实际上是一个 public static的静态方法，传入了对象实例和参数
Inline关键字修饰的函数是内联函数，用于优化函数调用的压栈操作，相当于在编译期间把内联函数的代码拷贝到函数调用处，可以提升性能，但是增加了代码量


#Handler机制

##关键类：

Handler：用于发送消息和处理消息，发送消息就是将消息对象放入当前线程的MessageQueue中，然后Looper轮询到消息后交给自己处理，构造时需要传入Looper或者自动获取当前线程的Looper

Looper：轮询器，一个线程只能有一个Looper，用于从消息队列中轮询消息，然后交给对应的Handler处理

MessageQueue：消息队列，是一个阻塞队列，当没有消息是线程会被阻塞，等待有消息时唤醒线程

Message：消息对象，采用了对象池技术，可以避免对象的频繁创建开销

#消息的发送和轮询


发送消息：最终都会调用MessageQueue的enqueueMessage中，如果队列中没有消息或者消息的发送时间小于第一个消息，则直接放入队列头部，否则则根据时间插入到队列中的合适位置，同时会刷新needWeak标记，然后判断needWeak，如果需要唤醒，调用naviteWeak唤醒Looper。


获取消息：核心方法为MessageQueue的next，首先调用nativePollOnce，这个方法是一个native阻塞方法（通过监控文件描述符的io操作来实现），新消息放入队列时，会调用naviteWeak来唤醒。唤醒之后，如果是消息屏障就先处理异步消息，否则判断消息执行时间是否到达来返回消息或者进入下一个循环继续阻塞。


IdleHandler：在消息队列空闲的时候，会尝试执行IdleHandler的任务


消息屏障机制：当messageQueue取出屏障消息时，会开启循环，找到下一个异步消息执行，相当于阻塞了同步消息而优先执行异步消息，Android中所有UI绘制都是异步消息，可以保证UI绘制任务优先执行。



#postDelay怎么保证消息的顺序

发送消息时会对消息队列按执行时间进行排序，如果没有消息或者当前消息执行时间小于队列的第一条消息，则直接插入到表头，否则会插入到合适的位置，保证delay时间长的不会阻塞住时间短的。

#Activity启动流程
1.Luancher 进程通过binder向AMS发起startActivity请求
2.AMS收到请求， ActivityStarter解析flag，启动模式等，ActivityStack处理activity栈
3.然后AMS通过socket调用到Zygote，fork新的app进程
4.app进程创建后，再通过binder向AMS发起attachApplication请求
5.AMS通过binder调用发送scheduleLaunchActivity到app进程
6.APP进程的binder线程ApplicationThread接收到请求，通过handler发送LAUNCH_ACTIVITY消息到主线程
7.ActivityThread接收到消息，执行到handleLaunchActivity，开始Activity的生命周期
![Alt text](../../../../images/a3aed8572f104535a3b0e679ea8c4cd9_tplv-k3u1fbpfcp-zoom-1.png)
应用第一次启动时，zygote进程fork出应用进程后，AMS会保存一个ProcessRecord信息（包名+进程uid），下一次启动判断这个ProcessRecord已经存在的话，就不会再新建进程，这就属于应用内打开Activity的过程了

#APK打包流程
![Alt text](../../../../images/4689f854f716495ea598a49d6ddac2b0_tplv-k3u1fbpfcp-zoom-1.png)

1.打包资源文件，生成R.java文件
2.处理aidl文件，生成相应的Java文件
3.编译项目源代码，生成class文件（所有的Java代码，包括R.java和.aidl文件）
4.转换所有的class文件，生成classes.dex文件
5.打包生成APK文件
6.对APK文件进行签名
7.对签名后的APK文件进行对齐处理。对齐的主要过程是将APK包中所有的资源文件距离文件起始偏移为4字节整数倍，这样通过内存映射访问apk文件时的速度会更快。对齐的作用就是减少运行时内存的使用


#APK安装流程：

1.拷贝阶段：通过PMS通过handler发送一个安装消息，包含一些安装的参数，PackageHandler收到消息后通过隐式intent绑定到拷贝的service。检查apk安装路径，包的状态，然后拷贝至/data/app包名下
2.装载阶段：installPackageLI，PackageParser解析AndroidManifest文件，解析四大组件等信息
3.验证apk的签名信息
4.执行 dex 优化，实际为 dex2oat 操作，用来将 apk 中的 dex 文件转换为 oat 文件
5.安装apk，创建data目录，安装成功则更新权限等信息
6.发送成功广播，安装失败则删除安装包和缓存

#Android系统启动流程



1.开机加载BootLoader，加载Linux内核
2.启动Init进程
3.读取init.rc配置文件，启动几个关键进程，包括Zygote、ServiceManager、SurfaceFlinger和MediaServer
4.zygote进程启动java runntime，然后fork出sytem_server进程
5.sytem_server启动AMS、WMS、电源管理、等等系统服务，并通过Binder注册到ServiceManager。
6.启动Launcher显示桌面
![Alt text](../../../../images/96bd5c17c1374385971a2ad840d964bc_tplv-k3u1fbpfcp-zoom-1.png)

#Webview的漏洞
1.4.2版本的addJavascriptInterface 造成的远程代码执行漏洞，当JS拿到Android这个对象后，就可以调用Android对象中所有的方法，包括系统类（java.lang.Runtime 类），从而执行任意代码

	4.2版本以上通过对调用的方法添加JavascriptInterface注解避免漏洞
	4.2以下采用url拦截或者js弹窗拦截的方式进行交互，不能采用对象映射


2.密码明文存储
3.域控制不严问题，允许导出的WebActivity未关闭file协议，可以外部启动并加载恶意file协议的文件，从而访问私有文件，所以不需要使用file协议的，需要关闭



#Dalvik与ART的区别
1.Dalvik：即时编译，应用每次运行都需要通过即时编译器（JIT）将字节码转换为机器码，所以运行效率相对ART较低。由于不需要预编译，所以安装过程较快
2.ART ：应用在第一次安装的时候，字节码就会预编译（AOT）成机器码，这样的话，虽然设备和应用的首次启动（安装慢了）会变慢，但是以后每次启动执行的时候，都可以直接运行，因此运行效率会提高
3.ART占用空间比Dalvik大（字节码变为机器码之后，可能会增加10%-20%）
4.预编译也可以明显改善电池续航，从而减少了 CPU 的使用频率，降低了能耗。


#热修复和插件化原理
java类加载机制

双亲委托机制：如果一个类加载器收到了类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行，如果父类加载器还存在其父类加载器，则进一步向上委托，依次递归，请求最终将到达顶层的启动类加载器，如果父类加载器可以完成类加载任务，就成功返回，倘若父类加载器无法完成此加载任务，子加载器才会尝试自己去加载

Java的类加载器分为：根加载器（加载java核心类）、扩展类加载器（加载jre扩展目录）、应用类加载器（加载classPath指定目录的类，自定义类加载器一般继承此加载器）

Android的类加载器：根加载器、BaseDexClassLoader、PathClassLoader（加载安装到系统的APK）、DexClassLoader（加载指定目录的dex和apk）

#热修复和插件化的区别

热修复：替换bug的类，需要把修复类抢先于bug类进行加载，让bug类得不到加载。通过反射修改DexClassLoader中DexPathList的dexElements数组，将需要加载的dex添加到数组前面。

插件化：运行未安装的插件apk的代码，不涉及到抢先加载，只需要将dex加载到dexElements中


#热修复的CLASS_ISPREVERIFIED问题

热修复是需要修复有bug的类，所以需要把dex放在数组的前端抢先加载补丁类，在虚拟机启动的时候，在verify选项被打开的时候，如果static方法、private方法、构造函数等，其中直接引用到的类都在同一个dex文件中，那么该类就会被打上CLASS_ISPREVERIFIED标志，且一旦类被打上CLASS_ISPREVERIFIED标志其他dex就不能再去替换这个类。
​		为了阻止类被打上CLASS_ISPREVERIFIED标志，先将一个预备好的hack.dex加入到dexElements的第一项，让后面的dex的所有类都引用hack.dex其中的一个类。

#常见热修复框架


Tinker实际上是类加载方案的升级，它增加了dex的差分算法，再将差分dex与apk中的classex.dex做合并，在运行bug类之前抢先加载补丁。

tinker差分算法：dexDiff , 新旧dex先排序，然后定义两个指针依次往下挪，如果old>new，则一定是新增，如果old<\new，则是删除，如果相等，则索引可能不同，需要记录索引的变化，最后如果相同的索引既有删除又有新增，则优化为replace操作。资源差分则采用BSDiff。

美团Robust，Instant Run方案，用代码插桩的方式，在每个方法中插入一段开关代码，如果需要修复，则走入if判断，执行补丁中的同名类的同名方法


#插件化方案

插件化主要是加载新的功能模块，最主要的功能除了加载类，还需要加载资源和生命周期的管理。


资源加载：通过反射AssetManager ，将资源所在路径添加到AssetManager的path中，然后创建一个Resource，hook住activity替换mResources实现资源的访问。具体分为两种：


1.合并式：插件资源合并到主工程，插件和主工程可以直接访问资源，合并资源会造成资源id冲突，所以需要修改aapt源码，id格式为0xPPTTNNNN，在编译期修改高两位PP段，不同插件使用不同的PP段标识。
修改resources.arsc文件，该文件列出了资源id到具体资源路径的映射，相当于一个索引。

2.独立式：插件只访问插件内部的资源，不能共享，也不会冲突。


**生命周期的管理：**没有在清单文件中注册过的Activity不能启动，现在的主流做法是预留各种启动模式的代理Activity占坑，然后通过hook住mInstrumentation对象，在启动插件Activity时替换intent为代理的Activity，从而绕过AMS的验证，系统以为是启动的代理Activity，然后真正启动时，需要还原原先的Intent，启动插件Activity，同时hook替换mResources，这样就实现了生命周期管理

#VirtualApk原理
如何加载插件类：（类加载机制）
插件创建ClassLoader，父类加载器是宿主的classloader，这样插件可以反射访问到宿主的类。

然后判断COMBINE_CLASSLOADER标记，如果COMBINE_CLASSLOADER为true，则会把dex插入到父类加载器的elements后边，使宿主可以访问插件的类。如果为false表示宿主与插件隔离，宿主不能访问插件。不论true或false，插件都可以访问宿主。

DroidPlugin是采用隔离模式，插件的classloader的父类加载器是BootClassLoader，所以相互都不能访问。

#如何加载资源：

1.COMBINE_CLASSLOADER为true：把插件资源添加到宿主Resources的AssetsManager的资源路径中去。
编译时过滤宿主和插件中的重复资源，然后修改R和资源表文件，让插件中只保留新的资源。
存在的问题：

2.宿主和插件开发可能是并行的，过滤资源时候如果依赖的是1.0的宿主，在1.1中资源发生了变化，插件就会找不到资源，滴滴是让public.xml让宿主资源id不可变来实现。
如果宿主和插件有相同名字的资源，例如都有一个about字符串，由于资源过滤，插件的会被过滤掉。


#activity的启动：

hook系统instrumentation，判断如果启动的是插件的类，则通过预埋的activity绕过ams对activity的验证，然后在真正启动activity的时候，还原intent来启动目标类，这样目标类就有正常的生命周期了

#service的启动：
hook系统ActivityManager，创建一个动态代理来替换系统中的单例对象，实际上启动LocalService来代理目标服务的生命周期。

#广播：

解析清单文件，把注册的静态广播转换为动态广播。


启动activity时的问题

在创建插件的ClassLoader时，有一个COMBINE_CLASSLOADER标记用来设置是否要讲插件的dex插入到宿主的dex数组中，让宿主可以访问插件的类。COMBINE_CLASSLOADER标记为false时，宿主不能访问到插件类
源码instrumentation中handleMessage处理启动activity时，给intent的extras设置了宿主的类加载器，如果extra中有一个插件中才有的序列化对象，读取extra时反序列化会出错，就会抛出找不到类异常。到现在版本仍未解决。
解决方案：直接用一个新的intent包装原始的intent来替换，intent是parcelable的。


#RecyclerView缓存


1.mAttachedScrap和mChangedScrap，用于缓存屏幕内的ViewHolder，例如下拉刷新后，屏幕内的ViewHolder需要刷新数据
2.mCachedViews，移除屏幕之外的，默认缓存两个，因为接下来可能马上往回滑动，再次使用到
3.ViewCacheExtension，预留的一个缓存扩展，暂时没有用到
4.RecycledViewPool，缓存屏幕外的 ViewHolder，需要重新绑定数据

#AsyncTask原理和缺陷

实际是通过Handler+线程池实现，内部含有两个线程池，一个用于排队，一个用于真正执行任务

#缺陷：
执行任务的线程池是个静态的全局线程池，最大线程数为128，如果任务队列满了，然后最大线程数也满了，再提交任务会出现崩溃；解决方案为自定义线程池
必须主线程初始化，内部handler获取主线程looper，否则不能正确切换到主线程。
结果丢失问题：如果activity重建，例如切横竖屏，因为持有的引用是重建之前的，新的Activity无法接收到结果。
内存泄漏，退出页面需要正确取消



#性能优化相关

卡顿优化

1.View本身绘制时间过长超过16ms造成掉帧，所以需要减少View嵌套层级，使用ViewStub和merge标签，优化过度绘制等

2.主线程执行耗时任务，合理使用线程，将耗时任务放到后台进行

3.内存抖动，会频繁触发GC，造成卡顿。使用内存分析工具优化内存使用，减少不必要对象的创建

#耗电优化

合理使用后台服务，合并网络请求，使用protobuf替换json进行服务端请求，cpu休眠锁等

#内存优化
内存泄露优化（包括常见的内存泄漏、分析方法、LeakCanrary等）、使用优化的数据结构（ArrayMap和SparseArray），对频繁创建的对象使用对象池技术，减少不必要的内存开销，

#启动优化

Application的attachBaseContext和onCreate，三方sdk采用线程池异步初始化，activity的onCreate中，设置布局的优化（xml的io操作、反射创建view的操作，通过异步inflater和自定义inflater.factory来优化）

1.延迟初始化非必要的库，拓展到异步启动器的设计和实现，有向无环图来解决库依赖的问题。
2.给闪屏页设置图片背景避免冷启动白屏，其实不能加快启动，只是避免白屏给用户的体验不佳


#apk大小优化
1，开启混淆压缩代码
2，压缩和混淆资源，图片压缩采用webP格式，整理Raw、assets资源
3，减少非必要so库，目前主流的机型都是支持armeabi-v7a的，并且armeabi-v7a兼容armeabi
4，移除未使用的资源，如图标，字符串，字体等
5，一些小图像可以使用矢量图，大矢量图渲染时间很长，不适用
6，减少三方库使用，避免枚举的使用
7，动态下发一些资源，如换肤包，so，字体等

#网络优化
1.HttpDNS优化，传统DNS解析一般是用UDP的方式与DNS服务器交互，HttpDNS采用Http协议交互，绕过运营商的 LocalDNS 服务器，有效的防止了域名劫持，提高域名解析的效率
2.使用缓存，需要服务端支持，或者通过OkHttp拦截器添加统一的缓存策略
3.HTTP 1.1支持长连接（PersistentConnection）和请求的流水线（Pipelining）处理，在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗和延迟。在HTTP1.1中默认开启keep-alive
4.数据压缩，gzip压缩，http2.0也支持header的压缩
5.根据网络质量来下载不同质量的图片

#View inflate流程

1.先从resource中获取一个xml parser用于加载布局
2.读取layout文件，调用createViewFromTag创建View
3.tryCreateView中，依次判断有没有设置Factory2和Factory，如果有则调用它的createView来创建view
4.如果没有factory则调用自己的createView来创建，内部是使用反射创建对象
5.注意，Factory2继承Factory增加了一个创建view的方法，相当于是一个扩展，它们可以对view创建的过程进行拦截，在创建view的时候做一些事情，例如换肤功能就用到这里。



#换肤的原理
1.制作皮肤包apk，只包含颜色、图片等，通过网络下载到sd卡；
2.通过反射构造皮肤包的AssetManager，再用此AssetManager创建Resource。
3.原apk和资源包中资源名称一样，提供一个资源映射的方法，通过原来的资源id找到资源名称，然后在皮肤包中查找皮肤资源具体的值；
4.通过自定义LayoutInflater传入自定义的Factory2拦截view的创建过程，查找到需要换肤的view和对应的可替换属性名(background、color、textcolor等)、属性值的类型（color\drawable\mipmap...）、属性值在原apk中的资源名(如color1)和资源的id，保存起来；
5.点击换肤时，遍历需要换肤的view集合，调用对应的方法（setColor等等）设置新的值。

#Android的startActivityForResult的实现为什么不使用回调。
因为匿名内部类会持有外部类的引用，使用回调时，例如A启动B去获取result，由于某些原因原A已经被销毁了，当B设置结果返回A时，实际上A已经被系统重建，和原先的A不是同一个对象了，所以就不能正确的获取结果。

#如何跨app启动activity？
1.shareUserId，设置同一个shareUserId的应用可以直接启动。
2.Exported 设置为true，向外部暴露activity，允许外部启动。
注意：为了安全需要添加自定义权限控制，注意被暴露的有权限的app需要先被安装，否则会获取不到权限。
会导致拒绝服务漏洞，例如：A启动B中的activity，往intent中添加一个序列化对象，这个对象只在A中有，B中没有这个类，如果在B中访问intent的extra，就会触发反序列化对象，由于找不到这个对象的类，造成B崩溃。处理方法：获取extra要捕获异常。
3.隐式启动activity，只要intentFilter匹配成功就可以启动。


#高性能日志采集
##传统直接读写文件的方式的缺点
读写文件的IO操作，需要两次拷贝，用户空间到内核空间，内核空间再到硬盘。为了避免频繁IO，采用缓存日志到内存，达到一定量时再统一写入文件，虽然避免了频繁IO，但是可能造成crash时日志丢失。多进程也无法保证写入顺序

#mmap方案：
mmap是一种内存映射文件的方法，即将一个文件或者其它对象映射到进程的地址空间，实现文件磁盘地址和进程虚拟地址空间中一段虚拟地址的一一对映关系。mmap操作提供了一种机制，让用户程序直接访问设备内存，这种机制，相比较在用户空间和内核空间互相拷贝数据，效率更高。

 
#mmap的回写时机：

1.内存不足
2.进程退出
3.调用 msync 或者 munmap
4.不设置 MAP_NOSYNC 情况下 30s-60s(仅限FreeBSD)

多进程写入映射同一个内存也会造成写入顺序无法保证，所以可以选择多进程映射不同的文件，每隔一段时间合并一次来解决

#性能优化工具：

查看方法执行时间：

#TraceView（影响性能）

1.用Debug.startMethodTrace来打点，生成trace文件，然后用adb导出分析
2.使用profiler工具，选择cpu，输出trace文件

#Systrace（轻量级）

1.TraceCompat.beginSection
2.使用python命令导出html，查看wall time和 cpu time

#MAT分析内存泄露：
profiler分析内存，手动触发gc，然后输出堆转储hporf文件，然后用mat分析引用链

#内存抖动：

使用profiler工具排查
