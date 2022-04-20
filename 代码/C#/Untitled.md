# SqlCommand调用存储过程

用一个命令对象调用存储过程，就是定义存储过程的名称，给过程的每个参数添加参数定义，然后用上一节中给出的方法执行命令。

为了使本节的示例更有说服力，下面定义一组可以用于插入、更新和删除Northwind示例数据库中Region表的记录的存储过程，这个表尽管很小，但可以用于给每种常见的存储过程编写示例。

**1. 调用没有返回值的存储过程**

调用存储过程的最简单示例是不给调用者返回任何值。下面定义了两个这样的存储过程，一个用于更新现有的Region记录，另一个用于删除指定的Region记录。

(1) 记录的更新

更新Region记录是很简单的，因为(假定主键码不能被更新)只有一个列可以更新。直接在SQL Server查询分析器中键入这些示例，或者运行本章下载代码中的StoredProcs.sql文件，在该文件中包含本节中的所有存储过程：

CREATE PROCEDURE RegionUpdate (@RegionID INTEGER,

​                                 @RegionDescription NCHAR(50)) AS

   SET NOCOUNT OFF

   UPDATE Region

​      SET RegionDescription = @RegionDescription

​      WHERE RegionID = @RegionID

GO

给真实世界中的表执行更新命令，需要重复选择和返回已更新的记录。这个存储过程有两个输入参数(@RegionID和@RegionDescription)，对数据库执行UPDATE语句。

要在.NET代码中运行这个存储过程，需要定义一个SQL命令，并执行它：

SqlCommand aCommand = new SqlCommand("RegionUpdate", conn);

 

aCommand.CommandType = CommandType.StoredProcedure;

aCommand.Parameters.Add(new SqlParameter ("@RegionID",  SqlDbType.Int,   0,

​                                          "RegionID"));

aCommand.Parameters.Add(new SqlParameter("@RegionDescription",

​                                       SqlDbType.NChar,

​                                       50,

​                                       "RegionDescription"));

aCommand.UpdatedRowSource = UpdateRowSource.None;

这段代码创建了一个新SqlCommand对象aCommand，并把它定义为一个存储过程。然后，依次添加每个参数，最后把存储过程的结果设置为UpdateRowSource枚举中的一个值。

该存储过程有两个参数：要更新的Region记录的惟一主键码；给这个记录的新描述。创建好命令后，就可以用下面的命令执行它：

aCommand.Parameters[0].Value = 999;

aCommand.Parameters[1].Value = "South Western England";

aCommand.ExecuteNonQuery();

这几个命令设置了每个参数的值，然后执行存储过程。该过程没有返回值，因此使用ExecuteNonQuery就足够了。命令参数可以像前面那样依次设置，或者按照名称来设置。

(2) 记录的删除

下一个存储过程可用于从数据库中删除一个Region记录：

CREATE PROCEDURE RegionDelete (@RegionID INTEGER) AS

   SET NOCOUNT OFF

   DELETE FROM Region

   WHERE       RegionID = @RegionID

GO

这个过程只需要该记录的主键码值。下面的代码使用SqlCommand对象调用这个存储过程：

SqlCommand aCommand = new SqlCommand("RegionDelete" , conn);

aCommand.CommandType = CommandType.StoredProcedure;

aCommand.Parameters.Add(new SqlParameter("@RegionID" , SqlDbType.Int , 0 ,  "RegionID"));

aCommand.UpdatedRowSource = UpdateRowSource.None;

这个命令只接收一个参数，如下面的代码所示，它执行RegionDelete存储过程，这是一个按照名称设置参数的示例：

aCommand.Parameters["@RegionID"].Value= 999;

aCommand.ExecuteNonQuery();

**2. 调用返回输出参数的存储过程**

前面两个执行存储过程的示例都没有返回值。如果存储过程包含输出参数，则它们就需要在.NET客户程序中定义，以便在过程返回时填充其输出参数。下面的示例说明了如何在数据库中插入记录，把该记录的主键码返回给调用者。

记录的插入

Region表仅由一个主键码(RegionID)和描述字段(RegionDescription)组成。要插入一个记录，需要生成该数字主键码，再把新行插入到数据库中。在这个示例中，通过在存储过程中创建一个主键码，简化了主键码的生成。使用的方法未经过任何加工，这就是本章的后面用一节的篇幅介绍键的生成的原因。下面使用这个示例就足够了：

