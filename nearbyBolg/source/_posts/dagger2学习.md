---
title: dagger2学习
date: 2016-10-28 14:54:43
categories: dagger2
tags: [android,dagger2]
---

> 为什么使用依赖注入

**使用依赖注入的可以带来以下好处：**

 1. 依赖的注入和配置独立于组件之处。
 2. 因为对象是在一个独立、不耦合的地方初始化，所以当注入抽象方法的时候，我们只需要修改对象的实现方式，而不是用大改代码库。
 3. 以来可以注入到一个组件中：我们可以注入这些依赖的模拟实现，这样使得测试更加简单。
 ![此处输入图片的描述][1]


  [1]: http://www.jcodecraeer.com/uploads/20150519/1431999088123888.png

>   基本特点

 1. 多个注入点:依赖，通过injected
 2. 多种绑定方法:以来，通过provided
 3. 多个modules:实现某种功能的绑定集合
 4. 多个对象图:实现一个范围的modules集合

>  区别点

 1. 再也没有使用反射:图的验证、配置和预先设置都在编译的时候执行。
 2. 容易调试和可跟踪:完全具体地调用提供和创建的堆栈。
 3. 更过的性能:谷歌声称他们提高了13%的处理性能
 4. 代码混淆:使用派遣方法，就如同自己写的代码一样
 当然素偶有这些很棒的特点都需要付出一个代价，那就是缺乏灵活性，例如：dagger2没用反射所有没有动态机制。

> 概念与注入

 1. @Inject：通常在需要依赖的地方使用这个注解.换句话说，你用它告诉Dagger这个类或者字段需要依赖注入。这样，Dagger就会构造一个这个类的实例并满足他们的依赖。
 2. @Module：Modules类里面的方法专门提供依赖，所以我们定义一个类，用@Module注解，这样dagger在构造类的实例的时候，就知道从哪里去找到需要的依赖。modules的一个重要的特征它们设计分区并组合在一起(比如说，在我们的app中可以有多个组成在一起的modules)。
 3. @Provide：再modules中，我们定义的方法是用这哥注解，以此告诉dagger我们想要构造对象并提供这些依赖。
 4. @Component:Components从根本上说就是一个注入器，也可以说是@Inject和@Modules的桥梁，它的主要作用就是连接这两个部分。Components可以提供所有定义了的类型的实例，比如：我们必须用@Componets注解一个借口然后列出所有的@Modules组成该组件，如果缺失了任何一块都在编译的时候报错。所有的组件都可以通过它的modules知道依赖的范围。
 5. @Scope:Scope可是非常的有用，dagger2可以通过自定义注解限定注解的作用域。
 6. @Qualifier:当类的类型不足以鉴别一个依赖的时候，我们就可以使用这个注解表示。

>  使用方式
app->build.gradle

    apply plugin: 'com.neenbedankt.android-apt'
     
    buildscript {
      repositories {
        jcenter()
      }
      dependencies {
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.4'
      }
    }
     
    android {
      ...
    }
     
    dependencies {
      apt 'com.google.dagger:dagger-compiler:2.0'
      compile 'com.google.dagger:dagger:2.0'
     
      ...
    }
如上所示，我们添加了编译和运行库，还有必不可少的apt插件，没有这插件，dagger可能不会正常工作，特别是在Android studio中。

> dagger2注解的使用方式

 1. 构造方法注入：在类的构造方法前面注入@Inject
 2. 成员变量注入：在类的成员变量(非私有)前面注解@Inject
 3. 函数方法注入：在函数前面注解@Inject