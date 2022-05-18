# C# WinForm项目三层架构简述

> 基于C#.NET的WinForm项目，我们经常使用基于三层架构，来构建项目框架，这里简单的梳理一下三层架构的相关知识

# 哪三层？

------

> 我们通常所说的三层框架指的是DAL、BIL和UIL三层，分别是数据层、业务逻辑层和界面层，以及与之搭配的实体类和通用类库，下面分别概述

## 实体类- Model

- 我们将数据存放在数据库中，数据表的结构，我们通常会用一个类来抽象，表的属性就是类的属性，我们通常将表的一行存储在一个类中。我们在Java中，通常将其称为实体类Entity，在C#中，我们通常将其称为Model。

- 如图，我们定义了一个数据表，表结构如下

  ![img](https://upload-images.jianshu.io/upload_images/5979706-bb08f28c53a4269c.png?imageMogr2/auto-orient/strip|imageView2/2/w/367/format/webp)

  表结构

- 我们根据表的信息，创建了如下的实体类



```csharp
namespace mtWeightModel
{
    [Serializable] //表示可以序列化
    public class User
    {
        public int Id { get; set; }
        public String username { get; set; }
        public String password { get; set; }
        public int status { get; set; }
    }
}
```

- 我们可以看到，在SQLServer数据库中，char varchar nchar nvarchar都用String类型，int就对应Int。
- Model类库一般来说需要被DAL、BIL和UI引用。

## DAL-数据访问层 - DataAccessLayer

- 数据访问层，就是调用我们数据库访问方法，专注于数据的增删改查操作，构建SQL语句，构建参数等，以下是一个典型DAL方法



```csharp
namespace mtWeightDAL
{
    /// <summary>
    /// 用户访问数据类
    /// </summary>
    public class UserService
    {
        /// <summary>
        /// 根据账号和密码比对用户信息
        /// </summary>
        /// <param name="objUser">包含用户名和密码的用户对象</param>
        /// <returns>返回用户对象信息（若无用户信息则对象为null）</returns>
        public User UserLogin(User objUser) {
            String sql = "SELECT Id,username,password,status FROM Users where username=@username and password=@password";
            SqlParameter[] param = new SqlParameter[] {
                new SqlParameter("@username",objUser.username),
                new SqlParameter("@password", objUser.password)
            };
            SqlDataReader objReader = SqlHelper.getReader(sql, param);
            if (objReader.Read())
            {
                objUser.Id = Convert.ToInt32(objReader["Id"]);
                objUser.status = Convert.ToInt32(objReader["status"]);
            }
            else {
                objUser = null;
            }
            objReader.Close();
            return objUser;
        }
    }
}
```

- 这里用到了一个SqlHelper类，使我们后面要单独说的数据库帮助类
- DAL就是根据业务需求，构建SQL语句，构建参数，调用帮助类，获取结果，DAL层被BIL层调用

## BLL-业务逻辑层 - Business Logic Layer

- BLL层索要负责的，就是处理业务逻辑上的问题，比如在调用数据库访问之前，对数据的处理、判断等。下面是一个最简单的业务逻辑方法，不处理任何信息，只做参数传递。



```csharp
namespace mtWeightBLL
{
    /// <summary>
    /// 用户业务逻辑类
    /// </summary>
    public class UserManager
    {
        //创建数据访问对象
        private UserService objUserService = new UserService();

        public User UserLogin(User objUser) {
            return objUserService.UserLogin(objUser);
        }
    }
}
```

- 那你可能就会有疑问，为什么不把业务逻辑和数据访问合在一起呢，偏要搞出两个层，不是多此一举么。那其实呢，我们分层解决问题的意义就是，功能专一，并且解耦我们的程序，我们在DAL层只关心我的数据库访问操作，我默认你给我的数据是合法的、正确的，那至于你如何保证数据的合法性和正确性就是你需要在BLL层里去做的了。
- BLL层只被UIL层引用

## UIL-用户表现层

- 就是窗体Form
- 这里有一个click事件



```kotlin
private void btnLogin_Click(object sender, EventArgs e)
        {
            //数据验证
            if (this.txtUsername.Text.Trim().Length == 0) {
                MessageBox.Show("请输入用户名", "登录提示");
                this.txtUsername.Focus();
            }
            if (this.txtPassword.Text.Trim().Length == 0)
            {
                MessageBox.Show("请输入密码", "登录提示");
                this.txtPassword.Focus();
            }

            // 封装对象
            User objUser = new User {
                username = this.txtUsername.Text.Trim(),
                password = this.txtPassword.Text.Trim()
            };
            try
            {
                objUser = objUserManager.UserLogin(objUser);
                if (objUser != null)
                {
                    if (objUser.status == 1)
                    {
                        Program.objCurrentUser = objUser;
                        this.DialogResult = DialogResult.OK;
                        this.Close();
                    }
                    else
                    {
                        MessageBox.Show("用户被禁用，请联系管理员", "登录提示");
                    }
                }
                else {
                    MessageBox.Show("用户名或密码错误！", "登录提示");
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("登录异常："+ex.Message,"登录提示");
            }
        }
```

## 通用类库

- 对于一些程序中用到的其他封装的类库，可以统一放在这里，比如一些第三方类库等

# 引用关系

------

![img](https://upload-images.jianshu.io/upload_images/5979706-247b856dd6638159.png?imageMogr2/auto-orient/strip|imageView2/2/w/638/format/webp)

引用关系图

# 数据库帮助类

------

- 我们可以用过封装ADO.NET形成自己的一套方法，但是我们知道在ADO.NET中，SQLServer、Access、Mysql和Oracle使用的是不同的类，那么我们可能需要对每一个数据库封装一套帮助类。其中常用的方法包括非查询类方法（两个重载[sql],[sql，参数]）和查询类方法（两个重载[sql],[sql，参数]），事务类方法、存储过程类方法等。
- 我们通常为会先规定一个接口，然后在帮助类中实现结构。这样后期可以通过反射或工厂的方式来实现不同数据库的切换（后面另说）。
- 下面是我定义的一个简单的数据库帮助类



```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Configuration;
using System.Data;
using System.Data.SqlClient;

namespace DbUtil
{
    public class SqlHelper
    {
        private static String ConnString = ConfigurationManager.ConnectionStrings["ConnString"].ToString();
        #region 格式化SQL语句
        /// <summary>
        /// 增删改非查询类方法
        /// </summary>
        /// <param name="sql">SQL语句</param>
        /// <returns></returns>
        public static int UPDATE(string sql) {
            SqlConnection conn = new SqlConnection(ConnString);
            SqlCommand cmd = new SqlCommand(sql, conn);
            try
            {
                conn.Open();
                return cmd.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                throw new Exception(ex.Message);
            }
            finally {
                conn.Close();
            }
        }
        /// <summary>
        /// 返回单条结果查询类方法
        /// </summary>
        /// <param name="sql">SQL语句</param>
        /// <returns></returns>
        public static object getSingleResult(string sql)
        {
            SqlConnection conn = new SqlConnection(ConnString);
            SqlCommand cmd = new SqlCommand(sql, conn);
            try
            {
                conn.Open();
                return cmd.ExecuteScalar();
            }
            catch (Exception ex)
            {

                throw new Exception(ex.Message);
            }
            finally
            {
                conn.Close();
            }
        }
        /// <summary>
        /// 多条结果查询类方法
        /// </summary>
        /// <param name="sql">SQL语句</param>
        /// <returns></returns>
        public static SqlDataReader getReader(string sql)
        {
            SqlConnection conn = new SqlConnection(ConnString);
            SqlCommand cmd = new SqlCommand(sql, conn);
            try
            {
                conn.Open();
                return cmd.ExecuteReader(CommandBehavior.CloseConnection);
            }
            catch (Exception ex)
            {
                conn.Close();
                throw new Exception(ex.Message);
            }
          
        }
        /// <summary>
        /// 返回DataSet数据集方法
        /// </summary>
        /// <param name="sql">SQL语句</param>
        /// <returns>结果集DataSet</returns>
        public static DataSet getDataSet(string sql)
        {
            SqlConnection conn = new SqlConnection(ConnString);
            SqlCommand cmd = new SqlCommand(sql, conn);
            SqlDataAdapter adapter = new SqlDataAdapter(cmd);
            DataSet ds = new DataSet();
            try
            {
                conn.Open();
                adapter.Fill(ds);
                return ds;
            }
            catch (Exception ex)
            {

                throw new Exception(ex.Message);
            }
            finally {
                conn.Close();
            }

        }
        #endregion

        #region 带参数SQL语句
        /// <summary>
        /// 增删改非查询类方法
        /// </summary>
        /// <param name="sql">SQL语句</param>
        /// <param name="param">SQl参数</param>
        /// <returns></returns>
        public static int UPDATE(string sql,SqlParameter[] param)
        {
            SqlConnection conn = new SqlConnection(ConnString);
            SqlCommand cmd = new SqlCommand(sql, conn);
            try
            {
                conn.Open();
                cmd.Parameters.AddRange(param);
                return cmd.ExecuteNonQuery();
            }
            catch (Exception ex)
            {
                throw new Exception(ex.Message);
            }
            finally
            {
                conn.Close();
            }
        }
        /// <summary>
        /// 返回单条结果查询类方法
        /// </summary>
        /// <param name="sql">SQL语句</param>
        /// <param name="param">SQL参数</param>
        /// <returns></returns>
        public static object getSingleResult(string sql, SqlParameter[] param)
        {
            SqlConnection conn = new SqlConnection(ConnString);
            SqlCommand cmd = new SqlCommand(sql, conn);
            try
            {
                conn.Open();
                cmd.Parameters.AddRange(param);
                return cmd.ExecuteScalar();
            }
            catch (Exception ex)
            {

                throw new Exception(ex.Message);
            }
            finally
            {
                conn.Close();
            }
        }
        /// <summary>
        /// 多条结果查询类方法
        /// </summary>
        /// <param name="sql">SQL语句</param>
        /// <param name="param">SQL参数</param>
        /// <returns></returns>
        public static SqlDataReader getReader(string sql,SqlParameter[] param)
        {
            SqlConnection conn = new SqlConnection(ConnString);
            SqlCommand cmd = new SqlCommand(sql, conn);
            try
            {
                conn.Open();
                cmd.Parameters.AddRange(param);
                return cmd.ExecuteReader(CommandBehavior.CloseConnection);
            }
            catch (Exception ex)
            {
                conn.Close();
                throw new Exception(ex.Message);
            }
        }
        #endregion
    }
}
```

# 项目创建

- 首先创建解决方案的过程中，创建默认项目Windows窗体应用程序UIL

- 然后分别在解决方案中添加类库项目DAL、BLL和CL（通用类库）和Model

- 然后新建一个DBUtil类库，里面放SQLHeader类

- 下面添加引用，从下网往上添加，先给DAL添加Model和DBHelper的引用，再为BLL添加DAL和Model的引用，再为UIL添加BLL和Model引用，然后在为需要使用通用类库的项目添加CL的引用。

- 引用完成后：

  ![img](https://upload-images.jianshu.io/upload_images/5979706-0c793f0f3378129b.png?imageMogr2/auto-orient/strip|imageView2/2/w/340/format/webp)

  ![img](https://upload-images.jianshu.io/upload_images/5979706-d286c81577714baf.png?imageMogr2/auto-orient/strip|imageView2/2/w/336/format/webp)

> 我把这个项目框架上传到这里，大家可以下载下来看一下[http://down.clubunion.cn/mtWeight.zip](https://link.jianshu.com/?t=http://down.clubunion.cn/mtWeight.zip)