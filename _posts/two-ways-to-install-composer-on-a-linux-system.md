---
title: Linux系统安装Composer的两种方法
tags: [PHP,Linux]
categories: 技术日记
date: 2021-04-26 00:12
---

实际使用的系统：CentOS 6.5

#### 1、方法一 CURL

下载composer.phar文件

    curl -sS https://getcomposer.org/installer | php

将composer.phar移动到环境变量中并且更名为composer

    mv composer.phar  /usr/local/bin/composer

使用国内镜像

    composer config -g repo.packagist composer https://packagist.phpcomposer.com

#### 2、方法二 直接下载composer

下载

    wget https://getcomposer.org/composer.phar

安装

    cp composer.phar /usr/local/bin/composer
    chmod u+x /usr/local/bin/composer

测试

composer --help

#### 3、安装的时候用root用户，使用的时候用非root用户

否则，如果使用root运行会有提示：

运行composer出现`do not run Composer as root/super user!`

解决方法：

第1步 创建非root的新用户

    [root@centos ~]# useradd newname
    [root@centos ~]# passwd  newname

第2步 切换为新用户账户

    [root@centos ~]# su newname

切换到新用户后 , 即可执行 原来的操作 , 顺利完成 composer 指令。

【注意】同时，需要使用root用户将 `/usr/local/bin/composer` 设置为755，否则其他用户无法执行该命令。

【本人亲测，使用上述方法2和步骤3成功安装了Composer 2.1-dev版。方法1未测试。】

