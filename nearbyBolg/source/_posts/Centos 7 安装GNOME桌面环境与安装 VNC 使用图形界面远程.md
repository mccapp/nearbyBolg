---
title: Centos 7 安装GNOME桌面环境与安装 VNC 使用图形界面远程
date: 2020-02-23 15:10:55
categories: linxu
tags: [linxu]
---

### 安装之前检查下是否已经安装VNC安装之前检查下是否已经安装VNC
```
[root@nearby ~]# rpm -q tigervnc tigervnc-server 
未安装软件包 tigervnc 
未安装软件包 tigervnc-server
```

没有安装的话直接使用yum进行安装

```
[root@nearby  ~]# yum -y install tigervnc tigervnc-server
......
已安装:
igervnc.x86_64 0:1.8.0-13.el7  tigervnc-server.x86_64 0:1.8.0-13.el7                   
 
作为依赖被安装:
fltk.x86_64 0:1.3.4-1.el7  mesa-libGLU.x86_64 0:9.0.0-4.el7    tigervnc-icons.noarch 0:1.8.0-13.el7   
 
完毕！
```

复制配置模板并进行配置

```
[root@nearby ~]# cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
[root@nearby ~]# vim /etc/systemd/system/vncserver@\:1.service 
```
将配置文件中的<user>改成对应的用户，我这里用root
```
	# Clean any existing files in /tmp/.X11-unix environment
	ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
	ExecStart=/usr/sbin/runuser -l <USER> -c "/usr/bin/vncserver %i"
	PIDFile=/home/<USER>/.vnc/%H%i.pid
	ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
```

调整为：
```
# Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/usr/sbin/runuser -l root -c "/usr/bin/vncserver %i"
PIDFile=/home/root/.vnc/%H%i.pid
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
```

要通知systemd重载此配置文件

```
[root@nearby ~]# systemctl daemon-reload
```

现在为这个用户配置个密码

```
[root@nearby ~]# vncpasswd 
Password:
Verify:
Would you like to enter a view-only password (y/n)? y
Password:
Verify:
[root@nearby ~]# 
```

设置开机服务自启
```
[root@nearby ~]# systemctl enable vncserver@:1.service
Created symlink from /etc/systemd/system/multi-user.target.wants/vncserver@:1.service to /etc/systemd/system/vncserver@:1.service.
```

启动服务

```
[root@nearby ~]# vncserver :1
A VNC server is already running as :1
 
New 'nearby.jump:2 (root)' desktop is nearby.jump:2
 
Starting applications specified in /root/.vnc/xstartup
Log file is /root/.vnc/nearby.jump:2.log
 
[root@nearby ~]# 
```


### 安Centos 7 安装GNOME桌面环境


第1步：安装GNOME及相应桌面管理工具
```
[root@nearby ~]# yum groupinstall -y "X Window System" "GNOME Desktop"
```

第2步：设置开机启动为GNOME
```
[root@nearby ~]# systemctl set-default graphical.target
```

第3步：重启
```
[root@nearby ~]# reboot
```


第4步：开启VNC服务需要用到的5900和5901端口。【此处阿里云服务器配置】
![Alt text](../../../../images/aliyunanquanzu01.png)
![Alt text](../../../../images/aliyunanquanzu02.png)


