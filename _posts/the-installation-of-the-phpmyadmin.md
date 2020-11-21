---
title: phpMyAdmin的安装  
tags: [lnmp,PHP,MySQL]
categories: Web构建
date: 2020-11-21 17:36
---



phpMyAdmin的安装

#### 一、本次的服务器运行环境

Linux-CentOS版本：8.1.1911

Nginx版本：1.14.1

MySQL版本：5.7.32

PHP版本： 7.2.24

### 二、安装过程

1、在LNMP环境已经安装好，且可以正常运行的情况下。从[phpMyAdmin的官网](https://www.phpmyadmin.net)上下载最新的版本。现在2020年11月21日是5.0.4版本。

2、下载完zip的文案压缩包，直接解压缩到目标目录（可通过http远程访问的目录）。

3、把phpMyAdmin根目录下的config.sample.inc.php复制为config.inc.php，作为其正式的配置文件使用。

4、然后在nginx的conf配置文件中给phpmyadmin配置一个虚拟主机文件。如果不想使用域名访问，可以使用localhost和指定的端口（例如8080，8000等），然后使用IP+端口来访问。

5、重启Nginx服务，让虚拟主机配置文件生效。通过浏览器访问测试效果。

### 三、可通过浏览器访问之后的两个问题

1、提示把配置文件的配置生成到phpmyadmin数据中存储。

2、提示“ 配置文件现在需要一个短语密码。 ”，则打开上述的config.inc.php文件，找到“blowfish_secret”，把它的值设置为一个至少32位长度的字符串。

再之后，刷新浏览器页面，重新登录。OK。

以上过程所有步骤，由本人亲测有效。
