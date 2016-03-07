---
title: 用Hexo和Github搭建博客（1）：安装环境
tags: [Hexo,Github]
categories: Web构建
---

## 安装git
### 下载msysGit
1、访问 msysGit 的项目主页，下载 msysGit。[http://msysgit.Github.io](http://msysgit.Github.io)

2、点击“Download”，下载软件（当前版本Git-2.7.2-64-bit.exe）
### 安装msysGit

1、点击安装程序（如 Git-2.7.2-64-bit.exe）开始安装；

2、配置安装目录，可手动修改路径，也可“Browse..”选择安装目录；

3、选择“not noly for Git Bash”,下一步；


4、不创建启动文件夹，下一步；

5、询问是否修改环境变量。默认选择“Use Git Bash Only”，即只在 msysGit 提供的 shell 环境中使用 Git，不修改环境变量。若需要在 CMD 中直接使用 Git，请选择第三项修改环境变量，即可在CMD命令行中使用 Git；

6、询问换行符的转换方式，默认。下一步；

7、安装进度显示、安装完成后会显示安装完成界面。

### 检测msysGit是否安装成功
任意目录下，鼠标右键新增Git功能“ Git Gui here”和“ Git Bash here”，点击即可进入 Git 命令行边界界面。说明安装成功。
## 安装Node.js

在 Windows 环境下安装 Node.js 非常简单，仅需下载安装文件并执行即可。 

下载地址：https://nodejs.org/
Win 64位系统的当前版本： 
[https://nodejs.org/dist/v4.3.1/node-v4.3.1-x64.msi](https://nodejs.org/dist/v4.3.1/node-v4.3.1-x64.msi)

安装好后，打开 cmd，查看 Node.js 是否可用

`node -v`

结果

`v5.7.0`

再测试 rpm 是否可用

`rpm -v`

结果

`3.6.0`

说明下面可以使用 npm 安装 Hexo 了。

## 安装 Hexo

1、任意目录下右键点击“ Git Bash here ”，打开 Git 的命令行界面。

2、输入如下命令，进行安装 Hexo

`npm install -g hexo`

3、创建 Hexo 目录

安装完成后，选择或者新建 Hexo 的根目录。让 Git Bash 切换到此目录，，执行如下命令，Hexo 会进行初始化，自动生成建立网站所需要的文件。

`hexo init`

安装依赖包

`npm install`

安装完成后，生成网站静态文件

`hexo g`

启动本地静态服务器

`hexo s`

浏览器打开 localhost:4000 即可查看。

至此，本地基于 Node.js 的 Hexo 博客静态系统已经搭建完成。


