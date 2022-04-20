##   存储过程

一、前言：在经过一段时间的存储过程开发之后，写下了一些开发时候的小结和经验与大家共享，希望对大家有益，主要是针对Sybase和SQL Server数据库，但其它数据库应该有一些共性。

二、适合读者对象：数据库开发程序员，数据库的数据量很多，涉及到对SP（存储过程）的优化的项目开发人员，对数据库有浓厚兴趣的人。

三、介绍：在数据库的开发过程中，经常会遇到复杂的业务逻辑和对数据库的操作，这个时候就会用SP来封装数据库操作。如果项目的SP较多，书写又没有一定的规范，将会影响以后的系统维护困难和大SP逻辑的难以理解，另外如果数据库的数据量大或者项目对SP的性能要求很，就会遇到优化的问题，否则速度有可能很慢，经过亲身经验，一个经过优化过的SP要比一个性能差的SP的效率甚至高几百倍。

四、 内容：

1、开发人员如果用到其他库的Table或View，务必在当前库中建立View来实现跨库操作，最好不要直接使用“databse.dbo.table_name”，因为sp_depends不能显示出该SP所使用的跨库table或view，不方便校验。

2、开发人员在提交SP前，必须已经使用set showplan on分析过查询计划，做过自身的查询优化检查。

3、高程序运行效率，优化应用程序，在SP编写过程中应该注意以下几点：

a) SQL的使用规范：

i. 尽量避免大事务操作，慎用holdlock子句，提高系统并发能力。

ii. 尽量避免反复访问同一张或几张表，尤其是数据量较大的表，可以考虑先根据条件提取数据到临时表中，然后再做连接。

iii. 尽量避免使用游标，因为游标的效率较差，如果游标操作的数据超过1万行，那么就应该改写；如果使用了游标，就要尽量避免在游标循环中再进行表连接的操作。

iv. 注意where字句写法，必须考虑语句顺序，应该根据索引顺序、范围大小来确定条件子句的前后顺序，尽可能的让字段顺序与索引顺序相一致，范围从大到小。

v. 不要在where子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引。

vi. 尽量使用exists代替select count(1)来判断是否存在记录，count函数只有在统计表中所有行数时使用，而且count(1)比count(*)更有效率。

vii. 尽量使用“>=”，不要使用“>”。

viii. 注意一些or子句和union子句之间的替换

ix. 注意表之间连接的数据类型，避免不同类型数据之间的连接。

x. 注意存储过程中参数和数据类型的关系。

xi. 注意insert、update操作的数据量，防止与其他应用冲突。如果数据量超过200个数据页面（400k），那么系统将会进行锁升级，页级锁会升级成表级锁。

b) 索引的使用规范：

i. 索引的创建要与应用结合考虑，建议大的OLTP表不要超过6个索引。

ii. 尽可能的使用索引字段作为查询条件，尤其是聚簇索引，必要时可以通过index index_name来强制指定索引

iii. 避免对大表查询时进行table scan，必要时考虑新建索引。

iv. 在使用索引字段作为条件时，如果该索引是联合索引，那么必须使用到该索引中的第一个字段作为条件时才能保证系统使用该索引，否则该索引将不会被使用。

v. 要注意索引的维护，周期性重建索引，重新编译存储过程。

c) tempdb的使用规范：

i. 尽量避免使用distinct、order by、group by、having、join、***pute，因为这些语句会加重tempdb的负担。

ii. 避免频繁创建和删除临时表，减少系统表资源的消耗。

iii. 在新建临时表时，如果一次性插入数据量很大，那么可以使用select into代替create table，避免log，提高速度；如果数据量不大，为了缓和系统表的资源，建议先create table，然后insert。

iv. 如果临时表的数据量较大，需要建立索引，那么应该将创建临时表和建立索引的过程放在单独一个子存储过程中，这样才能保证系统能够很好的使用到该临时表的索引。

v. 如果使用到了临时表，在存储过程的最后务必将所有的临时表显式删除，先truncate table，然后drop table，这样可以避免系统表的较长时间锁定。

vi. 慎用大的临时表与其他大表的连接查询和修改，减低系统表负担，因为这种操作会在一条语句中多次使用tempdb的系统表。

