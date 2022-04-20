## 数据库

id  //自动编号

AreaCode //文本

PID //数字

## 代码



```c#
using System;
using System.Drawing;
using System.Windows.Forms;
using System.Data;
using System.Data.OleDb;
private void treeEquipForm_Load(object sender, EventArgs e)
        {
			string sql = "select ID,AreaCode,pid from tblLocation";
			DataTable dt = DBHelper.ExecuteDataTable(sql);
			TreeNode treeNode = new TreeNode();
			treeNode.Text = "根节点";
			treeNode.Tag = 0;
			treeView1.Nodes.Add(treeNode);
			LoadData(treeView1.Nodes, 0, dt);
		}
		#region
		/// <summary>
		/// 
		/// </summary>
		/// <param name="treeNodeCollection"></param>
		/// <param name="pid"></param>
		/// <param name="dt"></param>
private void LoadData(TreeNodeCollection treeNodeCollection, int pid, DataTable dt)
		{
foreach (DataRow dr in dt.Select($"pid={pid}"))
		{
			int ID = Convert.ToInt32(dr[0]);
			string area = dr[1].ToString();
			TreeNode node = treeNodeCollection.Add(area);
			node.Tag = ID;
			LoadData(node.Nodes, ID, dt);
		}

	}
	#endregion
```