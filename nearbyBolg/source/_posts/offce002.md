---
title: offce 002
date: 2015-12-13 12:10:55
categories: offce
tags: [offce]
---
### 垃圾回收算法
##### 标记算法：

1.引用计数法
2.可达性分析法(注意GC root的类型，虚拟机栈和本地方法栈引用的对象、静态对象、字节码对象)

### 回收算法（复制算法、标记清除、标记整理）

新生代：对象存活率低，采用复制算法，堆中分为3个区域，Eden、from、to，每次分配对象都在Eden，第一次gc时，把存活对象复制到from，第二次gc把Eden和from的对象复制到to，第三次又把Eden和to的对象复制到from，依次往复。达到一定阈值时，把对象移入老年代。

老年代：对象存活率高，标记整理法

### java类加载机制


+ 双亲委托机制：如果一个类加载器收到了类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行，如果父类加载器还存在其父类加载器，则进一步向上委托，依次递归，请求最终将到达顶层的启动类加载器，如果父类加载器可以完

+ 成类加载任务，就成功返回，倘若父类加载器无法完成此加载任务，子加载器才会尝试自己去加载

+ Java的类加载器：根加载器（加载java核心类）、扩展类加载器（加载jre扩展目录）、应用类加载器（加载classPath指定目录的类，自定义类加载器一般继承此加载器）

+ Android的类加载器：根加载器、BaseDexClassLoader、PathClassLoader（加载安装到系统的APK）、DexClassLoader（加载指定目录的dex和apk）


### java匿名内部类


匿名内部类就是没有名字的内部类，（其实是有名字的，虚拟机定位这个类，编译之后会使用 外部类名$1这样的名字，数字按顺序生成）。
匿名内部类的构造方法由编译器生成，参数列表包括：

+ 外部类的引用（定义在非静态域）

+ 捕获的外部变量（方法体中使用的外部final对象）

+ 父类的构造参数

+ 如果父类也是一个非静态内部类则还有父类的外部类引用。

### 注意：

+ 不能继承父类或者实现接口（kotlin中是可以的）

+ 不能定义静态变量和方法

+ 会持有外部类的引用，可能会造成内存泄露。

拓展：lambda表达式可以替代部分匿名内部类，父类必须是接口，且只有一个方法。

### java泛型擦除
使用泛型可以声明集合存储的元素类型，取出元素时避免强转的操作。在java中，编译完成后泛型参数会被擦除，例如List<String>和List<Integer>编译完成后都是List类型。

### java泛型为什么会被擦除：

+ 运行时内存压力小，不同泛型的List都编译成同一个类型。泛型不擦除的语言如c#，在方法区就会真实存在各种不同的List类型，压力就会相对较大。

+ 兼容性的问题，1.5之前是没有泛型的，java当时的用户量很大，为了向下兼容。  

### 存在的问题：

1.基本类型无法用于泛型，只能用装箱类型，例如List，装箱操作有额外的开销。
2.泛型参数不能用于方法重载，因为编译完成后泛型被擦除，参数都是一样的。
3.泛型类型不能当做真实的类型来使用，例如方法参数中有一个泛型T，方法中不能直接new  T（），因为编译之后就是Object，不知道真实的类型。
4.静态方法无法引用类的泛型，因为类的泛型在实例化的时候才知道。
5.类型强转的额外开销。

泛型在特定场景可以通过反射获取，例如父类有一个泛型参数已经被确定，子类继承之后可以获取。例如gson中，解析带泛型的List，要传入一个TypeToken，实际上是new了一个子类，通过反射获取泛型类型。

### 如何写出线程安全的程序？

线程安全的本质，可变资源在线程间共享的问题。关键：可变资源、线程共享。

线程安全三要素：原子性、可见性、有序性

所以要保证线程安全：

1.共享不可变资源，final关键字的使用。
2.使用纯函数（不访问外部资源），使用ThreadLocal，不共享资源。
3.使用volatile关键字保证共享资源的可见性，并禁止指令重排序。
4.操作原子性（加锁保证操作的互斥性，原子类AtomicXXX的使用，CAS指令如Unsafe.compareAndSwap）

### Synchronized原理

底层通过一个监视器monitor实现，monitor对象包含一个count计数字段和owner字段指向获取锁的线程，当线程获取monitor后，count+1，owner指向线程，监视器处于锁定状态，其他线程不能获取monitor会进入阻塞状态，当前线程释放monitor后，其他线程可以继续竞争该锁。

