---
title: hexo插入本地图片
date: 2014-09-22 14:48:20
categories: hexo
tags: [hexo]
---

1.先把hexo[你的项目]目录下的配置文件[_config.yml ]打开，更改里面的 post_asset_folder：true。
2.在dos命令行下，操作到你的hexo[你的项目]目录下，输入 npm install hexo-asset-image --save 命令，这是下载安装一个可以上传本地图片的插件。 
3.稍等片刻，在dos命令行下，输入hexo n "xxx"来生成md博客文件后，在hexo[你的项目]目录下source/_posts文件夹里，会生成刚才输入命令的 "xxx".md文件和"xxx"文件夹
4.编辑打开"xxx".md文件，然后在"xxx".md文件中按照markdown的格式引入图片： ![你想输入的替代文字](图片名.png) 或者 <img src="/图片名.png">在"xxx"文件夹存放图片即可
5.另种方式：在hexo[你的项目]目录下source文件夹，新建images文件夹存放图片，引用方式如下两种
![Alt text](../../../../images/20180426104055.png)
<img src="../../../../images/20180426104055.png >
6.因为 hexo g生成网页后，会在hexo[你的项目]下生成public/xx/xx/xx/文件名/index.html 所以前xx/xx/xx/文件名/ 就像相当于 ../../../../这是在hexo目录下source文件夹，新建images管理图片的好处。



第一种方式就是对应的每个xx.md文件生成一个xx文件夹存放图片
第二种方式就是在source文件夹，新建images文件夹负责存储所有xx.md文件所有的图片。