C# WinForm中DataGirdView简单分页功能

```
//1.定义变量
　　
int pageSize = 0; //每页显示行数
int nMax = 0; //总记录数
int pageCount = 0; //页数＝总记录数/每页显示行数
int pageCurrent = 0; //当前页号
int nCurrent = 0; //当前记录行
//PS (在bdnInfo中添加上一页, 下一页, lblPageCountText, txtCurrentPage.Text)
//2.在窗体内分别添加
BindingNavigator (bdnInfo), BindingSource (bdsInfo), DataGirdView1 (dgvInfo)
//3.绑定dgvInfo数据
DataSet ds = new DataSet();
ds = userBLL.GetModel();
DaTaTable dt = new DaTaTable ();
dt = ds.Tables[0];
this.dgvInfo.DataSource = dt;
InitDataSet();

//4.初始化dgvInfo
private void InitDataSet()
{
  pageSize = 10; //设置页面行数
  nMax = dt.Rows.Count;
  pageCount = (nMax / pageSize); //计算出总页数
  if ( (nMax % pageSize) > 0)
  {
    pageCount++;
  }
  pageCurrent = 1; //当前页数从1开始
  nCurrent = 0; //当前记录数从0开始
  LoadData();
}

//5.加载数据
private void LoadData()
{
  int nStartPos = 0; //当前页面开始记录行
  int nEndPos = 0; //当前页面结束记录行
  System.Data.DataTable dtTemp = dt.Clone(); //克隆DataTable结构框架
  if (dtTemp != null || dtTemp.Rows.Count > 0)
  {
    if (pageCurrent == pageCount)
    {
      nEndPos = nMax;
    }
    else
    {
      nEndPos = pageSize * pageCurrent;
    }
    nStartPos = nCurrent;
    lblPageCount.Text = pageCount.ToString();//在bdnInfo中添加lable记录总页数
    txtCurrentPage.Text = Convert.ToString (pageCurrent); //在bdnInfo中添加Textbox记录当前页数
    //从元数据源复制记录行
    for (int i = nStartPos; i < nEndPos; i++)
    {
      if (i < dt.Rows.Count)
      {
        dtTemp.ImportRow (dt.Rows[i]);
        nCurrent++;
      }
    }
    bdsInfo.DataSource = dtTemp;
    bdnInfo.BindingSource = bdsInfo;
    dgvInfo.DataSource = bdsInfo;
  }
  else
  {
    return;
  }
}

//6.在bdnInfo的ItemClicked事件中添加代码
private void bdnInfo_ItemClicked (object sender, ToolStripItemClickedEventArgs e)
{
  if (e.ClickedItem.Text == "上一页")
  {
    pageCurrent--;
    if (pageCurrent <= 0)
    {
      MessageBox.Show ("已经是第一页，请点击“下一页”查看！");
      return;
    }
    else
    {
      if (pageCurrent > pageSize)
      {
        pageCurrent = Convert.ToInt32 (lblPageCount.Text);
      }
      else if (pageCurrent > Convert.ToInt32 (lblPageCount.Text) )
      {
        pageCurrent = Convert.ToInt32 (lblPageCount.Text);
      }
      nCurrent = pageSize * (pageCurrent - 1);
    }
    LoadData();
  }
  if (e.ClickedItem.Text == "下一页")
  {
    if (pageCurrent <= 0)
    {
      pageCurrent = 1;
    }
    pageCurrent++;
    if (pageCurrent > pageCount)
    {
      MessageBox.Show ("已经是最后一页，请点击“上一页”查看！");
      return;
    }
    else
    {
      nCurrent = pageSize * (pageCurrent - 1);
      if (nCurrent <= 0)
      {
        return;
      }
    }
    LoadData();
  }
}
```