### Java1.6之后对Synchronized进行了一些优化：

+ 锁自旋：线程的阻塞和唤醒需要 CPU 从用户态转为核心态，例如在Synchronized代码块中调用wait方法阻塞线程，wait会释放锁，所谓自旋，就是让该线程执行一段无意义的循环指令来等待一段时间，不会被立即挂起，看当前持有锁的线程是否会很快释放锁。缺点是需要占用 CPU，锁竞争的时间比较长时不实用）

+ 偏斜锁：如果一个线程获得了一个偏向锁，如果在接下来的一段时间中没有其他线程来竞争锁，那么持有偏向锁的线程再次进入或者退出同一个同步代码块，不需要再次进行抢占锁和释放锁的操作，一旦出现锁竞争，偏向锁会被撤销，并膨胀成轻量级锁

+ 轻量级锁：对于一块同步代码，虽然有多个不同线程会去执行，但是这些线程是在不同的时间段交替请求这把锁对象，也就是不存在锁竞争的情况。在这种情况下，锁会保持在轻量级锁的状态，从而避免重量级锁的阻塞和唤醒操作

Synchronized可以修饰静态方法（锁对象为字节码对象）、实例方法（锁为实例对象）和代码块，无论是否发生异常虚拟机都会正常释放锁
ReentrantLock发生异常时不能释放锁，所以一般需要在finaly代码块中释放锁，它包含公平锁和读写锁等用法，使用更灵活


### java虚拟机内存模型

+ 虚拟机栈：线程私有，随线程创建而创建。栈里面是一个一个“栈帧”，每个栈帧对应一次方法调用。栈帧中存放了局部变量表（基本数据类型变量和对象引用）、操作数栈、方法出口等信息。当栈调用深度大于JVM所允许的范围，会抛出StackOverflowError的错误。

+ 本地方法栈：线程私有，这部分主要与虚拟机用到的Native方法相关，一般情况下并不需要关心这部分内容。

+ 程序计数器：也叫PC寄存器，JVM支持多个线程同时运行，每个线程都有自己的程序计数器。倘若当前执行的是 JVM 的方法，则该寄存器中保存当前执行指令的地址；倘若执行的是native方法，则PC寄存器中为空。（PS：线程执行过程中并不都是一口气执行完，有可能在一个CPU时钟周期内没有执行完，由于时间片用完了，所以不得不暂停执行，当下一次获得CPU资源时，通过程序计数器就知道该从什么地方开始执行）

+ 方法区：方法区存放类的信息（包括类的字节码，类的结构）、常量、静态变量等。字符串常量池就是在方法区中。虽然Java虚拟机规范把方法区描述为堆的一个逻辑部分，但是它却有一个别名叫做Non-Heap（非堆），目的是与Java堆区分开来。很多人都更愿意把方法区称为“永久代”（Permanent Generation）。从jdk1.7已经开始准备“去永久代”的规划，jdk1.7的HotSpot中，已经把原本放在方法区中的静态变量、字符串常量池等移到堆内存中。

+ 堆：堆中存放的是数组（PS：数组也是对象）和对象。当申请不到空间时会抛出OutOfMemoryError。

### class加载过程

1.装载，将class文件加载进内存，在堆中生成class对象
2.链接，验证二进制数据流（类结构是否正确），分配静态变量设置默认值（初始化时才真正赋值），将符号引用转换为直接引用
3.初始化，初始化静态变量，静态代码块

### java内存模型、volatile的作用
##### 内存模型

+ 本地内存：存放的是 私有变量 和 主内存数据的副本。如果私有变量是基本数据类型，则直接存放在本地内存，如果是引用类型变量，存放的是引用，实际的数据存放在主内存。本地内存是不共享的，只有属于它的线程可以访问。

+ 主内存：存放的是共享的数据，所有线程都可以访问。当然它也有不少其他称呼，比如 堆内存，共享内存等等。

Java内存模型规定了所有对共享变量的读写操作都必须在本地内存中进行，需要先从主内存中拿到数据，复制到本地内存，然后在本地内存中对数据进行修改，再刷新回主内存，这就导致了多线程情况下数据的可见性问题，可以使用volatile关键字来修饰


