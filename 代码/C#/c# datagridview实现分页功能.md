# c# datagridview实现分页功能

## 前言

datagridview是我们用于显示数据的一个很常用的工具，如果数据量大的话，分页显示是很有必要的，所以在这里分享一下分页显示的功能实现步骤。

### 事先准备

- 四个按钮（Button），三个标签（label）

### 公共部分声明几个变量和容器



```cpp
public int pageSize = 10;      //每页记录数
public int recordCount = 0;    //总记录数
public int pageCount = 0;      //总页数
public int currentPage = 0;    //当前页
DataTable dtSource = new DataTable();
```

### 加载数据的方法



```csharp
///LoadPage方法
/// <summary>
/// loaddpage方法
/// </summary>
private void LoadPage()
{
    if (currentPage < 1) currentPage = 1;
    if (currentPage > pageCount) currentPage = pageCount;
    
    int beginRecord;
    int endRecord;
    DataTable dtTemp;
    dtTemp = dtSource.Clone();

    beginRecord = pageSize * (currentPage - 1);
    if (currentPage == 1) beginRecord = 0;
    endRecord = pageSize * currentPage;

    if (currentPage == pageCount) endRecord = recordCount;
    for (int i = beginRecord; i < endRecord; i++)
    {
        dtTemp.ImportRow(dtSource.Rows[i]);
    }
    tf_dgv1.DataSource = dtTemp;  //datagridview控件名是tf_dgv1
    toolStripLabel1.Text = currentPage.ToString();//当前页
    toolStripLabel4.Text = pageCount.ToString();//总页数
    toolStripLabel6.Text = recordCount.ToString();//总记录数
}
```

### 分页的方法



```csharp
/// <summary>
/// 分页的方法
/// </summary>
/// <param name="str"></param>
private void fenye(string str)    //str是sql语句
{
    SqlDataAdapter sda = new SqlDataAdapter(str, yb_db.yb_ConStr);
    DataSet ds = new DataSet();
    sda.Fill(ds);
    dtSource = ds.Tables[0];
    recordCount = dtSource.Rows.Count;
    pageCount = (recordCount / pageSize);
    if ((recordCount % pageSize) > 0)
    {
        pageCount++;
    }

    //默认第一页
    currentPage = 1;

    LoadPage();//调用加载数据的方法
}
```

### 在事件里调用



```csharp
private void chaxun_Click(object sender, EventArgs e)
{
    if (zyh_textbox.Text.Trim().Equals(string.Empty) || zyh_textbox.Text.Trim().Length <= 4)
    {
        MessageBox.Show("请输入正确的住院号！");
    }
    else
    {
        string str = "select * from test"; //这里是你的查询语句
        fenye(str);//分页
        LoadPage();//加载数据
       

    }
}
```

### 翻页按钮的事件里这样写

- 点击第一页



```undefined
currentPage = 1;
LoadPage();
```

- 点击最后一页



```undefined
currentPage = pageCount;
LoadPage();
```

- 点击上一页



```undefined
currentPage++;
LoadPage();
```

- 点击下一页



```undefined
currentPage --;
LoadPage();
```

## **完**##