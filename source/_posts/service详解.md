---
title: service详解
date: 2015-01-18 14:00:17
categories: android
tags: [service]
---


> **android server 详解**

 
>  Service的种类：



> 一、Service的种类

 1. 本地服务， Local Service
    用于应用程序内部。在Service可以调用Context.startService()启动，调用Context.stopService()结束。在内部可以调用Service.stopSelf()
    或
    Service.stopSelfResult()来自己停止。无论调用了多少次startService()，都只需调用一次stopService()来停止。
 2. 远程服务， Remote Service
    用于android系统内部的应用程序之间。可以定义接口并把接口暴露出来，以便其他应用进行操作。客户端建立到服务对象的连接，并通过那个连接来调用服务。调用Context.bindService()方法建立连接，并启动，以调用
    Context.unbindService()关闭连接。多个客户端可以绑定至同一个服务。如果服务此时还没有加载，bindService()会先加载它。

提供给可被其他应用复用，比如定义一个天气预报服务，提供与其他应用调用即可。

>  二、生命周期

1). 被启动的服务的生命周期：如果一个Service被某个Activity 调用 Context.startService方法启动，那么不管是否有Activity使用bindService绑定或unbindService解除绑定到该Service，该Service都在后台运行。如果一个Service被startService 方法多次启动，那么onCreate方法只会调用一次，onStart将会被调用多次（对应调用startService的次数），并且系统只会创建Service的一个实例（因此你应该知道只需要一次stopService调用）。该Service将会一直在后台运行，而不管对应程序的Activity是否在运行，直到被调用stopService，或自身的stopSelf方法。当然如果系统资源不足，android系统也可能结束服务。

2). 被绑定的服务的生命周期：如果一个Service被某个Activity 调用 Context.bindService 方法绑定启动，不管调用 bindService调用几次，onCreate方法都只会调用一次，同时onStart方法始终不会被调用。当连接建立之后，Service将会一直运行，除非调用Context.unbindService 断开连接或者之前调用bindService 的 Context 不存在了（如Activity被finish的时候），系统将会自动停止Service，对应onDestroy将被调用。

3).被启动又被绑定的服务的生命周期：如果一个Service又被启动又被绑定，则该Service将会一直在后台运行。并且不管如何调用，onCreate始终只会调用一次，对应startService调用多少次，Service的onStart便会调用多少次。调用unbindService将不会停止Service，而必须调用 stopService 或 Service的 stopSelf 来停止服务。

4).当服务被停止时清除服务：当一个Service被终止（1、调用stopService；2、调用stopSelf；3、不再有绑定的连接（没有被启动））时，onDestroy方法将会被调用，在这里你应当做一些清除工作，如停止在Service中创建并运行的线程。

> 特别注意：

 1. 你应当知道在调用 bindService 绑定到Service的时候，你就应当保证在某处调用 unbindService
    解除绑定（尽管Activity被finish的时候绑定会自动解除，并且Service会自动停止）

 2. 你应当注意 使用 startService 启动服务之后，一定要使用
    stopService停止服务，不管你是否使用bindService。

 3. 同时使用 startService 与 bindService 要注意到，Service
    的终止，需要unbindService与stopService同时调用，才能终止 Service，不管 startService 与
    bindService 的调用顺序，如果先调用 unbindService 此时服务不会自动终止，再调用 stopService
    之后服务才会停止，如果先调用 stopService 此时服务也不会终止，而再调用 unbindService 或者 之前调用
    bindService 的 Context 不存在了（如Activity 被 finish 的时候）之后服务才会自动停止。

 4. 当在旋转手机屏幕的时候，当手机屏幕在“横”“竖”变换时，此时如果你的 Activity 如果会自动旋转的话，旋转其实是 Activity
    的重新创建，因此旋转之前的使用 bindService 建立的连接便会断开（Context 不存在了），对应服务的生命周期与上述相同

 5. 在 sdk 2.0 及其以后的版本中，对应的 onStart 已经被否决变为了 onStartCommand，不过之前的 onStart
    任然有效。这意味着，如果你开发的应用程序用的 sdk 为 2.0 及其以后的版本，那么你应当使用 onStartCommand 而不是
    onStart。
![此处输入图片的描述][1]