+ volatile变量在修改后，会立即刷新主内存的值，对所有线程可见，当volatile变量写后，线程中本地内存中共享变量就会置为失效的状态，因此线程需要从主内存中去读取该变量的最新值。

+ volatile还可以防止指令重排序造成的线程安全问题，例如双重校验的懒汉式单例中，不加volatile的对象编译后的指令有可能重排序成：对象引用已经被赋值不等于null了，但是对象的构造方法还没有调用完成的情况。第二个线程去判断不为空，拿到的对象还未初始化完成造成错误。

### 如何安全停止一个线程
stop方法，被废弃。强行停止一个线程，没有资源的机会，如果正在处理任务，会留下一堆异常的数据。另一个线程再访问时就会发生错误。那么如何安全的结束呢：
1、设置volatile的boolean标志位，修改标志位来判断是否继续执行还是清理现场。

2、Interrupt方法：线程内部也需要做支持，判断是否被中断，和标志位类似的处理。支持sleep等系统方法（sleep过程中中断）。判断是否中断的两个方法的区别：
interrupted，静态方法，获取当前正在执行的线程是否被中断，中断之后会清空状态，重复获取就返回falseisInterrupted，线程的方法，获取当前线程的中断状态，不会被清空状态


### HashMap原理

底层是数组+链表的结构，默认数组长度16，加载因子0.75，在put时，（如果第一次put，会创建数组）如果元素个数大于数组长度*加载因子时，将触发扩容操作，数组长度翻倍，并重新计算hash将元素放入数组；

Java1.8中，如果元素过多，数组长64，链表长度超过8，将进行树化操作，将链表转为红黑树，红黑树的节点是链表节点占用空间的两倍，提高查询效率；

##### 如何计算元素存储的位置，如何解决hash冲突，为何数组长度必须为2的整数幂：
先把key取hash值，然后进行一个二次hash，方式为(n-1)&hash，这个二次hash是因为如果n正好等于2的幂，(n-1)&hash相当于对n取模，这样位运算效率很高，这样就相当于把元素均匀分布到了数组中，如果数组的位置没有元素，直接保存元素，如果已经有元素了，表示发生了hash冲突，将改为链表的存储方式，把新元素放在头部（1.8中是尾插法）


##### 为什么加载因子为0.75？设为1和0.5有什么问题？
loadFactor太大，比如等于1，那么就会有很高的哈希冲突的概率，会大大降低查询速度。

loadFactor太小，比如等于0.5，那么频繁扩容没，就会大大浪费空间。

### Hashtable

初始化容量不一样（11），线程安全对整个数组加锁，不允许null值，数据结构一直是数组+链表，不会转换为红黑树；

### ConcurrentHashMap:
1.5-1.7采用分段锁segment机制，不再是整个数组加锁，而是对单条或者几条链表和红黑树进行加锁。内部结构如图：segment数组，segment中类似HashMap的数组+链表。要通过hash让元素尽可能的均匀分布到不同的segment和数组中，所以对key取hash，用高位确定segment的位置，然后用低位确定数组的位置。

1.5的hash算法不好，元素多的时候会造成新加的节点分布在最后的几个桶，分布不均匀，

1.6就改善了hash算法。

1.7的优化是采用segment的懒加载机制，并用volatile的方式访问数组，保证数组的线程可见性，结合CAS指令来避免加锁。

1.8中则基于hashmap做优化，不再采用分段锁，而是对桶节点加锁，使用volatile和CAS乐观锁来实现读和写，再次提高了效率。

通过对Hashtable和ConcurrentHashMap的比较，得出一些锁优化的方法论，比如大锁不如小锁，长锁不如短锁，读写锁的分离等等

### 线程池原理
### 线程池的参数

1.corePoolSize：线程池大小，当向线程池提交任务时，如果线程池已创建的线程数小于corePoolSize，即便此时存在空闲线程，也会创建一个新的线程来执行任务，直到线程数大于或等于corePoolSize。（除了提交新任务来创建线程，也可以通过prestartCoreThread或prestartAllCoreThreads来提前创建核心线程）
2.maximumPoolSize：线程池最大大小，当任务队列满了，且已创建的线程数小于最大线程数，则创建新线程来执行任务，如果线程池任务队列为无界队列可以忽略该参数
3.keepAliveTime：线程存活时间，当线程数大于核心线程数时，线程空闲时间大于存活时间，那么这个线程将被销毁，如果线程池任务队列为无界队列可以忽略该参数
4.workQueue：任务队列，用于保存等待执行任务的阻塞队列
5.threadFactory：线程工厂，用于创建新线程，可以设置统一风格的线程名
6.handler：线程饱和策略，当任务队列和线程池都满了，继续提交任务将执行此策略

