---
title: rxjava 操作符简略
date: 2017-12-12 17:50:12
categories: rxjava
tags: [rxjava,rxandroid,android]
---

rxjava 

变换字符：Map()/FlatMap/ConcatMap()/Buffer()/delay()/do()/onErrorReturn()/onErrorResumeNext()/onExceptionResumeNext()/retry()/retryUntil()/retryWhen()/repeat()/repeatWhen()/
1.Map 作用：对被观察者发送的每1个事件都通过 指定的函数 处理，从而变换成另外一种事件 场景：数据类型转换
2.FlatMap 作用：将被观察者发送的事件进行拆分&单独转换，在合并成一个新的事件序列，最后进行发送。 场景：无序的将被观察者发送的整个事件序列进行变换
3.ConcatMap 作用：类似FlatMap() 操作符  与FlatMap（）的 区别在于：拆分 & 重新合并生成的事件序列 的顺序 = 被观察者旧序列生产的顺序 场景：有序的将被观察者发送的整个事件序列进行变换
4.Buffer 作用：定期从 被观察者（Obervable）需要发送的事件中 获取一定数量的事件 & 放到缓存区中，最终发送 场景：缓存被观察者发送的事件
5.delay 作用：使得被观察者延迟一段时间再发送事件 [observable]

1.在事件的生命周期中操作
6.do 作用：在某个事件的生命周期中调用 
0601：doOnEach/onNext/onError/onCompleted 当observable每次发送一次数据事件就会调用 
0602：doOnNext/doAftetNext  执行Next事件前调用/执行Next事件后调用
0603：doError()/doOnCompleted()/doOnTerminate()/doFianlly() 发送错误事件时/正常发送事件完毕后/无论正常发送或异常终止/最后执行
0604：doOnSubscribe()/doOnUnsubscribe() 观察者订阅时调用/观察者取消订阅时调用

2.错误处理 场景：发送事件过程中，遇到错误时的处理机制 
1.onErrorResumeNext（）拦截的错误 = Throwable；若需拦截Exception请用onExceptionResumeNext（）
2.若onErrorResumeNext（）拦截的错误 = Exception，则会将错误传递给观察者的onError方法

7.onErrorReturn 作用：遇到错误时，发送1个特殊事件&正常终止
8.onErrorResumeNext 作用：遇到错误时，发送1个新的Observable
9.onExceptionResumeNext  作用：遇到错误时，发送1个新的Observable
10.retry 作用：重试，即当出现错误时，让被观察者（Observable）重新发射数据
11.retryUntil 作用： 出现错误后，判断是否需要重新发送数据
1101.若需要重新发送 & 持续遇到错误，则持续重试
1102.作用类似于retry（Predicate predicate）
12.retryWhen 作用：遇到错误时，将发生的错误传递给一个新的被观察者（Observable），并决定是否需要重新订阅原始被观察者（Observable）& 发送事件
13.repeat 作用：无条件地、重复发送 被观察者事件
14.repeatWhen 作用：有条件地、重复发送 被观察者事件


合并操/组合操作符 
1.组合多个被观察者  顺序：concat concatArray 时间：merge mergeArray 错误处理：concatDelayError mergeDelayError
2.合并多个事件      数量：zip 时间：combineLatest combineLatestDelayError  合并成1个事件发送 reduce collect 
3.发送事件前追加发送事件  startWith startWithArray 
4.统计发送事件数量        count 

1.concat 作用：组合多个被观察者一起发送数据，合并后 按发送顺序串行执行  二者区别：组合被观察者的数量，即concat（）组合被观察者数量≤4个，而concatArray（）则可＞4个
2.merge  作用：组合多个被观察者一起发送数据，合并后 按时间线并行执行  区别上述concat（）操作符：同样是组合多个被观察者一起发送数据，但concat（）操作符合并后是按发送顺序串行执行
3.concatDelayError 如果事件出现异常 决定是否继续发送
4.zip 作用：合并 多个被观察者（Observable）发送的事件，生成一个新的事件序列（即组合过后的事件序列），并最终发送  场景：当需要展示的信息需要从多个地方获取(即信息 = 信息1 + 信息2 )&统一结合后再展示 如合并网络请求的发送&统一显示结果
5.combineLatest  作用：当两个Observables中的任何一个发送了数据后，将先发送了数据的Observables 的最新（最后）一个数据 与 另外一个Observable发送的每个数据结合，最终基于该函数的结果发送数据
6.reduce  作用：把被观察者需要发送的事件聚合成1个事件 & 发送 聚合的逻辑根据需求撰写，但本质都是前2个数据聚合，然后与后1个数据继续进行聚合，依次类推
7.collect 作用：将被观察者Observable发送的数据事件收集到一个数据结构里
8.startWith / startWithArray  作用：在一个被观察者发送事件前，追加发送一些数据 / 一个新的被观察者 
9.conunt 作用：统计被观察者发送事件的数量




