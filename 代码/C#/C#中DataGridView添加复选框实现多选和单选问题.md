# C#中DataGridView添加复选框实现多选和单选问题

------

声明：这里 DataGridView的命名为dGV_Data，全选cheakbox 命名为 cBox_All。

![img](https://img-blog.csdnimg.cn/20190818124821801.png)



如图，我们想在显示的数据中，每一条的最前方添加一个复选框来实现数据的随意选择问题，即在每条数据前插入一个checkbox，当然，此时通过界面来实现较为复杂，这里使用代码进行添加。  希望通过一个“全选”复选框将DataGridView中所有的checkboxs都选中

我们首先对DataGridView中的checkbox进行声明/定义，设置他的Name（ ***** 后面用的到）

```cs
            DataGridViewCheckBoxColumn checkColum = new DataGridViewCheckBoxColumn();
            checkColum.HeaderText = " ";
            checkColum.Name = "选择";
            checkColum.DataPropertyName = "Column1";

            checkColum.ReadOnly = false;
            checkColum.TrueValue = true;
            checkColum.FalseValue = false;
```

我们想在每条数据之前都插入checkbox，也就是最前面列的值全为checkbox，只需要将该列的值设置成事先定义好的DataGridViewCheckBoxColumn即可。然后，在该列的后方将查找到的数据连接到DataGridView的数据源文件。当然，这种方式不能循环更新DataGridView，否则会不断的添加checkbox，填加的数量=你更新的数量。

```cs
            SQLConnect SQLConnSele = new SQLConnect();//自定义的数据库连接函数，不用管这个
            MySqlConnection connSele = SQLConnSele.SQLConn();//这里是打开数据库连接
            string sqlSele = "SELECT cardID from person";
            MySqlCommand cmd = new MySqlCommand(sqlSele, connSele);
            MySqlDataAdapter da = new MySqlDataAdapter(cmd);
            DataSet ds = new DataSet();
            da.Fill(ds);//将查询的数据保存到一个数据源中            

            this.dGV_Data.Columns.Add(checkColum);
            this.dGV_Data.DataSource = ds.Tables[0];
```

当然了，这个问题也是有解决方法的。我们只需要将cheakbox的定义和添加，全部移动至 Form1_Load() 函数中即可，不需要再在其他地方重复添加。

------

此时，只能一个复选框一个复选框的进行选择，如果我们想要进行全选或全部取消，在数据较多的情况下，这种方法显然特别繁琐。怎么办呢？

![img](https://img-blog.csdnimg.cn/20190818130815585.png)

当然是加以一个全选按钮了，选中全选按钮则全选，否则取消。笔者本想在红色圆圈位置添加全选复选框的，那样看起来更加畅快，奈何，木的实现。于是用了个笨方法，添加了一个单独的cheakbox进行控制。下面是遍历控制代码。

```cs
            if (this.cBox_All.Checked == true)
            {
                for (int i = 0; i < this.dGV_Data.Rows.Count; i++)
                {
                    this.dGV_Data.Rows[i].Cells["选择"].Value = 1;
                }
            }
            else
            {
                for (int i = 0; i < this.dGV_Data.Rows.Count; i++)
                {
                    this.dGV_Data.Rows[i].Cells["选择"].Value = 0;
                }
            }
```

接下来就是遍历DataGridView中所有的数据是否被选中，然后进行相应的操作。判断是否被选中的条件语句为：（i从0开始遍历DataGridView）

```cs
if (this.dGV_Data.Rows[i].Cells["选择"].Value != null)
```

## 这个条件很重要。

接下来的操作跟DataGridView数据的操作一致，不再赘述。