> 例子：

        public class ServiceDemo extends Service {
        
        public static final String TAG = "ServiceDemo" ;
        public static final String ACTION = "com.demo.SERVICE_DEMO";
    
        /**
         * onBind 是 Service 的虚方法，因此我们不得不实现它。
         * 返回 null，表示客服端不能建立到此服务的连接，所以不会调用onServiceConnected。
         */
        @Override
        public IBinder onBind(Intent intent) {
            Log.i(TAG, this.toString() + " ServiceDemo onBind");
            return null;
        }
    
        @Override
        public void onCreate() {
            Log.i(TAG, this.toString() + " ServiceDemo onCreate");
            super.onCreate();
        }
    	
        @Override
        public void onStart(Intent intent, int startId) {
    	Log.i(TAG, this.toString() + " ServiceDemo onStart");
    	super.onStart(intent, startId);
        }
    
        @Override
        public int onStartCommand(Intent intent, int flags, int startId) {
            Log.i(TAG, this.toString() + " ServiceDemo onStartCommand");
            return super.onStartCommand(intent, flags, startId);
        }
    	
        @Override
        public boolean onUnbind(Intent intent) {
            Log.i(TAG, this.toString() + " ServiceDemo onUnbind");
            return super.onUnbind(intent);
        }
    	
        @Override
        public void onDestroy() {
            Log.i(TAG, this.toString() + " ServiceDemo onDestroy");
            super.onDestroy();
        }
    
    }
    <!-- android:exported 这个属性用于指示该服务是否能够被其他应用程序组件调用或跟它交互。
         如果设置为true，则能够被调用或交互，否则不能。设置为false时，
         只有同一个应用程序的组件或带有相同用户ID的应用程序才能启动或绑定该服务。-->
    <service android:name=".ServiceDemo" android:exported="false">
        <intent-filter>
            <action android:name="com.demo.SERVICE_DEMO" />
            <category android:name="android.intent.category.default" />
        </intent-filter>
    </service>
    
    通过Context.startService(Intent)方法启动service或者Context.bindService方法来绑定service
    
    
        public class MainActivity extends ActionBarActivity {
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            findViewById(R.id.btn_bindService).setOnClickListener(new OnClickListener() {
                @Override
                public void onClick(View v) {
    	        bindService(new Intent(ServiceDemo.ACTION), conn, BIND_AUTO_CREATE);
    	    }
    	});
            
            findViewById(R.id.btn_unbindService).setOnClickListener(new OnClickListener() {
                @Override
    	    public void onClick(View v) {
    		unbindService(conn);
    	    }
    	});
             
            findViewById(R.id.btn_startService).setOnClickListener(new OnClickListener() {
                @Override
                public void onClick(View v) {
            	startService(new Intent(ServiceDemo.ACTION));
                }
            });
            
            findViewById(R.id.btn_stopService).setOnClickListener(new OnClickListener() {
    	    @Override
    	    public void onClick(View v) {
    		stopService(new Intent(ServiceDemo.ACTION));
    	    }
    	});
        }
    	
        ServiceConnection conn = new ServiceConnection() {
            public void onServiceConnected(ComponentName name, IBinder service) {
                Log.i(ServiceDemo.TAG, service.toString() + " onServiceConnected");
            }
    	public void onServiceDisconnected(ComponentName name) {
    	    Log.i(ServiceDemo.TAG, "onServiceDisconnected");
    	}
        };
        
        @Override
        protected void onDestroy() {
            super.onDestroy();
        }
    }

  ![此处输入图片的描述][2]

> 日志输出：

![此处输入图片的描述][3]
上面的截图是点击绑定服务时输出的。可以看出，只调用了onCreate方法和onBind方法，当重复点击绑定服务时，没有再输出任何日志，并且不报错。onCreate方法是在第一次创建Service时调用的，而且只调用一次。另外，在绑定服务时，给定了参数BIND_AUTO_CREATE，即当服务不存在时，自动创建，如果服务已经启动了或者创建了，那么只会掉调用onBind方法。
![此处输入图片的描述][4]
当解除绑定的时，可以看出，Service调用onUnbind和onDestroy销毁了服务。
![此处输入图片的描述][5]
上面的截图是在多次点击启动服务时输出的。可以看出，在第一次点击时，因为Service还未创建，所以调用了onCreate方法，紧接着调用了onStartCommand和onStart方法。当再次点击启动服务时，仍然调用了onStartCommand和onStart方法，所以，在Service中做任务处理时需要注意这点，因为一个Service可以被重复启动。
![此处输入图片的描述][6]
当点停止服务的时，Service只是执行onDestroy方法，跟绑定还是有点小区别。
这里说一下，平常使用多的是startService方法，可以把一些耗时的任务放到后台去处理，当处理完成后，可以通过广播来通知前台。
而onBind方法更多的是结合AIDL来使用，这样一个应用可以通过绑定服务获得的IBinder来拿到后台的接口，进而调用AIDL中定义的方法，进行数据交换等。

