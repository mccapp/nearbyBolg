---
title: Git分支管理策略
date: 2016-10-24 11:55:47
categories: git
tags: [git]
---

Git分支管理策略

1.主要分支：master

	说明：代码库应该有一个、且仅有一个主分支。所有提供给用户使用的正式版本，都在这个主分支开发。

2.开发分支：Develop

	说明：主分支只用来分布重大版本，日常开发应该在另一个分支完成。我们把开发用的分支，叫做Develop

	命令：

	1.开启分支：git checkout -b  develop master
	2.将develop分支发布到Master分支命令：#切换到Master分支 git checkout master 
	#对Develop分支进行合并 git merge --no-ff develop -m"合并分支信息"

3.临时分支：Feature/Release/Fixbug

	1.Feature 功能分支：主要开发特定某功能，从Develop分支上面分出来。开发完成后，要再并入Develop。 
	命名格式采用 feature-* 创建一个临时分支 git checkout -b feature-x develop 
	开发完成后，将功能分支合并到develop分支。 

	1.git checkout develop  
	2.git merge --no-ff feature-x -m"合并分支信息" 
	3.git branch -d feature-x

	2.Felease 预发分支：是指发布正式版之前（即合并到Master分支之前），我们可能需要一个预发布的版本进行测试。
	从Develop分支上面分出来。
	开发完成后，要再并入Develop和Master。
	命名格式采用 release-* 创建预发分支 git checkout -b release-1.2 develop 开发确认没问题后。 
	1.git checkout master 
	2.git merge --on-ff release-1.2 -m"合并分支信息" 
	#对合并的生成的新节点，做一个标签 git tag -a 1.2  
	#再合并到develop分支： 

	1.git checkout develop  
	2.git merge --no-ff release-1.2 -m"合并分支信息" 
	3.git branch -d release-1.2

	3.Fixbug  bug分支：软件正式发布后，难免会出现bug，就需要创建一个分支，进行bug修补。
	从Master分支上面分出来的。修补结束以后，在合并到Master和Develop分支。
	命名格式fixbug-*  创建bug分支 git checkout -b fixbug-0.1 master 修复结束后 合并到master分支 
	1.git checkout master 
	2.git merge  --no-ff fixbug-0.1 -m"合并分支信息"  
	3.git tag -a 0.1.1 

	#再合并到develop分支 
	1.git checkout develop 
	2.git merge --no-ff fixbug-0.1 -m"合并分支信息" 
	3.git branch -d fixbug-0.1	

