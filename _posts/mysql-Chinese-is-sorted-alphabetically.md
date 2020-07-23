---
title: MySQL中文按首字母排序 
tags: [MySQL]
categories: 技术日记
date: 2020-07-23 11:28
---

### 一种分析

项目中有时候会遇到需要按照汉字拼音排序的需求。

如果要排序的字段编码使用的是GBK字符集，那就可以直接按照拼音排序。因为GBK内码编码时本身就采用了拼音排序的方法（常用一级汉字3755个采用拼音排序，二级汉字就不是了），直接在查询语句后面添加ORDER BY name ASC，查询结果将按照姓氏的升序排序。

如果存储姓名的字段采用的是utf-8字符集，需要在排序的时候对字段进行转码，对应的代码是ORDER BY convert(name using gbk) ASC，同样，查询的结果也是按照汉字拼音的升序排序。

怎样才能将编码转化为GBK呢？在MySQL中提供了函数CONVERT() ，该函数可用来获取一个类型的值。该函数的使用方式为 CONVERT(字段 USING GBK)。

例如：
	
	SELECT * FROM table ORDER BY CONVERT(field USING GBK) ASC

如果字段的值中包含数字和字母也可以排序，因为数字和字母在gbk字符集中本身就是能排序的，数字<字母<汉字。

以上亲测有效。

### 另一种分析：

使用MySQL过程中，我们经常会对一个字段进行排序查询，我们一般都是想要按照中文拼音首字母进行依次排序，但mysql中进行中文排序的时候，对汉字的排序结果往往都是错误的。 这种情况在MySQL的很多版本中都存在。

如果这个问题不解决，那么MySQL将无法实际处理中文。 出现这个问题的原因是因为MySQL在查询字符串时是大小写不敏感的，在编绎MySQL时一般以ISO-8859字符集作为默认的字符集，因此在比较过程中中文编码字符大小写转换造成了这种现象。

查了资料有两种解决方法：
1.对于包含中文的字段加上”binary”属性，使之作为二进制比较，例如将”name varchar(10)”改成”name varchar(10)binary”。

2. 如果不想对表结构进行修改或者重新编译MySQL，也可以在查询语句的 order by 部分使用 CONVERT 函数。

比如 name字段为中文，需要按其排序，则可以写select * from mytable order by CONVERT(name USING gbk);
　　




