# C#连接SQL Server数据库

| [日期：2015-02-26] | 来源：Linux社区 作者：guoquanyou | [字体：[大](javascript:ContentSize(16)) [中](javascript:ContentSize(0)) [小](javascript:ContentSize(12))] |
| ------------------ | -------------------------------- | ------------------------------------------------------------ |
|                    |                                  |                                                              |

对于不同的.NET数据提供者，ADO.NET采用不同的Connection对象连接数据库。这些Connection对象为我们屏蔽了具体的实现细节，并提供了一种统一的实现方法。

**Connection类有四种：SqlConnection，OleDbConnection，OdbcConnection和[Oracle](https://www.linuxidc.com/topicnews.aspx?tid=12)Connection。**

SqlConnection类的对象连接SQL Server数据库；OracleConnection 类的对象连接Oracle数据库；OleDbConnection类的对象连接支持OLE DB的数据库，如Access；而OdbcConnection类的对象连接任何支持ODBC的数据库。与数据库的所有通讯最终都是通过Connection对象来完成的。
 （1）用SqlConnection连接SQL Server

加入命名空间：using System.Data.SqlClient;

连接数据库： string conString = "data source=IP地址; Database=数据库名;user id=用户名; password=密码";

​             SqlConnection myconnection = new SqlConnection(conString);

​             myconnection.open();

（2）用OracleConnection连接Oracle

加入命名空间：using System.Data.OracleClient;

连接数据库： string conString = "data source=IP地址; Database=数据库名;user id=用户名; password=密码";

​             OracleConnection myconnection = new OracleConnection(conString);

​             myconnection.open();

（3）用 MySqlConnection连接MySQL

在.NET中连接MySQL数据库有两种方法：MySQL Connector/ODBC 和 MySQL Connector/NET，ODBC连接器是符合ODBC标准的交互平台，是.NET访问MySQL数据库最好的选择。

首先，需要下载安装MySql-connector-net-5.1.5.Data.msi这个组件。如果是默认安装，则可以在C:\Program Files\MySQL\MySQL Connector Net 5.1.5\Binaries\.NET2.0中找到MySql.Data.dll，将该文件复制到项目的bin目录下。并且在项目中添加引用MySql.Data.dll。实现代码如下：

加入命名空间：using MySql.Data.MySqlClient;

连接数据库： string conString = "server=IP地址; Database=数据库名;user id=用户名; password=密码";

​             MySqlConnection myconnection = new MySqlConnection(conString);

​             myconnection.open();

（4）用OleDbConnection连接各种数据源

由于数据源不同，相应的连接字符串也会不同。

加入命名空间：using System.Data.OleDb;

连接 SQL Server：  string conString = "Provider=SQLOLEDB.1; Persist Security Info=False; user id=用户名; Database=数据库名; data source=COMPUTER; ";

​                  OleDbConnection myconnection = new OleDbConnection(conString);

​                  myconnection.open();

连接 Access：  string conString = "Provider=Microsoft.Jet.OLEDB.4.0;  data source=C:\\Database1.mdb; Persist Security Info=False;";

​                  OleDbConnection myconnection = new OleDbConnection(conString);

​                  myconnection.open();

（也可以通过建立.udl文件来获得字符串）

连接 Oracle：  string conString = "Provider=MSDAORA;  user id=用户名; password=密码; data source=db; Persist Security Info=False;";

​                  OleDbConnection myconnection = new OleDbConnection(conString);

​                  myconnection.open();

（也可以通过OracleConnection连接） 

**注意：**使用不同的Connection对象需要导入不同的命名空间。OleDbConnection的命名空间为System.Data.OleDb。SqlConnection的命名空间为System.Data.SqlClient。OracleConnection的命名空间为System.Data.OracleClinet。

我们就可以使用如下两种方式连接数据库，**即采用集成的Windows验证和使用Sql Server身份验证进行数据库的登录。**

**1、集成的Windows身份验证语法范例**

string constr = "server=.;database=myschool;integrated security=SSPI";

说明：程序代码中，设置了一个针对Sql Server数据库的连接字符串。其中server表示运行Sql Server的计算机名，由于程序和数据库系统是位于同一台计算机的，所以我们可以用.(或localhost)取代当前的计算机名。database表示所使用的数据库名(myschool)。由于我们希望采用集成的Windows验证方式，所以设置 integrated security为SSPI即可。

**2、Sql Server 2005中的Windows身份验证模式如下：**

string constr = "server=.;database=myschool;uid=sa;pwd=sa";

说明：程序代码中，采用了使用已知的用户名和密码验证进行数据库的登录。数据库连接字符串是不区分大小写的。uid为指定的数据库用户名，pwd为指定的用户口令。为了安全起见，一般不要在代码中包括用户名和口令，你可以采用前面的集成的Windows验证方式或者对Web.Config文件中的连接字符串加密的方式提高程序的安全性。

**3、Sql Server 2005中的Sql Server身份验证模式如下：**

string constr = "data source=.;initial catalog=myschool;user id=sa;pwd=sa";

说明：程序代码中data source 表示运行数据库对应的计算机名，initial catalog表示所使用的数据库名。uid为指定的数据库用户名，pwd为指定的用户口令。

**4、Access数据库的连接字符串的形式如下：**

string connectionString =@"provider=Microsoft.Jet.OLEDB.4.0;data source=c:\DataSource\myschool.mdb";

说明：程序代码中，通过专门针对Access数据库的OLE DB提供程序，实现数据库的连接。这使用的的OLE DB提供程序为Microsoft.Jet.OLEDB.4.0，并且数据库存放在c:\DataSource目录下，其数据库文件为myschool.mdb。    

​      string constr = "server=.;database=myschool;integrated security=SSPI";
​      //string constr = "server=.;database=myschool;uid=sa;pwd=sa";
​      //string constr = "data source=.;initial catalog=myschool;user id=sa;pwd=sa";
​      SqlConnection con = new SqlConnection(constr);
​     // con.ConnectionString = constr;
​      string sql = "select count(*) from grade";
​      SqlCommand com = new SqlCommand(sql,con);
​      try
​      {
​        con.Open();
​        MessageBox.Show("成功连接数据库");
​        int x = (int)com.ExecuteScalar();
​        MessageBox.Show(string.Format("成功读取{0},条记录", x));
​      }
​      catch (Exception)
​      {

​        throw;
​      }
​      finally
​      {
​        con.Close();
​        MessageBox.Show("成功关闭数据库连接", "提示信息", MessageBoxButtons.YesNoCancel);
​      }

**5、Web.config 配置**

在ASP.NET 2.0中，使用了一种在运行时解析为连接字符串值的新的声明性表达式语法，按名称引用数据库连接字符串。连接字符串本身存储在 Web.config 文件中的 ＜connectionStrings＞配置节下面，以便易于在单个位置为应用程序中的所有页进行维护。

<?xml version="1.0"?>
<configuration>
<connectionStrings>
<add name="myschool" connectionString="Server=localhost;Integrated Security=True;Database=myschool;Persist Security Info=True" providerName="System.Data.SqlClient" />
</connectionStrings>
<system.web>
<pages styleSheetTheme="Default"/>
</system.web>
</configuration>

我们也可以用下面的方式从配置文件直接读取数据库连接字符串。首先我们需要引用using System.Web.Configuration命名空间，该命名空间包含用于设置 ASP.NET 配置的类。string connectionString =ConfigurationManager.ConnectionStrings["myschool"].ConnectionString;

**首先你应该区分Windows验证与Sql自身的验证的区别。** 
 Windows验证就是SqlServer服务器使用Windows自带的验证系统，如果你指定SqlServer内Windows的一个组有访问的权限，那么加入此组的Windows用户都有访问数据库的权限。此验证有个缺点，就是如果不是在域模式下，无法加入远程计算机的用户，所以如果使用C/S方式写程序的话，使用Windows验证无法使本地计算机的Windows帐户访问远程数据库服务器。 

 Sql验证就简单多了，就是使用sqlserver的企业管理器中自己定义由Sql控制的用户，指定用户权限等。这个帐户信息是由SqlServer自己维护的，所以SqlServer更换计算机后信息不会丢失，不用重新设定。 

 所以如果你的项目使用在一个比较大的网络中，而且对安全要求比较高，那么应该建立域，使用Windows验证，而且要与系统管理员配合详细设定可以访问SqlServer的Windows帐户。如果使用一个小网络，而且此网络仅用来使用项目，对安全没有高要求，那么使用SqlServer验证，而且更新，升级等都方便。 

Windows验证与SQL Server验证的数据库联接字符串是不同的。