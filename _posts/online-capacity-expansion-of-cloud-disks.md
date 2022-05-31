---
title: 云盘在线扩容
tags: [Linux,阿里云]
categories: 服务器
date: 2022-05-31 11:15
---


在线扩容，是在磁盘不够用时，直接不停机不停服的情况下扩展磁盘空间。

非常好用。不过，需要系统支持。


### 一、扩容概述（官网说明）

https://help.aliyun.com/document_detail/35095.html

这个是是前提，包括：扩容场景、系统盘扩容上限、数据盘扩容上限、扩容计费等。
是在进行技术操作前的一些须知与准备工作。
确认在用的ECS实例与云盘支持“在线扩容”之后，才能进行后续的操作。


### 二、在线扩容云盘（Linux系统）（官网说明）

https://help.aliyun.com/document_detail/113316.htm

这个是具体的执行Linux系统的云盘“在线扩容”的详细步骤。
包括：前提条件，背景信息，操作步骤，支持在线扩容的操作系统，常见问题，其他扩容场景。

其中【操作步骤】又包括：
步骤一：创建快照；
步骤二：在控制台扩容云盘容量；
步骤三：查看云盘分区情况；
步骤四：扩容分区；
步骤五：扩容文件系统。

### 三、实际操作步骤【关键】（实际操作）

1、例子：扩容/dev/vda 由80G扩容到160G

在阿里云上进行在线扩容操作 ：

存储与快照 > 云盘页面选择云盘后，单击云盘扩容，选中在线扩容，并设置扩容后容量
Linux上执行命令

2、查看磁盘信息：`fdisk -l`

3、查看要扩容的分区的文件系统类型：`df -hT`

4、安装growpart工具
CentOS 7及以上版本：运行命令 `yum install cloud-utils-growpart`
Debian 9及以上版本、Ubuntu14及以上版本：运行命令 `apt install -y cloud-guest-utils`

5、运行命令，扩容分区：`growpart /dev/vda 1`（1前面有空格）

6、扩容文件系统：`resize2fs /dev/vda1` （这里1前面没有空格）

7、查看是否扩容成功：`df -hT`

### 四、几个问题的解决

1、问题出现在上述详细步骤的“4、安装growpart工具”这一步，由于CentOS8不再被支持，而造成了yum源错误的问题。

错误提示为：

`Error:Failed to download metadata for repo 'AppStream'`

解决方案：

由于CentOS 8操作系统版本结束了生命周期而导致YUM源出错的问题：
https://help.aliyun.com/document_detail/405635.htm

