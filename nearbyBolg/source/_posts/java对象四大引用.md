---
title: java对象四大引用
date: 2015-09-22 11:47:38
categories: java
tags: [java]
---

> 强引用、软引用、弱引用、虚引用总结
    
    总结：
   

 1. 强引用(StrongReference)：普通new对象的引用
        Person person  = new Person();
        JVM宁愿抛出OutOfMenory异常也不会去回收该对象。
 2. 软引用(SoftReference)：
     SoftReference引用的装饰对象，该对象的强引用应该赋值空
    这里配合SoftReferenceQueue使用(也可以配合),因为SoftRefereceQueue本身也是new了一个对象，当它把别人的问题解决掉以后，也需要自己的问题解决，所有jvm把软引用对象回收后，就会把自己的对象引用放到这个队列中，我们可以通过队列的poll()方法查看,如果存在该ReferenceQueue引用也赋值为空，等待被jvm回收。注意：软引用对象在jvm内存不够的时候才会被回收，我们调用system.gc方法只是起通知作用，jvm什么时候扫描回收对象，是jvm自己的状态决定的。就算扫描到软引用对象也不一定回收。只有内存不够的时候才会回收
ReferenceQueue queue = new ReferenceQueue();
Person person = new Person();
SoftReference soft = new SoftReference(person,queue);
user = null;
 3. 弱引用(WeakReference)：弱引用也是用描述非必需对象的，当jvm进行垃圾回收时，只要扫描到，无论内存是否充足(与软引用的区别)，都会回收弱引用关联的对象。在java中，用java.lang.ref.WeakReference类表示
 WeakReference<Person> weak = new WeakRefence<Person>(new Person());
System.out.println(weak.get());
System.gc();//通知jvm的gc进行垃圾回收
System.out.println(weak.get());
 4. 虚引用(PhantomReference)：
    虚引用和前面的软引用，弱引用不同，它并不影响对象的生命周期。
    在java中用java.lang.ref.PhantomReference类表示。如果一个对象与虚引用关联，则跟没有引用与之关联一样，在任何时候都可能被垃圾回收器回收。要注意的是，虚引用必须和引用队列关联使用，当垃圾回收准备回收一个对象时，如果发现它还有虚引用，就会把这个虚引用加入到与之关联的引用队列中。程序可以通过判断引用队列中是否已经加入虚引用，来了解被引用的对象是否将要被垃圾回收。如果程序发现某个虚引用已经被加入引用队列，那么就可以在所引用的对象内存被回收之前采取必要的行动。
ReferenceQueue queue = new ReferenceQueue();
PhantomReference<Person> prf = new PhantomReference<Person>(new Person(),queue);
System.out.print(prf.get());
    http://www.cnblogs.com/dolphin0520/p/3741519.html
    http://www.cnblogs.com/dolphin0520/p/3749259.html