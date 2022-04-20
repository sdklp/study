很多数据都有父节点与子节点，我们希望单击父节点的时候可以展开父节点下的子节点数据。

比如一个医院科室表，有父科室与子科室，点击父科室后，在父科室下面可以展现该科室下的所有子科室。

我们来说一下在DataGridView中如何实现这个功能。

首先，创建示例数据：

**示例数据SQL**

```
create table Department
(
 ID int identity(1,1) not null,
 DName varchar(20) null,
 DparentId int null,
 Dtelphone varchar(20) null,
 Dhospital varchar(50) null
) 
 
insert into Department values('门诊外室',1,'1111','XXX医院')
insert into Department values('门诊内科',1,'2222','XXX医院')
insert into Department values('门诊手术',1,'3333','XXX医院')
insert into Department values('门诊儿科',1,'4444','XXX医院')
insert into Department values('神经内室',2,'5555','XXX医院')
insert into Department values('神经外科',2,'6666','XXX医院')
insert into Department values('住院手术',2,'7777','XXX医院')
insert into Department values('住院康复',2,'8888','XXX医院')
```

其实思路很简单，就是在展开父节点的时候，在父节点下插入新的DataGridViewRow;收缩父节点的时候，在父节点下删除该子节点的DataGridViewRow。

为了简便，代码中的数据读取我都直接硬编码了。

加载父节点数据，除了数据库中的列外我还新加了两列：IsEx与EX。

```
private void DataGridBing(DataTable table)
    {
      if (table.Rows.Count > 0)
      {
        for (int i = 0; i < table.Rows.Count; i++)
        { 
 
          int k = this.dataGridView1.Rows.Add();
          DataGridViewRow row = this.dataGridView1.Rows[k];
          row.Cells["ID"].Value = table.Rows[i]["ID"];
          row.Cells["DName"].Value = table.Rows[i]["DName"];
          row.Cells["Daddress"].Value = table.Rows[i]["Daddress"];
          row.Cells["Dtelphone"].Value = table.Rows[i]["Dtelphone"];
          //用于显示该行是否已经展开
          row.Cells["IsEx"].Value = "false";
          //用于显示展开或收缩符号，为了简单我就直接用字符串了，其实用图片比较美观
          row.Cells["EX"].Value = "+";
        }
      }
    }
```

下面就是Cell的单击事件了，分别在事件中写展开的插入与收缩的删除.

**插入子节点：**

```
string isEx=this.dataGridView1.Rows[e.RowIndex].Cells["IsEx"].Value.ToString();
      if (this.dataGridView1.Columns[e.ColumnIndex].Name == "EX" && isEx=="false")
      {
        string id = this.dataGridView1.Rows[e.RowIndex].Cells["ID"].Value.ToString();
        DataTable table = GetDataTable("select * from Department where DparentId="+id);
        if (table.Rows.Count > 0)
        {
          //插入行
          this.dataGridView1.Rows.Insert(e.RowIndex+1, table.Rows.Count);
          for (int i = 0; i < table.Rows.Count; i++)
          {
            DataGridViewRow row = this.dataGridView1.Rows[e.RowIndex + i+1];
            row.DefaultCellStyle.BackColor = Color.CadetBlue;
            row.Cells["ID"].Value = table.Rows[i]["ID"];
            row.Cells["DName"].Value = table.Rows[i]["DName"];
            row.Cells["Daddress"].Value = table.Rows[i]["Daddress"];
            row.Cells["Dtelphone"].Value = table.Rows[i]["Dtelphone"];
          }
        }
        //将IsEx设置为true，标明该节点已经展开
        this.dataGridView1.Rows[e.RowIndex].Cells["IsEx"].Value = "true";
        this.dataGridView1.Rows[e.RowIndex].Cells["EX"].Value = "-";
```

**删除子节点：**

```
if (this.dataGridView1.Columns[e.ColumnIndex].Name == "EX" && isEx == "true")
      {
        string id = this.dataGridView1.Rows[e.RowIndex].Cells["ID"].Value.ToString();
        DataTable table = GetDataTable("select * from Department where DparentId=" + id);
        if (table.Rows.Count > 0)
        {
          //利用Remove
          for (int i = 0; i < table.Rows.Count; i++)
          {
            foreach (DataGridViewRow row in this.dataGridView1.Rows)
            {
              if (row.Cells["ID"].Value.Equals(table.Rows[i]["ID"]))
              {
                this.dataGridView1.Rows.Remove(row);
              }
            }
          }
        }
        ////将IsEx设置为false，标明该节点已经收缩
        this.dataGridView1.Rows[e.RowIndex].Cells["IsEx"].Value = "false";
        this.dataGridView1.Rows[e.RowIndex].Cells["EX"].Value = "+";
      }
```

