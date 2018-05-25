---
title: linux定时任务精确到秒 
tags: [Linux]
categories: 技术日记
---

linux的crond定时任务只能精确到分，如何才能做到控制到秒呢?答案肯定是明显的，实现的方法也有多种，但本文只介绍一个相对来说比较精确，又方便的写法。

### 使用说明

增加一个普通的定时器脚本de >dateTime.shde>，放于/目录

此脚本用于打印当前系统时间

	#!/bin/bash
	date

增加可执行权限

	chmod +x /dateTime.sh

编写定时任务

	crontab -e

脚本内容为

	*/1 * * * * /dateTime.sh >> /dateTime.log
	*/1 * * * * sleep 5 && /dateTime.sh >> /dateTime.log
	*/1 * * * * sleep 10 && /dateTime.sh >> /dateTime.log
	*/1 * * * * sleep 15 && /dateTime.sh >> /dateTime.log
	*/1 * * * * sleep 20 && /dateTime.sh >> /dateTime.log
	...
	*/1 * * * * sleep 55 && /dateTime.sh >> /dateTime.log

重新启动crond

	service crond restart

程序就会每5秒执行一次脚本，并将时间写入/dateTime.log文件中

### 核心

主要是使用linux的sleep控制脚本执行时间

[原文链接](http://blog.csdn.net/dounine/article/details/53357113)




