# C#连接SQL Server数据库增删改查



------

**C#连接SQL Server数据库**

**1、引入命名空间**

```c#
using System.Data;

using System.Data.SqlClient;
```

**2、创建数据库连接字符串**

连接字符串有多种写法，这里简单两种：

```c#
①、string strConn =”Server=服务器名；Database=数据库名；uid=用户名；pwd=密码；”;

②、string strConn =”Data Source=服务器名；initial catalog=数据库名； integrated security=True；Connect Timeout=30；”;
```

**3、建立连接对象**

SqlConnection conn=new SqlConnection(strConn); 

**4、打开数据库连接**

```c#
if (conn.State == ConnectionState.Closed) //判断数据库连接是否已经打开，如果没打开则打开数据库连接

{

  conn.Open(); //打开数据库连接

}
```

**5、创建SqlCommand等进行数据库操作**

增删改查等操作...

**6、关闭数据库连接**

```c#
conn.Close(); //关闭数据库连接
```



**解释：**

“uid”也可写作“user id”：指的是连接数据库的验证用户名。

“pwd”也可写作“password”：指的是连接数据库的验证密码。

注意：如果你的SQL Server设置为SQL Server 身份验证则需要用户名和密码来登录。 如果你的SQL Server设置为Windows 验证，那么在这里就不需要使用“user id”和“password”这样的方式来登录，而需要使用“integrated security=True;”来进行登录。

“Database”也可写作“initial catalog=”：指的是使用的数据源，也就是你数据库的名称。

“Server”也可写作“Data Source”：指的是使用的服务器名称。

“Connect Timeout=30”：指的是连接超时时间为30秒。



下面来看个简单的登录示例：

```c#
Codeusing System;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient; //引入命名空间

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("请输入您的账号：");
            string userName = Console.ReadLine();
            Console.WriteLine("请输入您的密码：");
            string userPwd = Console.ReadLine();
            
            string strConn = @"Data Source=.;Initial Catalog=Test; integrated security=True;"; //定义连接字符串
            SqlConnection conn = new SqlConnection(strConn); //定义连接对象并实例化（实例化时需要提供连接字符串作为参数）
            try
            {
                if (conn.State == ConnectionState.Closed) //判断数据库连接是否已经打开，如果没打开则打开数据库连接
                {
                    conn.Open(); //打开数据库连接
                }
                using (SqlCommand cmd = conn.CreateCommand())
                {
                    //SQL参数化查询可以防止SQL注入
                    cmd.CommandText = "select * from T_User where user_name=@UserName and user_pwd=@PassWord";
                    cmd.Parameters.AddWithValue("@UserName", userName);
                    cmd.Parameters.AddWithValue("@PassWord", userPwd);
                    using (SqlDataReader reader = cmd.ExecuteReader()) //将查询到的数据保存在reader这个变量里
                    {
                        //从reader中读取下一行数据,如果没有数据,reader.Read()返回flase 如果reader.Read()的结果不为空, 则说明查询结果存在
                        if (reader.Read())
                        {
                            Console.WriteLine("登录成功");
                        }
                        else //说明查询结果不存在
                        {
                            Console.WriteLine("您输入的账号或密码有误！");
                        }
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            finally
            {
                conn.Close(); //关闭数据库连接
            }
            Console.ReadKey();
        }
    }
}
```



**数据库连接字符串通常写在config配置文件中，方便修改。**

1、首先先在工程里添加system.configuration.dll程序集的引用。

2、打开config配置文件，添加如下代码：

