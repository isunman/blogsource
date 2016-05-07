---
title: Xcode磁盘空间大清理
tags: [Xcode]
categories: Mac使用
---

Mac 电脑上使用Xcode时间长了，磁盘空间越来越小，在明确没有其他大量文件占用空间情况下，磁盘也是不够用了。此处应该检查Xcode缓存占用的空间，一般会比较大（除非你经常清除Xcode缓存）。

先说结果吧：本人使用的 Mac mini 500G 磁盘，Xcode 的缓存竟然占去 256G 空间。

如下图：

![](http://7xi3f2.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-05-07%20%E4%B8%8A%E5%8D%8812.05.07.png)

下面是清除 Xcode 缓存的步骤：

（1）移除对旧设备的支持

影响：连接设备调试时，会重新自动生成

路径：~/Library/Developer/Xcode/iOS DeviceSupport

（2）移除旧版本的模拟器支持

影响：可重新下载了

路径：~/Library/Application Support/iPhone Simulator

路径：~/Library/Application Support/iPhone Simulator/x.x/tmp (模拟器临时文件)

路径：~/Library/Application Support/iPhone Simulator/x.x/Applications (模拟器安装的应用)

**（3）移除Archives**

影响：不可恢复；Adhoc或者App Store版本会被删除。建议备份dSYM文件夹

路径：~/Library/Developer/Xcode/Archives

**（4）移除DerivedData**

影响：可重新生成；会删除build生成的项目索引、build输出以及日志。重新打开项目时会重新生成，大的项目会耗费一些时间。

路径：~/Library/Developer/Xcode/DerivedData

（5）移除旧的Docsets

影响：不可恢复；将删除旧的Docsets文档

路径：~/Library/Developer/Shared/Documentation/DocSets


本人亲测了（3）和（4），不影响 Xcode 任何使用。以下是步骤截图：

首先，进入到对应目录（直接进入找不到）

![](http://7xi3f2.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-05-07%20%E4%B8%8A%E5%8D%8812.03.15.png)

找到对应目录之后，直接删除到废纸篓，还需要彻底删除。此处使用的工具 Dr.Cleaner 进行废纸篓清除。

![](http://7xi3f2.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-05-07%20%E4%B8%8A%E5%8D%8812.01.35.png)

最后是彻底清除之后的磁盘容量数据，这下，清爽多了。

![](http://7xi3f2.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-05-07%20%E4%B8%8A%E5%8D%8811.10.05.png)


PS：因为需要清理的磁盘空间实在太大，清除时用了很长时间。
