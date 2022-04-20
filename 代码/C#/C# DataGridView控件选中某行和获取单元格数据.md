## C# DataGridView控件选中某行和获取单元格数据

***\**\*顶\*\**\***

DataGridView的几个基本操作：
1、获得某个（指定的）单元格的值：
dataGridView1.Row[i].Cells[j].Value;
2、获得选中的总行数：
dataGridView1.SelectedRows.Count;
3、获得当前选中行的索引：
dataGridView1.CurrentRow.Index;
4、获得当前选中单元格的值：
dataGridView1.CurrentCell.Value;
5、取选中行的数据
string[] str = new string[dataGridView.Rows.Count];
for(int i;i<dataGridView1.Rows.Count;i++)
{
if(dataGridView1.Rows[i].Selected == true)
{
str[i] = dataGridView1.Rows[i].Cells[1].Value.ToString();
}
}
7、获取选中行的某个数据
int a = dataGridView1.SelectedRows.Index;
dataGridView1.Rows[a].Cells[“你想要的某一列的索引，想要几就写几”].Value;



获得某个（指定的）单元格的值： dataGridView1.Row[i].Cells[j].Value; Row[i] 应该是Rows[i]



int a=dataGridView1.CurrentRow.Index;
string str=dataGridView1.Row[a].Cells[“strName”].Value.Tostring();

selectedRows[0]当前选中的行
.cell[列索引].values 就是当前选中行的某个单元格的值

DataGridView1.SelectedCells(0).Value.ToString 取当前选择单元内容
DataGridView1.Rows(e.RowIndex).Cells(2).Value.ToString 当前选择单元第N列内容