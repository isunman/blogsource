---
title: 对多维数组进行排序的函数array_multisort
tags: [PHP]
categories: 技术日记
date: 2019-07-31 17:57
---

经常会面临这样的需求，虽然有时候我们可以在数据库查询的时候，直接对数据进行排序，但还是无法满足日益复杂的业务需求。这里边会用到两个函数一个是`array_column()`函数，这个函数接受三个参数。详情参见这里是从二维数组中抽出一个键的值，作为一个新的数组返回。
另一个是`array_multisort()`函数，这个函数是一个排序函数，[详情参见这里](https://www.php.net/manual/zh/function.array-multisort.php)。
它会依照第一个参数数组的排序规则，依照第一个参数数组的值在第三个参数重的位置对第三个参数进行排序。

听不明白吧？听不明白就对了，还是直接看代码来的实在：

    $orgin = array(
     array(
    	'id' => 5698,
    	'first_name' => 'Bill',
    	'last_name' => 'Gates',
      ),
     array(
    	'id' => 4767,
    	'first_name' => 'Steve',
    	'last_name' => 'Jobs',
     ),
     array(
    	'id' => 3809,
    	'first_name' => 'Mark',
    	'last_name' => 'Zuckerberg',
      )
    );
    
    $idArr = array_column($orgin, 'id');
    array_multisort($idArr,SORT_ASC,$orgin);
    var_dump($orgin);


这个打印的结果是：

    array (size=3)
     	 0 => 
    array (size=3)
      'id' => int 3809
      'first_name' => string 'Mark' (length=4)
      'last_name' => string 'Zuckerberg' (length=10)
      1 => 
    array (size=3)
      'id' => int 4767
      'first_name' => string 'Steve' (length=5)
      'last_name' => string 'Jobs' (length=4)
      2 => 
    array (size=3)
      'id' => int 5698
      'first_name' => string 'Bill' (length=4)   
      'last_name' => string 'Gates' (length=5)

此函数有点意思。


