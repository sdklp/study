# Sql Server中判断表或者数据库是否存在



**SQL Server中判断数据库是否存在：**
　　法(一):



　　　　select * From master.dbo.sysdatabases where name='数据库名'

　　法(二):
　　　　if db_id('数据库名') is not null

　　　　　　drop database 。。。
　　　  go

　　　　create 。。。

 **SQL Server中判断表对象是否存在：**
　　select count(*) from sysobjects where id = object_id('数据库名.Owner.表名')

　　if exists 

 　　　　(select count(*) from sysobjects where id = object_id('数据库名.Owner.表名'))
　　　　print '存在'
　　else
　　　　print '不存在'

**SQL Server中判断表中字段是否存在：**
　　if exists

　　　　　　(select * from syscolumns where name='colname1' and id=object_id('数据库名.Owner.表名'))
　　　　print '存在'
　　else
　　　　print '不存在'
　(代表表tablename1中存在colname1字段 )
例：
　　select * from syscolumns where name='Test' and id=object_id('dbo.test')

 

**SQL Server中判断存储过程或视图是否存在：**

 　if object_id('视图或存储过程名') is not null
 　　　drop proc/view 。。。
　　 go

　　 create proc/view 。。。

 

　　或

 

　　if Exists(select * from sysobjects where name='视图或存储过程名' AND type = 'P/V')
 　　　drop proc/view  。。。
　　go 

　　create proc/view  。。。 





# [sqlserver中判断表或临时表是否存在](https://www.cnblogs.com/yugen/archive/2010/07/25/1784749.html)

1、判断数据表是否存在

　　方法一：

use yourdb;
go

if object_id(N'tablename',N'U') is not null
print '存在'
else
print '不存在'


例如：
use fireweb;
go

if object_id(N'TEMP_TBL',N'U') is not null
print '存在'
else
print '不存在'

 

方法二：

USE [实例名]
GO

IF EXISTS (SELECT * FROM dbo.SysObjects WHERE ID = object_id(N'[表名]') AND OBJECTPROPERTY(ID, 'IsTable') = 1)
PRINT '存在'
ELSE
PRINT'不存在'


例如：
use fireweb;
go

IF EXISTS (SELECT * FROM dbo.SysObjects WHERE ID = object_id(N'TEMP_TBL') AND OBJECTPROPERTY(ID, 'IsTable') = 1)
PRINT '存在'
ELSE
PRINT'不存在'

2、临时表是否存在：

方法一：
use fireweb;
go

if exists(select * from tempdb..sysobjects where id=object_id('tempdb..##TEMP_TBL'))
PRINT '存在'
ELSE
PRINT'不存在'


方法二：
use fireweb;
go

if exists (select * from tempdb.dbo.sysobjects where id = object_id(N'tempdb..#TEMP_TBL') and type='U')
PRINT '存在'
ELSE
PRINT'不存在'