> 三、Local 与 Remote 服务绑定

1) Local 服务绑定：Local 服务的绑定较简单，首先在 Service 中我们需要实现 Service 的抽象方法 onBind，并返回一个实现 IBinder 接口的对象。

Service 中的代码：

        public class LocalService extends Service{
    	
        public static final String TAG = "LocalService" ;
        public static final String ACTION = "com.demo.LOCAL_SERVICE";
        public SimpleBinder sBinder;
    
        public class SimpleBinder extends Binder{
    
            public LocalService getService(){
                return LocalService.this;
            }
             
            public int add(int a, int b){
                return a + b;
            }
        }
    	
        @Override
        public void onCreate() {
    	super.onCreate();
    	// 创建 SimpleBinder
            sBinder = new SimpleBinder();
        }
    	
        @Override
        public IBinder onBind(Intent intent) {
    	// 返回 SimpleBinder 对象
    	Log.i(TAG, "LocalService onBind");
            return sBinder;
        }
    	
        @Override
        public boolean onUnbind(Intent intent) {
    	Log.i(TAG, "LocalService onUnbind");
    	return super.onUnbind(intent);
        }
    
    }
    <service android:name=".LocalService" android:exported="false">
        <intent-filter>
    	<action android:name="com.demo.LOCAL_SERVICE" />
    	<category android:name="android.intent.category.default" />
        </intent-filter>
    </service>
    public class LocalActivity extends ActionBarActivity {
    	
        private ServiceConnection sc;
        private boolean isBind;
    	
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
    	setContentView(R.layout.activity_local);
    		
    	sc = new ServiceConnection() {
    
                @Override
    	    public void onServiceConnected(ComponentName name, IBinder service) {
    	        Log.i(LocalService.TAG, "onServiceConnected");
    		LocalService.SimpleBinder sBinder = (LocalService.SimpleBinder)service;
                    Log.i(LocalService.TAG, "3 + 5 = " + sBinder.add(3, 5));
    	    }
    
                @Override
                public void onServiceDisconnected(ComponentName name) {
                    Log.i(LocalService.TAG, " onServiceDisconnected");
                }
            };
            
            findViewById(R.id.btn_bindService).setOnClickListener(new OnClickListener() {
                
                @Override
                public void onClick(View v) {
                    bindService(new Intent(LocalService.ACTION), sc, Context.BIND_AUTO_CREATE);
                    isBind = true;
                }
            });
            findViewById(R.id.btn_unbindService).setOnClickListener(new OnClickListener() {
                 
                @Override
                public void onClick(View v) {
                    if(isBind){
                        unbindService(sc);
                        isBind = false;
                    }
                }
            });
        }
    }

在 Activity 中，我们通过 ServiceConnection 接口来取得建立连接 与 连接意外丢失的回调。bindService有三个参数，第一个是用于区分 Service 的Intent 与 startService 中的 Intent 一致，第二个是实现了 ServiceConnection 接口的对象，最后一个是 flag 标志位。有两个flag，BIND_DEBUG_UNBIND 与 BIND_AUTO_CREATE，前者用于调试（详细内容可以查看javadoc 上面描述的很清楚），后者默认使用。unbindService 解除绑定，参数则为之前创建的 ServiceConnection 接口对象。另外，多次调用 unbindService 来释放相同的连接会抛出异常，因此我创建了一个 boolean 变量来判断是否 unbindService 已经被调用过。

运行结果：
![此处输入图片的描述][7]