### 如何配置线程池？需要看任务的类型

cpu密集型需要配置较小的线程数，避免cpu过度切换反而效率低下
IO密集型，线程池可以稍大，提高cpu的利用率；混合型任务则可配置两个线程池分别来执行；

java自带的线程池

线程池                   核心线程    最大线程              存活时间           任务队列
CachedThreadPool        0          Integer.MAX_VALUE    60S               SynchronousQueue
FixedThreadPool         n          n                    0                 LinkedBlockingQueue
SingleThreadExecutor    1          1                    0                 LinkedBlockingQueue
ScheduledThreadPool     n          Integer.MAX_VALUE    0                 DelayWorkQueue

SynchronousQueue：只能有一个元素的队列，插入和获取元素都会阻塞线程


### java方法分派（多态）

子类复写父类方法，调用方法调用子类还是父类？ 取决于运行时具体的调用者类型，实例是子类就调用子类的方法。

### HTTPS

+ 对称加密和非对称加密

+ 对称加密：加密和解密使用同一个秘钥，使用对应的加密和解密算法进行加解密

+ 非对称加密：加密和解密使用不同的秘钥，分为公钥和私钥，公钥和私钥相互可解，意思就是私钥加密的密文只有公钥可解，反之亦然。

+ 数字签名技术
​   非对称加密在实际使用中，公钥会公开出来，私钥保存在自己手中不公开。由于私钥加密的密文只有公钥可解，那么如果有一个密文用你的公钥可以解开，那么可以说明这个密文肯定是你的私钥加密的，这就诞生了数字签名技术。
​   只有信息的发送者才能产生的别人无法伪造的一段数字串，这段数字串同时也是对信息的发送者发送信息真实性的一个有效证明


### https的本质

https的本质就是：用非对称加密的方式协商出一个对称加密的会话秘钥来进行会话


+ 首先服务端需要有一个证书，证书包含了自己的公钥和服务端信息，如hash算法、加密算法、域名、有效期等等。此证书需要由可信任的第三方（CA机构）的私钥进行签名，实际上是对证书做一个hash，得到hash值然后签名，CA机构也可能不止一级而是一个证书链
+ 为什么要用第三方机构来颁发证书呢？为了安全的传输自己的公钥，系统都预置了可信任的根证书，三方机构是否可信任由系统来保证

### 客户端如何校验CA证书

1.客户端收到证书后，用证书中的公钥去解密该Hash值，得到hash-a
2.客户端用证书中指定的签名算法，计算出一个hash-b，比较hash-a和hash-b
3.除了校验hash值，还会校验CA证书有效期和域名等

### SSL握手过程

1.客户端A访问服务端B，客户端生成一个随机数1、将自己支持的SSL版本号、加密套件（包括哈希算法和加密算法）等信息发送给服务端
2.服务端B收到请求，选择一个加密套件，也生成一个随机数2，将随机数和自己的证书一同返回给客户端
3.客户端收到证书，校验证书是否有效（方法之前说过了），通过校验后，生成一个随机数3，用证书中的公钥加密随机数3，发送给服务端B
4.服务端收到加密的随机数，用私钥解密
5.服务端和客户端都有了随机数1、2、3，通过这三个随机数，生成一个对称加密的会话密钥
6.服务端和客户端分别通知对方之后的会话用会话秘钥来完成，握手结束

### 为什么要用非对称加密来握手，而用对称加密来会话

对称加密握手的话，由于双方的秘钥是一样的，相当于秘钥公开了，和没加密没有区别
而会话阶段，对称加密效率较非对称高


### TCP为什么要三次握手和四次挥手

+ “三次握手”的目的是“为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误”。例如：第一次请求由于网络拥堵没有到达服务端，客户端又发起第二次请求，正常完成了连接，传输完数据之后断开，这时第一次的请求到达了服务端，如果没有第三次握手，会直接建立一条没有用的连接，server端一直等待，浪费资源。

