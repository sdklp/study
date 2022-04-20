很多应用要用到TreeView来显示组织机构,以下演示TreeView如何与数据库进行绑定。

数据库结构如下（递归现实）：

id(guid)                                                                    pid(guid)                                                                   name

18a83618-8751-47ef-91a0-e2dcde42bb71                                                                                          **公司

c775c004-48ed-4664-8b0c-8fd26fea5f4e        18a83618-8751-47ef-91a0-e2dcde42bb71           A部门

a43696f0-a906-4b4a-8abf-a01fb7d54daf        c775c004-48ed-4664-8b0c-8fd26fea5f4e              A部门->1班组

0d7fb83a-c056-482e-800b-52e20c74791b      c775c004-48ed-4664-8b0c-8fd26fea5f4e              A部门->2班组

de28685a-aaff-4876-abe1-bb003d17db64      18a83618-8751-47ef-91a0-e2dcde42bb71            B部门

绑定到TreeView的最终效果如下图：

![img](http://zsrimg.ikafan.com/file_images/article/201507/2015731153519668.png?2015631153535)

**1、新建一个TreeView控件**

![img](http://zsrimg.ikafan.com/file_images/article/201507/2015731153606742.png?2015631153616)

**二、绑定**

**2.1 传统做法（行不通）**

![img](http://zsrimg.ikafan.com/file_images/article/201507/2015731153642663.png?2015631153718)

**2.2 正确做法：自己建立一个递归的方法。**

**2.2.1 递归其实就是方法的重复调用**，下面是我总结的两种方法，对于初学者来说不明白其中的代码也没关系，直接拷贝下来用就可以了。

```c#
#region 绑定TreeView
    /// <summary>
    /// 绑定TreeView（利用TreeNode）
    /// </summary>
    /// <param name="p_Node">TreeNode（TreeView的一个节点）</param>
    /// <param name="pid_val">父id的值</param>
    /// <param name="id">数据库 id 字段名</param>
    /// <param name="pid">数据库 父id 字段名</param>
    /// <param name="text">数据库 文本 字段值</param>
    protected void Bind_Tv(DataTable dt,TreeNode p_Node, string pid_val, string id, string pid, string text)
    {
      DataView dv = new DataView(dt);//将DataTable存到DataView中，以便于筛选数据
      TreeNode tn;//建立TreeView的节点（TreeNode），以便将取出的数据添加到节点中
      //以下为三元运算符，如果父id为空，则为构建“父id字段 is null”的查询条件，否则构建“父id字段=父id字段值”的查询条件
      string filter = string.IsNullOrEmpty(pid_val) ? pid+" is null" :  string.Format(pid+"='{0}'", pid_val);
      dv.RowFilter = filter;//利用DataView将数据进行筛选，选出相同 父id值 的数据
      foreach (DataRowView row in dv)
      {
        tn = new TreeNode();//建立一个新节点（学名叫：一个实例）
        if (p_Node==null)//如果为根节点
        {
          tn.Value = row[id].ToString();//节点的Value值，一般为数据库的id值
          tn.Text = row[text].ToString();//节点的Text，节点的文本显示
          TreeView1.Nodes.Add(tn);//将该节点加入到TreeView中
          Bind_Tv(dt,tn, tn.Value, id, pid, text);//递归（反复调用这个方法，直到把数据取完为止）
        }
        else//如果不是根节点
        {
          tn.Value = row[id].ToString();//节点Value值
          tn.Text = row[text].ToString();//节点Text值
          p_Node.ChildNodes.Add(tn);//该节点加入到上级节点中
          Bind_Tv(dt,tn, tn.Value, id, pid, text);//递归
        }
      }
    }
 
    /// <summary>
    /// 绑定TreeView（利用TreeNodeCollection）
    /// </summary>
    /// <param name="tnc">TreeNodeCollection（TreeView的节点集合）</param>
    /// <param name="pid_val">父id的值</param>
    /// <param name="id">数据库 id 字段名</param>
    /// <param name="pid">数据库 父id 字段名</param>
    /// <param name="text">数据库 文本 字段值</param>
    private void Bind_Tv(DataTable dt,TreeNodeCollection tnc, string pid_val, string id, string pid, string text)
    {
      DataView dv = new DataView(dt);//将DataTable存到DataView中，以便于筛选数据
      TreeNode tn;//建立TreeView的节点（TreeNode），以便将取出的数据添加到节点中
      //以下为三元运算符，如果父id为空，则为构建“父id字段 is null”的查询条件，否则构建“父id字段=父id字段值”的查询条件
      string filter = string.IsNullOrEmpty(pid_val) ? pid + " is null" : string.Format(pid + "='{0}'", pid_val);
      dv.RowFilter = filter;//利用DataView将数据进行筛选，选出相同 父id值 的数据
      foreach (DataRowView drv in dv)
      {
        tn = new TreeNode();//建立一个新节点（学名叫：一个实例）
        tn.Value = drv[id].ToString();//节点的Value值，一般为数据库的id值
        tn.Text = drv[text].ToString();//节点的Text，节点的文本显示
        tnc.Add(tn);//将该节点加入到TreeNodeCollection（节点集合）中
        Bind_Tv(dt, tn.ChildNodes, tn.Value, id, pid, text);//递归（反复调用这个方法，直到把数据取完为止）
      }
    }
    #endregion
```

**2.2.2 调用**

![img](http://zsrimg.ikafan.com/file_images/article/201507/2015731153826416.png?2015631153838)

**2.2.3 关于数据**

—》测试数据（作用：模拟一个真实的数据库表），这个方法的作用是建立一个表，表中有三个字段，分别为id、父id、名称，然后往里面插入些数据。

```c#
 private DataTable Test_Table()
    {
      DataTable dt = new DataTable();
      DataRow dr;
      dt.Columns.Add(new DataColumn("id", typeof(Guid)));//id列 类型guid
      dt.Columns.Add(new DataColumn("parent_id", typeof(Guid)));//父id列 类型guid
      dt.Columns.Add(new DataColumn("name", typeof(string)));//名称列 类型string
      //构造 公司 根节点
      dr = dt.NewRow();
      var node0=dr[0] = Guid.NewGuid();
      dr[1] = DBNull.Value;
      dr[2] = "** 公司";
      dt.Rows.Add(dr);
      //构造 部门 节点
      string[] department = { "A部门", "B部门", "C部门"};
      for (int i = 0; i < department.Length; i++) {
        dr = dt.NewRow();
        var node1=dr[0] = Guid.NewGuid();
        dr[1] = node0;//（部门节点）属于公司根节点
        dr[2] = department[i];
        dt.Rows.Add(dr);
        //构造 班组 节点
        for (int j = 1; j < 4; j++)
        {
          dr = dt.NewRow();
          dr[0] = Guid.NewGuid();
          dr[1] = node1;
          dr[2] = j+"班组";
          dt.Rows.Add(dr);
        }
      }
      return dt;
    }
```

—》真实数据 我们要在数据库中建立三个字段 分别为id、父id、名称，然后向里面插入数据，需要注意的就是第一行数据的父id要设置为空。

然后调用如下方法把数据库中的数据取出。

```c#
/// <summary>
    /// 取出数据库中数据，生成DataTable
    /// </summary>
    /// <param name="str_Con">数据库连接</param>
    /// <param name="str_Cmd">sql语句</param>
    /// <returns></returns>
    private DataTable exe_Table(string str_Con,string str_Cmd)
    {
      DataSet ds = new DataSet();
      using (OracleConnection conn = new OracleConnection(str_Con))
      {
        using (OracleDataAdapter oda = new OracleDataAdapter(str_Cmd, conn))
        {
          conn.Open();
          oda.Fill(ds);
        }
      }
      return ds.Tables[0];
    }
```

最后在 !IsPostBack中调用

```c#
if (!IsPostBack)
      {
        Bind_Tv(exe_Table("连接字符串", "select * from 表"), TreeView1.Nodes, null, "id字段", "父id字段", "名称字段");
      }
```

以上就是本文的全部内容，希望对大家熟练掌握TreeView绑定数据库有所帮助。