在绑定服务的时候，需要一个服务连接对象，ServiceConnection，服务一旦连接，就会调用onServiceConnected方法，我们可以在这个方法里面返回我们的本地服务对象，具体看代码；而在服务断开时候会调用onServiceDisconnected方法，我们可以清理一些服务资源。

2) Remote 服务绑定：之前所谈的Service属于Local Service，即Service和Client在同一进程内（即同一application内），Service的生命周期服从进程的生命周期。在实际应用上，有时希望Service作为后台服务，不仅被同一进程内的activity使用，也可被其他进程所使用。

通常每个应用程序都在它自己的进程内运行，但有时需要在进程之间传递对象（IPC通信），你可以通过应用程序UI的方式写个运行在一个不同的进程中的service。在android平台中，一个进程通常不能访问其它进程中的内存区域。所以，他们需要把对象拆分成操作系统能理解的简单形式，以便伪装成对象跨越边界访问。编写这种伪装代码相当的枯燥乏味，好在android为我们提供了AIDL工具可以来做这件事。

AIDL(android接口描述语言)是一个IDL语言，它可以生成一段代码，可以使在一个android设备上运行的两个进程使用内部通信进程进行交互。如果你需要在一个进程中(例如在一个Activity中)访问另一个进程中(例如一个Service)某个对象的方法，你就可以使用AIDL来生成这样的代码来伪装传递各种参数。

 ![此处输入图片的描述][8]
 Android提供AIDL（Android Interface Definition Language）工具帮助IPC之间接口的建立，大大地简化了开发者视图。通过下面的步骤实现client和service之间的通信：
1）定义AIDL接口 ，Eclipse将自动为Service建立接口IService
2）Client连接Service，连接到IService暴露给Client的Stub，获得stub对象；换句话，Service通过接口中的Stub向client提供服务，在IService中对抽象IService.Stub具体实现。 
3）Client和Service连接后，Client可向使用本地方法那样，简单地直接调用IService.Stub里面的方法。
下面的例子给出client从提供定时计数的Remote Service，称为TestRemoteService，中获得服务的例子。

**步骤1：通过AIDL文件定义Service向client提供的接口，ITestRemoteService.aidl文件如下**

    interface ITestRemoteService { 
        int getCounter(); 
    }
    
    我们在src的目录下添加一个ITestRemoteService.aidl文件，语法和java的相同。在这个例子中Service很简单，只提供计数器的值，故在接口中我们定义了int getCounter( )。

AIDL文件很简单，Eclipse会根据文件自动生成相关的一个java interface文件，不过没有显示出来，如果直接使用命令行工具会帮助生成java文件。

**步骤2：Remote Service的编写，通过onBind()，在client连接时，传递stub对象。 TestRemoteService.java文件如下**：

    //Service提供一个定时计数器，采用Runnable的方式实现。
    public class TestRemoteService extends Service{ 
        private Handler serviceHandler = null;
        private int counter = 0; 
        private TestCounterTask myTask = new TestCounterTask();  
         
        public void onCreate() {  
            super.onCreate(); 
            showInfo("remote service onCreate()");
        }  
    
        public void onDestroy() { 
            super.onDestroy(); 
            serviceHandler.removeCallbacks(myTask); //停止计数器
            serviceHandler = null; 
            showInfo("remote service onDestroy()");
        }  
    
        public void onStart(Intent intent, int startId) { 
           // 开启计数器 
            super.onStart(intent, startId); 
            serviceHandler=new Handler(); 
            serviceHandler.postDelayed(myTask, 1000); 
            showInfo("remote service onStart()"); 
        } 
    
       //步骤2.1：具体实现接口中暴露给client的Stub，提供一个stub inner class来具体实现。 
        private ITestRemoteService.Stub stub= new ITestRemoteService.Stub() {
           //步骤2.1：具体实现AIDL文件中接口的定义的各个方法。
            public int getCounter() throws RemoteException {  
                showInfo("getCounter()");
                return counter; 
            } 
        };  
         
    //步骤2.2：当client连接时，将触发onBind()，Service向client返回一个stub对象，
    //由此client可以通过stub对象来访问Service，本例中通过stub.getCounter()就可以获得计时器的当前计数。
    //在这个例子中，我们向所有的client传递同一stub对象。 
       public IBinder onBind(Intent arg0) {  
       //我们特别跟踪了stub对象的地址，可以在client连接service中看看通过ServiceConnection传递给client
            showInfo("onBind() " + stub); 
            return stub; 
       }
    
        //用Runnable使用定时计数器，每10秒计数器加1。
        private class TestCounterTask implements Runnable{
            public void run() {  
                ++ counter; 
                serviceHandler.postDelayed(myTask,10000); 
                showInfo("running " + counter); 
            } 
        }  
        //showInfo() 帮助我们进行信息跟踪，更好了解Service的运行情况
        private void showInfo(String s){ 
            System.out.println("[" +getClass().getSimpleName()+"@" + Thread.currentThread().getName()+ "] " + s);
        } 
    }

