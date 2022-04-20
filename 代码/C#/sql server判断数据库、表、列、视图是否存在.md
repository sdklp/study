# [sql server判断数据库、表、列、视图是否存在](https://www.cnblogs.com/1175429393wljblog/p/6430458.html)

**1 判断数据库是否存在**

if exists (select * from sys.databases where name = '数据库名') drop database [数据库名]

**2 判断表是否存在**

if exists (select * from sysobjects where id = object_id(N'[表名]') and OBJECTPROPERTY(id, N'IsUserTable') = 1) drop table [表名]

**3 判断存储过程是否存在**

if exists (select * from sysobjects where id = object_id(N'[存储过程名]') and OBJECTPROPERTY(id, N'IsProcedure') = 1) drop procedure [存储过程名]

**4 判断临时表是否存在**

if object_id('tempdb..#临时表名') is not null drop table #临时表名

**5 判断视图是否存在**

--判断是否存在'MyView52'这个试图 IF EXISTS (SELECT TABLE_NAME FROM INFORMATION_SCHEMA.VIEWS WHERE TABLE_NAME = N'MyView52') PRINT '存在' else PRINT '不存在'

**6 判断函数是否存在**

-- 判断要创建的函数名是否存在 if exists (select * from dbo.sysobjects where id = object_id(N'[dbo].[函数名]') and xtype in (N'FN', N'IF', N'TF')) drop function [dbo].[函数名]

**7 获取用户创建的对象信息**

SELECT [name],[id],crdate FROM sysobjects where xtype='U'

**8 判断列是否存在**

if exists(select * from syscolumns where id=object_id('表名') and name='列名') alter table 表名 drop column 列名

**9 判断列是否自增列**

if columnproperty(object_id('table'),'col','IsIdentity')=1 print '自增列' else print '不是自增列'
SELECT * FROM sys.columns WHERE object_id=OBJECT_ID('表名') AND is_identity=1

**10 判断表中是否存在索引**

if exists(select * from sysindexes where id=object_id('表名') and name='索引名') print '存在' else print '不存在'

删除索引 drop index 表名.索引名

或: drop index 索引名 on 表名(貌似2000不行)

**11 查看数据库中对象**

SELECT * FROM sys.sysobjects WHERE name='对象名' SELECT * FROM sys.sysobjects WHERE name='对象名'