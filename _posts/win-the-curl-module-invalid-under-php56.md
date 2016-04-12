---
title: Win下php5.6的curl模块无效
tags: [wamp,PHP]
categories: Web构建
---

表现：
1、在调用 curl_init 函数时，提示错误

	Fatal error: Call to undefined function curl_init() in ……

2、在 phpinfo 页不显示 curl 模块信息。

前提：
在 php.ini 配置文件中已经启用了：

	;extension=php_curl.dll

去掉了前面的分号，而且在ext目录下存在php_curl.dll文件。

解决方案：

官方说明文档中的 [http://php.net/manual/zh/curl.installation.php](http://php.net/manual/zh/curl.installation.php)

**Note: Win32用户注意
要在Windows环境下使用这个模块，libeay32.dll和ssleay32.dll必须放到PATH环境变量包含的目录下。 不用cURL网站上的libcurl.dll。**

> I had to also also copy libssh2.dll into my Apache24 folder for this to > work with my PHP 5.6.2 installation. So altogether I had to do the > following:

> Move to Windows\system32 folder:
> libssh2.dll, php_curl.dll, ssleay32.dll, libeay32.dll

> Move to Apache24\bin folder
> libssh2.dll

> Uncomment extension=php_curl.dll