![此处输入图片的描述][9]

**步骤3：Client和Service建立连接，获得stub，ServiceTest4.java代码如下**

    public class ServiceTest4 extends Activity{
    
        //步骤3.1 定义接口变量 
        private ITestRemoteService remoteService = null;
        private boolean isStarted = false;
        //步骤3.1 定义连接变量，实现ServiceConnection接口
        private CounterServiceConnection conn = null;
          
        protected void onCreate(Bundle savedInstanceState) {
            //5个button分别触发startService( )，stopService( ) ,
            //bindService( ), releaseService( )和invokeService( )，
            //下面两行，一行是显示从Service中获得的计数值，一行显示状态。    
        } 
    
        private void startService(){ 
            Intent i = new Intent();
            //我的这个包里面还有层次，如*.part1、*.part2,etc
            i.setClassName("com.wei.android.learning", "com.wei.android.learning.part5.TestRemoteService"); 
    //和之前的local service一样，通过intent开启Service，触发onCreate()[if Service没有开启]->onStart()
            startService(i); 
            isStarted = true;  
            updateServiceStatus();  
        } 
        private void stopService(){ 
            Intent i = new Intent(); 
            i.setClassName("com.wei.android.learning","com.wei.android.learning.part5.TestRemoteService");
            stopService(i); //触发Service的 onDestroy()[if Service存在]
            isStarted = false;
            updateServiceStatus(); 
        }    
       //步骤3.3：bindService( )通过一个实现ServiceConnection接口的类于Service之间建立连接，
       //注意到里面的参数Context.BIND_AUTO_CREATE，触发onCreate()[if Service不存在] –> onBind().
        private void bindService(){
            if(conn == null){ 
                conn = new CounterServiceConnection();
                Intent i = new Intent();
                i.setClassName("com.wei.android.learning","com.wei.android.learning.part5.TestRemoteService");
               bindService(i, conn,Context.BIND_AUTO_CREATE);
                updateServiceStatus();
            } 
        } 
    
        private void releaseService(){ 
            if(conn !=null){ 
                unbindService(conn); //断开连接，解除绑定
                conn = null; 
                updateServiceStatus(); 
            } 
        } 
        private void invokeService(){  
            if(conn != null){ 
                try{ 
                //一旦client成功绑定到Service，就可以直接使用stub中的方法。
                    Integer counter =remoteService.getCounter(); 
                    TextView t = (TextView)findViewById(R.id.st4_notApplicable);
                    t.setText("Counter value : " + Integer.toString(counter));
                }catch(RemoteException e){ 
                    Log.e(getClass().getSimpleName(),e.toString()); 
                } 
            } 
        } 
        //步骤3.2 class CounterServiceConnection实现ServiceConnection接口，
        //需要具体实现里面两个触发onServiceConnected()和onServiceDisconnected() 
        private class CounterServiceConnection implements ServiceConnection{
            @Override 
            public void onServiceConnected(ComponentName name, IBinder service) {
                // 从连接中获得stub对象，根据我们的跟踪，remoteService就是service中的stub对象
                remoteService = ITestRemoteService.Stub.asInterface(service);
                showInfo("onServiceConnected()" + remoteService);
            } 
    
            @Override
            public void onServiceDisconnected(ComponentName name) { 
                remoteService = null;
                updateServiceStatus(); 
                showInfo("onServiceDisconnected");
            } 
        } 
        
        private void updateServiceStatus() {
            TextView t = (TextView)findViewById( R.id.st4_serviceStatus); 
            t.setText( "Service status: "+(conn == null ? "unbound" : "bound")+ ","+ (isStarted ? "started" : "not started"; ));     
          } 
          
        private void showInfo(String s){
            System.out.println("[" +getClass().getSimpleName()+"@" + Thread.currentThread().getName()+ "] " + s);
        }
    }
    
 
 ![此处输入图片的描述][10]
 
 
 注意：

