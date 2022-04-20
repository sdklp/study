# C#连接MySQL数据库实例

项目目的：
连接mysql查询数据并将数据显示到界面的datagridview里面.

Step1:添加动态链接库文件
Visual Studio,在 项目(右键)-管理NuGet程序包(N) 然后在浏览里面搜索MySql.Data并进行安装。
Step2：using所需要的库

```html
using MySql.Data.MySqlClient;
1.
```

step3：建立连接(MySqlConnection类)

```html
  using MySql.Data.MySqlClient;  
    public MySqlConnection connect()
    {
         String connetStr = "server=127.0.0.1;port=3306;user=root;password=a123456.; database=thzdb;";
        MySqlConnection con = new MySqlConnection(connetStr);

        con.Open();
        Console.WriteLine("数据库连接成功");
        return con;
    }
```

step4：数据查询并显示
　Sql查询语句获取的数据是分格式的，我们还用SqlDataReader来做，然后用IDataReader来接收读取，
.net中的DataGridView类是一个功能全面的显示数据集合的控件;绑定到DataGridView的方式有DataTable,DataSet,实现了IList<T>接口的类等;下面说一下如何简单地将List<T>中的数据绑定到DataGridView中.

```html
//Movie域对象,属性有Name, Category, ReleaseRegon,Director等;
//List<T>的非泛型化类是ArrayList.
IList<Movie> movieList = new List<Movie>();
//......
this.dataGridView.DataSource = movieList;

```

通过这两行,在窗口界面就能看到数据能显示到列表中了,栏标题名称就是Movie中字段的名称;若想定制化具体的栏名可通过DataGridViewRow类或其它方式实现.
以下是代码：

```html
private void mainForm_Load(object sender, EventArgs e)
        {
            //我想查询一个用户表的信息，该用户有姓名，密码，信息三列
            //1.定义一个用户类型的List数组，userInfo类的代码在下方
            List<userInfo> userInfo = new List<userInfo>();
            //2.我们要读取查询语句的数据，并且保存了。这里我们将使用IDataReader语句
            //数据库类的实例，类的代码在下方
            DB db = new DB();

            //解析方法
            using (IDataReader read = db.read("select * from userInfo"))
            {
                while (read.Read())
                {
                    userInfo a = new userInfo();
                    a.user_Name = read[0].ToString();
                    a.user_Passwd = read[1].ToString();
                    a.user_region = read[2].ToString();
                    userInfo.Add(a);
                }
            }
            this.dataGridView1.DataSource = userInfo;//将List的数据绑定到DataGridView中
        }

```

userInfo类的代码：

```html
public class userInfo
    {

        public string user_Name { get; set; }
        public string user_Passwd { get; set; }
        public string user_region { get; set; }
    }

```

DB类的代码：

```html
using System;
using MySql.Data.MySqlClient;

namespace WindowsFormsApp14
{
    public class DB
    {

        //数据库操作
        //1.连接数据库
        public MySqlConnection connect()
        {
             String connetStr = "server=127.0.0.1;port=3306;user=root;password=a123456.; database=thzdb;";
            MySqlConnection con = new MySqlConnection(connetStr);

            con.Open();
            Console.WriteLine("数据库连接成功");
            return con;
        }
        //执行语句的数据库方法
        public MySqlCommand command(string sql)
        {

            MySqlCommand cmd = new MySqlCommand(sql, connect());
            return cmd;

        }
        //行数影响的方法
        public int Execute(string sql)
        {
            return command(sql).ExecuteNonQuery();

        }
        //返回查询结果的方法
        public MySqlDataReader read(string sql)
        {
            return command(sql).ExecuteReader();
        }


    }

}

```

mysql中创建数据：
![C#连接MySQL数据库实例_java](../../../我的文档/Typora/Pictures/watermark,size_14,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=.png)
insert into thzdb.userinfo values(‘zyr1’,‘a123456’,‘陕西商洛’);
![C#连接MySQL数据库实例_java_02](../../../我的文档/Typora/Pictures/watermark,size_14,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=-16392102666291.png)
运行后的结果：
![C#连接MySQL数据库实例_java_03](../../../我的文档/Typora/Pictures/watermark,size_14,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=-16392102666312.png)