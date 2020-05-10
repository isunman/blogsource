---
title: Discuz因图片分辨率过高造成提示“没有合法的文件被上传”的解决方案  
tags: [PHP, Discuz]
categories: 技术日记
date: 2018-05-25 13:00
---

用相机拍的照片的分辨率是特别高的，分辨率达到3000*5000px以上级别，这么高的图片的品质肯定是杠杠的，但在网页上面来讲确实太大了，因为我们普通的网页也用不着展示这么高清这么大的图片。我觉得原因有二：一是因为我们普通的网页确实不需要这么大的；二是这么大的图片通常有好几兆甚至十几兆大小，这样页面的加载速度回很慢的。

我们在Discuz中遇到一个问题，那就是上传某些图片的时候提示“没有合法的文件被上传”，而不是说您上传的文件太大了。想到不合法通常会想到的是图片的格式不对，不是普通的jpg或者png之类的，而是其他的非主流的图片格式。但不管把这样的图片转变成png还是jpg都不能上传，但吧图片一缩小却是可以上传的。这样不就是因为文件大小的问题么？（越大一般来讲文件体积也越大）。但检查后发现文件体积并没有超过网站限制以及对应服务器的php配置。用相机拍的照片的分辨率是特别高的，分辨率达到3000*5000px以上级别，这么高的图片的品质肯定是杠杠的，但在网页上面来讲确实太大了，因为我们普通的网页也用不着展示这么高清这么大的图片。我觉得原因有二：一是因为我们普通的网页确实不需要这么大的；二是这么大的图片通常有好几兆甚至十几兆大小，这样页面的加载速度回很慢的。

我们在Discuz中遇到一个问题，那就是上传某些图片的时候提示“没有合法的文件被上传”，而不是说您上传的文件太大了。想到不合法通常会想到的是图片的格式不对，不是普通的jpg或者png之类的，而是其他的非主流的图片格式。但不管把这样的图片转变成png还是jpg都不能上传，但吧图片一缩小却是可以上传的。这样不就是因为文件大小的问题么？（越大一般来讲文件体积也越大）。但检查后发现文件体积并没有超过网站限制以及对应服务器的

### php配置

	function get_image_info($target, $allowswf = false) {
	    $ext = discuz_upload::fileext($target);
	    $isimage = discuz_upload::is_image_ext($ext);
	    if(!$isimage && ($ext != 'swf' || !$allowswf)) {
	        return false;
	    } elseif(!is_readable($target)) {
	        return false;
	    } elseif($imageinfo = @getimagesize($target)) {
	        list($width, $height, $type) = !empty($imageinfo) ? $imageinfo : array('', '', '');
	        $size = $width * $height;
	        if($size > 16777216 || $size < 16 ) {
	            return false;
	        } elseif($ext == 'swf' && $type != 4 && $type != 13) {
	            return false;
	        } elseif($isimage && !in_array($type, array(1,2,3,6,13))) {
	            return false;
	        } elseif(!$allowswf && ($ext == 'swf' || $type == 4 || $type == 13)) {
	            return false;
	        }
	        return $imageinfo;
	    } else {
	        return false;
	    }
	}

这个方法就是获取图片的信息，而返回的值就是真或者假，而为假的时候就提示非法，为真的时候验证通过，上传流程正常执行。而其中就有一个$size变量，它的值是通过获取图片的长宽，然后长宽乘积得到，默认是16777216，开根之后是4096，也就是超过4096×4096px的图片就会之前的报错。而开始的时候我们上传的是5000*4000=2000000，这个值是大于设定的临界的，于是就返回flase。

[原文链接](http://www.muquan.net/article/390.html)


