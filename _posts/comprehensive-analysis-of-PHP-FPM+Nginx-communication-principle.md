---
title: 全面剖析 PHP-FPM+Nginx 通信原理
tags: [lnmp,Nginx,PHP]
categories: Web构建
date: 2023-08-21 14:50
---


## 引言

用了这么久了PHP+Nginx了，你了解他们之间的通信原理吗？这一次做一回真正的PHPer（在上一篇文章里边已经全面介绍了CGI、FastCGI、PHP-FPM，所以本文对于这些概念不再介绍的那么详细）

## PHP-FPM

PHP-FPM的全称是PHP FastCGI Process Manager，PHP-FPM是FastCGI的实现，并提供了进程管理的功能。FastCGI进程包含master进程和worker进程两种进程。master进程只有一个，负责监听端口，接收Nginx的请求，而worker进程则一般有多个(可配置)，每个进程内部都嵌入了一个PHP解释器，是PHP代码真正执行的地方。

## Nginx

Nginx (“engine x”) 是一个高性能的HTTP和反向代理服务器，也是一个IMAP/POP3/SMTP服务器。这里介绍一下什么是正向代理和反向代理，这个对于我们理解Nginx很重要

## 正向代理

我们那访问国外的网站为例，比如访问Google、Facebook。我们需要借助vpn才能访问，我们借助vpn访问国外的网站，其实就是个正向代理的过程，上图：