Service.onBind如果返回null，则调用 bindService 会启动 Service，但不会连接上 Service，因此 ServiceConnection.onServiceConnected 不会被调用，但你任然需要使用 unbindService 函数断开它，这样 Service 才会停止。

其它：

1、在什么情况下使用 startService 或 bindService 或 同时使用startService 和 bindService

如果你只是想要启动一个后台服务长期进行某项任务那么使用 startService 便可以了。如果你想要与正在运行的 Service 取得联系，那么有两种方法，一种是使用 broadcast ，另外是使用 bindService ，前者的缺点是如果交流较为频繁，容易造成性能上的问题，并且 BroadcastReceiver 本身执行代码的时间是很短的（也许执行到一半，后面的代码便不会执行），而后者则没有这些问题，因此我们肯定选择使用 bindService（这个时候你便同时在使用 startService 和 bindService 了，这在 Activity 中更新 Service 的某些运行状态是相当有用的）。另外如果你的服务只是公开一个远程接口，供连接上的客服端（android 的 Service 是C/S架构）远程调用执行方法。这个时候你可以不让服务一开始就运行，而只用 bindService ，这样在第一次 bindService 的时候才会创建服务的实例运行它，这会节约很多系统资源，特别是如果你的服务是Remote Service，那么该效果会越明显（当然在 Service 创建的时候会花去一定时间，你应当注意到这点）。

2、在 AndroidManifest.xml 里 Service 元素的常见选项

android:name，服务类名

android:label，服务的名字，如果此项不设置，那么默认显示的服务名则为类名

android:icon，服务的图标

android:permission，申明此服务的权限，这意味着只有提供了该权限的应用才能控制或连接此服务

android:process，表示该服务是否运行在另外一个进程，如果设置了此项，那么将会在包名后面加上这段字符串表示另一进程的名字

android:enabled，如果此项设置为 true，那么 Service 将会默认被系统启动，不设置默认此项为 false

android:exported，表示该服务是否能够被其他应用程序所控制或连接，不设置默认此项为 false

3、Service 与 Thread 的区别

很多时候，你可能会问，为什么要用 Service，而不用 Thread 呢，因为用 Thread 是很方便的，比起 Service 也方便多了，下面我详细的来解释一下。

1). Thread：Thread 是程序执行的最小单元，它是分配CPU的基本单位。可以用 Thread 来执行一些异步的操作。

2). Service：Service 是android的一种机制，当它运行的时候如果是Local Service，那么对应的 Service 是运行在主进程的 main 线程上的。如：onCreate，onStart 这些函数在被系统调用的时候都是在主进程的 main 线程上运行的。如果是Remote Service，那么对应的 Service 则是运行在独立进程的 main 线程上。因此请不要把 Service 理解成线程，它跟线程半毛钱的关系都没有！

既然这样，那么我们为什么要用 Service 呢？其实这跟 android 的系统机制有关，我们先拿 Thread 来说。Thread 的运行是独立于 Activity 的，也就是说当一个 Activity 被 finish 之后，如果你没有主动停止 Thread 或者 Thread 里的 run 方法没有执行完毕的话，Thread 也会一直执行。因此这里会出现一个问题：当 Activity 被 finish 之后，你不再持有该 Thread 的引用。另一方面，你没有办法在不同的 Activity 中对同一 Thread 进行控制。

举个例子：如果你的 Thread 需要不停地隔一段时间就要连接服务器做某种同步的话，该 Thread 需要在 Activity 没有start的时候也在运行。这个时候当你 start 一个 Activity 就没有办法在该 Activity 里面控制之前创建的 Thread。因此你便需要创建并启动一个 Service ，在 Service 里面创建、运行并控制该 Thread，这样便解决了该问题（因为任何 Activity 都可以控制同一 Service，而系统也只会创建一个对应 Service 的实例）。

因此你可以把 Service 想象成一种消息服务，而你可以在任何有 Context 的地方调用 Context.startService、Context.stopService、Context.bindService，Context.unbindService，来控制它，你也可以在 Service 里注册 BroadcastReceiver，在其他地方通过发送 broadcast 来控制它，当然这些都是 Thread 做不到的。



