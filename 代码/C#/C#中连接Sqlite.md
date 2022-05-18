# C#连接Sqlite

# 1、Slite简介

SQLite，是一款轻型的[数据库](https://cloud.tencent.com/solution/database?from=10680)，是遵守ACID的关联式数据库管理系统，它的设计目标是嵌入式的，而且目前已经在很多嵌入式产品中使用了它，它占用资源非常的低，在嵌入式设备中，可能只需要几百K的内存就够了。它能够支持Windows/Linux/Unix等等主流的操作系统，同时能够跟很多程序语言相结合，比如 Tcl、C#、PHP、Java等，还有ODBC接口，同样比起Mysql、[PostgreSQL](https://cloud.tencent.com/product/postgresql?from=10680)这两款开源世界著名的数据库管理系统来讲，它的处理速度比他们都快。SQLite第一个Alpha版本诞生于2000年5月。  至今已经有13个年头，SQLite也迎来了一个版本 SQLite 3已经发布。

# 2、在C#中连接Sqlite

连接Sqlite首先需要添加System.Data.SQLite.dll和System.Data.SQLite.Linq.dll的引用，这两个dll文件你可以根据你的操作系统版本选择合适的安装版本，安装完成之后的文件路径为C:\Program Files\System.Data.SQLite\2008\bin。

添加了上面所说的两个引用之后，为方便调用，写了一个SqlHelper类：

```javascript
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Data;
using System.Data.SQLite;

namespace ConsoleSqlite
{
    public class DBHelper
    {
        public SQLiteConnection GetCon()
        {
            string strFilePath = @"Data Source=C:\test.db";
            string strCon = strFilePath + ";Pooling=true;FailIfMissing=false";
            SQLiteConnection sqliteCon = new SQLiteConnection(strCon);
            return sqliteCon;
        }
    }

    public class SqlHelper : DBHelper
    {
        private DataTable dt;
        private SQLiteConnection conn;
        private SQLiteCommand cmd;
        private SQLiteDataAdapter sda;
        /// <summary>
        /// 数据库操作类
        /// </summary>
        /// <param name="sql"></param>
        /// <returns></returns>
        public int RunSQL(string sql)
        {
            int count = 0;
            try
            {
                conn = GetCon();
                conn.Open();
                cmd = new SQLiteCommand(sql, conn);
                cmd.ExecuteNonQuery();
                count = count + 1;
            }
            catch (Exception)
            {
                throw;
            }
            finally
            {
                conn.Close();
            }
            return count;
        }
        /// <summary>
        /// 获得datatable
        /// </summary>
        /// <param name="sql"></param>
        /// <returns></returns>
        public DataTable GetDataTable(string sql)
        {
            DataSet ds = null;
            try
            {
                conn = GetCon();
                sda = new SQLiteDataAdapter(sql, conn);//OracleDataAdapter：网络适配器
                ds = new DataSet();
                sda.Fill(ds);//将结果填充到ds中
                dt = ds.Tables[0];
            }
            catch (Exception)
            {

                throw;
            }
            finally
            {
                conn.Close();
            }
            return dt;
        }
        /// <summary>
        /// 返回记录总条数
        /// </summary>
        /// <param name="strTableName"></param>
        /// <returns></returns>
        public int GetCount(string strTableName)
        {
            string strSql = "select count(*) from " + strTableName;
            int count = 0;
            DataTable dtCount = GetDataTable(strSql);
            count = int.Parse(dtCount.Rows[0][0].ToString());
            return count;
        }
    }
}
```

上面的类中，包含了基本的操作，一般人是够用了，为了测试我的类建立的是否正确，我新建了一个控制台程序，代码如下：

```javascript
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Data;

namespace ConsoleSqlite
{
    class Program
    {
        static void Main(string[] args)
        {
            SqlHelper helper = new SqlHelper();
            string strSql = "Select * From county";
            DataTable dt = helper.GetDataTable(strSql);
            Console.WriteLine("Hello");
        }
    }
}
```