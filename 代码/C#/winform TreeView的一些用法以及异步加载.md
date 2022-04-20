对于树型控件的一些方法，以及异步加载。

下面是TreeView的一些用法

```
private void BindTreeView()
{
    treeView1.LabelEdit = false;//不可编辑
    //添加结点
    TreeNode root = new TreeNode();
    root.Text = "根节点";
    //一级
    TreeNode node1 = new TreeNode();
    node1.Text = "1";
    TreeNode node2 = new TreeNode();
    node2.Text = "2";
    //二级
    TreeNode node11 = new TreeNode();
    node11.Text = "11";
    TreeNode node12 = new TreeNode();
    node12.Text = "12";
    TreeNode node21 = new TreeNode();
    node21.Text = "21";
    TreeNode node22 = new TreeNode();
    node22.Text = "22";
    //二级加入一级
    node1.Nodes.Add(node11);
    node1.Nodes.Add(node12);
    node2.Nodes.Add(node21);
    node2.Nodes.Add(node22);
    //一级加入根
    root.Nodes.Add(node1);
    root.Nodes.Add(node2);
    //
    treeView1.Nodes.Add(root);
}
private void treeView1_AfterSelect(object sender, TreeViewEventArgs e)
{
    if (treeView1.SelectedNode != null)
    {
        MessageBox.Show(treeView1.SelectedNode.Text);
    }
}
```

基本用法有了，下面，就把我写的异步加载列表的方法写进去了，因为是参考网上的方法临时 写的，可能会有些不足之处，也有可以改进，优化的一些方法

```
 DataTable dt = data.createDT();
        #region 树的异步加载
        /// <summary>
        /// 载入根节点
        /// </summary>
        public void make_rootView()
        {

            foreach (DataRow row in dt.Select("module_fatherid='M01'"))
            {
                TreeNode tn = new TreeNode();
                string sk=row["module_id"].ToString();
                tn.Text = row["module_name"].ToString();
                treeView1.Nodes.Add(tn);
                DataRow[] row2=(dt.Select("module_fatherid='" + sk + "'"));
                if (row2.Count()!=0)
                {
                    
                    TreeNode tn1 = new TreeNode();
                    tn1.Text = "";
                    tn.Nodes.Add(tn1);
                }
            }
        }
        /// <summary>
        /// 载入子节点
        /// </summary>
        /// <param name="node_id"></param>
        /// <param name="node"></param>
        public void make_view(string node_id,TreeNode node)
        {
            node.Nodes.Clear();
            foreach (DataRow row in dt.Select("module_fatherid='"+node_id+"'"))
            {
                TreeNode tn = new TreeNode();
                string sk = row["module_id"].ToString();
                tn.Text = row["module_name"].ToString();
                node.Nodes.Add(tn);

                if ((dt.Select("module_fatherid='" + sk + "'")).Count() != 0)
                {
                    TreeNode tn1 = new TreeNode();
                    tn1.Text = "";
                    tn.Nodes.Add(tn1);
                }
            }
        }
        /// <summary>
        /// 查找父节点的ID
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        public void make_NodeView(object sender, TreeViewCancelEventArgs e)
        {
            TreeNode tn = e.Node;
            string node_id = "";
            foreach (DataRow row in dt.Select("module_name='" + tn.Text + "'"))
            {
                node_id = row["module_id"].ToString();
            }
            make_view(node_id, tn);
        }
        #endregion

        private void treeView1_BeforeExpand(object sender, TreeViewCancelEventArgs e)
        {
            make_NodeView(sender, e);
            
        }
```

在刚载入时调用 

```
 private void Form1_Load(object sender, EventArgs e)
        {           make_rootView();   }
```

在树型控件的事件treeView1_BeforeExpand调用 

```
private void treeView1_BeforeExpand(object sender, TreeViewCancelEventArgs e)
        { make_NodeView(sender, e);   }
```

下面是我的数据源，我是根据下面这个表格的结构来写代码 ，根据结构的不一样，代码 也应做相应的改变

```
public class data
    {
        public static DataTable createDT()
        {
            DataTable dt = new DataTable();
            dt.Columns.Add("module_id");
            dt.Columns.Add("module_name");
            dt.Columns.Add("module_fatherid");
            dt.Columns.Add("module_url");
            dt.Columns.Add("module_order");

            dt.Rows.Add("C1", "全国", "0", "", "1");
            dt.Rows.Add("M01", "广东", "C1", "", "1");

            dt.Rows.Add("M0101", "深圳", "M01", "", "100");
            dt.Rows.Add("M010101", "南山区", "M0101", "", "1000");
            dt.Rows.Add("M010102", "罗湖区", "M0101", "", "1001");
            dt.Rows.Add("M010103", "福田区", "M0101", "", "1002");
            dt.Rows.Add("M010104", "宝安区", "M0101", "", "1003");
            dt.Rows.Add("M010105", "龙岗区", "M0101", "", "1004");

            dt.Rows.Add("M01010301", "上梅林", "M010103", "", "1002001");
            dt.Rows.Add("M01010302", "下梅林", "M010103", "", "1002002");
            dt.Rows.Add("M01010303", "车公庙", "M010103", "", "1002003");
            dt.Rows.Add("M01010304", "竹子林", "M010103", "", "1002004");
            dt.Rows.Add("M01010305", "八卦岭", "M010103", "", "1002005");
            dt.Rows.Add("M01010306", "华强北", "M010103", "", "1002006");

            dt.Rows.Add("M0102", "广州", "M01", "", "101");
            dt.Rows.Add("M010201", "越秀区", "M0102", "", "1105");
            dt.Rows.Add("M010202", "海珠区", "M0102", "", "1106");
            dt.Rows.Add("M010203", "天河区", "M0102", "", "1107");
            dt.Rows.Add("M010204", "白云区", "M0102", "", "1108");
            dt.Rows.Add("M010205", "黄埔区", "M0102", "", "1109");
            dt.Rows.Add("M010206", "荔湾区", "M0102", "", "1110");
            dt.Rows.Add("M010207", "罗岗区", "M0102", "", "1111");
            dt.Rows.Add("M010208", "南沙区", "M0102", "", "1112");
            return dt;
        }
    }
```

以上表结构是从网上摘录下来的！！！