d) 合理的算法使用：

根据上面已提到的SQL优化技术和ASE Tuning手册中的SQL优化内容,结合实际应用,采用多种算法进行比较,以获得消耗资源最少、效率最高的方法。具体可用ASE调优命令：set statistics io on, set statistics time on , set showplan on 等  

# 调用存储过程 



用一个命令对象调用存储过程，就是定义存储过程的名称，给过程的每个参数添加参数定义，然后用上一节中给出的方法执行命令。

为了使本节的示例更有说服力，下面定义一组可以用于插入、更新和删除Northwind示例数据库中Region表的记录的存储过程，这个表尽管很小，但可以用于给每种常见的存储过程编写示例。

#### 1. 调用没有返回值的存储过程

调用存储过程的最简单示例是不给调用者返回任何值。下面定义了两个这样的存储过程，一个用于更新现有的Region记录，另一个用于删除指定的Region记录。

(1) 记录的更新

更新Region记录是很简单的，因为(假定主键码不能被更新)只有一个列可以更新。直接在SQL Server查询分析器中键入这些示例，或者运行本章下载代码中的StoredProcs.sql文件，在该文件中包含本节中的所有存储过程：

```mssql
CREATE PROCEDURE RegionUpdate (@RegionID INTEGER,@RegionDescription NCHAR(50)) AS
   SET NOCOUNT OFF
   UPDATE Region
      SET RegionDescription = @RegionDescription
      WHERE RegionID = @RegionID
GO
```

给真实世界中的表执行更新命令，需要重复选择和返回已更新的记录。这个存储过程有两个输入参数(@RegionID和@RegionDescription)，对数据库执行UPDATE语句。

要在.NET代码中运行这个存储过程，需要定义一个SQL命令，并执行它：

```
SqlCommand aCommand = new SqlCommand("RegionUpdate", conn); 
aCommand.CommandType = CommandType.StoredProcedure;
aCommand.Parameters.Add(new SqlParameter ("@RegionID",  SqlDbType.Int,  0, "RegionID"));
aCommand.Parameters.Add(new SqlParameter("@RegionDescription", SqlDbType.NChar, 50, "RegionDescription"));
aCommand.UpdatedRowSource = UpdateRowSource.None;
```

这段代码创建了一个新SqlCommand对象aCommand，并把它定义为一个存储过程。然后，依次添加每个参数，最后把存储过程的结果设置为UpdateRowSource枚举中的一个值。

该存储过程有两个参数：要更新的Region记录的惟一主键码；给这个记录的新描述。创建好命令后，就可以用下面的命令执行它：

```
aCommand.Parameters[0].Value = 999;
aCommand.Parameters[1].Value = "South Western England";
aCommand.ExecuteNonQuery();
```

这几个命令设置了每个参数的值，然后执行存储过程。该过程没有返回值，因此使用ExecuteNonQuery就足够了。命令参数可以像前面那样依次设置，或者按照名称来设置。

(2) 记录的删除

下一个存储过程可用于从数据库中删除一个Region记录：

```
CREATE PROCEDURE RegionDelete (@RegionID INTEGER) AS
   SET NOCOUNT OFF
   DELETE FROM Region
   WHERE RegionID = @RegionID
GO
```

这个过程只需要该记录的主键码值。下面的代码使用SqlCommand对象调用这个存储过程：

```
SqlCommand aCommand = new SqlCommand("RegionDelete" , conn);
aCommand.CommandType = CommandType.StoredProcedure;
aCommand.Parameters.Add(new SqlParameter("@RegionID" , SqlDbType.Int , 0 ,"RegionID"));
aCommand.UpdatedRowSource = UpdateRowSource.None;
```

这个命令只接收一个参数，如下面的代码所示，它执行RegionDelete存储过程，这是一个按照名称设置参数的示例：

```
aCommand.Parameters["@RegionID"].Value= 999;
aCommand.ExecuteNonQuery();
```



#### 2. 调用返回输出参数的存储过程

前面两个执行存储过程的示例都没有返回值。如果存储过程包含输出参数，则它们就需要在.NET客户程序中定义，以便在过程返回时填充其输出参数。下面的示例说明了如何在数据库中插入记录，把该记录的主键码返回给调用者。