这里面通过比较ID来唯一确定一行，循环比较多，因为子节点是紧接着父节点的，我们可以确定子节点所在的行数，所以用RemoveAt()方法更好。



```
//利用RemoveAt
          for (int i = table.Rows.Count; i > 0; i--)
          {
            //删除行
            this.dataGridView1.Rows.RemoveAt(i + e.RowIndex);
          }
```

上面的做法是通过不断的插入与删除来实现，但这样与数据库的交互变得很频繁。更好的做法应该是插入一次，然后通过隐藏或显示行来实现我们的效果。

为此，我们还要在grid中新增两个列：

**IsInsert：用来判断该行是否已经有插入子节点数据**

**RowCount：用来保存该行下插入的子节点数量。**

在方法DataGridBing中，绑定数据时，应该再加一列：

```
//是否插入
row.Cells["IsInsert"].Value = "false";
```

而在增加节点的时候，我们要多做一个判断，如果IsInsert为false就插入数据，如果为true就显示数据

**展开行**

```
if (this.dataGridView1.Columns[e.ColumnIndex].Name == "EX" && isEx=="false")
      {
        if (this.dataGridView1.Rows[e.RowIndex].Cells["IsInsert"].Value.ToString() == "false")
        {
          string id = this.dataGridView1.Rows[e.RowIndex].Cells["ID"].Value.ToString();
          DataTable table = GetDataTable("select * from Department where DparentId=" + id);
          if (table.Rows.Count > 0)
          {
            //插入行
            this.dataGridView1.Rows.Insert(e.RowIndex + 1, table.Rows.Count);
            for (int i = 0; i < table.Rows.Count; i++)
            {
              DataGridViewRow row = this.dataGridView1.Rows[e.RowIndex + i + 1];
              row.DefaultCellStyle.BackColor = Color.CadetBlue;
              row.Cells["ID"].Value = table.Rows[i]["ID"];
              row.Cells["DName"].Value = table.Rows[i]["DName"];
              row.Cells["Daddress"].Value = table.Rows[i]["Daddress"];
              row.Cells["Dtelphone"].Value = table.Rows[i]["Dtelphone"];
            }
            this.dataGridView1.Rows[e.RowIndex].Cells["IsInsert"].Value = "true";
            this.dataGridView1.Rows[e.RowIndex].Cells["RowCount"].Value = table.Rows.Count;
          }
        }
        else
        {
          //显示数据
          int RowCount = Convert.ToInt32(this.dataGridView1.Rows[e.RowIndex].Cells["RowCount"].Value);
          for (int i = 1; i <= RowCount; i++)
          {
            this.dataGridView1.Rows[e.RowIndex + i].Visible = true;
          }
        }
        //将IsEx设置为true，标明该节点已经展开
        this.dataGridView1.Rows[e.RowIndex].Cells["IsEx"].Value = "true";
        this.dataGridView1.Rows[e.RowIndex].Cells["EX"].Value = "-";
      }
```

收缩的时候，我们直接隐藏行就可以了.

**收缩行**

```c#
if (this.dataGridView1.Columns[e.ColumnIndex].Name == "EX" && isEx == "true")
      {
        int RowCount = Convert.ToInt32(this.dataGridView1.Rows[e.RowIndex].Cells["RowCount"].Value);
        for (int i = 1; i <= RowCount; i++)
        {
          //隐藏行
          this.dataGridView1.Rows[e.RowIndex + i].Visible = false;
        }
        ////将IsEx设置为false，标明该节点已经收缩
        this.dataGridView1.Rows[e.RowIndex].Cells["IsEx"].Value = "false";
        this.dataGridView1.Rows[e.RowIndex].Cells["EX"].Value = "+";
      }
```

大家知道DataGridView是如何实现展开收缩的吧，希望大家不仅知道是如何实现的还要动手实验一番，才不枉小编辛苦整理此文章哦