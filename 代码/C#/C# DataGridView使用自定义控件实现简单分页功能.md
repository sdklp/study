本例子使用自定义控件方法实现，数据库使用的是SQL Server，实现过程如下：

  1、新建一个自定义控件，命名为：PageControl。

  ![img](https://img2018.cnblogs.com/i-beta/1227623/201911/1227623-20191115183243837-286423411.png)

  2、PageControl代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
    public partial class PageControl : UserControl
    {
        //委托及事件
        public delegate void BindPage(int pageSize, int pageIndex, out int totalCount);
        public event BindPage BindPageEvent;

        //属性
        public int PageSize { get; set; } = 1;  //每页显示记录数
        public int PageIndex { get; set; }      //页序号
        public int TotalCount { get; set; }     //总记录数

        public int PageCount { get; set; }      //总页数

        public PageControl()
        {
            InitializeComponent();

            //取消下划线
            linkFirst.LinkBehavior = LinkBehavior.NeverUnderline;
            linkPrev.LinkBehavior = LinkBehavior.NeverUnderline;
            linkNext.LinkBehavior = LinkBehavior.NeverUnderline;
            linkLast.LinkBehavior = LinkBehavior.NeverUnderline;
            linkGo.LinkBehavior = LinkBehavior.NeverUnderline;
        }

        /// <summary>
        /// 设置页
        /// </summary>
        public void SetPage()
        {
            //总记录数
            int totalCount = 0;
            BindPageEvent(PageSize, PageIndex + 1, out totalCount);
            TotalCount = totalCount;

            //总页数
            if (TotalCount % PageSize == 0)
                PageCount = TotalCount / PageSize;
            else
                PageCount = TotalCount / PageSize + 1;

            //当前页及总页数
            txtCurrentPage.Text = (PageIndex + 1).ToString();
            lblTotalPage.Text = "共 " + PageCount.ToString() + " 页";
        }

        /// <summary>
        /// 首页
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkFirst_LinkClicked(object sender, LinkLabelLinkClickedEventArgs e)
        {
            if (e.Button == MouseButtons.Left)
            {
                PageIndex = 0;
                SetPage();
            }
        }

        /// <summary>
        /// 上一页
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkPrve_LinkClicked(object sender, LinkLabelLinkClickedEventArgs e)
        {
            if (e.Button == MouseButtons.Left)
            {
                PageIndex--;
                if (PageIndex < 0)
                {
                    PageIndex = 0;
                }
                SetPage();
            }
        }

        /// <summary>
        /// 下一页
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkNext_LinkClicked(object sender, LinkLabelLinkClickedEventArgs e)
        {
            if (e.Button == MouseButtons.Left)
            {
                PageIndex++;
                if (PageIndex > PageCount - 1)
                {
                    PageIndex = PageCount - 1;
                }
                SetPage();
            }
        }

        /// <summary>
        /// 末页
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkLast_LinkClicked(object sender, LinkLabelLinkClickedEventArgs e)
        {
            if (e.Button == MouseButtons.Left)
            {
                PageIndex = PageCount - 1;
                SetPage();
            }
        }

        /// <summary>
        /// 只能按0-9、Delete、Enter、Backspace键
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void txtSetPage_KeyPress(object sender, KeyPressEventArgs e)
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
        private void linkGo_LinkClicked(object sender, LinkLabelLinkClickedEventArgs e)
        {
            if (e.Button == MouseButtons.Left)
            {
                Go();
            }
        }

        private void Go()
        {
            if (string.IsNullOrEmpty(txtCurrentPage.Text))
            {
                MessageBox.Show("指定页不能为空。", "提示", MessageBoxButtons.OK, MessageBoxIcon.Information);
                txtCurrentPage.Focus();
                return;
            }

            if (int.Parse(txtCurrentPage.Text) > PageCount)
            {
                MessageBox.Show("指定页已超过总页数。", "提示", MessageBoxButtons.OK, MessageBoxIcon.Information);
                txtCurrentPage.Focus();
                return;
            }

            PageIndex = int.Parse(txtCurrentPage.Text) - 1;
            SetPage();
        }

        /// <summary>
        /// linkFirst鼠标移过颜色
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkFirst_MouseMove(object sender, MouseEventArgs e)
        {
            linkFirst.LinkColor = Color.Red;
        }

        /// <summary>
        /// linkFirst鼠标离开颜色
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkFirst_MouseLeave(object sender, EventArgs e)
        {
            linkFirst.LinkColor = Color.Black;
        }

        /// <summary>
        /// linkPrev鼠标移过颜色
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkPrev_MouseMove(object sender, MouseEventArgs e)
        {
            linkPrev.LinkColor = Color.Red;
        }

        /// <summary>
        /// linkPrev鼠标离开颜色
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkPrev_MouseLeave(object sender, EventArgs e)
        {
            linkPrev.LinkColor = Color.Black;
        }

        /// <summary>
        /// linkNext鼠标移过颜色
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkNext_MouseMove(object sender, MouseEventArgs e)
        {
            linkNext.LinkColor = Color.Red;
        }

        /// <summary>
        /// linkNext鼠标离开颜色
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkNext_MouseLeave(object sender, EventArgs e)
        {
            linkNext.LinkColor = Color.Black;
        }

        /// <summary>
        /// linkLast鼠标移过颜色
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkLast_MouseMove(object sender, MouseEventArgs e)
        {
            linkLast.LinkColor = Color.Red;
        }

        /// <summary>
        /// linkLast鼠标离开颜色
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkLast_MouseLeave(object sender, EventArgs e)
        {
            linkLast.LinkColor = Color.Black;
        }

        /// <summary>
        /// linkGo鼠标移过颜色
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkGo_MouseMove(object sender, MouseEventArgs e)
        {
            linkGo.LinkColor = Color.Red;
        }

        /// <summary>
        /// linkGo鼠标离开颜色
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void linkGo_MouseLeave(object sender, EventArgs e)
        {
            linkGo.LinkColor = Color.Black;
        }
    }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

  3、SQL Server创建存储过程PageTest：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

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

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

  4、新建一个WinForm程序，命名为Main，并拖入一个DataGridView控件及上面新建的PageControl控件，代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
        private void Main_Load(object sender, EventArgs e)
        {
            pageControl1.PageSize = 20;
            pageControl1.PageIndex = 0;
            pageControl1.BindPageEvent += BindPage;
            pageControl1.SetPage();
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
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

  5、执行程序：

![img](https://img2018.cnblogs.com/i-beta/1227623/201911/1227623-20191115184045035-1403992134.png)