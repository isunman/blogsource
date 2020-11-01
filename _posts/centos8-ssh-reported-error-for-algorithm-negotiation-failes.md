---
title: CentOS8解决SSHSecureShellClient连接Linux报错Algorithm negotiation failes
tags: [Linux]
categories: 技术日记
date: 2020-11-01 16:05
---



解决SSHSecureShellClient连接Linux报错Algorithm negotiation failes

今天新服务器版本已经到了CentOS8，但是SSH Secure Shell还是多年前的3.2.9，所以在连服务器时遇到标题中的报错。这个问题很悲剧，浪费了我很多时间。

网上解决这个问题的博文非常非常多，都是这样的方案：

1. 打开/etc/ssh文件目录下的文件sshd_config  
> sudo vim /etc/ssh/sshd_config

2. 在文件末尾添加以下信息

>     Ciphers aes128-cbc,aes192-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr,3des-cbc,arcfour128,arcfour256,arcfour,blowfish-cbc,cast128-cbc
>       
>     MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160,hmac-sha1-96,hmac-md5-96  
>     
>     KexAlgorithms diffie-hellman-group1-sha1,diffie-hellman-group14-sha1,diffie-hellman-group-exchange-sha1,diffie-hellman-group-exchange-sha256,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group1-sha1,curve25519-sha256@libssh.org

3. 保存后重启ssh服务


> sudo /etc/init.d/ssh restart

大家都说好用，但是我试了之后悲剧了，增加了配置后重启ssh就启动不了了，会报错。

但是我依然没能解决问题，最终决定弃用落后的SSH Secure Shell Client了，换成【FileZilla】解决问题：FileZilla也支持ssh协议传输文件，而且操作稍微比SSH Secure Shell Client人性化。

还有一种方案是把CentOS8退回到CentOS6,这还有什么用呢？

因此，应该是CentOS8和SSH Secure File Transfer Client之前的冲突解决不了的问题。
　