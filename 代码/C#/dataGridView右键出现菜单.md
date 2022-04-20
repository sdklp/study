# C# dataGridView选中一行右键出现菜单，对数据进行操作（datatable的操作）

### **1.为dataGridView绑定数据，设置数据选定一行**

this.dataGridView1.SelectionMode = DataGridViewSelectionMode.FullRowSelect;  //选中整行可在属性中修改

datagridview.AutoGenerateColumns = false;//不让datagridview自动生成列，可在属性中修改

datagridview.AllowUserToAddRows = true;//禁止自动生成行可在属性中修改

 ![img](https://img-blog.csdn.net/20180620220610672?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTY5NDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **2.添加contextMenuStrip控件并绑定dataGridView**

![img](https://img-blog.csdn.net/20180620221051470?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTY5NDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **3.设置选中一行时右键单击出现菜单**

 //dataGridView选中一行时右键出现菜单
    private void dataGridView1_CellMouseUp(object sender, DataGridViewCellMouseEventArgs e)
    {
      if (e.Button == MouseButtons.Right)
      {
        if (e.RowIndex >= 0)
        {
          dataGridView1.ClearSelection();
          dataGridView1.Rows[e.RowIndex].Selected = true;
          dataGridView1.CurrentCell = dataGridView1.Rows[e.RowIndex].Cells[e.ColumnIndex];
          contextMenuStrip1.Show(MousePosition.X, MousePosition.Y);
        }
      }

​    }

![img](https://img-blog.csdn.net/20180620221558287?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTY5NDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **4.获取选中行某一单元格的值** 

![img](https://img-blog.csdn.net/20180620222110845?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTY5NDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```cs
//判断单元格是否为空
if(this.dataGridView1.Rows[i].Cells[i].Value == null)
{
//do something
}
//判断datagridview 有没有选中行
if(dataGridView1.SelectedRows.Count > 0){
      //如果选中执行代码
int aa= DataGridView1.CurrentRow.Index; //获得包含当前单元格的行的索引 
}else{
     MessageBox.Show("请选中后操作");
}
//取消默认选中行，在绑定数据后会默认选中第一行   只需要在绑定后加以下任意一行代码即可
             dataGridView1.ClearSelection();
           //dataGridView1.CurrentCell = null;
           //dataGridView1.Rows[0].Selected = false;
//在事件中为datagridview新增一行 
int index = this.datagridview.Rows.Add();
this.dgvPayPhase.Rows[index].Cells[0].Value =测试;//可直接为某个单元格赋值
//当控件的DataSource绑定数据源后，便不能直接添加新行了（可以删除、修改） 下面这种方式是针对已经对datagridview绑定过数据源后添加一行的方式
       ((DataTable)sourceGridView.DataSource).Rows.Add();
//根据条件设置dataGridView1行的颜色
int intGrade = Convert.ToInt32(this.dataGridView1.Rows[e.RowIndex].Cells["dgvAge"].Value);
                if (intGrade ==30)
                {
                    dataGridView1.Rows[e.RowIndex].DefaultCellStyle.BackColor = Color.Red;
                }
                else if(intGrade==25)
                {
                    dataGridView1.Rows[e.RowIndex].DefaultCellStyle.BackColor = Color.Brown;
                }
            //check多选框 可多条选择  前提是datagridview中有一列的类型是DataGridViewCheckBoxColumn
            StringBuilder sb = new StringBuilder();
            int count = Convert.ToInt32(MaintenanceGV.Rows.Count.ToString());
            for (int i = 0; i < count; i++)
            {
                //如果DataGridView是可编辑的，将数据提交，否则处于编辑状态的行无法取到 
                MaintenanceGV.EndEdit();
                DataGridViewCheckBoxCell checkCell = (DataGridViewCheckBoxCell)MaintenanceGV.Rows[i].Cells["check"];
                Boolean flag = Convert.ToBoolean(checkCell.Value);
                if (flag == true)     //查找被选择的数据行 
                {
                    //从 DATAGRIDVIEW 中获取数据项 
                    string z_zcode = MaintenanceGV.Rows[i].Cells[1].Value.ToString().Trim();
                    sb.AppendLine(z_zcode);
                }
            }
            MessageBox.Show(sb.ToString());
            foreach (DataRow dr in Accdt.Rows)
                    {//对datatable中字段判断是否为空    判断datatable中某列是否有空值，如果有为其赋值
                        if (dr["name"] == DBNull.Value || dr["name"].ToString() == "")
                        {
                            dr["name"] = "空";
                        }
                        if (dr["sex"] == DBNull.Value || dr["sex"].ToString() == "")
                        {
                            dr["sex"] = "空";
                        }                    
                    }
 //获取datatable一列中所有的值  去重     去除一列中值得重复项
        public string[] GetNamesFromDataTable(DataTable dataTable)
        {
            DataView dv = dataTable.DefaultView;
            dataTable = dv.ToTable(true, "jcxm");//jcxm是datatable的列名
            string[] names = new string[dataTable.Rows.Count];
            for (int i = 0; i < names.Length; i++)
            {
                names[i] = dataTable.Rows[i][0].ToString();
            }
            return names;
        }
```

### **5.对数据进行操作**

![img](https://img-blog.csdn.net/20180620222151265?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTY5NDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 6.遍历datagridview

```cs
foreach (DataGridViewRow dgr in dgv.Rows)
            {
                if (Convert.ToString(dgr.Cells[6].Value) != "成功")
                {
                    string aa=  Convert.ToString(dgr.Cells["编号"].Value);
                } 
            }
```

 