CREATE PROCEDURE RegionInsert(@RegionDescription NCHAR(50),

​                               @RegionID INTEGER OUTPUT)AS

   SET NOCOUNT OFF

   SELECT @RegionID = MAX(RegionID)+ 1

   FROM Region

   INSERT INTO Region(RegionID, RegionDescription)

   VALUES(@RegionID, @RegionDescription)

GO

插入过程创建一个新 Region 记录，在数据库本身生成主键码值时，这个值作为输出参数从过程返回(@RegionID) 。这对于这个简单示例来说就足够了，但对于比较复杂的表 ( 特别是有默认值的表 ) ，通常不使用输出参数，而选择整个插入的行，把该行返回给调用者。 .NET 类可以处理这两种情况。

SqlCommand  aCommand = new SqlCommand("RegionInsert" , conn);

aCommand.CommandType = CommandType.StoredProcedure;

aCommand.Parameters.Add(new SqlParameter("@RegionDescription" ,  SqlDbType.NChar , 50 ,

​                                          "RegionDescription"));

aCommand.Parameters.Add(new SqlParameter("@RegionID" ,   SqlDbType.Int, 0 ,

​                                            ParameterDirection.Output ,false ,  0 ,  0 ,  "RegionID" ,  DataRowVersion.Default ,

​                                            null));

aCommand.UpdatedRowSource = UpdateRowSource.OutputParameters;

其中参数的定义比较复杂。第二个参数@RegionID定义为包含其参数定向，在这个示例中是Output。除这个标志之外，该示例还在最后一行使用UpdateRowSource枚举表示通过输出参数从这个存储过程返回的数据。当从一个DataTable(详见本章后面的内容)中执行存储过程调用时，主要使用这个标志。

调用这个存储过程类似于前面的示例，但在这个实例中，需要在执行完过程后读取输出参数：

aCommand.Parameters["@RegionDescription"].Value = "South West";

aCommand.ExecuteNonQuery();

int newRegionID = (int) aCommand.Parameters["@RegionID"].Value;

在执行完命令后，读取@RegionID参数的值，并把它的数据类型转换为整型。

如果调用的存储过程返回输出参数和一组记录行，该怎么办？在该实例中，应定义合适的参数，而不是调用ExecuteNonQuery()，应调用另一个方法(例如ExecuteReader())，遍历所有的返回记录

 \----------------------------------------------------------------------------------------------------------------

 本人实例

![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                  // 打开数据库
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                 DataOpen();
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                sqlCommand.Connection  =  conn;
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                 // 设置存储器参数
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                 sqlCommand.CommandType  =  CommandType.StoredProcedure;
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                sqlCommand.CommandText  =   " tg_GetSum " ;
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                sqlCommand.Parameters.Clear();
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                sqlCommand.Parameters.Add( " @startDate " , SqlDbType.DateTime).Value  =  startDate;
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                sqlCommand.Parameters.Add( " @endDate " , SqlDbType.DateTime).Value  =  endDate;
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                sqlCommand.Parameters.Add( new  SqlParameter( " @sum " , SqlDbType.Money,  20 , 

​                                             Parameter Direction.Output,  false ,  20 ,  2 ,  " sum " , DataRowVersion.Default,  null ));
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                sqlCommand.Parameters.Add( new  SqlParameter( " @bigsum " , SqlDbType.Money,  20 , 

​                                        ParameterDirection.Output,  false ,  20 ,  2 ,  " bigsum " , DataRowVersion.Default,  null ));
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                sqlCommand.Parameters.Add( new  SqlParameter( " @gtsum " , SqlDbType.Money,  20 , 

​                                            ParameterDirection.Output,  false ,  20 ,  2 ,  " gtsum " , DataRowVersion.Default,  null ));
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                //执行
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                sqlCommand.ExecuteNonQuery();
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                 string  sum  =  sqlCommand.Parameters[ " @sum " ].Value.ToString();
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                 string  bigsum  =  sqlCommand.Parameters[ " @bigsum " ].Value.ToString();
![img](http://images.csdn.net/syntaxhighlighting/OutliningIndicators/None.gif)                 string  gtsum  =  sqlCommand.Parameters[ " @gtsum " ].Value.ToString();