![image.png](https://www.qbl.link/upload/images/20190512/6369329188384408933667292.png)

```c#
Code<!-- 配置数据库连接字符串 方式一 -->
<connectionStrings>
    <add name="strConn" connectionString="Data Source=.;Initial Catalog=Test; integrated security=True;" providerName="System.Data.SqlClient"/>
</connectionStrings>

<!-- 配置数据库连接字符串 方式二 -->
<appSettings>
    <add key="strConn" value="Data Source=.;Initial Catalog=Test; integrated security=True;" />
</appSettings>
```

3、写代码读取配置文件的参数

```c#
Code//以下两种方式，二选一即可
string strConn = ConfigurationManager.ConnectionStrings["strConn"].ToString(); //方式一
string strConn = ConfigurationSettings.AppSettings["strConn"].ToString(); //方式二
```



**查**

代码示例（方式一）：

```c#
Codeusing System;
using System.Configuration;
using System.Data;
using System.Data.SqlClient; //引入命名空间

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //读取出config文件中定义参数的值
            string strConn = ConfigurationManager.ConnectionStrings["strConn"].ToString();
            DataTable dt = null;
            try
            {
                //这里使用了using，会自动释放资源，所以不需要手动关闭数据库连接
                using (SqlConnection conn = new SqlConnection(strConn)) //定义连接对象并实例化（实例化时需要提供连接字符串作为参数）
                {
                    if (conn.State == ConnectionState.Closed) //判断数据库连接是否已经打开，如果没打开则打开数据库连接
                    {
                        conn.Open(); //打开数据库连接
                    }
                    using (SqlCommand cmd = new SqlCommand("select * from T_Student", conn))
                    {
                        using (SqlDataReader dr = cmd.ExecuteReader())
                        {
                            dt = new DataTable();
                            dt.Load(dr);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }

            if (dt != null)
            {
                Console.WriteLine("学号\t\t姓名\t\t性别\t年龄\t班别\r\n");
                //读出DataTable中的数据
                for (int i = 0; i < dt.Rows.Count; i++) //行
                {
                    for (int j = 0; j < dt.Columns.Count; j++) //列
                    {
                        Console.Write(dt.Rows[i][j]);
                        Console.Write("\t");
                    }
                    Console.WriteLine();
                }
            }
            Console.ReadKey();
        }
    }
}
```

代码示例（方式二）：

```c#
Codeusing System;
using System.Configuration;
using System.Data;
using System.Data.SqlClient; //引入命名空间

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //读取出config文件中定义参数的值
            string strConn = ConfigurationManager.ConnectionStrings["strConn"].ToString();
            DataSet ds = null;
            try
            {
                //这里使用了using，会自动释放资源，所以不需要手动关闭数据库连接
                using (SqlConnection conn = new SqlConnection(strConn)) //定义连接对象并实例化（实例化时需要提供连接字符串作为参数）
                {
                    if (conn.State == ConnectionState.Closed) //判断数据库连接是否已经打开，如果没打开则打开数据库连接
                    {
                        conn.Open(); //打开数据库连接
                    }                    
                    //实例化适配器，在实例化时需要提供查询语句和连接对象作为参数
                    using (SqlDataAdapter da = new SqlDataAdapter("select * from T_Student", conn))
                    {
                        ds = new DataSet(); //实例化记录集
                        da.Fill(ds, "myTable"); //通过适配器的Fill方法，把数据填充到记录集中，这时候需要提供记录集对象和一个表名作为参数  
                    }                                       
                }
            }
            catch(Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            if (ds != null)
            {
                Console.WriteLine("学号\t\t姓名\t\t性别\t年龄\t班别\r\n");
                //读出ds.Tables["myTable"]中的数据
                for (int i = 0; i < ds.Tables["myTable"].Rows.Count; i++) //行
                {
                    for (int j = 0; j < ds.Tables["myTable"].Columns.Count; j++) //列
                    {
                        Console.Write(ds.Tables["myTable"].Rows[i][j]);
                        Console.Write("\t");
                    }
                    Console.WriteLine();
                }             
            }                      
            Console.ReadKey();
        }
    }
}
```

运行结果：

![image.png](https://www.qbl.link/upload/images/20190512/6369329516008341974246783.png)



**增删改**

代码示例：

```c#
Codeusing System;
using System.Configuration;
using System.Data;
using System.Data.SqlClient; //引入命名空间

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //读取出config文件中定义参数的值
            string strConn = ConfigurationManager.ConnectionStrings["strConn"].ToString();
            try
            {
                //这里使用了using，会自动释放资源，所以不需要手动关闭数据库连接
                using (SqlConnection conn = new SqlConnection(strConn)) //定义连接对象并实例化（实例化时需要提供连接字符串作为参数）
                {
                    if (conn.State == ConnectionState.Closed) //判断数据库连接是否已经打开，如果没打开则打开数据库连接
                    {
                        conn.Open(); //打开数据库连接
                    }
                    string strIns = "insert into T_Student (stu_id,stu_name,stu_gender,stu_age,stu_class) values('7','张丽丽','女','20','163班')"; //增
                    //string strUp = "update T_Student set stu_name='张小丽' where stu_id='7'"; //改
                    //string strDel = "delete T_Student where stu_id='7'"; //删
                    using (SqlCommand cmd = new SqlCommand(strIns, conn))
                    {
                        //增删改等操作都是使用cmd.ExecuteNonQuery(); 
                        int result = cmd.ExecuteNonQuery(); //返回受影响的行数
                        Console.WriteLine("受影响的行数:"+ result);
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }     
            Console.ReadKey();
        }
    }
}
```



为了避免写过多重复的代码造成冗余，我们可以简单的把这些增删改查的操作封装成一个数据库帮助类，代码如下：

```c#
Codeusing System;
using System.Collections.Generic;
using System.Configuration;
using System.Data;
using System.Data.SqlClient;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp
{
    public class SqlHelper
    {
        /// <summary>
        /// 连接字符串
        /// </summary>
        private string StrConn { get; set; }

        #region 定义变量

        //表示 SQL Server 数据库的一个打开的连接,若要确保连接始终关闭，请在 using 块内部打开连接。这样可确保在代码退出代码块时自动关闭连接。 
        private SqlConnection conn = null;

        //表示要对 SQL Server 数据库执行的一个 Transact-SQL 语句或存储过程。
        private SqlCommand cmd = null;

        //可以用SqlDataReader类对象从SQL Server数据库中读取行,DataReader对象允许你以向前的，只读的方式读取数据
        private SqlDataReader dr = null;

        //DataTable 是一个临时保存数据的网格虚拟表(表示内存中数据的一个表)。
        private DataTable dt = null;

        //SqlDataAdapter是 DataSet和 SQL Server之间的桥接器。SqlDataAdapter通过对数据源使用适当的Transact-SQL语句映射 Fill（它可填充DataSet中的数据以匹配数据源中的数据）和 Update（它可更改数据源中的数据以匹配 DataSet中的数据）来提供这一桥接。当SqlDataAdapter填充 DataSet时，它为返回的数据创建必需的表和列（如果这些表和列尚不存在）。
        //private SqlDataAdapter da = null;

        //因为DataSet可以看做是内存中的数据库，也因此可以说DataSet是数据表的集合，它可以包含任意多个数据表（DataTable），而且每一 DataSet中的数据表（DataTable)对应一个数据源中的数据表（Table）或是数据视图（View）。
        //private DataSet ds = null;

        #endregion

        public SqlHelper()
        {
            //读取出config文件中定义参数的值
            StrConn = ConfigurationManager.ConnectionStrings["strConn"].ToString();
            conn = new SqlConnection(StrConn); //定义连接对象并实例化（实例化时需要提供连接字符串作为参数）
        }

        public SqlHelper( string strConn)
        {
            StrConn = strConn;
            conn = new SqlConnection(StrConn); //定义连接对象并实例化（实例化时需要提供连接字符串作为参数）
        }

        #region 判断数据库连接是否已经打开，如果没打开则打开数据库连接,并返回该连接对象。

        /// <summary>
        /// 判断数据库连接是否已经打开，如果没打开则打开数据库连接,并返回该连接对象。
        /// </summary>
        /// <returns>返回该连接对象</returns>
        private SqlConnection OpenDatabase()
        {            
            try
            {                
                if (conn.State == ConnectionState.Closed) //判断数据库连接是否已经打开，如果没打开则打开数据库连接
                {
                    conn.Open(); //打开数据库连接
                }
                return conn;
            }
            catch(Exception ex)
            {
                throw ex;
            }
        }

        #endregion

        #region 执行查询的sql语句

        /// <summary>
        /// 执行查询sql语句并返回一个DataTable表
        /// </summary>
        /// <param name="strSelect">查询sql语句</param>
        /// <returns>返回一个DataTable表</returns>
        public DataTable Select(string strSelect)
        {
            try
            {
                using (cmd = new SqlCommand(strSelect, OpenDatabase()))
                {
                    using (dr = cmd.ExecuteReader())
                    {
                        dt = new DataTable();
                        dt.Load(dr);
                    }
                }                
                return dt;
            }
            catch (Exception ex)
            {
                throw ex;
            }
            finally
            {
                conn.Close();//关闭数据库连接
            }
        }

        /// <summary>
        /// 执行查询带参的sql语句并返回一个DataTable表
        /// </summary>
        /// <param name="strSelect">查询sql语句</param>
        /// <param name="par">sql语句中的参数</param>
        /// <returns>返回一个DataTable表</returns>
        public DataTable Select(string strSelect, SqlParameter par)
        {
            try
            {                
                using (cmd = new SqlCommand(strSelect, OpenDatabase()))
                {
                    cmd.Parameters.Add(par);
                    using (dr = cmd.ExecuteReader())
                    {
                        dt = new DataTable();
                        dt.Load(dr);
                    }
                }
                return dt;
            }
            catch(Exception ex)
            {
                throw ex;
            }
            finally
            {
                conn.Close();//关闭数据库连接
            }
        }

        public DataTable Select(string strSelect, SqlParameter[] par)
        {
            try
            {               
                using (cmd = new SqlCommand(strSelect, OpenDatabase()))
                {
                    cmd.Parameters.AddRange(par);
                    using (dr = cmd.ExecuteReader())
                    {
                        dt = new DataTable();
                        dt.Load(dr);
                    }
                }
                return dt;
            }
            catch (Exception ex)
            {
                throw ex;
            }
            finally
            {
                conn.Close();//关闭数据库连接
            }           
        }

        #endregion

        #region 执行增、删、改sql语句

        /// <summary>
        /// 执行无参的增、删、改sql语句,并返回受影响的行数
        /// </summary>
        /// <param name="strSql">增、删、改的sql语句</param>
        /// <returns>返回受影响的行数</returns>
        public int ExecuteNonQuery(string strSql)
        {
            try
            {
                int result = 0;//结果
                using (cmd = new SqlCommand(strSql, OpenDatabase()))
                {
                    result = cmd.ExecuteNonQuery();
                }
                return result;
            }
            catch (Exception ex)
            {
                throw ex;
            }
            finally
            {
                conn.Close();//关闭数据库连接
            }            
        }

        /// <summary>
        /// 执行带参的增、删、改sql语句,并返回受影响的行数
        /// </summary>
        /// <param name="strSql">增、删、改的sql语句</param>
        /// <param name="par">sql语句中的参数</param>
        /// <returns>返回受影响的行数</returns>        
        public int ExecuteNonQuery(string strSql, SqlParameter par)
        {
            try
            {
                int result = 0;
                using (cmd = new SqlCommand(strSql, OpenDatabase()))
                {
                    cmd.Parameters.Add(par);
                    result = cmd.ExecuteNonQuery();
                }
                return result;
            }
            catch (Exception ex)
            {
                throw ex;
            }
            finally
            {
                conn.Close();//关闭数据库连接
            }           
        }

        public int ExecuteNonQuery(string strSql, SqlParameter[] par)
        {
            try
            {
                int result = 0;
                using (cmd = new SqlCommand(strSql, OpenDatabase()))
                {
                    cmd.Parameters.AddRange(par);
                    result = cmd.ExecuteNonQuery();
                }
                return result;
            }
            catch (Exception ex)
            {
                throw ex;
            }
            finally
            {
                conn.Close();//关闭数据库连接
            }           
        }

        #endregion






    }

}
```