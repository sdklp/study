# C#连接sqlServer数据库详解



 **C# 是如何跟SQL Server进行连接的？**

![img](https://img-blog.csdn.net/20140529223537625)





​    在C#/.NET程序设计中，离不开ADO.NET。ADO.NET是.NET连接数据库的重要组件。使用其可以很方便地访问数据库，ADO.NET还可以访问Oracle数据库、Access数据库、SQL Server数据库等主流的数据库。使用ADO.NET连接数据库主要使用ADO.NET中的5个类。

​    **数据库连接类Connection：**如果连接SQLServer数据库，可以使用SqlConnetion类。在使用SqlConnection类是要引用一个System.Data.SqlClient的命名空间。

​    **数据库命令类Command：**如果连接的是SQLServer数据库，可以使用SqlCommand。数据库命令类主要执行对数据库的操作，比如插入、删除、修改等。

​    **数据库读取类DataReader：**如果连接SQLServer数据库，可以使用SqlDataReader。数据库读取类是数据库命令类在执行了查询操作后返回的结果的数据类型。数据库读取类只是数据库的连接状态处于打开状态时才能使用，当数据库关闭时数据库读取类中就不能够再取值了。

​    **数据集类DataSet：**数据集相当于一个虚拟数据库，每一个数据集中包括了多张数据表。即使数据库的连接处于断开状态，还是可以从数据集中继续存取记录，只是数据是存放在数据集中的，并没有存放在数据库中。

​    **数据适配类DataAdapter：**如果连接SQLServer数据库，可以使用SqlDataAdapter。数据适配器经常和数据集一起使用，通过数据适配器可以把数据库中的数据存放到数据集中，数据适配器可以说是数据集和数据库之间的一个桥梁。



 连接数据库一般有两种方式：

​    ***\*1、使用SQL用户名、密码验证\****

​    Data Source = 服务器名；Initial Catalog = 数据库名；User ID = 用户名；Pwd = 密码（没有密码可以省略）

​        例如：public string connString = "Data Source=xp;Initial Catalog=ExpressManager;User ID = sa;Pwd = 123";

​    ***\*2、使用windows身份验证\****

​    Data Source = 服务器名；Initial Catalog = 数据库名；Integrated Security = TRUE(或者：SSPI)

​        例如：public string connString = "Data Source=xp;Initial Catalog=ExpressManager;Integrated Security=TRUE";

![img](https://img-blog.csdn.net/20140529225632171)

   在身份验证可以选SQL 用户名、密码验证。



   接下来就是在源文件里加入连接数据库的代码，首先得在xxx.cs源文件中加入以下语句

   

```c#
using System.Data; 

using System.Data.SqlClient;
```

   接下来就是对数据库的操作类方法的实现：



**[csharp]** 

1. // 数据库操作类 

2. ```c#
   1.   **class** Express 
   2.   { 
   3. ​    **public** **string** connString = "Data Source=xp;Initial Catalog=ExpressManager;Integrated Security=TRUE"; 
   4. ​    //创建连接对象的变量 
   5. ​    **public** SqlConnection conn; 
   6. ​    // 执行对数据表中数据的增加、删除、修改操作 
   7. ​    **public** **int** NonQuery(**string** sql) 
   8. ​    { 
   9. ​      conn = **new** SqlConnection(connString); 
   10. ​      **int** a = -1; 
   11. ​      **try** 
   12. ​      { 
   13. ​        conn.Open(); //打开数据库 
   14. ​        SqlCommand cmd = **new** SqlCommand(sql, conn); 
   15. ​        a = cmd.ExecuteNonQuery(); 
   16. ​      } 
   17. ​      **catch** 
   18. ​      { 
   19.  
   20. ​      } 
   21. ​      **finally** 
   22. ​      { 
   23. ​        **if** (conn.State == ConnectionState.Open) 
   24. ​        { 
   25. ​          conn.Close();  //关闭数据库 
   26. ​        } 
   27. ​      } 
   28. ​      **return** a; 
   29.  
   30. ​    } 
   31. ​    // 执行对数据表中数据的查询操作 
   32. ​    **public** DataSet Query(**string** sql) 
   33. ​    { 
   34. ​      conn = **new** SqlConnection(connString); 
   35. ​      DataSet ds = **new** DataSet(); 
   36. ​      **try** 
   37. ​      { 
   38. ​        conn.Open();   //打开数据库 
   39. ​        SqlDataAdapter adp = **new** SqlDataAdapter(sql, conn); 
   40. ​        adp.Fill(ds); 
   41. ​      } 
   42. ​      **catch** 
   43. ​      { 
   44.  
   45. ​      } 
   46. ​      **finally** 
   47. ​      { 
   48. ​        **if**(conn.State== ConnectionState.Open) 
   49. ​        conn.Close();   //关闭数据库 
   50. ​      } 
   51. ​      **return** ds; 
   52. ​    } 
   53.   } 
   ```

   

### 另一文章如下

```c#
// 方法一：
SqlConnectionStringBuilder sr = new SqlConnectionStringBuilder();   // 创建一个连接对象

sr.DataSource = "127.0.0.1";   // 本机连接
sr.UserID = "用户名";   // 用户名，系统默认为sa
sr.Password = "密码";    // 密码
sr.InitialCatalog = "Student";    // 需要查询的数据库

// 方法二：
string sqlstr = "Data Source = 127.0.0.1; Initial Catalog = Student; User ID = 用户名; Password =  密码";

// 创建连接
SqlConnection Myconnect = new SqlConnection(sqlstr);    // 使用方法一时：sr.ToString()
// 打开连接
Myconnect.Open();   

string name = Console.ReadLine();  // 输入查询内容
// 查询语句
string sqlStr = "select * from dbo.db_Student_Imformation where 姓名 = '"+ name + "';";
// 执行SQL语句的对象
SqlCommand Mycom = new SqlCommand(sqlStr,Myconnect);  // 参数1：查询语句； 参数2：连接对象，必须要打开状态

// 接收结果
SqlDataReader checkstr = Mycom.ExecuteReader();   // 只能查询

while(checkstr.Read())
{
      Console.WriteLine(checkstr["ID"].ToString());   // 读取数据
}
Myconnect.Close();   // 关闭数据库
```

