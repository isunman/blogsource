---
title: 用Hexo和Github搭建博客（2）：部署到Github
tags: [Hexo]
categories: Web构建
date: 2016-02-25 21:46
---


## Github设置
1、必须已经有一个Github账号（没有的去面壁）

2、创建一个新的 repositories

Github页面分为个人主页和项目主页，此处建立个人主页为例：

例如我的用户名为isunman，则新建的 repositories 名称为 “isunman.Github.com”

## 部署

1、编辑 Hexo 根目录下的_config.yml文件。

在文件的最后，代码修改为：

    deploy:
    
    	type: git

    	repo: https://Github.com/isunman/isunman.Github.com.git

		branch: master
 

你在部署时，要把下面的 isunman 都换成你的账号名。

>这个是Github官方规定的,必须要名字和账号名保持一致！要不然到后面部署完毕后，你会发现找不到这个页面的。
>
>User & Organization Pages live in a special repository dedicated to Github Pages files. You will need to name this repository with the account name.
>
>You must use the username.Github.io naming scheme.
Content from the master branch will be used to build and 
publish your Github Pages site.
You can only use your own account name for a User or Organization Page repository. A repository like joe/bob.Github.io will not build a User Pages site.When User Pages are built, they are available at http(s)://<username>.Github.io.

执行下列指令即可完成部署。


先生成静态文件

`hexo g`

再部署到 Github

`hexo d`



Hexo常用命令

	hexo g == hexo generate 
	hexo d == hexo deploy 
	hexo s == hexo server 
	hexo n == hexo new

记住：

（1）每次修改本地文件后，需要 hexo g 才能重新生成静态文件。

（2）每次使用命令时，都要在 Hexo 根目录下。
 
至此，我们的博客已经完成，在浏览器访问 isunman.Github.io 即可查看效果！




