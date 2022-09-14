---
title: PHP中怎么使用fsockopen实现异步请求与响应状态码499
tags: [PHP,Nginx]
categories: Web构建
date: 2022-08-21 01:46
---


### 问题：

在PHP中使用fsockopen实现异步请求时，发现请求有发出，但是响应不是200而是499，导致被请求脚本并未按要求执行。

### 这个问题的起源与解决方案。

php中怎么使用fsockopen实现异步请求，相信很多没有经验的人对此束手无策，为此本文总结了问题出现的原因和解决方法，通过这篇文章希望你能解决这个问题。

php执行一段程序，有可能几毫秒就执行完毕，也有可能耗时较长。

例如，用户下单这个事件，如果调用了些第三方服务进行发邮件、短信、推送等通知，可能导致前端一直在等待。

而有的时候，我们并不关心这些耗时脚本的返回结果，只要执行就行了。这时候就需要采用异步的方式执行。

众所周知，PHP没有直接支持多线程这种东西。我们可以采用折衷的方式实现。这里主要说的就是fsockopen。

通过fsockopen发送请求并忽略返回结果，程序可以马上返回。

示例代码：

    $fp = fsockopen("www.example.com", 80, $errno, $errstr, 30);
	if (!$fp) {
   		echo "$errstr ($errno)<br />\n";
	} else {
   		$out = "GET /backend.php   HTTP/1.1\r\n";
    	$out .= "Host: www.example.com\r\n";
    	$out .= "Connection: Close\r\n\r\n";
 
    	fwrite($fp, $out);
    	/*忽略执行结果
    	while (!feof($fp)) {
      	  echo fgets($fp, 128);
    	}*/
   		fclose($fp);
	}

需要注意的是我们需要手动拼出header头信息。通过打开注释部分，可以查看请求返回结果，但这时候又变成同步的了，因为程序会等待返回结果才结束。

### 实际测试遇到的问题

实际测试的时候发现，不忽略执行结果，调试的时候每次都会成功发送sock请求；但忽略执行结果，经常看到没有成功发送sock请求。查看nginx日志，发现很多状态码为499的请求。

后来找到了原因：

`fwrite`之后马上执行`fclose`，nginx会直接返回499，不会把请求转发给php处理。

客户端主动端口请求连接时，NGINX 不会将该请求代理给上游服务（FastCGI PHP 进程），这个时候 access log 中会以 499 记录这个请求。

### 解决方案：

1)nginx.conf增加配置

    #忽略客户端中断
	fastcgi_ignore_client_abort on;

2)fwrite之后使用usleep函数休眠20毫秒：

    usleep(20000);

后来测试就没有发现失败的情况了。

----------

相关链接：

[参考文章1](https://www.yisu.com/zixun/367919.html "参考文章1")

[参考文章2](https://blog.csdn.net/panjiapengfly/article/details/103010517 "参考文章2")

[参考文章3](https://blog.csdn.net/wang_quan_li/article/details/41806529 "参考文章3")



