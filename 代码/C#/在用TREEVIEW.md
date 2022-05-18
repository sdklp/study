





#region 加载树形结构数据
		public void LoadTree(string sql,TreeNodeCollection tnCn)
		{
			//string sql = "select ID,AreaCode,pid from tblLocation";
			DataTable dt = DBHelper.ExecuteDataTable(sql);
			TreeNode treeNode = new TreeNode();
			treeNode.Text = "全部";
			treeNode.Tag = 0;
			tnCn.Add(treeNode);
			LoadData(tnCn, 0, dt);
		}       
        /// <summary>
        /// TreeView加载数据
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







    private void treeView1_BeforeExpand(object sender, TreeViewCancelEventArgs e)
        {
    		
            showTreeView.make_NodeView(sender, e);
        }