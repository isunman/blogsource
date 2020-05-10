---
title: PHP模拟上传文件使用CURLFile函数  
tags: [PHP,上传文件]
categories: 技术日记
date: 2018-09-20 16:30
---


### 一、CURLFile 介绍

#### CURLFile::__construct

(PHP 5 >= 5.5.0, PHP 7)
CURLFile::__construct -- curl_file_create — 创建 CURLFile 对象

#### 说明

面向对象风格

    public CURLFile::__construct ( string $filename [, string $mimetype [, string $postname ]] )

过程化风格

    CURLFile curl_file_create ( string $filename [, string $mimetype [, string $postname ]] )

创建 CURLFile 对象，使用 CURLOPT_POSTFIELDS 选项上传文件。

#### 参数

filename 被上传文件的 路径。

mimetype 被上传文件的 MIME 类型。

postname 上传数据里面的文件名。

#### 返回值 

返回 CURLFile 对象。

### 二、使用示例

#### 1、一个单独的函数定义

    
	public function testUpload01(){
		$file = __DIR__.'\assets\test.jpg';
		//var_dump($file);
		//$post['file'] = '@'.$file;
		$obj = new CurlFile($file);
		<span style="color:#ff0000;">$obj->setMimeType("image/jpeg");//必须指定文件类型，否则会默认为application/octet-stream，二进制流文件</span>
		$post['file'] =  $obj;
		$post['abc'] = "abc";
		var_dump($post);
		$ch = curl_init();
		
		curl_setopt($ch, CURLOPT_HEADER, false);
		//启用时会发送一个常规的POST请求，类型为：application/x-www-form-urlencoded，就像表单提交的一样。
		curl_setopt($ch, CURLOPT_POST, true);
		curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
		curl_setopt($ch,CURLOPT_BINARYTRANSFER,true);
		curl_setopt($ch, CURLOPT_POSTFIELDS,$post);
		curl_setopt($ch, CURLOPT_URL, "http://localhost/fs/upload.php");//上传类
		
		$info= curl_exec($ch);
		curl_close($ch);
		var_dump($info);
		file_put_contents('./1.html',$info);
		$res=json_decode($info,true);
		//var_dump($res);

#### 2、模拟php curl向远程服务器上传文件

第1个文件：test.php 可以向远程服务器模拟提交上传文件的PHP文件

    <?php
		header('content-type:text/html;charset=utf8');
		
		$ch = curl_init();
		
		//加@符号curl就会把它当成是文件上传处理
		$data = array('img'=>'@'. dirname(__FILE__).'/img/1.jpg');
		curl_setopt($ch,CURLOPT_URL,"http://localhost:8088/curl/get_img.php");
		curl_setopt($ch,CURLOPT_RETURNTRANSFER,true);
		curl_setopt($ch,CURLOPT_POST,true);
		curl_setopt($ch,CURLOPT_POSTFIELDS,$data);
		$result = curl_exec($ch);
		curl_close($ch);
		echo json_decode($result);
	
	?>

第2个文件：get_img.php 可以接收上传文件的PHP文件

    <?php
	if($_FILES){
		$filename = $_FILES['img']['name'];
		$tmpname = $_FILES['img']['tmp_name'];
		if(move_uploaded_file($tmpname,dirname(__FILE__).'/upload/'.$filename)){
			echo json_encode('上传成功');
		}else{
			$data = json_encode($_FILES);
			echo $data;
		}
	}
	
	?>

#### 3、本人实测可用综合示例

第1个文件 test.php 模拟上传文件的PHP文件

    <?php
	$file = __DIR__.'\test.jpg';
	$obj = new CurlFile($file);
	$obj->setMimeType("image/jpeg");//必须指定文件类型，否则会默认为application/octet-stream，二进制流文件</span>
	$post['file'] =  $obj;
	$post['abc'] = "abc";
	var_dump($post);
	$ch = curl_init();
	
	curl_setopt($ch, CURLOPT_HEADER, false);
	//启用时会发送一个常规的POST请求，类型为：application/x-www-form-urlencoded，就像表单提交的一样。
	curl_setopt($ch, CURLOPT_POST, true);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
	curl_setopt($ch,CURLOPT_BINARYTRANSFER,true);
	curl_setopt($ch, CURLOPT_POSTFIELDS,$post);
	curl_setopt($ch, CURLOPT_URL, "http://locaolhost/test/get_img.php");//上传类
	
	$info= curl_exec($ch);
	curl_close($ch);
	var_dump($info);
	file_put_contents('./1.html',$info);
	$res=json_decode($info,true);
	?>

第2个文件 get_img.php 接收数据的PHP文件

    <?php

	print_r($_FILES);
	if($_FILES){
		$filename = $_FILES['file']['name'];
		$tmpname = $_FILES['file']['tmp_name'];
		if(move_uploaded_file($tmpname,dirname(__FILE__).'/'.$filename)){
			echo json_encode('SUCCESS');
		}else{
			$data = json_encode($_FILES);
			echo $data;
		}
	}
	
	?>

### 小结

1、经实际测试，可以使用。如果使用本地签发的SSL证书的https协议，注意本地环境无法测试，可以暂时使用http代替。

线上的https协议可以正常使用。

2、线上在使用curl中转当前服务器上传的文件再次转发到另一个远程服务器时，需要把文件先临时存储到当前服务器，待另一个远程服务器接收并处理完成后再把当前服务器上的临时文件删除即可。

　　