4、拥有service的进程具有较高的优先级

官方文档告诉我们，Android系统会尽量保持拥有service的进程运行，只要在该service已经被启动(start)或者客户端连接(bindService)到它。当内存不足时，需要保持，拥有service的进程具有较高的优先级。

1). 如果service正在调用onCreate,onStartCommand或者onDestory方法，那么用于当前service的进程则变为前台进程以避免被killed。

2). 如果当前service已经被启动(start)，拥有它的进程则比那些用户可见的进程优先级低一些，但是比那些不可见的进程更重要，这就意味着service一般不会被killed.

3). 如果客户端已经连接到service (bindService),那么拥有Service的进程则拥有最高的优先级，可以认为service是可见的。

4). 如果service可以使用startForeground(int, Notification)方法来将service设置为前台状态，那么系统就认为是对用户可见的，并不会在内存不足时killed。

如果有其他的应用组件作为Service,Activity等运行在相同的进程中，那么将会增加该进程的重要性。

5、注意事项

Service的onCreate的方法只会被调用一次，就是你无论多少次的startService又 bindService，Service只被创建一次。如果先是bind了，那么start的时候就直接运行Service的onStart方法，如果先是start，那么bind的时候就直接运行onBind方法。如果你先bind上了，就stop不掉了，只能先UnbindService, 再StopService,所以是先start还是先bind行为是有区别的。 

Android中的服务和windows中的服务是类似的东西，服务一般没有用户操作界面，它运行于系统中不容易被用户发觉，可以使用它开发如监控之类的 程序。

服务不能自己运行，需要通过调用Context.startService()或Context.bindService()方法启动服务。

这两个方法都可以启动Service，但是它们的使用场合有所不同。使用startService()方法启用服务，调用者与服务之间没有关连，即使调用者退出了，服务仍然运行。使用bindService()方法启用服务，调用者与服务绑定在了一起，调用者一旦退出，服务也就终止，大有“不求同 时生，必须同时死”的特点。

如果打算采用Context.startService()方法启动服务，在服务未被创建时，系统会先调用服务的onCreate()方法，接着调用onStart()方法。如果调用startService()方法前服务已经被创建，多次调用startService()方法并不会导致多次 创建服务，但会导致多次调用onStart()方法。采用startService()方法启动的服务，只能调用Context.stopService()方法结 束服务，服务结束时会调用onDestroy()方法。

如果打算采用Context.bindService()方法启动服务，在服务未被创建时，系统会先调用服务的onCreate()方法，接着调用onBind()方法。这个时候调用者和服务绑定在一起，调用者退出了，系统就会先调用服务的onUnbind()方法，接着调用onDestroy()方法。如果调用bindService()方法前服务已经被绑定，多次调用bindService()方法并不会导致多次创建服务及绑定(也就是说onCreate()和onBind()方法并不会被多次调用)。

如果调用者希望与正在绑定的服务解除绑定，可以调用unbindService()方法，调用该方法也会导致系统调用服务的 onUnbind()-->onDestroy()方法.   

原文地址：http://aswang.iteye.com/blog/1424309


  [1]: http://static.oschina.net/uploads/space/2015/0730/095222_Jssm_1175007.png
  [2]: http://static.oschina.net/uploads/space/2015/0730/160834_rK7t_1175007.png
  [3]: http://static.oschina.net/uploads/space/2015/0730/160949_ye2r_1175007.png
  [4]: http://static.oschina.net/uploads/space/2015/0730/161109_fGYl_1175007.png
  [5]: http://static.oschina.net/uploads/space/2015/0730/161604_zRTz_1175007.png
  [6]: http://static.oschina.net/uploads/space/2015/0730/161711_V5xw_1175007.png
  [7]: http://static.oschina.net/uploads/space/2015/0730/173329_fLfe_1175007.png
  [8]: http://static.oschina.net/uploads/space/2015/0802/170432_x9mE_1175007.png
  [9]: http://static.oschina.net/uploads/space/2015/0802/171454_Lix4_1175007.png
  [10]: http://static.oschina.net/uploads/space/2015/0802/171402_Rt1I_1175007.png