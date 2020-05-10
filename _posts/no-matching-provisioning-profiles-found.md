---
title: Xcode错误：No matching provisioning profiles found
tags: [iOS, Xcode]
categories: iOS
date: 2016-03-08 22:13
---

**更新证书错误：No matching provisioning profiles found**

在Xcode中当你在更新了你得证书而再重新编译你的程序，真机调试会出现“Your build settings specify a provisioning profile with the UUID‘XXX’, however, no such provisioning profile was found.  Xcode can resolve this issue by downloading a new provisioning profile from the Member Center.”。

一般出现这种结果，会有以下2种原因：

### 开发证书没有问题，此项目的问题

有时是在别的电脑上真机调试过，把项目拷贝到另一条电脑上进行打包时。本人是从其他电脑上拷贝过来项目进行企业分发版打包时出现此错误。

由于查看开发者证书，处于有效状态，且其他项目可以正常打包，只有当前项目遇到此问题，因此找到了下面这个可以解决问题的方法。

1.关闭项目，找到项目文件XXXX.xcodeproj，在文件上点击右键，选择“显示包内容”（Show Package Contents）。会新打开一个Finder。

2.在新打开的Finder中找到project.pbxproj，并且打开，找到你所有包含报错的UUID‘XXX’的行，删除。

3.保存，重新启动项目，再编译，就OK了。

### 开发证书失效或者过期

如果所有项目都有这个问题，包括以前正常的项目（中间没有更改过），也突然这么不正常了，就非常可能是开发者的开发证书的问题。需要重新安装开发者的开发证书（注意：不是生产证书和分发证书）。

目前遇到的问题，都可以使用上面两种方法解决。
