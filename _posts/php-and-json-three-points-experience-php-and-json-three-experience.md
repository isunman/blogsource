---
title: PHP与JSON的三点心得
tags: [JSON,PHP]
categories: 技术日记
---

### 1、json_encode处理中文

用PHP的json_encode来处理中文的时候, 中文都会被编码, 变成不可读的, 类似”\u***”的格式, 还会在一定程度上增加传输的数据量。
而在PHP5.4, 这个问题终于得以解决, Json新增了一个选项: JSON_UNESCAPED_UNICODE, 故名思议, 就是说, Json不要编码Unicode，而是现实中文本身。

参考：http://php.net/manual/en/function.json-encode.php


### 2、PHP进行POST提交JSON格式数据做参数

无论是使用curl还是fsockopen，都需要注意：

'Content-Type: application/json'

同时注意不再使用`http_build_query`函数。

看一下
`http_build_query` 的定义和描述。

定义：生成 url-encoded 之后的请求字符串。
描述：使用给出的关联（或下标）数组生成一个url-encoded请求字符串。参数 formdata 可以是数组或包含属性的对象。一个formdata数组可以是简单的一维结构，也可以是由数组组成的数组（其依次可以包含其它数组）。如果在基础数组中使用了数字下标同时给出了numeric_prefix参数，此参数值将会作为基础数组中的数字下标元素的前缀。这是为了让PHP或其它CGI程序在稍后对数据进行解码时获取合法的变量名。

### 3、PHP处理返回JSON格式的数据

使用 json_decode($return,true);注意后面那个参数“true”，可以输出关联数组。

直接赋值给变量，即是一个数组。可直接使用。