记录的插入

Region表仅由一个主键码(RegionID)和描述字段(RegionDescription)组成。要插入一个记录，需要生成该数字主键码，再把新行插入到数据库中。在这个示例中，通过在存储过程中创建一个主键码，简化了主键码的生成。使用的方法未经过任何加工，这就是本章的后面用一节的篇幅介绍键的生成的原因。下面使用这个示例就足够了：

```
CREATE PROCEDURE RegionInsert(@RegionDescription NCHAR(50), @RegionID INTEGER OUTPUT)AS
   SET NOCOUNT OFF
   SELECT @RegionID = MAX(RegionID)+ 1
   FROM Region
   INSERT INTO Region(RegionID, RegionDescription)
   VALUES(@RegionID, @RegionDescription)
GO
```

插入过程创建一个新Region记录，在数据库本身生成主键码值时，这个值作为输出参数从过程返回(@RegionID)。这对于这个简单示例来说就足够了，但对于比较复杂的表(特别是有默认值的表)，通常不使用输出参数，而选择整个插入的行，把该行返回给调用者。.NET类可以处理这两种情况。

```
SqlCommand  aCommand = new SqlCommand("RegionInsert" , conn);
aCommand.CommandType = CommandType.StoredProcedure;
aCommand.Parameters.Add(new SqlParameter("@RegionDescription",SqlDbType.NChar,50, "RegionDescription"));
aCommand.Parameters.Add(new SqlParameter("@RegionID",SqlDbType.Int,0, ParameterDirection.Output ,false , 0 ,0 ,"RegionID" ,DataRowVersion.Default , null));
aCommand.UpdatedRowSource = UpdateRowSource.OutputParameters;
```

其中参数的定义比较复杂。第二个参数@RegionID定义为包含其参数定向，在这个示例中是Output。除这个标志之外，该示例还在最后一行使用UpdateRowSource枚举表示通过输出参数从这个存储过程返回的数据。当从一个DataTable(详见本章后面的内容)中执行存储过程调用时，主要使用这个标志。

调用这个存储过程类似于前面的示例，但在这个实例中，需要在执行完过程后读取输出参数：

```
aCommand.Parameters["@RegionDescription"].Value = "South West";
aCommand.ExecuteNonQuery();
int newRegionID = (int) aCommand.Parameters["@RegionID"].Value;
```

在执行完命令后，读取@RegionID参数的值，并把它的数据类型转换为整型。

如果调用的存储过程返回输出参数和一组记录行，该怎么办？在该实例中，应定义合适的参数，而不是调用ExecuteNonQuery()，应调用另一个方法(例如ExecuteReader())，遍历所有的返回记录



# 专用于SqlServer2005的高效分页存储过程(支持多字段任意排序,不要求排序字段唯一)



```
set ANSI_NULLS ON
set QUOTED_IDENTIFIER ON
go
-- =============================================
-- Author:        <杨俊明>
-- Create date: <2006-11-05>
-- Description:    <高效分页存储过程，仅适用于Sql2005>
-- Notes:        <排序字段强烈建议建索引>
-- =============================================
Alter Procedure [dbo].[up_Page2005] 
 @TableName varchar(50),        --表名
 @Fields varchar(5000) = '*',    --字段名(全部字段为*)
 @OrderField varchar(5000),        --排序字段(必须!支持多字段)
 @sqlWhere varchar(5000) = Null,--条件语句(不用加where)
 @pageSize int,                    --每页多少条记录
 @pageIndex int = 1 ,            --指定当前为第几页
 @TotalPage int output            --返回总页数 
as
begin

    Begin Tran --开始事务

    Declare @sql nvarchar(4000);
    Declare @totalRecord int;    

    --计算总记录数
         
    if (@SqlWhere='' or @sqlWhere=NULL)
        set @sql = 'select @totalRecord = count(*) from ' + @TableName
    else
        set @sql = 'select @totalRecord = count(*) from ' + @TableName + ' where ' + @sqlWhere

    EXEC sp_executesql @sql,N'@totalRecord int OUTPUT',@totalRecord OUTPUT--计算总记录数        
    
    --计算总页数
    select @TotalPage=CEILING((@totalRecord+0.0)/@PageSize)

    if (@SqlWhere='' or @sqlWhere=NULL)
        set @sql = 'Select * FROM (select ROW_NUMBER() Over(order by ' + @OrderField + ') as rowId,' + @Fields + ' from ' + @TableName 
    else
        set @sql = 'Select * FROM (select ROW_NUMBER() Over(order by ' + @OrderField + ') as rowId,' + @Fields + ' from ' + @TableName + ' where ' + @SqlWhere    
        
    
    --处理页数超出范围情况
    if @PageIndex<=0 
        Set @pageIndex = 1
    
    if @pageIndex>@TotalPage
        Set @pageIndex = @TotalPage

     --处理开始点和结束点
    Declare @StartRecord int
    Declare @EndRecord int
    
    set @StartRecord = (@pageIndex-1)*@PageSize + 1
    set @EndRecord = @StartRecord + @pageSize - 1

    --继续合成sql语句
    set @Sql = @Sql + ') as ' + @TableName + ' where rowId between ' + Convert(varchar(50),@StartRecord) + ' and ' +  Convert(varchar(50),@EndRecord)
    
    Exec(@Sql)
    \---------------------------------------------------
    If @@Error <> 0
      Begin
        RollBack Tran
        Return -1
      End
     Else
      Begin
        Commit Tran
        Return @totalRecord ---返回记录总数
      End    
end
```



