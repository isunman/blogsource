---
title: PHP fsockopen 异步调用接口在nginx上偶尔实效的情况
tags: [lnmp,PHP]
categories: 技术日记
date: 2020-12-26 12:55
---


在下面这段代码里面，调用后忽略执行结果直接返回，可用于php异步执行。

    private function fsock_asy_do($get){
        $fp = fsockopen("ssl://www.xxx.com", 443, $errno, $errstr, 30);
        if (!$fp) {
            echo "$errstr ($errno)<br />\n";
        } else {
            stream_set_blocking($fp,0);//开启非阻塞模式
            $out = "GET /".$get." HTTP/1.1\r\n";
            $out .= "Host: www.xxxx.com\r\n";
            $out .= "Connection: Close\r\n\r\n";
         
            fwrite($fp, $out);
            /*忽略执行结果
            while (!feof($fp)) {
                echo fgets($fp, 128);
            }*/
            usleep(1000);
            fclose($fp);
        }
    }
在nginx服务器上有一个比较诡异的情况就是有时候无法调用异步的脚本。

查阅相关资料后，是nginx 499 的问题。

其中解决方案有以下，经过对每一个方案的验证最终得出结果：

###  1、NGINX 499

查看 NGINX access log，发现这样的请求会以 499（Client Closed Request）记录。确定问题是因为：客户端主动端口请求连接时，NGINX 不会将该请求代理给上游服务（FastCGI PHP 进程），这个时候 access log 中会以 499 记录这个请求。

要解决这个问题需要将 NGINX FastCGI 忽略客户端中断配置打开：

fastcgi_ignore_client_abort on;
这样无论客户端是否断开，都会将这个请求代理给上游，并且会记录上游服务处理后的返回状态。

### 2、NGINX 线程原因

将nginx的worker_processes 由之前的auto修改为2（我的是单核服务器）

### 3、NGINX 499

nginx对499的定义是”client has closed connection”，并且在这些情况下会返回这个状态码：

upstream 在收到读写事件处理之前时发现连接不可用。
server处理请求未结束，而client提前关闭了连接。
upstream出错，执行next_upstream时发现连接不可用。
一个不安全的做法是在fclose之前，让当前的进程先睡眠一段时间；我这里设置为10毫秒，这10毫秒的延迟对我完成整个请求的影响不大，同时我也认为nginx一定能在10毫米内把请求转到fastcgi去执行。这个时间间隔很难把握，不能保证php一定有执行到。

这种方式并不是真正的异步，只是很取巧的强制关闭连接而不等待服务器端响应。所以在Laruence的那2篇文章中，有2个问题：

①PHP使用fsock不能叫做异步，只是伪异步。

②fwrite之后马上执行fclose，nginx会直接返回499

由于我的代码上面usleep为1000，初步估计是时间不够导致没发出去就close了，所以调整为20000。并进行最后测试。
测试2天结果显示正常，的确是usleep数值过小的问题。

