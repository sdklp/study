# C# winforms datagridview 设置右键菜单【完整版】

## ***\*步骤一：编辑右键菜单\****

1.创建窗体文件，拖入DataGridView，拖入一个contextMenuStrip控件

![img](https://img-blog.csdnimg.cn/20190308115311342.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaV9BaXh5,size_16,color_FFFFFF,t_70)

2.编辑contextMenuStrip控件（就是定义你想要右键弹出的菜单，比如我的右键菜单只想弹出“定位”和“拾取边界”，如果你还想增加一些菜单选项，直接在下图的contextMenuStrip控件里的键入处输入菜单选项即可。若是想要为某个菜单选项添加事件，直接双击该菜单选项即可。）

![img](https://img-blog.csdnimg.cn/20190308115434808.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaV9BaXh5,size_16,color_FFFFFF,t_70)

3.绑定DataGridView和你新增的contextMenuStrip1

打开设计页面，选中DataGridView，属性，contextMenuStrip中选择下拉:contextMenuStrip1

![img](https://img-blog.csdnimg.cn/20190308120836591.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaV9BaXh5,size_16,color_FFFFFF,t_70)

 

## **步骤二：右键选中DataGridView的某行时，弹出步骤一创建的右键菜单**。

1.步骤一已经把一个快捷菜单contextMenuStrip1创建完成。要想点击右键弹出菜单，还需要给DataGridView的CellMouseDown事件添加处理程序。

如下图，进入设计页面，选中DataGridView空间，右键查看属性，在属性里面找到CellMouseDown且双击，即可创建DataGridView1_CellMouseDown（）方法，直接在方法中写代码即可。

![img](https://img-blog.csdnimg.cn/20190308122922323.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaV9BaXh5,size_16,color_FFFFFF,t_70)

### 代码如下

```c#
		
private void dataGridView1_CellMouseClick(object sender, DataGridViewCellMouseEventArgs e)
        {
			if (e.Button == MouseButtons.Right)
			{
				if (e.RowIndex >= 0)
				{
					if (dataGridView1.Rows[e.RowIndex].Selected == false)
					{
						dataGridView1.ClearSelection();
						dataGridView1.Rows[e.RowIndex].Selected = true;
					}
				}
				if (dataGridView1.SelectedRows.Count == 1)
				{
					dataGridView1.CurrentCell = dataGridView1.Rows[e.RowIndex].Cells[e.ColumnIndex];}
			contextMenuStrip1.Show(MousePosition.X, MousePosition.Y);
		}
	}
```