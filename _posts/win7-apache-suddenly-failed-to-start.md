---
title: win7下Apache突然启动失败
tags: [wamp]
categories: Web构建
date: 2020-12-18 12:45
---


win7下Apache突然启动失败

前段时间有一次重启了电脑（win7）之后，发现Apache服务并未随系统自动启动，结果在手动启动的时候也无法启动。

报错为：
> Windows 不能在本地计算机启动apache2.4。有关更多信息，查阅系统事件日志。如果这是非Microsoft服务，请与服务厂商联系，并参考特定服务错误代码1。

分析后发现最大可能是80端口被占用，于是win+r 运行cmd

输入`netstat -ano`

可以看到80端口被PID4占用，于是打开任务管理器-进程-查看，选择列，勾选PID

可以看到pid 4 的被NT kernel & System 占用

该进程是Http.sys。它是http API的驱动组件，Http栈服务器。如果该端口被Http.sys占用，说明一些正在使用http.sys的应用程序在运行。这就是阻止Apache运行的原因，因为Http.sys占用着80端口。我们提供了一种应用程序的机制来帮助控制端口共享，但是我需要调查导致你遇到这种困难的是什么特殊应用程序。如果你能提供给我们“netsh http show servicestate”这条命令的输出结果，我就能找出是哪个应用程序在使用Http.sys。

你可以按照下面步骤禁用http.sys：

第一步： net stop http

第二步：Sc config http start= disabled

【正式操作步骤】

于是运行`net stop http`

按“Y”确定

再运行 `Sc config http start= disabled`

好了，现在启动Apache，发现可以正常启动了。

再查看一下netstat -ano

发现pid 4占用了445端口（不再是80端口）。
　　




