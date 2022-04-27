---
title: linux查找并移动文件 
tags: [Linux]
categories: 服务器
date: 2021-08-15 11:48
---

linux查找并移动文件

### 原文参考

    find . -name '10-*.dat' -exec mv {} ../ \;

这里：

=> -exec mv {} /mnt/mp3 \; - 运行mv命令。

=> {} - 字符 '{}' 代表find到的所有内容。

=>../表示当前用户目录的上一级目录

=> \; - 结束 /bin/mv 命令。


### 亲测可用

移动搜索匹配条件的文件到指定目录

查找并移动

    find . -name 'faverifyimage_*.png' -exec mv {} ../ \;

只查找

    find . -name 'faverifyimage_*.png';

以下命令是：

移动【当前目录下（不含子目录）】【以faverifyimage_开头的图片文件】到【faverifyimgbk】目录。

说明：扩展了“不含子目录”这个条件，只操作“当前目录”；同时，继续执行了其他格式的图片文件。

    find . -maxdepth 1 -name 'faverifyimage_*.png' -exec mv {} ./faverifyimgbk \;
    find . -maxdepth 1 -name 'faverifyimage_*.jpg' -exec mv {} ./faverifyimgbk \;
    find . -maxdepth 1 -name 'faverifyimage_*.jpeg' -exec mv {} ./faverifyimgbk \;

之后好久没更新。
因为github在2021年08月13日之后停止了账号密码的登录机制。
　　




