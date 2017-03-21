---
title: hexo部署问题
date: 2017-03-21 14:48:20
categories: hexo
tags: [hexo]
---

这两天学着使用hexo和github page搭建个人博客 
到使用hexo deploy自动部署到github page的时候出现了错误：

FATAL bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': Invalid argument
Error: bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': Invalid argument

![此处输入图片的描述][1]

在服务器上部署一点问题都没有，但是在本地部署是就一直报这个错误 
最后把_config.yml中的Git仓库链接改成了ssh链接，然后又给git账户增加了ssh key才解决了问题


要使用自动部署首先要安装hexo-deployer-git工具：
$ npm install hexo-deployer-git --save

配置_config.yml中的deploy：


deploy:
 type: git
 repo:git@github.com:your_github_user_name/your_github_user_name.github.io.git
 branch: master


 注意：这里的repo需要设置成你git仓库的ssh链接

 生成 ssh key

命令行中输入：

$ ssh-keygen -t rsa -C greenovia@qq.com（换成你的邮箱地址）
接着出现的一些步骤都可以回车跳过，如下： 
![此处输入图片的描述][2]
这样在 /c/Users/Administrator/.ssh/id_rsa文件中就生成了公钥


配置github账户的ssh key

打开id_rsa.pub文件将一整串公钥拷贝下来


进入你的github账户设置，在ssh and GPG keys中新增一个ssh key，如下 
![此处输入图片的描述][3]

把刚刚拷贝出来的公钥粘贴到key中，title放空就好
![此处输入图片的描述][4]


验证ssh key：

$ ssh -T git@github.com


出现下面的语句说明你的ssh key已经配置好了

Hi wispyoureyes! You've successfully authenticated, but GitHub does not provide shell access.


初始化本地git仓库

设置Git的user name和email：

$ git config --global user.name "wuyanqina"
$ git config --global user.email "greenovia@qq.com"（换成你的邮箱地址）

在本地的hexo init生成的文件夹中初始化git仓库：

$ git init


将本地仓库和远程仓库连接（这一步骤可以不做）：

$ git remote add origin git@github.com:your_github_user_name/your_github_user_name.github.io.git(远程仓库ssh地址) 


做完以上这些步骤，说明你的仓库可以使用ssh方式来上传下载代码，而不需要输入用户名和密码了


  [1]: http://img.blog.csdn.net/20170306142722422?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR3JlZW5vdmlh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast
  [2]: http://img.blog.csdn.net/20170306142941806?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR3JlZW5vdmlh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast
  [3]: http://img.blog.csdn.net/20170306143047462?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR3JlZW5vdmlh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast
  [4]: http://img.blog.csdn.net/20170306143103647?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR3JlZW5vdmlh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast