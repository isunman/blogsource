---
title: MySQL数据库升级或者迁移  
tags: [wamp,MySQL]
categories: Web构建
date: 2016-04-12 12:56
---

假设数据库根目录为 mysqlroot，则：

使用cmd切换到目录mysqlroot/bin，然后使用命令

    mysqldump -u root -p 密码 --all-databases > all.sql

把整个数据库里所有数据库文件备份到all.sql
根据数据库文件的大小，需要导出的时间不同。只要没有提示错误，在命令行看不到结果，实际上就是在执行。可以在bin目录下查看正在生成的all.sql文件一直在增大。

备份完整之后，把all.sql移动到合适的目录备用。

![导出导入数据库命令](http://7xrl2u.com1.z0.glb.clouddn.com/0000mysql.png)
 

升级或者迁移数据库，mysql配置好可以正常使用以后，在“根目录/bin”下执行命令

    mysql -u root -p 密码 < all.sql

则原备份数据库可恢复。

注意：实际在命令行执行时，出现“密码”的地方留空即可，等当前这一条命令输入完毕按回车之后会提示输入密码。
