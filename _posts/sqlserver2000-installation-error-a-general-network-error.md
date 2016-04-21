---
title: 安装sqlserver2000出错，一般性网络错误
tags: [SQLServer]
categories: 服务器
---

### 问题：
安装sqlserver2000出错，一般性网络错误，错误提示如下：

	安装程序配置服务器失败。参考服务器错误日志和C:\windows\sqlstp.log了解更多信息
	正在启动?
	
	Chinese_PRC_CI_AS
	
	-m -Q -T4022 -T3659
	
	正在与服务?
	
	driver={sql server};server=zhangxx\MATCHONE;UID=sa;PWD=;database=master
	
	[Microsoft][ODBC SQL Server Driver][DBMSLPCN]一般性网络错误。
	
	[Microsoft][ODBC SQL Server Driver][DBMSLPCN]ConnectionRead (WrapperRead()).
	
	driver={sql server};server=zhangxx\MATCHONE;UID=sa;PWD=;database=master
	
	[Microsoft][ODBC SQL Server Driver][DBMSLPCN]一般性网络错误。
	
	[Microsoft][ODBC SQL Server Driver][DBMSLPCN]ConnectionRead (WrapperRead()).
	
	driver={sql server};server=zhangxx\MATCHONE;UID=sa;PWD=;database=master
	
	[Microsoft][ODBC SQL Server Driver][DBMSLPCN]一般性网络错误。
	
	[Microsoft][ODBC SQL Server Driver][DBMSLPCN]ConnectionRead (WrapperRead()).
	
	SQL Server 配置?
	
	###############################################################################
	
	13:11:03 Process Exit Code: (-1) 
	13:11:25 安装程序配置服务器失败。参考服务器错误日志和 C:\WINDOWS\sqlstp.log 了解更多信息。
	13:11:25 Action CleanUpInstall:
	13:11:25 C:\WINDOWS\TEMP\SqlSetup\Bin\scm.exe  -Silent 1 -Action 4 -Service SQLAgent$MATCHONE
	13:11:25 Process Exit Code: (1060) 指定的服务并未以已安装的服务存在。
	
	13:11:25 C:\WINDOWS\TEMP\SqlSetup\Bin\scm.exe  -Silent 1 -Action 4 -Service MSSQL$MATCHONE
	13:11:25 Process Exit Code: (0) 
	13:11:25 StatsGenerate returned: 2
	13:11:25 StatsGenerate (0xc0200006,0x1,0xf000000,0x300,2052,303,0x0,0x1,0,0,0
	13:11:25 StatsGenerate -1,zhang)
	13:11:25 Installation Failed.
	
	SQL SERVER 2000在C:\windows\sqlstp.log中提示错误如下：
	=======================================================
	driver={sql server};server=soyo;UID=sa;PWD=;database=master
	[Microsoft][ODBC SQL Server Driver][Shared Memory]一般性网络错误。
	[Microsoft][ODBC SQL Server Driver][Shared Memory]ConnectionRead (WrapperRead()).
	driver={sql server};server=soyo;UID=sa;PWD=;database=master
	[Microsoft][ODBC SQL Server Driver][Shared Memory]一般性网络错误。
	[Microsoft][ODBC SQL Server Driver][Shared Memory]ConnectionRead (WrapperRead()).
	driver={sql server};server=soyo;UID=sa;PWD=;database=master
	[Microsoft][ODBC SQL Server Driver][Shared Memory]一般性网络错误。
	[Microsoft][ODBC SQL Server Driver][Shared Memory]ConnectionR
	=======================================================


### 解决方案

**把计算机名改为大写，重新启动**

OK  真的解决问题。

另外提示一下，在安装sqlServer时 安装路径下不能有中文！

