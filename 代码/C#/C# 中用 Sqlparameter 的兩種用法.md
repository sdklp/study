## C# 中用 Sqlparameter 的兩種用法

這篇文章主要介紹了C# 中用 Sqlparameter 的幾種用法,文中給大家列舉了兩種用法，非常不錯，具有一定的參考借鑑價值，需要的朋友可以參考下

新建一個表：

```c#
create table abc
(
id int IDENTITY(1,1) NOT NULL,
name nvarchar(100) ,
sex nvarchar(10)
)
insert into abc values(‘asf','男')
insert into abc values(‘ai','女')
```

建立表格完成。

新建一個儲存過程：

```c#
create procedure selbyid
(
@id int,
@thename nvarchar(100) output
)
as
select @thename= name from abc where id=@id
```

在執行的過程中可以用sqlparameter 的幾種格式來呼叫儲存過程：

**第一種是：**

```c#
public string connString = ConfigurationManager.ConnectionStrings["connStr"].ConnectionString;//儲存連結字串，方便資源複用。
public SqlConnection getcon( )
{
SqlConnection conn = new SqlConnection();
conn.ConnectionString = connString;
return conn;
}
private void btnsqlparauseing_Click(object sender, EventArgs e)
{
SqlConnection con = getcon();
con.Open();
string sqlstr = "insert into abc values(@name,@sex)"; //免除sql注入攻擊
SqlCommand cmd = new SqlCommand( );
cmd.Connection = con;
cmd.CommandText = sqlstr;
SqlParameter para = new SqlParameter(); //宣告引數
para= new SqlParameter("@name", SqlDbType.NVarChar,100);//生成一個名字為@Id的引數,必須以@開頭表示是新增的引數，並設定其型別長度，型別長度與資料庫中對應欄位相同，但是不能超出資料庫欄位大小的範圍，否則報錯。
para.Value = txtname.Text.ToString().Trim(); //這個是輸入引數，所以可以賦值。
cmd.Parameters.Add(para);            //引數增加到cmd中。
para = new SqlParameter("@sex", SqlDbType.NVarChar, 10);
para.Value = txtsex.Text.ToString().Trim();
cmd.Parameters.Add(para);
int i =cmd.ExecuteNonQuery(); //執行sql語句，並且返回影響的行數。
MessageBox.Show(i.ToString() + "命令完成行受影響插入成功", "提示",MessageBoxButtons.OK,MessageBoxIcon.Information);
con.Close();
}
```

**2.第二種是呼叫sqlparameter幾種方式來呼叫儲存過程：**

1.

```c#
private void btnshuchu_Click(object sender, EventArgs e)
{
SqlConnection con = getcon();
con.Open();
SqlCommand cmd = new SqlCommand();
cmd.Connection = con;
cmd.CommandText = "selbyid"; //儲存過程的名稱
cmd.CommandType = CommandType.StoredProcedure; //說明是儲存過程
SqlParameter para = new SqlParameter();     //宣告sqlparameter引數
para = new SqlParameter("@id", SqlDbType.Int); //這個引數是輸入引數
para.Value = int.Parse(txtid.Text.ToString().Trim()); //因為是輸入引數所以可以賦值
cmd.Parameters.Add(para); //加入cmd中
para=new SqlParameter("@thename",SqlDbType.NVarChar,100);//引數的大小可以小於資料庫的引數規定值，但不能夠大於資料庫的引數大小。
cmd.Parameters.Add(para); //和下面一句不可掉亂，先增加再指明它是輸出引數來的。
cmd.Parameters["@thename"].Direction = ParameterDirection.Output; //增加後，用output說明是輸出引數。
int i=cmd.ExecuteNonQuery();
string name = cmd.Parameters["@thename"].Value.ToString(); //經過執行，儲存過程返回了輸出引數。
MessageBox.Show("命令完成 " + name + "是所查記錄", "提示", MessageBoxButtons.OK, MessageBoxIcon.Information);
con.Close();
}
```

套路就是： 輸出引數先宣告，再賦值，再加入cmd的引數中，最後用cmd.ExecuteNonQuery()執行。

2.用AddWithValue：

```c#
private void btnothers_Click(object sender, EventArgs e)
{
SqlConnection con = getcon();
SqlCommand cmd = new SqlCommand();
cmd.Connection = con;
cmd.CommandText = "selbyid";
cmd.CommandType = CommandType.StoredProcedure;
SqlParameter para = new SqlParameter();
cmd.Parameters.AddWithValue("@id", Convert.ToInt32(txtid.Text.Trim()));//輸入引數可以用addWithValue來格式化引數，但輸出引數只能用Add
cmd.Parameters.Add("@thename", SqlDbType.NVarChar,100).Direction = ParameterDirection.Output; //和下面一句不可順序掉亂，否則會報錯，先加入cmd中再指明它是輸出引數來的。
con.Open();
int i = cmd.ExecuteNonQuery();
string name = cmd.Parameters["@thename"].Value.ToString(); //輸出引數返回一個數值。
MessageBox.Show("命令完成 " + name + "是所查記錄", "提示", MessageBoxButtons.OK, MessageBoxIcon.Information);
con.Close();
}
```

3.用引數陣列實現呼叫輸入和輸出引數的儲存過程：

```c#
private void btnshuzu_Click(object sender, EventArgs e)
{
SqlConnection con = getcon();
SqlCommand cmd = new SqlCommand();
cmd.Connection = con;
cmd.CommandText = "selbyid";
cmd.CommandType = CommandType.StoredProcedure;
SqlParameter[] para = { new SqlParameter("@id", SqlDbType.Int)};
para[0].Value = Convert.ToInt32(txtid.Text.ToString().Trim());
cmd.Parameters.AddRange(para); //輸入引數和輸出引數分別加入到cmd.Parameter中。
cmd.Parameters.Add("@thename",SqlDbType.NVarChar,100).Direction = ParameterDirection.Output; //和下面一句不可掉亂，先增加再指明它是輸出引數來的。   
con.Open();
int i = cmd.ExecuteNonQuery();
string name = cmd.Parameters["@thename"].Value.ToString();
MessageBox.Show("命令完成 " + name + "是所查記錄", "提示", MessageBoxButtons.OK, MessageBoxIcon.Information);
con.Close();
}
```