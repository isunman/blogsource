---
title: windows2000/2003/2008中安装sqlserver 2000图文教程  
tags: [SQLServer]
categories: 服务器
date: 2016-04-21 18:22
---


本文章来详细的以图文形式来说明在windows操作系统中如何来安装sqlserver2000数据库的方法，有需要了解的朋友可以参考一下。

### 首先是sql2000的第一部分,rar包的安装过程:

第1步:解压rar的sql2000压缩包到当前路径,点击autorun.exe或者setup.bat进行安装,如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0001.jpg)
 
第2步:点击"安装SQL Server 2000 组件",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0002.jpg)

第3步:点击"安装数据库服务器",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0003.jpg)
 
第4步:点击"继续",如下图:
 
![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0004.jpg)
 
第5步:点击"下一步",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0005.jpg)
 
第6步:选择"本地计算机",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0006.jpg)
 
第7步:如果出现如下图的报错,请重启服务器,然后重新安装sql2000,如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0007.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0008.jpg)
 
第8步:选中如下图的按钮,点击"下一步",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0009.jpg)
 
第9步:默认,不用修改,点击"下一步",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0011.jpg)
 
第10步:点击"是"继续,如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0012.jpg)

第11步:选择"服务器和客户端工具",点击"下一步"继续,如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0013.jpg)
 
第12步:选中"默认",点击"下一步"继续,如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0014.jpg)

第13步:修改安装路径,一般安装在非系统盘,D盘或者E盘,如下三张图片:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0015.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0016.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0017.jpg)
 
第14步:选择"使用本地系统账户",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0018.jpg)

第15步:选择"混合模式",并输入sa(管理员)密码,如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0019.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0020.jpg)
 
第16步:点击"下一步",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0021.jpg)

第17步:在"每客户"输入5,如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0022.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0023.jpg)

第18步:点击"继续"正式进入安装过程,如下面几张图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0024.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0025.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0026.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0028.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0029.jpg)

第19步:点击"完成".

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0030.jpg)

### 接下来是sql2000的第二部分,sp4补丁包的安装过程:

第1步:解压sql2ksp4.rar这个压缩包到当前路径,点击setup.bat进行安装,如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0031.jpg)
 
第2步:点击"下一步",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0032.jpg)

第3步:点击"是",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0033.jpg)

第4步:继续点击"是",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0034.jpg)

第5步:点击"下一步",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0035.jpg)

第6步:选择"我用来登录到自己计算机上的windows账户信息(windows身份验证)",点击"下一步",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0036.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0037.jpg)


第7步:勾选"升级microsoft search 并应用SQL Server 2000 SP4(必须)",点击"继续",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0038.jpg)

第8步:点击"确认",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0039.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0040.jpg)

第9步:点击"下一步",进入安装过程,如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0041.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0042.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0043.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0044.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0045.jpg)

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0046.jpg)

第10步:点击"完成",如下图:

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0047.jpg)

至此,sql2000全部安装完毕.

![](http://7xrl2u.com1.z0.glb.clouddn.com/sqlserver2000setup0048.jpg)


[原文参考](http://www.111cn.net/sys/Windows/42656.htm)

