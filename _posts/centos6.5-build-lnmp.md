---
title: CentOS6.5搭建LNMP  
tags: [lnmp]
categories: Web构建
date: 2016-08-17 19:27
---



CentOS6.5搭建LNMP  

### 1：查看环境：


    [root@10-4-14-168 html]# cat /etc/redhat-release
    CentOS release 6.5 (Final)

### 2：关掉防火墙


	[root@10-4-14-168 html]# chkconfig iptables off

### 3：配置CentOS 6.0 第三方yum源（CentOS默认的标准源里没有nginx软件包）

	#wget http://www.atomicorp.com/installers/atomic
	#sh ./atomic
	#yum check-update

### 4：安装开发包和库文件　

	#yum -y install ntp make openssl openssl-devel pcre pcre-devel libpng
	libpng-devel libjpeg-6b libjpeg-devel-6b freetype freetype-devel gd gd-devel zlib zlib-devel
	gcc gcc-c++ libXpm libXpm-devel ncurses ncurses-devel libmcrypt libmcrypt-devel libxml2
	libxml2-devel imake autoconf automake screen sysstat compat-libstdc++-33 curl curl-devel

### 5：卸载已安装的apache、mysql、php

	# yum remove httpd
	# yum remove mysql
	# yum remove php

### 6：安装nginx

	# yum install nginx
	# service nginx start
	# chkconfig --levels 235 nginx on
	//设2、3、5级别开机启动

### 7：安装mysql

	# yum install mysql mysql-server mysql-devel
	# service mysqld start
	# chkconfig --levels 235 mysqld on

登陆MySQL删除空用户，修改root密码
	mysql>select user,host,password from mysql.user;
	 
	mysql>drop user ''@localhost;
	 
	mysql>update mysql.user set password = PASSWORD('*********') where user='root';
	 
	mysql>flush privileges;
　　
### 8：安装php

	# yum install php lighttpd-fastcgi php-cli php-mysql php-gd php-imap php-ldap
	php-odbc php-pear php-xml php-xmlrpc php-mbstring php-mcrypt php-mssql php-snmp php-soap

//安装php和所需组件使PHP支持MySQL、FastCGI模式


	#yum install  php-tidy php-common php-devel php-fpm php-mysql
	
	# service php-fpm start
	# chkconfig --levels 235 php-fpm on

### 9：配置nginx支持php

	# mv /etc/nginx/nginx.conf /etc/nginx/nginx.confbak
	//将配置文件改为备份文件
 
	# cp /etc/nginx/nginx.conf.default /etc/nginx/nginx.conf
	//由于原配置文件要自己去写因此可以使用默认的配置文件作为配置文件
 
	//修改nginx配置文件，添加fastcgi支持
	# vi /etc/nginx/nginx.conf
	index index.php index.html index.htm;
	//加入index.php
	 
	location ~ \.php$ {
	            root           /usr/share/nginx/html;
	            fastcgi_pass   127.0.0.1:9000;
	            fastcgi_index  index.php;
	            fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/html$fastcgi_script_name;
	            include        fastcgi_params;
        }

	//将以上代码注释去掉，并修改成nginx默认路径
 
### 10：配置php

//编辑文件php.ini，在文件末尾添加cgi.fix_pathinfo = 1
	[root@CentOS ~]# vi /etc/php.ini

### 11：重启nginx php-fpm

	# service nginx restart
	# service php-fpm restart
　　
### 12：建立info.php文件

	# vi /usr/share/nginx/html/info.php

	<?php
	   phpinfo();
	?>
　　
### 13：测试nginx是否解析php

本地浏览器输入：

	192.168.1.105/info.php

显示php界面  环境搭建成功
　　