# 如何返回存储过程中的OUTPUT值



 

```
SqlCommand   cmd=new   SqlCommand();    
  cmd.Connection=this.conn   ;    
  cmd.CommandType=CommandType.StoredProcedure;  
  cmd.CommandText="Returnrowcount";  
  cmd.Parameters.Add(new   SqlParameter("@sql",SqlDbType.VarChar,50));  
  cmd.Parameters["@sql"].Value=QueryString;  
  cmd.Parameters.Add(new   SqlParameter("@rowscnt",SqlDbType.Int,4));  
  cmd.Parameters["@rowscnt"].Direction=ParameterDirection.Output;  
  cmd.ExecuteNonQuery();  
  int   rowcount=Convert.ToInt32(cmd.Parameters["@rowscnt"].Value);

＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝
specify   the   parameter   first:  

  SqlParameter   sampParm   =   cmd.Parameters.Add("RETURN_VALUE",   SqlDbType.Int);  
  sampParm.Direction   =   ParameterDirection.ReturnValue;  
  ....  

  then,   to   get   its   value,   use  
  cmd.Parameters["RETURN_VALUE"].Value
```

 \----------------------------------------------------------------------------------------------------------------

 本人实例

![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                  // 打开数据库
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                

```
 DataOpen();
 sqlCommand.Connection  =  conn;
 // 设置存储器参数
 sqlCommand.CommandType  =  CommandType.StoredProcedure;
 sqlCommand.CommandText  =   " tg_GetSum " ;
 sqlCommand.Parameters.Clear();
 sqlCommand.Parameters.Add( " @startDate " , SqlDbType.DateTime).Value  =  startDate;
 sqlCommand.Parameters.Add( " @endDate " , SqlDbType.DateTime).Value  =  endDate;
 sqlCommand.Parameters.Add( new  SqlParameter( " @sum " , SqlDbType.Money,  20 , 
 Parameter Direction.Output,  false ,  20 ,  2 ,  " sum " , DataRowVersion.Default,  null ));
 sqlCommand.Parameters.Add( new  SqlParameter( " @bigsum " , SqlDbType.Money,  20 , 
 ParameterDirection.Output,  false ,  20 ,  2 ,  " bigsum " , DataRowVersion.Default,  null ));
 sqlCommand.Parameters.Add( new  SqlParameter( " @gtsum " , SqlDbType.Money,  20 , 
 ParameterDirection.Output,  false ,  20 ,  2 ,  " gtsum " , DataRowVersion.Default,  null ));
 //执行
 sqlCommand.ExecuteNonQuery();
 string  sum  =  sqlCommand.Parameters[ " @sum " ].Value.ToString();
 string  bigsum  =  sqlCommand.Parameters[ " @bigsum " ].Value.ToString();
 string  gtsum  =  sqlCommand.Parameters[ " @gtsum " ].Value.ToString();
```

