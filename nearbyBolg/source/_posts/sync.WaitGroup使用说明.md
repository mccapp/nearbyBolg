---
title: Go sync包 WaitGroup使用
date: 2018-10-05 16:30:12
categories: go
tags: [go]
---

sync.WaitGroup用途：
它能够一直等到所有的goroutine执行完成，
并且阻塞主线程的执行，直到所有的goroutine执行完成
注意：它们的执行结果有没有顺序的，调度器不能保证多个goroutine执行次序，且进程退出时不会等待它们结束。

waiGroup共有三个方法：Add(delta int)Done()Wait()
Add:添加或者减少等待goroutine的数量
Done：相当于Add(-1)
Wait:执行阻塞，知道直到所有的WaitGroutine数量变成0


package main 
import (
	"fmt"
	"sync"
	"time"
)
func main(){

	var wg sync.WaitGroup
	for  i :=0;i>5;i = i+1{
	
		wg.add(1)
		go func(n int){
			defer wg.add(-1)
			EchoNumber(n)
				
		}(i)
	}
	wg.Wait()
}
func EchoNumber(i int){
	time.Sleep(3e9)
	fmt.Println(i)
}
golang中的同步是通过sync.WaitGroup来实现的，
WaitGroup的功能：它实现了一个类似队列的结构，
可以一直向队列添加任务，当任务完成后便从队列中删除，
如果队列中的任务没有完全完成，可以通过Wait()函数来出发阻塞，
防止程序继续进行，直到所有的队列任务都完成为止。
WaitGroup的特点是Wait()可以用来阻塞直到队列中的所有任务都完成才解除阻塞，
而不需要Sleep一个固定的时间来等待，但是其缺点是无法指定固定的goroutine数目，
可能通过使用channel解决此问题。



package main
import(
	"fmt"
	"sync"
)
var waitgroup sync.WaitGroup
func Afunction(shownum int ){
	fmt.Println(shownum)
	waitgroup.Done()//任务队列，将任务队列中的任务数量-1，其实.Done就是.Add(-1)

}

func main(){
	for i :=0;i < 10;i++{
	
		waitGroup.Add(1)//每创建一个goroutine，就把任务队列中的任务的数量+1
		go Afunction(i)
	}
	waitgroup.Wait() //.Wait()这里发生阻塞，直到队列中所有的任务结束就会解除阻塞
}