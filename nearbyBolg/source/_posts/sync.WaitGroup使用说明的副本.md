---
title: Go语言中 new()和make()的区别详解
date: 2018-10-06 18:30:12
categories: go
tags: [go]
---

#概述
Go语言中的new和make 一直是新手比较容易混淆的东西，咋一看很相似，接下来我们来分析两者的区别

#new的主要特性

首先new是内建函数，你可以从  http://golang.org/pkg/builtin/#new  这儿看到它，它的定义也很简单：

```
代码如下：
func new(Type) *Type
```

官方文档对于它的描述是：

内建函数new用来分配内存，它的第一个参数是类型，不是一个值，它的返回值是一个指向新分配类型零值的指针

根据这段描述，我们可以自己实现一个类似的new的功能
```
代码如下：
func newInt() *Int{
	var i int
	return &i	
}
someint :=newInt()
```
这个函数的功能跟 someInt :=new(int) 一摸一样，所以在我们自定义new开头的函数时，出于约定应该返回类型的指针。



#make的主要特性

make也是内建函数，你可以从http://golang.org/pkg/builtin/#make 这儿看到它，它的定义比 new 多了一个参数，返回值也不同：
	
```
代码如下：
func make(Type,size INtergerType)Type
```

官方文档对于它的描述是：

内建函数make用来为slie ，map 或 chan 类型分配内存和初始化一个对象(注意：只能用在这三种类型上)，跟new类似，第一个参数也是一个类型而不是一个值，
跟new不同的是，make返回的类型的引用而不是指针，而返回值也依赖于具体传入的类型，具体说明如下：



代码如下：

Slice ：第二个参数size指定了它的长度，它的容易和长度相同。
你可以传入第三个参数来指定不同的容量值，但必须不能长度值小。
比如 make([]int,0,10)
Map:根据size大小来初始化分配内存，不过分配后的map长度为0，如果size被忽略了，那么会在初始化分配内存时分配一个小尺寸的内存
Channel：管道缓冲区依据缓冲区容量被初始化。如果容量为0或者忽略容量。管道是没有缓冲区的

总结

new 的作用是初始化一个指向类型的指针(*T)，make 的作用是为 slice，map 或 chan 初始化并返回引用(T)。
