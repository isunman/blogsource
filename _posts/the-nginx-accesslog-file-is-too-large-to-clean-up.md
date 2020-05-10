---
title: Nginx access.log文件太大，自动释放清理  
tags: [lnmp,Linux]
categories: 服务器
date: 2020-05-10 18:30
---



Nginx access.log文件太大，自动释放清理

Nginx在涉及大流量时，会发生非常庞大的日志文件，包含access.log和error.log，日志会随着连接不断增加，到无限大。如果日志文件太大，会导致Nginx运行缓慢，卡顿，也是存储资源的浪费。

该文件为nginx的访问日志文件可以删除,删除后nginx启动还会产生
如果要关闭日志功能,在nginx配置文件中找到access_log一行,改为access_log off;

### 手动释放清理Nginx日志文件access.log

查看并查找相关信息及路径

    # 查看空间占用
    $ df -h
     
    # 定位Nginx
    $ which nginx
    /usr/local/nginx/logs
     
    # 列出日志文件
    $ cd /usr/local/nginx/logs
    ls
     
    # 查看日志文件大小
    $ du -sh ./*
     
    # 暂停Nginx并删除日志文件
    # nginx -s stop
    rm -rf *.log

这里需要注意的是，看到网上有人说重启Nginx可以清除日志文件，这是错误的。重启并不会清空日志文件，你需要手动清理。

另外，你也可以使用覆盖日志的方法清理Nginx日志文件

    echo "" > /usr/local/nginx/access.log

如果不需要日志文件就直接关闭（不建议），nginx.conf

    access_log off; 

### 对Nginx access.log进行分割

通过shell脚本+linux的定时任务进行的一个平滑切分。不需要重启nginx进程。代码cut_logs.sh

    #!/bin/bash
    log_path=/usr/local/nginx/logs/access.log
    save_path=/usr/local/nginx/logs/bak/access_$(date +%Y%m%d -d 'yesterday').log
    cp $log_path $save_path && echo > $log_path

设置定时任务

    $ crontab -e
    #输入
    0 0  * * * /usr/bin/sh cut_logs.sh #每天的00:00执行日志切分
     
    $ crontab -l #查看定时任务是否添加成功




