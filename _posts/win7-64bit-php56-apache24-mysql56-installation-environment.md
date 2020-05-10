---
title: win7(64位)php5.6-Apache2.4-mysql5.6环境安装
tags: [wamp]
categories: Web构建
date: 2016-04-12 12:41
---

### 软件版本：
php-5.6.20-Win32-VC11-x64.zip 
下载地址: [http://windows.php.net/download](http://windows.php.net/download)

httpd-2.4.20-win64-VC11.zip
下载地址: [http://www.apachelounge.com/download](http://www.apachelounge.com/download)

mysql-5.6.29-winx64.zip
下载地址: [http://dev.mysql.com/downloads/mysql](http://dev.mysql.com/downloads/mysql)


**安装前请注意：**

你注意下下载PHP，Apache的网站，上面有提示要安装Visual C++库的。

Apache2.4需要VC10库支持，Microsoft Visual C++ 2010 SP1 Redistributable Package (x64)

PHP5.6需要VC11库支持，Visual C++ Redistributable for Visual Studio 2012 Update 1

注1：如果Apache2.4没有安装VC10库，会出现错误。

注2：如果PHP5.6没有安装VC11库，会出现错误。


### 一、安装配置Apache2.4.20(httpd-2.4.20-win64-VC11.zip)

##### 1、解压下载的安装包:httpd-2.4.20-win64-VC11.zip将其放到自己的安装目录(我的目录C:\wamp\Apache24)

![](http://7xrl2u.com1.z0.glb.clouddn.com/wamp%E5%8D%87%E7%BA%A700000.PNG)

#####2、然后对http.conf(C:\wamp\Apache24\conf\http.conf)配置文件进行修改

(1)修改ServerRoot Apache的根路径:(37行)

        ServerRoot"C:/wamp/Apache24"

改成=>

	ServerRoot "C:\wamp\Apache24"

(2)修改ServerName你的主机名称:(217行)

        ServerName www.example.com:80

将前面的#去掉（改为自己测试域名或者127.0.0.1）,该属性在从命令行启动Apache时需要用到。

(3)修改DocumentRoot

Apache访问的主文件夹目录,就是php、html代码文件的位置。

Apache默认的路径是在htdocs(C:\wamp\Apache24\htdocs)下面,里面会有个简单的入口文件index.html。这个路径可以自己进行修改,我这里将其配置在我自己新建的文件夹www(C:\wamp\htdocs)下。(247行)

	 DocumentRoot "C:/wamp/Apache24/htdocs"
	<Directory"C:/wamp/Apache24/htdocs">

改为=>

	DocumentRoot "C:\wamp\htdocs"
	<Directory "C:\wamp\htdocs">

(4)修改入口文件配置:DirectoryIndex

一般情况下我们都是以index.php、index.html、index.htm作为web项目的入口。Apache默认的入口只有index.html需要添加其他两个的支持，当然这个入口文件的设置可以根据自己的需要增减,如果要求比较严格的话可以只写一个index.php,这样在项目里面的入口就只能是index.php(274行)

	<IfModuledir_module>
		DirectoryIndex index.html
	</IfModule>

改为=>

	<IfModuledir_module>
		DirectoryIndex  index.php index.htm index.html
	</IfModule>

(5)设定serverscript的目录:(358行)

	ScriptAlias/cgi-bin/ "C:/wamp/Apache24/cgi-bin/"

改为=> 

	ScriptAlias/cgi-bin/ "C:\wamp\Apache24/cgi-bin"

(6)(380行)

	<Directory"C:/wamp/Apache24/cgi-bin">
		AllowOverride None
		Options None
		Require all granted
	</Directory>

改为=>

	<Directory"C:\wamp\Apache24/cgi-bin">
		AllowOverride None
		Options None
		Require all granted
	</Directory>

##### 3、启动Apache

开始---运行,输入cmd,打开命令提示符。接着进入C:\wamp\Apache24\bin目录下回车httpd回车，如图所示.
没有报错的话就可以测试了（保持该命令窗口为打开的状态）。

> 把Apache24\htdocs目录下的index.html放到C:\wamp\htdocs目录下，用浏览器访问会出现“It works”那么就说明apache已经正确安装并启动了。也可以自己写一个简单的index.html文件也可以打开。
 

##### 4、将Apache加入到window服务启动项里面并设置成开机启动

先关闭httpd的服务（将命令窗口关闭即可）
重新打开一个新的命令窗口进入到C:\wamp\Apache24\bin目录下：
添加HTTP服务的命令是：

	httpd.exe -kinstall -n "servicename"

servicename是服务的名称，我添加的是：

	httpd.exe -k install -n "Apache24"

命令成功后会有成功的提示。此时可以在window服务启动项中看到Apache24这个服务
然后点击启动即可，若不想设置成开机启动的话也可以将启动类型修改为手动。

如果要卸载这个服务的话，先要停止这个服务，然后输入

	httpd.exe -k uninstall -n "Apache24"

卸载这个服务。

当然也可以通过C:\wamp\Apache24\bin下面的ApacheMonitor.exe来启动Apache.这里就不多说了。

如此Apache的配置就基本完成了。

![](http://7xrl2u.com1.z0.glb.clouddn.com/wamp%E5%8D%87%E7%BA%A700001.PNG)

###二、安装配置php5.6.29（mysql-5.6.29-winx64.zip）

1、将下载的php-5.6.20-Win32-VC11-x64.zip  解压到安装目录下我的是（C:\wamp\php-5.6.20-Win32-VC11-x64）。

2、将目录下的php.ini-development文件复制一份并改名为php.ini，这是php的配置文件。

3、为Apache服务添加php支持

打开Apache的配置文件http.conf在最后加上

	# php5 support
	LoadModule php5_module C:/wamp/php-5.6.20-Win32-VC11-x64/php5apache2_4.dll
	AddType application/x-httpd-php .php .html .htm
	# configure thepath to php.ini
	PHPIniDir "C:/wamp/php-5.6.20-Win32-VC11-x64"

这里我添加在LoadModule下面
添加的时候要保证你的php5apache2_4.dll文件确实存在。php5.5的早期版本里面是没有这个文件的，不过高点版本里面已经有了，可以打开php安装目录找下这个文件

	PHPIniDir "C:/wamp/php-5.6.20-Win32-VC11-x64"

这个就是你的php根目录

![](http://7xrl2u.com1.z0.glb.clouddn.com/wamp%E5%8D%87%E7%BA%A700002.png)
 

4.重启Apache服务器。

5.测试。删除www中其他文件，新建一个index.php，内容为

	<?php phpinfo(); ?>


保存，访问出现php的信息就说明php已经成功安装。

**备注：**
Php的一些常用配置修改：（C:\wamp\php-5.6.20-Win32-VC11-x64\php.ini）
时区的设置：

	date.timezone = Asia/Shanghai

错误报告等级：

	error_reporting = E_ALL

这个在开发模式下可以全部打开。

![](http://7xrl2u.com1.z0.glb.clouddn.com/wamp%E5%8D%87%E7%BA%A700003.png)


### 三、安装配置mysql5.6.29（mysql-5.6.29-winx64.zip）

MySQL5.6.29解压版（zip版）安装配置教程

现将MySQL5.6.29解压版（zip版）的安装配置过程记录如下，希望能给需要安装该版本的朋友一点参考作用。

##### 1、下载MySQL 5.6.29

从MySQL官方网站mysql.com找到MySQL Community Server 5.6.29的下载地址为
[http://dev.mysql.com/downloads/mysql/](http://dev.mysql.com/downloads/mysql/)，在这里可以选择操作系统平台。下面有三个可选的下载文件，第一个是MySQL Installer 5.6 for Windows，这将下载下来一个.msi可执行安装文件。另外有两个解压版（Zip版）分别是Windows (x86, 64-bit), ZIP Archive 和 Windows (x86, 32-bit), ZIP Archive。下载下来，分别是mysql-5.6.29-winx64.zip 和 mysql-5.6.29-win32.zip。我选择的是Windows (x86, 64-bit), ZIP Archive，因为我的服务器操作系统是Windows 7 sp1 64bit。

##### 2、安装MySQL 5.6.29

下载的zip包有349MB，下载了几分钟就好了。

###### （1）、将mysql-5.6.29-winx64.zip 解压到D:\mysql-5.6.29\目录。

###### （2）、清理里面的调试文件

打开这个目录，发现里面的文件夹和文件跟一个安装好后的MySQL基本没有区别。可能你会很郁闷，这个MySQL5.6.29居然有1.04GB，呵呵，仔细一看你就会发现，里面有很有调试文件。后缀为.lib或.pdb的，其实可以删除掉。还有一些名为debug的目录，也删除掉吧。这样是不是就小很多了。

###### （3）、创建my.ini作为MySQL的配置文件

默认情况下没有my.ini文件，这需要我们手工创建一个。怎么创建呢？有没有像php.ini那样有模板呢？其实在MySQL5.6.29中带了一个my-default.ini，可以算作模板，只是里面的内容实在太少了。于是我带大家手工创建一个my.ini。
直接创建一个文本文件，命名为my.ini。打开它，输入如下内容：

	[mysqld]
	
	#绑定IPv4和3306端口
	bind-address = 0.0.0.0
	port = 3306
	
	# 设置mysql的安装目录
	basedir=C:/wamp/mysql-5.6.29-winx64
	
	# 设置mysql数据库的数据的存放目录
	datadir=C:/wamp/mysql-5.6.29-winx64/data
	
	# 允许最大连接数
	max_connections=200

好了，这样一个基本的MySQL环境所需要的参数就够了。

###### （4）、将MySQL安装成服务
打开一个cmd.exe，将目录切换到C:\wamp\mysql-5.6.29-winx64\bin，运行： 

	mysqld -install

提示服务安装成功！运行services.msc并查看，确实有一个名为MySQL的服务了，启动它。

到此，MySQL5.6.29 已经可以正常使用了。

#### 3、配置MySQL 5.6.29

安装完后还要配置一下才能正常使用。

###### （1）、my.ini的参数配置
关于my.ini里面更多更复杂的参数配置，我这里就不介绍了。需要对MySQL进行优化的兄弟们可以参照MySQL官网的手册来操作。

###### （2）、配置root用户登录
默认情况下root是空密码，所以直接运行

	C:\wamp\mysql-5.6.29-winx64\bin\mysql -uroot -p

提示输入密码时，直接回车即可以root身份进入管理MySQL了。
root没有密码是太恐怖了，我们来给它设置一个密码。运行

	C:\wamp\mysql-5.6.29-winx64\bin\mysqladmin -uroot -p password <新密码> 

将<新密码>替换为你的自定义密码，然后按回车。这时会提示输入密码，其实是指的原密码，原密码因为是空，所以这里再回车即可完成设置。

###### （3）、其它操作
用root用户及其新密码登录进去之后，就可以完成其它所有的正常工作了。在此我就不赘述了。

#### 4、安装完成mysql之后，为php添加mysql支持

打开php的配置文件php.ini（C:\wamp\php-5.6.20-Win32-VC11-x64\php.ini）

###### （1）在721行

	; extension_dir = "ext"

去掉前面的“;”，并改为

	extension_dir ="C:\wamp\php-5.6.20-Win32-VC11-x64\ext"

打开php的扩展支持，ext文件夹下有很多php的扩展支持.dll文件，感兴趣的同学可以看一下。
###### （2）然后就是打开php的mysql扩展

在875、876行，去掉前面的“;”

	extension=php_mysql.dll
	extension=php_mysqli.dll

当然也可以打开881行的php_pdo_mysql.dll启用php的pdo支持我一般都用这个。

**注：在第863行到第888行有很多扩展选择，你要用到什么，去掉前面的“;”就可以了。当然如果要添加其他的扩展支持如redis支持，php本身可能没有提供相应的dll文件，就需要自己去找到相应版本的dll添加到ext文件夹中，然后在配置文件中添加一个extension=…
完成之后，重启Apache。**

在访问 phpinfo 的时候就可以看到。

![](http://7xrl2u.com1.z0.glb.clouddn.com/wamp%E5%8D%87%E7%BA%A700004.png)



至此，WAMP环境配置好了。过程中参考了网络上的一些资源，截图和实际配置都是本人亲自操作。

最后，来看一下配置，以应用软件后台显示的为准：

![](http://7xrl2u.com1.z0.glb.clouddn.com/wamp%E5%8D%87%E7%BA%A700005.PNG)
 
