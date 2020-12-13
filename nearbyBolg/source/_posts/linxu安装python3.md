---
title: linxu安装python3
date: 2020-02-22 23:53:55
categories: linxu
tags: [linxu]
---

###  1.首先来看一下系统版本
```
	[root@python3 ~]# cat /etc/redhat-release 
	CentOS Linux release 7.6.1810 (Core)
```

### 2.更新一下yum源，yum源我这里用的都是阿里云的源
```
	[root@python3 ~]# cd /etc/yum.repos.d/
	[root@python3 yum.repos.d]# mkdir bak
	[root@python3 yum.repos.d]# mv * bak
	[root@python3 yum.repos.d]# wget http://mirrors.aliyun.com/repo/Centos-7.repo
	[root@python3 yum.repos.d]# wget http://mirrors.aliyun.com/repo/epel-7.repo
```

### 3.安装python3.7之前需要先安装一些依赖，，这也是上面更新yum源的原因
```
	[root@python3 ~]# yum -y install zlib-devel bzip2-devel openssl-devel openssl-static ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel [root@python3 ~]# yum -y groupinstall "Development tools"
	CentOS Linux release 7.6.1810 (Core)
	[root@python3 ~]# cat /etc/redhat-release 
	CentOS Linux release 7.6.1810 (Core)
	[root@python3 ~]# cat /etc/redhat-release 
	CentOS Linux release 7.6.1810 (Core)
```

### 4.下载python3.7安装包，方法很多，我这里直接用wget从官网上下载
```
	[root@python3 ~]# cd /usr/local/src/
	[root@python3 src]# wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz
```
	将安装包下载到/usr/local/src/目录下

### 5.解压&&移动
```
	[root@python3 src]# tar xvf Python-3.7.0.tar.xz
	[root@python3 src]# mv Python-3.7.0 /usr/local/python-3.7
	[root@python3 src]# cd /usr/local/python-3.7/ 
```


### 6.安装&&编译
```
	[root@python3 python-3.7]# ./configure --prefix=/usr/local/sbin/python-3.7
```
	
将python3.7安装到/usr/local/sbin/python-3.7目录下

```
	[root@python3 python-3.7]# make && make install
	安装完成后，若出现以下两行，说明安装成功；
	Installing collected packages: setuptools, pip
	Successfully installed pip-10.0.1 setuptools-39.0.1
```
### 7.安装成功之后就能使用了
```
	[root@python3 python-3.7]# /usr/local/sbin/python-3.7/bin/python3
	Python 3.7.0 (default, Mar 15 2019, 00:44:37) 
	[GCC 4.8.5 20150623 (Red Hat 4.8.5-36)] on linux
	Type "help", "copyright", "credits" or "license" for more information.
	>>>
	【此处退出的命令 exit()】
	虽然能用了，但是命令较长使用起来并不方便,we konw 系统本身是带的有python的，看一下版本
	[root@python3 ~]# python -V
	Python 2.7.5	
```
### 8.看一下python命令的绝对路径	
```
	[root@python3 ~]# which python


	/usr/bin/python

	[root@python3 ~]# ll /usr/bin/ |grep python


	-rwxr-xr-x. 1 root root 11312 Nov 14 00:00 abrt-action-analyze-python
	lrwxrwxrwx. 1 root root 7 Mar 14 18:19 python -> python2
	lrwxrwxrwx. 1 root root 9 Mar 14 18:19 python2 -> python2.7
	-rwxr-xr-x. 1 root root 7216 Oct 31 07:46 python2.7
```
可以看到python命令是通过软链接连接到python2.7的，[做运维的小年轻]那我们现在安装了python3.7，是不是可以将python命令做一个软链接链接到python3，答案是肯定的昂。

### 9.将python命令通过软链接指向到python3命令（就是上面那一大串/usr/local/sbin/python-3.7/bin/python3）
```
	[root@python3 ~]# rm -rf /usr/bin/python

	[root@python3 ~]# ln -sv /usr/local/sbin/python-3.7/bin/python3 /usr/bin/python

	‘/usr/bin/python’ -> ‘/usr/local/sbin/python-3.7/bin/python3’

	[root@python3 ~]# ll /usr/bin/ |grep python

	-rwxr-xr-x. 1 root root 11312 Nov 14 00:00 abrt-action-analyze-python
	lrwxrwxrwx. 1 root root 38 Mar 14 19:24 python -> /usr/local/sbin/python-3.7/bin/python3
	lrwxrwxrwx. 1 root root 9 Mar 14 18:19 python2 -> python2.7
	-rwxr-xr-x. 1 root root 7216 Oct 31 07:46 python2.7
```
### 10.查看现在的默认版本
```
	[root@python3 ~]# python -V
	Python 3.7.0
```
### 11.修改完python默认版本之后，会存不能执行yum命令，需要做一些修改，如下：	
①将/usr/bin/yum的顶部的：

vim !/usr/bin/python  改成  !/usr/bin/python2.7 

②将/usr/libexec/urlgrabber-ext-down的顶部的：

vim /usr/bin/python  改为   /usr/bin/python2.7

③将/usr/bin/yum-config-manager的顶部的


### 12.最后将pip指向到python3.7
```
	[root@python3 ~]# ln -s /usr/local/sbin/python-3.7/bin/pip3 /usr/bin/pip 或者 ln -sf /usr/local/sbin/python-3.7/bin/pip3 /usr/bin/pip

	[root@python3 ~]# pip --version
	pip 10.0.1 from /usr/local/sbin/python-3.7/lib/python3.7/site-packages/pip (python 3.7)
```
	