![](https://pic1.zhimg.com/80/v2-264693523af2543b568ed9aa7c7d97ec_720w.webp)

vpn对于用户来说，是可以感知到的（因为用户需要配置连接），vpn对于google服务器来说，是不可感知的(google服务器只知道有http请求过来)。所以，对于用户来说可以感知到，而对于服务器来说感知不到的服务器，就是正向代理服务器（vpn）

## 反向代理

拿Nginx作为反向代理服务器实现负载均衡来举例，假设此时我们访问百度，看图：

![](https://pic4.zhimg.com/80/v2-a9736998fd30679e11150296ada745d7_720w.webp)


当用户访问百度时，所有的请求会到达一个反向代理服务器，这个反向代理服务器会将请求分发给后边的某一台服务器去处理。此时，这个代理服务器其实对用户来说是不可感知的，用户感知到的是百度的服务器给自己返回了结果，并不知道代理服务器的存在。也就是说，对于用户来说不可感知，对于服务器来说是可以感知的，就叫反向代理服务器（Nginx）

## PHP-FPM+Nginx通信

FastCGI致力于减少Web服务器与CGI程序之间互动的开销，从而使服务器可以同时处理更多的Web请求。与CGI这种为每个请求创建一个新的进程不同，FastCGI使用持续的进程来处理一连串的请求。**这些进程由FastCGI进程管理器管理，而不是web服务器**。

通过图来理解PHP-FPM和Nginx的通信

![](https://pic1.zhimg.com/80/v2-5b4221240c64823f21975422586e91dc_720w.webp)

（1）当Nginx收到http请求（动态请求），它会初始化FastCGI环境。（如果是Apache服务器，则初始化modefastcgi模块、如果是Nginx服务器则初始化ngxhttp_fastcgi_module）

（2）我们在配置nginx解析php请求时，一般会有这样一行配置：

    fastcgi_pass 127.0.0.1:9000;
或者长这样：
    fastcgi_pass unix:/tmp/php-cgi.sock;

它其实是Nginx和PHP-FPM一个通信载体（或者说通信方式），目的是为了让Nginx知道，收到动态请求之后该往哪儿发。（关于这两种配置的区别，后边会专门介绍）

（3）Nginx将请求采用socket的方式转给FastCGI主进程

（4）FastCGI主进程选择一个空闲的worker进程连接，然后Nginx将CGI环境变量和标准输入发送该worker进程（php-cgi）

（5）worker进程完成处理后将标准输出和错误信息从同一socket连接返回给Nginx

（6）worker进程关闭连接，等待下一个连接

## 不从配置的角度，再描述一下PHP和Nginx的通信

我们知道Nginx也是有master和worker进程的，worker进程直接处理每一个网络请求
其实在Nginx+PHP的架构里边，php可以看做是一个cgi程序的角色，因此出现了php-fpm进程管理器来处理这些php请求。php-fpm和nginx一样，也会监听端口（通过nginx.conf里的配置我们知道，nginx默认监听8080端口，php-fpm默认监听9000端口），并且有master和worker进程，worker负责处理每一个php请求
关于fastcgi：fastcgi是一个协议。市面上有多种实现了fastcgi协议的进程管理器，php-fpm就是其中的一种。php-fpm作为一种fastcgi进程管理服务，会监听端口，一般默认监听9000端口，并且是监听本机，也就是只接收来自本机的端口请求
关于fastcgi的配置文件，目前fastcgi的配置文件一般放在nginx.conf同级目录下，配置文件形式，一般有两种：

    fastcgi.conf和 fastcgi_params。
    不同的nginx版本会有不同的配置文件，这两个配置文件有一个非常重要的区别：
    fastcgi_parames文件中缺少下列配置：
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

我们可以打开fastcgi_params文件加上上述行，也可以在要使用配置的地方动态添加。使得该配置生效

当需要处理php请求时，nginx的worker进程会将请求移交给php-fpm的worker进程进行处理，也就是最开头所说的nginx调用了php，其实严格得讲是nginx间接调用php（反向代理的方式）
我本机配置了能正常解析php程序的nginx配置，介绍一下每一行配置的含义

    server{
    listen 8080;
    index index.php
    root /work/html/;
    location ~ [^/]\.php(/|$)
    {
    root /work/html/;
    fastcgi_pass 127.0.0.1:9000;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
    }
    access_log /work/html/logs/test.log;
    }

第一个大括号 server{ }：代表一个独立的server
listen 8080：代表该server监听8080端口
location ~ [^/]\.php(/|$){ }：代表一个能匹配对应uri的location，用于匹配一类uri，并对所匹配的uri请求做自定义的逻辑、配置。这里的location，匹配了所有带.php的uri请求，例如：http://192.168.244.128:8011/test.php/asdasdhttp://192.168.244.128:8011/index.php等
root /work/html/：请求资源根目录，告诉匹配到该location下的uri到/work/html/文件夹下去寻找同名资源
fastcgi_pass 127.0.0.1:9000：这行代码的意思是，将进入到该location内的uri请求看做是cgi程序，并将请求发送到9000端口，交由php-fpm处理(php-fpm配置中会看见它监听了此端口)
fastcgiparam SCRIPTFILENAME

fastcgiscriptname; ：这行配置意思是：动态添加了一行fastcgi配置，配置内容为SCRIPTFILENAME，告知管理进程，cgi脚本名称。由于我的nginx中只有fastcgiparams文件，没有fastcgi.conf文件，所以要使php-fpm知道SCRIPT_FILENAME的具体值，就必须要动态的添加这行配置

include fastcgi_params; 引入fastcgi配置文件

### fastcgi_pass

Nginx和PHP-FPM的进程间通信有两种方式,一种是TCP Socket,一种是Unix Socket.

**Tcp Socket方式是IP加端口,可以跨服务器.而UNIX Socket不经过网络,只能用于Nginx跟PHP-FPM都在同一服务器的场景，用哪种取决于你的PHP-FPM配置**

**Tcp Socket方式：**

nginx.conf中配置：fastcgi_pass 127.0.0.1:9000;

php-fpm.conf中配置：listen=127.0.0.1:9000;

**Unix Domain Socket方式:**

nginx.conf中配置：fastcgi_pass unix:/tmp/php-fpm.sock;

php-fpm中配置：listen = /tmp/php-fpm.sock；

(php-fpm.sock是一个文件,由php-fpm生成)
**
举例：**

两种通信配置方式，Nginx和PHP-FPM的通信过程如下：

**Tcp Socket：**

    Nginx <=socket <=TCP/IP <=socket <=PHP-FPM

(上边画Nginx和PHP-FPM通信的图时就是这种方式，这种情况是Nginx和PHP-FPM在同一台机器上)

看一下Nginx和PHP-FPM不在同一台机器上的情况：

    Nginx <=> socket <=> TCP/IP <=> 物理层 <=> 路由器 <=> 物理层 <=> 
    TCP/IP <=> socket <=> PHP-FPM

**Unix Socket：**

    Nginx <=> socket <=> PHP-FPM

**include fastcgi_params;**
在nginx中有很多的fasgcgi*的配置，更多的配置可以在nginx.conf的同级目录中看到，在fastcgi.conf和fastcgiparams中，这两个的区别，上边有说明。看一下里边的内容：

![](https://pic2.zhimg.com/80/v2-9e8044d378df04f7e255662c51242885_720w.webp)

这里边的内容都会被传递给PHP-FPM所管理的fastcgi进程。为什么会传递这些呢？相信大家都用过$SERVER这个全局变量，这里边的值就是从此配置中拿到的。我们可以在fastcgiparams中看到REMOTEADDR，就是客户端地址，PHP之所以能拿到客户端信息，就是因为Nginx的配置中的fastcgiparams。更多ngxhttpfastcgimodule模块的内容，可以看官方文档：http://nginx.org/en/docs/http/ngxhttp_fastcgi_module.html

## php-fpm.conf配置

熟悉php-fpm的配置，能帮助我们优化服务器性能

    emergency_restart_threshold = 60
    emergency_restart_interval = 60s

表示在emergencyrestartinterval所设值内出现SIGSEGV或者SIGBUS错误的php-cgi进程数如果超过 emergencyrestartthreshold个，php-fpm就会优雅重启。这两个选项一般保持默认值

    process_control_timeout = 0
设置子进程接受主进程复用信号的超时时间. 可用单位: s(秒), m(分), h(小时), 或者 d(天) 默认单位: s(秒). 默认值: 0.

    listen = 127.0.0.1:9000

fpm监听端口，即nginx中php处理的地址，一般默认值即可。可用格式为: ‘ip:port’, ‘port’, ‘/path/to/unix/socket’. 每个进程池都需要设置.

    request_slowlog_timeout = 10s
    #当一个请求该设置的超时时间后，就会将对应的PHP调用堆栈信息完整写入到慢日志中. 
    设置为 ’0′ 表示 ‘Off’
    slowlog = log/$pool.log.slow
    慢请求的记录日志,配合request_slowlog_timeout使用

下边几个配置参数比较重要：

    pm
    pm指的是process manager，指定进程管理器如何控制子进程的数量，它为必填项，支持3个值
    (1)static: 使用固定的子进程数量，由pm.max_children指定(可以同时存活的子进程的最大数量)
    (2)dynamic：基于下面的参数动态的调整子进程的数量，至少有一个子进程(会使用下边几个配置)
    pm.start_servers: 启动时创建的子进程数量，默认值为min_spare_servers + max_spare_servers -
     min_spare_servers) / 2
    pm.min_spare_servers: 空闲状态的子进程的最小数量，如果不足，新的子进程会被自动创建
    pm.max_spare_servers: 空闲状态的子进程的最大数量，如果超过，一些子进程会被杀死
    (3)ondemand: 启动时不会创建子进程，当新的请求到达时才创建，有下边两个配置
    pm.max_children
    pm.process_idle_timeout 子进程的空闲超时时间，如果超时时间到没有新的请求可以服务，则会被
    杀死
    区别：
    如果pm设置为 static，那么其实只有pm.max_children这个参数生效。系统会开启设置数量的
    php-fpm进程
    如果pm设置为 dynamic，那么pm.max_children参数失效，后面3个参数生效
    系统会在php-fpm运行开始 的时候启动pm.start_servers个php-fpm进程，
    然后根据系统的需求动态在pm.min_spare_servers和pm.max_spare_servers之间调整php-fpm进程数
    还有一个比较重要的配置：
    pm.max_requests
    每一个子进程的最大请求服务数量，如果超过了这个值，该子进程会被自动重启。在解决第三方库的
    内存泄漏问题时，这个参数会很有用。默认值为0，指子进程可以持续不断的服务请求

## PHP-FPM进程池

php-fpm.conf中默认配置了一个进程池，我们可以打开我们的php-fpm.conf看一下，下边是我的：

![](https://pic3.zhimg.com/80/v2-7365bfe451b0102092f528ea338dc5b6_720w.webp)


现在我们执行一下:ps -aux|grep php-fpm

![](https://pic1.zhimg.com/80/v2-e62c2b5b0a6bef72d702cdc81fb82174_720w.webp)

会看见有一个master，10个worker进程，和我们配置的一样（www为进程池名）

想配置多个，这样做即可：

![](https://pic2.zhimg.com/80/v2-56011c2443b60d0280be15ebd9df9ff9_720w.webp)

在nginx中fastcgi_pass这个地方配置使用哪个进程池即可。

（完）

原文参考：[知乎文章](https://zhuanlan.zhihu.com/p/339250896)