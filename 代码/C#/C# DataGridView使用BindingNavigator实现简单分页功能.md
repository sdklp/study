 接上一篇《[DataGridView使用自定义控件实现简单分页功能](https://www.cnblogs.com/atomy/p/11854991.html)》，本篇使用BindingNavigator来实现简单分页功能。其实也只是借用了一个BindingNavigator空壳，

实现原理和代码与上一篇几乎一样，实现方法如下：

　1、新建一个WinForm程序，命名为BindingNavigatorMain，并拖入一个DataGridView控件及一个BindingNavigator控件。在BindingNavigator右下角弹窗中添加

一个Button(转到)，BindingNavigator的样式如下：

![img](https://img2018.cnblogs.com/i-beta/1227623/201911/1227623-20191116154955057-1116654070.png)

  2、BindingNavigatorMain的代码如下：

```
        private int pageSize;   //每页显示记录数
        private int pageIndex;  //页序号
        private int totalCount; //总记录数
        private int pageCount;  //总页数

        public BindingNavigatorMain()
        {
            InitializeComponent();
        }

        private void BindingNavigatorMain_Load(object sender, EventArgs e)
        {
            pageSize = 20;
            pageIndex = 0;
            SetPage();
        }

        //设置页
        private void SetPage()
        {
            //总记录数
            totalCount = 0;
            BindPage(pageSize, pageIndex + 1, out totalCount);

            //总页数
            if (totalCount % pageSize == 0)
                pageCount = totalCount / pageSize;
            else
                pageCount = totalCount / pageSize + 1;

            //当前页及总页数
            txtCurrentPage.Text = (pageIndex + 1).ToString();
            lblTotalPage.Text = "共 " + pageCount.ToString() + " 页";

            //BindingNavigator数据源不进行BindingSource赋值，但恢复控件可用性。
            bindingNavigatorMoveFirstItem.Enabled = true;
            bindingNavigatorMovePreviousItem.Enabled = true;
            txtCurrentPage.Enabled = true;
            lblTotalPage.Enabled = true;
            bindingNavigatorMoveNextItem.Enabled = true;
            bindingNavigatorMoveLastItem.Enabled = true;
        }

        /// <summary>
        /// 绑定页
        /// </summary>
        /// <param name="pageSize">每页显示记录数</param>
        /// <param name="pageIndex">页序号</param>
        /// <param name="totalCount">总记录数</param>
        private void BindPage(int pageSize, int pageIndex, out int totalCount)
        {
            SqlConnection conn = null;
            SqlCommand cmd = null;
            totalCount = 0;

            #region 连接数据库测试
            try
            {
                //数据库连接
                conn = new SqlConnection("server=.;database=DB_TEST;Uid=sa;pwd=********;");
                conn.Open();

                //SqlCommand
                cmd = new SqlCommand();
                cmd.Connection = conn;
                cmd.CommandText = "PageTest";
                cmd.CommandType = CommandType.StoredProcedure;

                SqlParameter[] param =
                {
                    new SqlParameter("@PageSize",SqlDbType.Int),
                    new SqlParameter("@PageIndex",SqlDbType.Int),
                    new SqlParameter("@TotalCount",SqlDbType.Int)
                };
                param[0].Value = pageSize;
                param[1].Value = pageIndex;
                param[2].Direction = ParameterDirection.Output;
                cmd.Parameters.AddRange(param);

                //DataTable
                DataTable dt = new DataTable("MF_MO");
                dt.Columns.Add(new DataColumn("MO_NO", typeof(String)));
                dt.Columns.Add(new DataColumn("MRP_NO", typeof(String)));
                dt.Columns.Add(new DataColumn("QTY", typeof(Decimal)));
                dt.Columns.Add(new DataColumn("BIL_NO", typeof(String)));

                #region 方法一：SqlDataReader
                SqlDataReader dr = cmd.ExecuteReader();
                dt.Load(dr, LoadOption.PreserveChanges);
                dr.Close();
                totalCount = (int)param[2].Value;
                dataGridView1.DataSource = dt;
                #endregion

                #region #方法二：SqlDataAdapter
                //SqlDataAdapter da = new SqlDataAdapter();
                //da.SelectCommand = cmd;
                //dt.BeginLoadData();
                //da.Fill(dt);
                //dt.EndLoadData();
                //totalCount = (int)param[2].Value;
                //dataGridView1.DataSource = dt;
                #endregion
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "提示", MessageBoxButtons.OK, MessageBoxIcon.Information);
            }
            finally
            {
                conn.Close();
                cmd.Dispose();
            }
            #endregion
        }

        /// <summary>
        /// 首页
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void bindingNavigatorMoveFirstItem_Click(object sender, EventArgs e)
        {
            pageIndex = 0;
            SetPage();
        }

        /// <summary>
        /// 上一页
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void bindingNavigatorMovePreviousItem_Click(object sender, EventArgs e)
        {
            pageIndex--;
            if (pageIndex < 0)
            {
                pageIndex = 0;
            }
            SetPage();
        }

        /// <summary>
        /// 下一页
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void bindingNavigatorMoveNextItem_Click(object sender, EventArgs e)
        {
            pageIndex++;
            if (pageIndex > pageCount - 1)
            {
                pageIndex = pageCount - 1;
            }
            SetPage();
        }

        /// <summary>
        /// 末页
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void bindingNavigatorMoveLastItem_Click(object sender, EventArgs e)
        {
            pageIndex = pageCount - 1;
            SetPage();
        }

        /// <summary>
        /// 只能按0-9、Delete、Enter、Backspace键
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void txtCurrentPage_KeyPress(object sender, KeyPressEventArgs e)
        {
            if ((e.KeyChar >= 48 && e.KeyChar <= 57) || e.KeyChar == 8 || e.KeyChar == 13 || e.KeyChar == 127)
            {
                e.Handled = false;
                if (e.KeyChar == 13)
                {
                    Go();
                }
            }
            else
            {
                e.Handled = true;
            }
        }

        /// <summary>
        /// 指定页
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void btnGo_Click(object sender, EventArgs e)
        {
            Go();
        }

        private void Go()
        {
            if (string.IsNullOrEmpty(txtCurrentPage.Text))
            {
                MessageBox.Show("指定页不能为空。", "提示", MessageBoxButtons.OK, MessageBoxIcon.Information);
                txtCurrentPage.Focus();
                return;
            }

            if (int.Parse(txtCurrentPage.Text) > pageCount)
            {
                MessageBox.Show("指定页已超过总页数。", "提示", MessageBoxButtons.OK, MessageBoxIcon.Information);
                txtCurrentPage.Focus();
                return;
            }

            pageIndex = int.Parse(txtCurrentPage.Text) - 1;
            SetPage();
        }
```

  3、SQL Server创建存储过程PageTest：

```
CREATE PROCEDURE [dbo].[PageTest]
    @PageSize INT,
    @PageIndex INT,
    @TotalCount INT OUTPUT
AS
BEGIN
    --总记录数
    SELECT @TotalCount=COUNT(1) FROM MF_MO
    
    --记录返回(使用动态SQL绕开参数嗅探问题，效率大幅度提升。)
    DECLARE @SQL NVARCHAR(1000)
    SET @SQL=
        'SELECT TOP ('+CONVERT(VARCHAR(32),@PageSize)+') MO_NO,MRP_NO,QTY,BIL_NO '+
        'FROM MF_MO A '+
        'WHERE NOT EXISTS (SELECT 1 FROM (SELECT TOP ('+CONVERT(VARCHAR(32),(@PageIndex-1)*@PageSize)+') MO_NO FROM MF_MO ORDER BY MO_NO) B WHERE A.MO_NO=B.MO_NO) '+
        'ORDER BY MO_NO'
    EXEC (@SQL)
END
```

  4、执行程序：