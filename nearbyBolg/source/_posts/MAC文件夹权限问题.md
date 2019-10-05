---
title: MAC /usr/local 文件夹权限问题
date: 2019-03-22 12:28:11
categories: mac
tags: [Mac/Linux]
---

# 修改文件夹权限
sudo chown -R $(whoami) /usr/local/

如果失败提示Operation not permitted 或其他权限不足，则需要关闭Rootless

Rootless 苹果从 OS X El Capitan 10.11 系统开始使用了 Rootless 机制，系统默认将会锁定 /system、/sbin、/usr 这三个目录。用户要获取这三个目录的写权限，需要关闭Rootless


# 关闭Rootless
+ 重启 Mac
+ 开机时后按下 Command+R，进入恢复模式。
+ 在上面的菜单实用工具中找到并打开 Terminal
+ 输入如下命令：


```
csrutil disable

```

+ 重启MAC，正常进入系统，此时已经可以给/system、/sbin、/usr 者几个目录进行权限更改
+ 打开 Terminal
+ 输入如下命令：



```
sudo chown -R $(whoami) /usr/local

```