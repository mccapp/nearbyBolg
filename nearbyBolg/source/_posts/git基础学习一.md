---
title: git基础学习一
date: 2015-09-21 11:13:18
categories: git
tags: [git]
---

> git常用命令

 1. git mkdir xx(创建xx文件夹) cd xx(切换到xx文件夹) 
 2. touch a.md(新建a.md文件)
 3. gitstatus (查看git文件内容状态) 
 4. git init(初始化仓库)
 5. git add xx(添加xx文件到暂存区) git add . (添加所有文件到暂存区)
 6. git commit -m"content"(content 提交内容留言)
 7. git log (查看日志)
 8. git branch (查看分支，* master是默认分支)
 9. git branch a (新建名为a的分支)
 10. git checkout a (切换到名为a的分支 快捷方式 git checkout -b a 新建a分支并切换到该分支)
 11. git merge (分支合并 切换到主分支 再执行 git merge a 将a分支合并到主分支上)
 12. git branch -d a (删除a分支)
 13. git branch -D a (执行 branch -d a 删除失败，比如a分支未合并到主分支上，git branch -D a就是执行强制删除)
 14. git tag v1.0(创建标签v1.0版本。标签，用于标记版本。切换某版本 git checkout v1.0)
 

> PUSH&PUll

 1. git push origin master (推代码到远程仓库)
 2. git pull origin master (拉代码到本地)
 3. git clone git@github.com:mccapp/test.git (将远程代码复制到本地)
 4. git remote add origin git@github.com:mccapp/test.git (将本地项目与github上的项目进行关联)
 5. git remote -v (查看有那些远程仓库)
 6. git config --blobal user.name "mccapp" (设置github用户名)
 7. git config --blobal user.email  "rsinn@sina.com"(设置邮箱)
 

> git进阶
 [进阶链接][1]


[团队合作git][2]
[rxjava进阶教程][3]


  [1]: http://stormzhang.com/github/2016/06/16/learn-github-from-zero5/
  [2]: http://stormzhang.com/github/2016/07/09/learn-from-github-from-zero6/
  [3]: http://blog.csdn.net/column/details/retrofit.html
 