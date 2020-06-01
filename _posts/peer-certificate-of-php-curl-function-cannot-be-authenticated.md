---
title: PHP的curl函数的证书错误 Peer certificate cannot be authenticated
tags: [PHP,第三方API]
categories: 技术日记
date: 2020-06-01 17:14
---


系统的短信发送突然全部失败了。使用postman工具测试阿里云云通信的短信API可以正常发送。

经过一番仔细核查，原因已找到，是PHP系统自带的CURL函数中的证书验证问题。

错误提示：

    Peer certificate cannot be authenticated with known CA certificates

中文的意思“对等证书不能使用已知的CA证书进行身份验证”。

解决方案：
curl的设置中加入这样一项

    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);

即在请求中“信任任何证书”，不再进行CA证书验证。

但是至少耽误了一天半的短信发送，只是因为周末和月底，没被发现而已。从2020年05月30日 19:36之后到2020年06月01日10:30之前，都是失败的。

**奇怪的就是**：为什么之前可以用，突然在2020年05月30日晚上失败了，而且是在没有更改任何服务器配置和项目相关代码的情况。后来检测，其他项目中使用相同方法的也失败了。不得不逐一修复。

关于CURL函数可以参考官网说明
[https://www.php.net/manual/en/function.curl-setopt.php](https://www.php.net/manual/en/function.curl-setopt.php)

以下为php curl https ssl 证书相关的设置汇总：

    $curl = curl_init();
    curl_setopt($curl,CURLOPT_URL,$url);
    curl_setopt($curl, CURLOPT_PORT, 443);
    curl_setopt($curl, CURLOPT_SSLVERSION, 3);
    curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false); //信任任何证书
    curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, 0); // 检查证书中是否设置域名,0不验证
    curl_setopt($curl, CURLOPT_VERBOSE, 1); //debug模式
    curl_setopt($curl, CURLOPT_SSLCERT, "./keys/client.crt"); //client.crt文件路径
    curl_setopt($curl, CURLOPT_SSLCERTPASSWD, "112358"); //client证书密码
    curl_setopt($curl, CURLOPT_SSLKEY, "./keys/client.key");
    curl_setopt($curl, CURLOPT_POST, 1);
    curl_setopt($curl, CURLOPT_POSTFIELDS, $data);


----------

所以，在最后总结三点：

1、怎么能及时地发现问题；（有没有报警机制）

2、怎么能快速地解决问题；（核查问题的能力）

3、怎么能避免问题不再发生。（预防可能遇到的坑）
　　