+“四次挥手”原因是因为tcp是全双工模式，接收到FIN时意味对方将没有数据再发来，但是自己还是可以继续发送数据。

### 为什么TCP是可靠的？
### TCP基于连接，具有以下机制：

+ 确认和重传：接收方收到报文后就会进行确认，发送方一段时间没有收到确认就会重传。
+ 数据校验。
+ 数据合理分片与排序，TCP会对数据进行分片，接收方会缓存为按序到达的数据，重新排序后再提交给应用层。
+ 流程控制：当接收方来不及接收发送的数据时，则会提示发送方降低发送的速度，防止包丢失。
+ 拥塞控制：当网络发生拥塞时，减少数据的发送。

UDP是无连接、不安全的，每个数据包都包含接收的ip等信息，客户端只管发送，没有确认重传机制，所以速度更快，但是可能会丢包。



### HTTP1.0、1.1、2.0的区别
##### 1.1和1.0：

+ 增加新的控制缓存策略的Header，如Entity tag，If-Unmodified-Since, If-Match, If-None-Match；

+ 增加了range请求头，允许请求资源的一部分，支持了多线程断点续传下载，优化了带宽和连接；

+ 增加了Host头，允许一台物理服务器上存在多个虚拟主机，共享一个IP地址，通过Host来区分；

+ 增加了keep-alive支持TCP长连接，一定程度弥补了每次请求都重新创建连接的情况；

#### SPDY：

SPDY是Http1.x版本的优化方案，包括多路复用技术、请求优先级（多路复用时，多个请求并行于共用的TCP连接，可以设置请求的优先级防止关键请求被阻塞）、header压缩和服务端推送功能；SPDY的特性并入了Http2.0中；

#### 1.1和2.0：

+ 支持了新的二进制格式，1.x版本只支持文本协议

+ 多路复用技术，在HTTP/1.1协议中，同一时间针对同一域名下的请求有一定数量限制，超过限制数目的请求会被阻塞。多个请求是串行处理，当一个请求超时，后续请求就会被阻塞，而在2.0中，一个TCP连接上并行多个请求，某个请求耗时不影响其他连接；

+ Header压缩，多个请求可以差量更新Header字段，降低流量提高效率；

+ 服务端推送功能

### 三方授权方式

+ Basic：格式：Authorization: Basic username:password(Base64ed)

+ Bearer：格式：Authorization: Bearer 

### bearer token 的获取⽅式（ OAuth2 的授权流程）：


1.第三⽅⽹站向授权⽅⽹站申请第三⽅授权合作，拿到 client id 和 client secret


2.⽤户在使⽤第三⽅⽹站时，点击「通过 XX (如 GitHub) 授权」按钮，第三⽅⽹站将⻚⾯跳转到授权⽅⽹站，并传⼊ client id 作为⾃⼰的身份标识


3.授权⽅⽹站根据 client id ，将第三⽅⽹站的信息和需要的⽤户权限展示给⽤户，询问⽤户是否同意授权


4.⽤户点击「同意授权」按钮后，授权⽅⽹站将⻚⾯跳转回第三⽅⽹站，并传⼊ Authorization code 作为⽤户认可的凭证。


5.第三⽅⽹站将 Authorization code 发送回⾃⼰的服务器


6.服务器将 Authorization code 和 client secret ⼀并发送给授权⽅的服务器，授权⽅返回 access token。


### WebSocket和Socket的区别

+ WebSocket是应用层的一个持久化协议，http它一次请求和响应就断开连接，属于非持久化协议。WebSocket分为握手和数据传输两个阶段，采用http协议握手然后建立全双工的tcp连接。
+ Socket是传输层的一个协议抽象，包括TCP和UDP，TCP基于连接，拥有确认和重传，拥塞控制和流程控制等机制的可靠的协议。UDP则面向无连接，基于数据报，相对于TCP速度快但不可靠。

### 多线程下载和断点续传
两个核心Header，Content-Length表示文件的总字节数，RANGE表示从某一个位置开始传输。

首先，获取到文件大小后，通过线程数来计算每个线程下载的开始位置。

然后，通过range来设置从哪个位置传输。

当暂停或者退出时，记录已下载的位置，下次恢复后从记录的位置下载。

使用RandAccessFile来保存文件，这个类的特点是可以通过移动文件指针来设置写入的位置。


