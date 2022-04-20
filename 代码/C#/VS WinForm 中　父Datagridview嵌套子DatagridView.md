最近项目中应用到　在一个主Datagridview嵌套另一个子DatagridView．
效果如下：
![在这里插入图片描述](../../../我的文档/Typora/Pictures/20201119101654326.png#pic_center)
![在这里插入图片描述](../../../我的文档/Typora/Pictures/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NlZ2NsbGl3Zg==,size_16,color_FFFFFF,t_70#pic_center.png)

实现的思路: 在父 Datagridview 点击的当前行的下方，显示子Datagridview.

```c#
//Step 1
    private void Form_Load(object sender, EventArgs e)
    {
      //dgv : Master grid
      //dataGridView1: Detail grid       
      dgv.Controls.Add(dataGridView1);
      dataGridView1.Visible = false;
    }

​    //Step 2
​    // text the rowheader of char '+' for each row in datagrid event: RowsAdded
​    private void dgv_RowsAdded(object sender, DataGridViewRowsAddedEventArgs e)
​    {
​      dgv.Rows[e.RowIndex].HeaderCell.Style.Alignment = DataGridViewContentAlignment.MiddleLeft;
​      dgv.Rows[e.RowIndex].HeaderCell.Value = "+";
​    }

​    //Step 3
​    // set the position of the detail (sub) datagridview showing
​    private void dgv_RowHeaderMouseClick(object sender, DataGridViewCellMouseEventArgs e)
​    {      
​      dgv.CurrentRow.HeaderCell.Style.Alignment = DataGridViewContentAlignment.MiddleLeft;
​      dgv.CurrentRow.HeaderCell.Value = Convert.ToString(dgv.CurrentRow.HeaderCell.Value).Trim() == "+" ? "-" : "+"; // switch text the rowheader of char '+' or  '-'

​      int scrollRowIndex = dgv.FirstDisplayedScrollingRowIndex; // the first  row in the top of the  datagrid
​      if ((e.RowIndex - scrollRowIndex) > 10) // if the current row is the tenth row in the current page, set it to the top of the datagrid
​      {
​        dgv.FirstDisplayedScrollingRowIndex = e.RowIndex;
​      }
​      
​         //set the position of the detail (sub) datagridview"
​          System.Drawing.Rectangle rect = dgv.GetCellDisplayRectangle(-1, e.RowIndex  , false);
​          dataGridView1.Left = rect.Left;
​          dataGridView1.Top = rect.Bottom ;
​          dataGridView1.Width = dgv.Width ;
​          dataGridView1.Height = dgv.Height;
​          dataGridView1.Visible = Convert.ToString(dgv.CurrentRow.HeaderCell.Value).Trim() == "-" ? true : false; // show or hide the detail grid
​    }
```

