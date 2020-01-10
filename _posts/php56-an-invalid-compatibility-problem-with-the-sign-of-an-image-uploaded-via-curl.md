---
title: PHP5.6通过CURL上传图片@符无效的兼容问题
tags: [PHP算法,PHP]
categories: 技术日记
---


今天本来想试试一个图片云的API，于是本地做了个上传图片的测试，结果灰常郁闷的发现以前一直用的好好的CURL上传图片居然死活不起作用，本来几分钟搞定的事情，结果折腾了大半天才终于找到原因，居然是兼容性问题，真是无语。。

网上搜索PHP通过CURL上传图片几乎都是类似下面的代码：


    <?php
    header('Content-type:text/html; charset=utf-8'); //声明编码
    $ch = curl_init();
    $url = 'https://xxx.com/api/mobile/auto_upload.php?uid=9705459';
    
    //post数据，使用@符号，curl就会认为是有文件上传
    $curlPost = array('Filedata'=>'@/Users/finup/Documents/11.png');
    
    
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_HEADER, 1);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_POST, 1); //POST提交
    curl_setopt($ch, CURLOPT_POSTFIELDS,$curlPost);
    $data =curl_exec($ch);
    curl_close($ch);
    echo '<pre>';
    var_dump($data);

以前上传图片都是在图片路径前面增加一个 @符号，如：

    $file = __DIR__ .'/0634134726bc5b8b.jpg';
	$data = array('mypic'=>'@'. $file);

这样就行了，但现在这种情况一般只适用于 PHP5.6以下的版本。
对5.6来说，直接加@是木有用的啦。

解决办法有两个：一种是兼容；二是使用新的方法。

1 . 兼容方法：
主要用到 CURL 的一个配置参数 CURLOPT_SAFE_UPLOAD
CURLOPT_SAFE_UPLOAD 在 PHP5.5中默认值是 false
而在 PHP5.6中已经默认为 true 了。
所以只需要增加一行强制设置为 false 就行，如下：

**注意：该参数的设置顺序，必须在设置 CURLOPT_POSTFIELDS 参数之前才有效哦！！！**


    <?php
    $url = 'http://127.0.0.1/test3.php';
    $file = __DIR__ .'/0634134726bc5b8b.jpg';
    $data = array('mypic'=>'@'. $file);
    $curl = curl_init();
    curl_setopt($curl, CURLOPT_URL, $url);
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($curl, CURLOPT_POST, true);
    curl_setopt($curl, CURLOPT_SAFE_UPLOAD, false);
    curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
    $content = curl_exec($curl);
    curl_close($curl);
    print_r($content);

2 . 新的图片上传方法：
使用 CURLFile 类来处理文件，如下：

    $url = 'http://127.0.0.1/test3.php';
    $file = __DIR__ .'/0634134726bc5b8b.jpg';
    $data = array('mypic'=>new CURLFile($file));
    $curl = curl_init();
    curl_setopt($curl, CURLOPT_URL, $url);
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($curl, CURLOPT_POST, true);
    curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
    $content = curl_exec($curl);
    curl_close($curl);
    print_r($content);

我这里只是最简单的应用，具体可参考下面官方文档：
http://php.net/manual/en/class.curlfile.php

这下终于上传成功了。。。太坑爹了，一天时间又被折腾完了。


