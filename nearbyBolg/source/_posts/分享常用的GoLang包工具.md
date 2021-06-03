---
title: 分享常用的GoLang包工具
date: 2018-03-31 18:53:55
categories: go
tags: [go]
---
包名													链接地址														备注
machinery异步队列										https://github.com/RichardKnop/machinery	
Mqtt通信												https://github.com/eclipse/paho.mqtt.golang					go文档http://www.eclipse.org/paho/clients/golang/
微信开发												https://github.com/chanxuehong/wechat	
fasthttp包											https://github.com/valyala/fasthttp	
数据库操作包											https://github.com/gocraft/dbr	
mysql db链式操作										https://github.com/gohouse/gorose	
glide包管理工具										https://zhuanlan.zhihu.com/p/27994151	
文件配置包											https://github.com/spf13/viper	
cli应用执行程序包										https://github.com/spf13/cobra	
redis包 命令式操作									https://github.com/garyburd/redigo/redis	
redis包 便捷操作										https://github.com/go-redis/redis	
日志包												https://www.jianshu.com/p/5fac8bed4505	
日志包												https://github.com/zbindenren/logrus_mail					邮件通知logrus的hooks
日志包   											https://go.uber.org/zap 	https://go.uber.org/zap/zapcore 	https://gopkg.in/natefinch/lumberjack.v2
gin Api文档包										https://github.com/swaggo/gin-swagger	
govalidator 参数验证器								https://github.com/asaskevich/govalidator	
json编解码库											https://github.com/json-iterator/go	
json web token										https://github.com/dgrijalva/jwt-go	
类型转换												https://github.com/Unknwon/com	
优雅的重启Http服务									https://github.com/fvbock/endless							目前只支持linux环境下使用，零时间重启。
权限控制												https://github.com/mikespook/gorbac	
WebSocket包											https://github.com/gorilla/websocket	
定时任务包											https://github.com/robfig/cron		
CloudXNS-DDNS										https://github.com/zwh8800/cloudxns-ddns					动态域名客户端 docker 镜像
Restful和gprc相互转换									https://github.com/grpc-ecosystem/grpc-gateway				example => https://github.com/go-up/go-example
proto构建Go代码工具									https://github.com/tuneinc/truss							用于RPC协议文档生成
Api网关												https://github.com/fagongzi/gateway	
GO直播服务器											https://github.com/gwuhaolin/livego	
RabbitMQ包											https://github.com/streadway/amqp	
gin中文文档											https://github.com/skybebe/gin-doc-cn
http限流												https://github.com/didip/tollbooth
ORM包												https://github.com/go-sql-driver/mysql
TCP网络编程框架										https://github.com/leesper/holmes	https://github.com/leesper/tao
web框架												https://github.com/gin-gonic/gin
数据结构的可视化										https://github.com/Arafatk/DataViz
生成图												https://github.com/awalterschulze/gographviz	
与任何json交互										https://github.com/bitly/go-simplejson
现代文本索引Go库，类似于lucene。						https://github.com/blevesearch/bleve
Google 的一个开源图(Graph)数据库，						https://github.com/cayleygraph/cayleygraph 					其灵感来自于 Freebase 和 Google 的 Knowledge Graph 背后的图数据库。
top工具												https://github.com/cjbassi/gotop							golang重写的top工具，界面简洁、功能强大
缓存库 												https://github.com/coocood/freecache						无额外的 GC 负荷。数百万对象的垃圾收集延迟仅在数百毫秒特性：* 可存储数以百万计条* 零垃圾收集负荷* 高并发而且线程安全的访问* 近乎 LRU 的算法* 严格限制内存使用
高可用的分布式key-value存储，							https://github.com/coreos/etcd	可以用于配置共享和服务发现；类似于zookeeper和consul；提供restful的http接口，使用简单；基于raft算法的强一致性、高可用的服务存储目录。
可视化工具											https://github.com/davecheney/httpstat						可以测试http状态的可视化工具，通过这个工具可以看出来http响应信息。包括dns解析、tcp连接等信息,httpstat一共有golang版本和python版本。
调试工具												https://github.com/derekparker/delve						Devle是一个非常棒的golang 调试工具，支持多种调试方式，直接运行调试，或者attach到一个正在运行中的golang程序，进行调试。
基本数据结构与算法的实现。								https://github.com/floyernick/Data-Structures-and-Algorithms
将ASCII图转换成手绘图。								https://github.com/esimov/diagram
模拟鼠标键盘事件、截屏等								https://github.com/go-vgo/robotgo
packr 方便的潜入静态资源文件到golang 二进制文件中			https://github.com/gobuffalo/packr
git服务器。											https://github.com/gogits/gogs
从源码中提取json结构。 								https://github.com/newhook/go-symbols
go源码编写vim工具。									https://github.com/mdempsky/gocode
消息队列。											https://github.com/nsqio/nsq
golang对于es操作库。 									https://github.com/olivere/elastic
支持类似于jQuery的功能。 								https://github.com/PuerkitoBio/goquery
javascript解析器。 									https://github.com/robertkrimen/otto
从go源码中查找符号表信息。 								https://github.com/rogpeppe/godef
嵌入式数据库sqlite相关go操作。 							https://github.com/rqlite/rqlite
从源码生成uml图，支持C++, Java, Python, Ruby and C#。 	https://github.com/ruben2020/tags2uml
Zookeeper Golang客户端								https://github.com/samuel/go-zookeeper
golang调试、测试使用，可以用来打印任何结构。 				https://github.com/sanity-io/litter
机器基本信息仪表盘。 									https://github.com/senorprogrammer/wtf
golang操作kafka库。 									https://github.com/Shopify/sarama
日志框架。 											https://github.com/Sirupsen/logrus
CLI命令行  											https://github.com/spf13/cobra			Cobra既是一个用来创建强大的现代CLI命令行的golang库，也是一个生成程序应用和命令行文件的程序。
Hugo是由Go语言实现的静态网站生成器。						https://github.com/spf13/hugo 			简单、易用、高效、易扩展、快速部署。 
配置文件操作库。 										https://github.com/spf13/viper
多机器之间的同步工具。 									https://github.com/syncthing/syncthing
golang操作leveldb，一个本地数据库，高效kv。 			https://github.com/syndtr/goleveldb 	Leveldb是一个google实现的非常高效的kv数据库，目前的版本1.2能够支持billion级别的数据量了。LevelDB 只是一个 C/C++ 编程语言的库, 不包含网络服务封装。
生成golang调用关系图。 								https://github.com/TrueFurby/go-callvis
web框架。 											https://github.com/urfave/negroni
生成图表库。											https://github.com/vdobler/chart
序列化反序列化工具，类似于pb。 							https://github.com/vmihailenco/msgpack
生成图表库。 											https://github.com/wcharczuk/go-chart
access Kafka metadata in Zookeeper 					https://github.com/wvanbergen/kazoo-go
补充返回值。											https://github.com/sqs/goreturns


