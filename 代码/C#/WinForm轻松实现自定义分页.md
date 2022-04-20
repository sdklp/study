## WinForm轻松实现自定义分页

以前都是做web开发，最近接触了下WinForm，发现WinForm分页控件好像都没有，网上搜索了一下，发现有很多网友写的分页控件，分页效果应该都能实现吧，只是其风格都不是很符合我想要的。做web的时候，我习惯了Extjs的Grid分页效果，所以也想在WinForm中做个类似的效果，所以咬咬牙，做个山寨版本的吧，虽然自己写费时费力，在项目进度考虑中不是很可取，但是还是特别想山寨一回，做自己喜欢的风格。

按照惯例，还是先看看实现效果图吧（有图有真像，才好继续下文呀）

![img](https://images0.cnblogs.com/i/388072/201407/231529050254386.x-png)

应用效果：（效果有点难看，因为我是刚装的

xp系统，还是经典主题，如果换成Win7系统或其他主题，效果还是会很不错的）

 ![img](https://images0.cnblogs.com/i/388072/201407/231529481975697.x-png)

我们要做的就是上图显示的一个自定义控件，这个效果参考自我做

web开发使用的Extjs之Grid的分页效果（如下图）

 ![img](https://images0.cnblogs.com/i/388072/201407/231530516356548.x-png)

Extjs的动画效果我们暂时就不实现了，这里只做个外观看起来想像即可，完全一样就脱离“山寨”概念了，总要比人家差点吧，谁让咱是模仿呢！

言归正传，我们现在就看看具体怎么实现吧：

 

**第一步：先布局**

  注：我们创建的是用户自定义控件，而不是WinForm窗体

![img](https://images0.cnblogs.com/i/388072/201407/231531408073827.x-png)

就是先做出个显示效果，这个布局很简单，在这就不多说，重点就是“首页、前一页、后一页、末页”图标，每个图标分两种，一是能点击的高亮效果，一个是灰色不不能点击。以下是套图：（大家如果不喜欢，可以去做成自己喜欢的风格图片）

![img](https://images0.cnblogs.com/i/388072/201407/231532417607338.x-png)

**第二步：编写分页代码**

  布局好了，那么第二步我们就要代码实现正确显示文字信息，分页事件，每页条数选择事件，公开属性和事件。以下是完整代码： 



[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
  1 /// <summary>
  2 /// 声明委托
  3 /// </summary>
  4 /// <param name="e"></param>
  5 public delegate void EventPagingHandler (EventArgs e);
  6 public partial class Paging : UserControl
  7 {
  8     public Paging()
  9     {
 10         InitializeComponent();
 11     }
 12     public event EventPagingHandler EventPaging;
 13     #region 公开属性
 14     private int _pageSize = 50;
 15     /// <summary>
 16     /// 每页显示记录数(默认50)
 17     /// </summary>
 18     public int PageSize
 19     {
 20         get
 21         {
 22             return _pageSize;
 23         }
 24         set
 25         {
 26             if (value > 0)
 27             {
 28                 _pageSize = value;
 29             }
 30             else
 31             {
 32                 _pageSize = 50;
 33             }
 34             this.comboPageSize.Text = _pageSize.ToString();
 35         }
 36     }
 37     private int _currentPage = 1;
 38     /// <summary>
 39     /// 当前页
 40     /// </summary>
 41     public int CurrentPage
 42     {
 43         get
 44         {
 45             return _currentPage;
 46         }
 47         set
 48         {
 49             if (value > 0)
 50             {
 51                 _currentPage = value;
 52             }
 53             else
 54             {
 55                 _currentPage = 1;
 56             }
 57 
 58         }
 59     }
 60     private int _totalCount = 0;
 61     /// <summary>
 62     /// 总记录数
 63     /// </summary>
 64     public int TotalCount
 65     {
 66         get
 67         {
 68             return _totalCount;
 69         }
 70         set
 71         {
 72             if (value >= 0)
 73             {
 74                 _totalCount = value;
 75             }
 76             else
 77             {
 78                 _totalCount = 0;
 79             }
 80             this.lblTotalCount.Text = this._totalCount.ToString();
 81             CalculatePageCount();
 82             this.lblRecordRegion.Text = GetRecordRegion();
 83         }
 84     }
 85 
 86     private int _pageCount = 0;
 87     /// <summary>
 88     /// 页数
 89     /// </summary>
 90     public int PageCount
 91     {
 92         get
 93         {
 94             return _pageCount;
 95         }
 96         set
 97         {
 98             if (value >= 0)
 99             {
100                 _pageCount = value;
101             }
102             else
103             {
104                 _pageCount = 0;
105             }
106             this.lblPageCount.Text = _pageCount + "";
107         }
108     }
109     #endregion
110 
111     /// <summary>
112     /// 计算页数
113     /// </summary>
114     private void CalculatePageCount()
115     {
116         if (this.TotalCount > 0)
117         {
118             this.PageCount = Convert.ToInt32 (Math.Ceiling (Convert.ToDouble (this.TotalCount) / Convert.ToDouble (this.PageSize) ) );
119         }
120         else
121         {
122             this.PageCount = 0;
123         }
124     }
125 
126     /// <summary>
127     /// 获取显示记录区间（格式如：1-50）
128     /// </summary>
129     /// <returns></returns>
130     private string GetRecordRegion()
131     {
132         if (this.PageCount == 1) //只有一页
133         {
134             return "1-" + this.TotalCount.ToString();
135         }
136         else  //有多页
137         {
138             if (this.CurrentPage == 1) //当前显示为第一页
139             {
140                 return "1-" + this.PageSize;
141             }
142             else if (this.CurrentPage == this.PageCount) //当前显示为最后一页
143             {
144                 return ( (this.CurrentPage - 1) * this.PageSize + 1) + "-" + this.TotalCount;
145             }
146             else //中间页
147             {
148                 return ( (this.CurrentPage - 1) * this.PageSize + 1)   + "-" + this.CurrentPage  * this.PageSize;
149             }
150         }
151     }
152 
153     /// <summary>
154     /// 数据绑定
155     /// </summary>
156     public void Bind()
157     {
158         if (this.EventPaging != null)
159         {
160             this.EventPaging (new EventArgs() );
161         }
162         if (this.CurrentPage > this.PageCount)
163         {
164             this.CurrentPage = this.PageCount;
165         }
166         this.txtBoxCurPage.Text = this.CurrentPage + "";
167         this.lblTotalCount.Text = this.TotalCount + "";
168         this.lblPageCount.Text = this.PageCount + "";
169         this.lblRecordRegion.Text = GetRecordRegion();
170         if (this.CurrentPage == 1)
171         {
172             this.btnFirst.Enabled = false;
173             this.btnPrev.Enabled = false;
174             this.btnFirst.Image = global::CHVM.Properties.Resources.page_first_disabled;
175             this.btnPrev.Image = global::CHVM.Properties.Resources.page_prev_disabled;
176         }
177         else
178         {
179             this.btnFirst.Enabled = true;
180             this.btnPrev.Enabled = true;
181             this.btnFirst.Image = global::CHVM.Properties.Resources.page_first;
182             this.btnPrev.Image = global::CHVM.Properties.Resources.page_prev;
183         }
184         if (this.CurrentPage == this.PageCount)
185         {
186             this.btnNext.Enabled = false;
187             this.btnLast.Enabled = false;
188             this.btnNext.Image = global::CHVM.Properties.Resources.page_next_disabled;
189             this.btnLast.Image = global::CHVM.Properties.Resources.page_last_disabled;
190         }
191         else
192         {
193             this.btnNext.Enabled = true;
194             this.btnLast.Enabled = true;
195             this.btnNext.Image = global::CHVM.Properties.Resources.page_next;
196             this.btnLast.Image = global::CHVM.Properties.Resources.page_last;
197         }
198         if (this.TotalCount == 0)
199         {
200             this.btnFirst.Enabled = false;
201             this.btnPrev.Enabled = false;
202             this.btnNext.Enabled = false;
203             this.btnLast.Enabled = false;
204             this.btnFirst.Image = global::CHVM.Properties.Resources.page_first_disabled;
205             this.btnPrev.Image = global::CHVM.Properties.Resources.page_prev_disabled;
206             this.btnNext.Image = global::CHVM.Properties.Resources.page_next_disabled;
207             this.btnLast.Image = global::CHVM.Properties.Resources.page_last_disabled;
208         }
209     }
210 
211     private void btnFirst_Click (object sender, EventArgs e)
212     {
213         this.CurrentPage = 1;
214         this.Bind();
215     }
216 
217     private void btnPrev_Click (object sender, EventArgs e)
218     {
219         this.CurrentPage -= 1;
220         this.Bind();
221     }
222 
223     private void btnNext_Click (object sender, EventArgs e)
224     {
225         this.CurrentPage += 1;
226         this.Bind();
227     }
228 
229     private void btnLast_Click (object sender, EventArgs e)
230     {
231         this.CurrentPage = this.PageCount;
232         this.Bind();
233     }
234 
235     /// <summary>
236     ///  改变每页条数
237     /// </summary>
238     /// <param name="sender"></param>
239     /// <param name="e"></param>
240     private void comboPageSize_SelectedIndexChanged (object sender, EventArgs e)
241     {
242         this.PageSize = Convert.ToInt32 (comboPageSize.Text);
243         this.Bind();
244     }
245 }
246 
247 这里重点提两点：一是图片切换：
248 this.btnFirst.Image = global::CHVM.Properties.Resources.page_first_disabled;
249 Image对象是在Properties.Resource.resx中自动生成的，代码如下：
250 internal static System.Drawing.Bitmap page_first
251 {
252     get {
253         object obj = ResourceManager.GetObject ("page-first", resourceCulture);
254         return ( (System.Drawing.Bitmap) (obj) );
255     }
256 }
257 
258 internal static System.Drawing.Bitmap page_first_disabled
259 {
260     get {
261         object obj = ResourceManager.GetObject ("page_first_disabled", resourceCulture);
262         return ( (System.Drawing.Bitmap) (obj) );
263     }
264 }
265 二是应用了委托事件：我们在这定义了一个分页事件
266 public event EventPagingHandler EventPaging;
267 在数据绑定方法中实现它：
268 /// <summary>
269 /// 数据绑定
270 /// </summary>
271 public void Bind()
272 {
273     if (this.EventPaging != null)
274     {
275         this.EventPaging (new EventArgs() );
276     }
277     //… 以下省略
278 }
279 这里需要大家对C#的委托和事件有一定的了解，不清楚的可以直接使用，或者先去查阅相关参考资料，这里我们就不谈委托机制了。
280 
281 第三步：应用
282 值得一提的是，WinForm并不能直接把用户自定控件往Windows窗体中拖拽，而自动生成实例（ASP.NET是可以直接拖拽的）。那么如果我们需要在应用中使用，只能自己修改Desginer.cs代码了。
283 先声明：
284 private CHVM.PagingControl.Paging paging1;
285 然后在InitializeComponent() 方法中实例化：
286 this.paging1 = new CHVM.PagingControl.Paging();
287 //
288 // paging1
289 //
290 this.paging1.CurrentPage = 1;
291 this.paging1.Location = new System.Drawing.Point (3, 347);
292 this.paging1.Name = "paging1";
293 this.paging1.PageCount = 0;
294 this.paging1.PageSize = 50;
295 this.paging1.Size = new System.Drawing.Size (512, 30);
296 this.paging1.TabIndex = 8;
297 this.paging1.TotalCount = 0;
298 //在这里注册事件
299 this.paging1.EventPaging += new CHVM.PagingControl.EventPagingHandler (this.paging1_EventPaging);
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

 

加完后就能看到效果了，相当于托了一个分页控件的效果：（如下图所示）

![img](https://images0.cnblogs.com/i/388072/201407/231534061354061.x-png)

最后在事件中加入分页事件需要执行的代码：

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

![img](https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

![复制代码](https://common.cnblogs.com/images/copycode.gif)

```
  1  /// <summary>
  2 
  3         /// 分页事件
  4 
  5         /// </summary>
  6 
  7         /// <param name="e"></param>
  8 
  9         private void paging1_EventPaging(EventArgs e)
 10 
 11         {
 12 
 13             GvDataBind();   //DataGridView数据绑定
 14 
 15         }
 16 
 17         /// <summary>
 18 
 19         /// 查询
 20 
 21         /// </summary>
 22 
 23         /// <param name="sender"></param>
 24 
 25         /// <param name="e"></param>
 26 
 27         private void btnQuery_Click(object sender, EventArgs e)
 28 
 29         {
 30 
 31             paging1_EventPaging(e);
 32 
 33         }
 34 
 35         /// <summary>
 36 
 37         /// gvOperateLogList 数据邦定
 38 
 39         /// </summary>
 40 
 41         private void GvDataBind()
 42 
 43         {
 44 
 45             PagingCondition paging = new PagingCondition()
 46 
 47             {
 48 
 49                 startIndex=paging1.CurrentPage,
 50 
 51                 pageSize = paging1.PageSize
 52 
 53             };
 54 
 55             MultiCondition condition = new MultiCondition();
 56 
 57             condition.DateSign="FOperateTime";
 58 
 59             condition.BeginDate = dtBegin.Value;
 60 
 61             condition.EndDate = dtEnd.Value;
 62 
 63             if (comboOperator.Text != "")
 64 
 65             {
 66 
 67                 condition.Dict.Add("FOperator", comboOperator.Text);
 68 
 69             }
 70 
 71             if (comboType.Text != "")
 72 
 73             {
 74 
 75                 condition.Dict.Add("FType", comboType.Text);
 76 
 77             }
 78 
 79             if (comboObject.Text != "")
 80 
 81             {
 82 
 83                 condition.Dict.Add("FOptObject", comboObject.Text);
 84 
 85             }
 86 
 87             if (txtBoxContent.Text != "")
 88 
 89             {
 90 
 91                 condition.Dict.Add("FContent", txtBoxContent.Text);
 92 
 93             }
 94 
 95             DataTable dt = GetByCondition(paging, condition);
 96 
 97             paging1.TotalCount = Convert.ToInt32(dt.TableName);
 98 
 99             gvOperateLogList.DataSource = dt;
100 
101             gvOperateLogList.Columns.Clear();
102 
103             var dict = GetGvColumnsDict();
104 
105             DataGridViewHelp.DisplayColList(gvOperateLogList, dict);
106 
107         }
```

![复制代码](https://common.cnblogs.com/images/copycode.gif)

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

注：MultiCondition、PagingCondition是我专门针对分页综合查询定义的两个类，兴趣的话可以去了解一下：

查询条件就统一定义在MultiCondition中（详见：http://xuzhihong1987.blog.163.com/blog/static/267315872011294150763 ），

PagingCondition是分页条件（详见： http://xuzhihong1987.blog.163.com/blog/static/2673158720112941950801 ）,

Extjs+LINQ轻松实现高级综合查询：

http://xuzhihong1987.blog.163.com/blog/static/2673158720112943356111/

其他：

 

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

 

到此实现就全部完成了，运行效果后就是前面所示的效果！也可以动态修改每页条数。

![img](https://images0.cnblogs.com/i/388072/201407/231536351198880.x-png)

说在最后，改功能简单是简单，但是涉及到很多知识点，委托、事件、

DataGridView数据动态绑定，综合查询，我这里用的是Oracle数据库，如果用LINQ语法的话查询数据会比较方便，写起代码也会显得很优雅。

/// <summary>
/// 获取条件查询数据
/// </summary>
/// <param name="paging"></param>
/// <param name="conditon"></param>
/// <returns></returns>
private DataTable GetByCondition (PagingCondition paging, MultiCondition conditon)
{
  string strSql = "select * from TOperateLog ";
  string strSqlGetCount = "select count(1) from TOperateLog ";
  string strWhere = " where 1=1 ";
  if (conditon != null)
  {
    if (conditon.DateSign == "FOperateTime") //操作日期
    {
      if (conditon.BeginDate != DateTime.MinValue)
      {
        strWhere += string.Format (" and FOperateTime>='{0}'", conditon.BeginDate.ToString ("yyyy-MM-dd HH:mm:ss") );
      }
      if (conditon.EndDate != DateTime.MaxValue)
      {
        strWhere += string.Format (" and FOperateTime<='{0}'", conditon.EndDate.AddDays (1).ToString ("yyyy-MM-dd HH:mm:ss") );
      }
    }
    var dict = conditon.Dict;
    if (dict != null)
    {
      foreach (var key in dict.Keys)
      {
        if (key.Equals ("FType") ) //操作类型
        {
          strWhere += string.Format (" and FType='{0}'", dict[key]);
        }
        if (key.Equals ("FOperator") ) //操作人员
        {
          strWhere += string.Format (" and FOperator='{0}'", dict[key]);
        }
        else if (key.Equals ("FOptObject") )            //操作对象
        {
          strWhere += string.Format (" and FOptObject='{0}'", dict[key]);
        }
        else if (key.Equals ("FContent") ) //操作内容
        {
          strWhere += string.Format (" and FContent like '%{0}%'", dict[key]);
        }
      }
    }
  }
  strWhere += " order by FOperateTime ";
  strSql += strWhere;
  strSqlGetCount += strWhere;
  if (paging != null)
  {
    if (paging.needPaging)
      //  strSql = string.Format ("select * from ( {0} ) where ROWNUM>={1} and ROWNUM<={2}", strSql, paging.startIndex, paging.startIndex + paging.pageSize - 1);
    strSql = string.Format ("select * from (select T.*,RowNum RN from ({0})T where ROWNUM <={1}) where RN>={2} ", strSql, paging.startIndex + paging.pageSize - 1, paging.startIndex);
  }
}
DataTable dt = DataCon.Query (strSql).Tables[0];
dt.TableName = DataCon.GetSingle (strSqlGetCount) + "";
return dt;
}

==========================================================================

C#开发WinForm分页控件 

 

闲暇之余，自己动手做了个分页控件，真是受益良多

 

**[WinFormPager.dll控件下载地址](http://www.everbox.com/f/MfISH5ME61QklBUBUJWWjzhykJ) [WinFormPager源代码下载地址](http://www.everbox.com/f/TqXCCnDkFoEhnC2VmjAMmskZfL)**

 

以下是调用分页控件**WinFormPager**方法**：**

 

//第一步：指定返回的记录数

 

winFormPager1.RecordCount = 返回记录数;

 

//第二步：在控件的PageChanged事件中执行绑定DataGridView的方法

 

private void winFormPager1_PageChanged()
 {
   dataGridView1.DataSource = GetList(winFormPager1.PageSize,winFormPager1.CurrentPage);//GetList为获取数据库记录方法,不介绍
 }

 

//注：完成以上步骤就可成功调用，其余PageSize属性等可在属性浏览器中设置

 

以下是分页控件**WinformPager**完整代码：

 

using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Drawing;
using System.Data;
using System.Text;
using System.Windows.Forms;
using DevComponents.DotNetBar;

 

namespace WinFormPager{    public partial class WinFormPager : UserControl    {        int currentPage = 1;//当前页         /// <summary>        /// 当前页         /// </summary>        [Description("当前页"), Category("分页设置")]        public int CurrentPage        {            get { return currentPage; }            set { currentPage = value; }        }        int pageSize = 10;//每页显示条数        /// <summary>        /// 每页显示条数        /// </summary>        [Description("每页显示条数"), Category("分页设置")]         public int PageSize        {            get { return pageSize; }            set { pageSize = value; }        }        int pageTotal = 0;//总共多少页         /// <summary>        /// 总共多少页         /// </summary>        [Description("总共多少页"), Category("分页设置")]         public int PageTotal        {            get { return pageTotal; }            set { pageTotal = value; }        }        int currentGroup = 1;//当前组        /// <summary>        /// 当前组        /// </summary>        [Description("当前组"), Category("分页设置")]         public int CurrentGroup        {            get { return currentGroup; }            set { currentGroup = value; }        }        int groupSize = 10;//每组显示页数        /// <summary>        /// 每组显示页数        /// </summary>        [Description("每组显示页数"), Category("分页设置")]         public int GroupSize        {            get { return groupSize; }            set { groupSize = value; }        }        int groupTotal = 0;//总共多少组        /// <summary>        /// 总共多少组        /// </summary>        [Description("总共多少组"), Category("分页设置")]          public int GroupTotal        {            get { return groupTotal; }            set { groupTotal = value; }        }        /// <summary>        /// 总的记录数        /// </summary>        private int recordCount;//总的记录数        [Description("总的记录数"), Category("分页设置")]         public int RecordCount        {            get { return recordCount; }            set             {                recordCount = value;                InitData();// 初始化数据                PageChanged();//当前页改变事件            }        }        private int buttonWidth = 20;//按钮宽度        /// <summary>        /// 按钮宽度        /// </summary>        [Description("按钮宽度"), Category("分页设置")]         public int ButtonWidth        {            get { return buttonWidth; }            set { buttonWidth = value; }        }        private int buttonHeight = 23;//按钮高度        /// <summary>        /// 按钮高度        /// </summary>        [Description("按钮高度"), Category("分页设置")]         public int ButtonHeight        {            get { return buttonHeight; }            set { buttonHeight = value; }        }        private int buttonDistance = 0;//按钮间距离        /// <summary>        /// 按钮间距离        /// </summary>        [Description("按钮间距离"), Category("分页设置")]         public int ButtonDistance        {            get { return buttonDistance; }            set { buttonDistance = value; }        }        List<Control> listControl = new List<Control>();//分页的按钮集合

 

​    public delegate void PageChangeDelegate();
​    /// <summary>
​    /// 当前页改变时发生的事件
​    /// </summary>
​    [Description("当前页改变时发生的事件"), Category("分页设置")]
​    public event PageChangeDelegate PageChanged;
​    public WinFormPager()
​    {
​      InitializeComponent();
​      PageChanged = SetBtnPrePageAndBtnNextPage;
​      PageChanged();
​    }
​    /// <summary>
​    /// 初始化数据
​    /// </summary>
​    private void InitData() 
​    {
​      PageTotal = RecordCount / PageSize;//总共多少页      
​      if (RecordCount % PageSize != 0)
​      {
​        PageTotal++;
​      }
​      GroupTotal = PageTotal / GroupSize;//总共多少组
​      if (PageTotal % GroupSize != 0)
​      {
​        GroupTotal++;
​      }
​      for (int i = 0; i < PageTotal; i++)
​      {
​        cmbPage.Items.Add((i + 1).ToString());//添加下拉框值
​      }
​      BuildPageControl();//创建分页数字按钮    
​    }
​    /// <summary>
​    /// 创建分页数字按钮
​    /// </summary>
​    private void BuildPageControl()
​    {
​      int x = 0;//按钮横坐标
​      int y = 0;//按钮纵坐标
​      int num = 0;//按钮数
​      for (int i = GroupSize * (CurrentGroup - 1); i < GroupSize * CurrentGroup; i++)
​      {
​        if (i + 1 > PageTotal)
​        {
​          break;
​        }
​        num++;
​      }
​      int xBtnPreGroup = x + (ButtonWidth + ButtonDistance) * num;//btnPerGroup横坐标
​      //指定上一组 下一组 上一页 下一页 坐标 
​      btnPreGroup.Location = new Point(xBtnPreGroup, y);
​      btnNextGroup.Location = new Point(btnPreGroup.Location.X + btnPreGroup.Width + ButtonDistance, y);
​      btnPrePage.Location = new Point(btnNextGroup.Location.X + btnNextGroup.Width + ButtonDistance, y);
​      btnNextPage.Location = new Point(btnPrePage.Location.X + btnPrePage.Width + ButtonDistance, y);
​      cmbPage.Location = new Point(btnNextPage.Location.X + btnNextPage.Width + ButtonDistance, y+(ButtonHeight-cmbPage.Height)/2);
​      btnGo.Location = new Point(cmbPage.Location.X + cmbPage.Width + ButtonDistance, y);
​      //设置整个控件的宽度、高度
​      this.Width = btnGo.Location.X + btnGo.Width;
​      this.Height = ButtonHeight;
​      btnPreGroup.Height = ButtonHeight;
​      btnNextGroup.Height = ButtonHeight;
​      btnPrePage.Height = ButtonHeight;
​      btnNextPage.Height = ButtonHeight;
​      cmbPage.Height = ButtonHeight;
​      btnGo.Height = ButtonHeight;
​      //循环遍历移除控件
​      foreach (Control c in listControl)
​      {
​        this.Controls.Remove(c);
​      }
​      listControl = new List<Control>();
​      ButtonX button = null;
​      //循环创建控件
​      for (int i = GroupSize * (currentGroup - 1); i < GroupSize * CurrentGroup; i++)
​      {
​        if (i + 1 > PageTotal)
​        {
​          break;
​        }
​        button = new ButtonX();
​        button.Text = (i + 1).ToString();
​        button.Width = ButtonWidth;
​        button.Height = ButtonHeight;
​        button.ColorTable = DevComponents.DotNetBar.eButtonColor.OrangeWithBackground;
​        button.Location = new Point(x, y);
​        button.Click += new EventHandler(button_Click);
​        this.Controls.Add(button);
​        button.BringToFront();
​        listControl.Add(button);//添加进分页按钮的集合
​        x += ButtonWidth + ButtonDistance;
​      }
​      //上一组是否可用
​      if (CurrentGroup == 1)
​      {
​        btnPreGroup.Enabled = false;
​      }
​      else
​      {
​        btnPreGroup.Enabled = true;
​      }
​      //下一组是否可用
​      if (CurrentGroup == GroupTotal)
​      {
​        btnNextGroup.Enabled = false;
​      }
​      else
​      {
​        btnNextGroup.Enabled = true;
​      }
​    }
​    /// <summary>
​    /// 数字按钮分页
​    /// </summary>
​    private void button_Click(object sender, EventArgs e)
​    {
​      CurrentPage = int.Parse((sender as ButtonX).Text);
​      PageChanged();
​    }
​    /// <summary>
​    /// 设置上一页、下一页是否可用以及当前页按钮字体颜色
​    /// </summary>
​    private void SetBtnPrePageAndBtnNextPage() 
​    {
​      //上一页是否可用
​      if (CurrentPage == 1)
​      {
​        btnPrePage.Enabled = false;
​      }
​      else
​      {
​        btnPrePage.Enabled = true;
​      }
​      //下一页是否可用
​      if (CurrentPage == PageTotal)
​      {
​        btnNextPage.Enabled = false;
​      }
​      else
​      {
​        btnNextPage.Enabled = true;
​      }
​      //设置数字分页按钮文本颜色
​      foreach (Control c in this.Controls)
​      {
​        //当前页字体为红色
​        if (c.Text == CurrentPage.ToString() && c is ButtonX)
​        {
​          c.ForeColor = Color.Red;
​        }
​        else
​        {
​          c.ForeColor = Color.Blue;
​        }
​      }
​    }
​    /// <summary>
​    /// 上一组
​    /// </summary>
​    private void btnPreGroup_Click(object sender, EventArgs e)
​    {
​      CurrentGroup--;
​      BuildPageControl();
​      CurrentPage = GroupSize * (CurrentGroup - 1) + 1; ;
​      PageChanged();
​    }
​    /// <summary>
​    /// 下一组
​    /// </summary>
​    private void btnNextGroup_Click(object sender, EventArgs e)
​    {
​      CurrentGroup++;
​      BuildPageControl();
​      CurrentPage = GroupSize * (CurrentGroup - 1) + 1; ;
​      PageChanged();
​    }
​    /// <summary>
​    /// 上一页
​    /// </summary>
​    private void btnPrePage_Click(object sender, EventArgs e)
​    {
​      //如果是当前组的第一页，直接上一组
​      if (CurrentPage == GroupSize * (CurrentGroup - 1) + 1)
​      {
​        CurrentGroup--;
​        BuildPageControl();
​        CurrentPage --; ;
​        PageChanged();
​        return;
​      }
​      CurrentPage--;
​      PageChanged();
​    }
​    /// <summary>
​    /// 下一页
​    /// </summary>
​    private void btnNextPage_Click(object sender, EventArgs e)
​    {
​      //如果是当前组的最后一页，直接下一组
​      if (CurrentPage == GroupSize * (CurrentGroup - 1) + GroupSize)
​      {
​        btnNextGroup_Click(null, null);
​        return;
​      }
​      CurrentPage++;
​      PageChanged();
​    }
​    /// <summary>
​    /// 转到第几页
​    /// </summary>
​    private void btnGo_Click(object sender, EventArgs e)
​    {
​      try
​      {
​        CurrentPage = int.Parse(cmbPage.Text);
​        PageChanged();
​      }
​      catch
​      {
​        MessageBox.Show("请输入数字");
​      }
​    }
  }
}

===============================================================================

http://liyaguang20111105.blog.163.com/blog/static/19929420220146283255809/ 

在winform的设计中，要实现对DataGridView控件的分页功能，需要两个控件：BindingSource、BindingNavigator，根据需求可对BindingNavigator进行自由的扩展，下图的示例则是根据一般需求对分页功能的实现。红色区域是对BindingNavigator控件扩展后的效果。

 

![winform中DataGridView实现分页功能 - 李亚光 - 李亚光 廊坊师范学院九期信息技术提高班](http://img0.ph.126.net/51phm9FizlPaXHm6AvCxbA==/1361494462367502108.png)

 

具体实现过程 ：

//窗体构造方法中定义分页所需变量：

int pageSize = 0;   //每页显示行数

int nMax = 0;     //总记录数

int pageCount = 0;  //页数＝总记录数/每页显示行数

int pageCurrent = 0;  //当前页号

int nCurrent = 0;   //当前记录行

DataTable dtInfo = new DataTable(); //存取查询数据结果

 

//分页功能实现

public void InitDataSet()

{

  //判断每页显示记录数是否为空，在初始话窗体时为真

  if (txtRecordNumOfPage.Text.Trim() == "")

  {

​    try

​    {

​      //pageSize = Convert.ToInt16(ConfigurationManager.AppSettings["PageSize"]);   //设置页面行数

 

​      //读取配置文件中设置的每页显示条数

​      string szConfigFileName = Application.ExecutablePath + ".config";

​      XmlDocument doc = new XmlDocument();

​      doc.Load(szConfigFileName);

​      XmlNode root = doc.SelectSingleNode("configuration");

​      XmlNode node = root.SelectSingleNode("appSettings/add[@key='PageSize']");

​      XmlElement el = node as XmlElement;

​      pageSize = Convert.ToUInt16(el.GetAttribute("value"));

​    }

​    catch

​    {

​    }

​    if (pageSize == 0)

​    {

​      pageSize = 20;    //如果读取配置文件失败，则默认将每页显示条数设置为20

​    }

​    txtRecordNumOfPage.Text = pageSize.ToString();  //界面显示的“每页记录数”赋值

  }

  else

  {

​    //读取界面设置的每页显示条数

​    pageSize = Convert.ToUInt16(txtRecordNumOfPage.Text.Trim());

  }

​    //总记录数赋值

​    nMax = dtInfo.Rows.Count;

​    pageCount = (nMax / pageSize);  //采用整除计算页数

​    //判断整除后是否有余数，有则对页数进行+1

​    if ((nMax % pageSize) > 0) pageCount++;

​    pageCurrent = 1;  //当前页数从1开始

​    nCurrent = 0;    //当前记录数从0开始

​    //调用显示数据方法

​    LoadData();

}

 

//显示数据方法

private void LoadData()

{

  int nStartPos = 0;  //当前页面开始记录行

  int nEndPos = 0;   //当前页面结束记录行

  //判断查询结果是否为空

  if (dtInfo.Rows.Count == 0)

  {

​    dgvExperInfo.DataSource = null;

​    return;

  }

  else

  {

​    DataTable dtTemp = dtInfo.Clone();  //克隆DataTable结构，即将字段名称进行复制

 

​    if (pageCurrent == 1)

​    {

​      bindingNavigatorMoveFirstPage.Enabled = false;

​      bindingNavigatorMovePreviousPage.Enabled = false;

​    }

​    else

​    {

​      bindingNavigatorMoveFirstPage.Enabled = true;

​      bindingNavigatorMovePreviousPage.Enabled = true;

​    }

 

​    if (pageCurrent == pageCount)

​    {

​      nEndPos = nMax;

​      bindingNavigatorMoveLastPage.Enabled = false;

​      bindingNavigatorMoveNextPage.Enabled = false;

​    }

​    else

​    {

​      bindingNavigatorMoveLastPage.Enabled = true;

​      bindingNavigatorMoveNextPage.Enabled = true;

​      nEndPos = pageSize * pageCurrent;

​    }

 

​    nStartPos = nCurrent;

 

​    lblPageCount.Text = pageCount.ToString();       //界面显示总页数

​    lblCurrentPage.Text = Convert.ToString(pageCurrent);//当前页数

​    txtCurrentPage.Text = Convert.ToString(pageCurrent);//跳转到页数的显示

 

​    //从元数据源复制记录行

​    for (int i = nStartPos; i < nEndPos; i++)

​    {

​      dtTemp.ImportRow(dtInfo.Rows[i]);

​      nCurrent++;

​    }

​    bdsInfo.DataSource = dtTemp;

​    bdnInfo.BindingSource = bdsInfo;

​    dgvExperInfo.DataSource = bdsInfo;

​    dgvExperInfo.ClearSelection();

  }

}

 

//BindingNavigator控件上的项目点击事件，通过配置各个Item的Text值进行判断执行

private void bdnInfo_ItemClicked(object sender, ToolStripItemClickedEventArgs e)

{

  if (e.ClickedItem.Text == "上一页")

  {

​    pageCurrent--;

​    if (pageCurrent <= 0)

​    {

​      MessageBox.Show("已经是第一页，请点击“下一页”查看！");

​      pageCurrent++;

​      return;

​    }

​    else

​    {

​      nCurrent = pageSize * (pageCurrent - 1);

​    }

​    LoadData();

   }

  if (e.ClickedItem.Text == "下一页")

  {

​    pageCurrent++;

​    if (pageCurrent > pageCount)

​    {

​      MessageBox.Show("已经是最后一页，请点击“上一页”查看！");

​      pageCurrent--;

​      return;

​     }

​    else

​    {

​      nCurrent=pageSize*(pageCurrent-1);

​    }

​    LoadData();

  }

  if (e.ClickedItem.Text == "首页")

  {

​    pageCurrent = 1;

​    nCurrent = 0;

​    LoadData();

  }

  if (e.ClickedItem.Text == "尾页")

  {

​    pageCurrent = pageCount;

​    nCurrent = pageSize * (pageCurrent - 1);

​    LoadData();

  }

}

//跳转页实现

private void btnPage_Click(object sender, EventArgs e)

{

  if (txtCurrentPage.Text.Trim() != "")

  {

​    pageCurrent = Convert.ToInt16(txtCurrentPage.Text.Trim());

​    //若输入页号大于最大显示页号，则跳转至最大页

​    if (pageCurrent > pageCount)

​    {

​      pageCurrent = pageCount;

​      nCurrent = pageSize * (pageCurrent - 1);

​    }

​    //若输入页号小于1，则跳转至第一页

​    else if (pageCurrent < 1)

​    {

​      pageCurrent = 1;

​      nCurrent = 0;

​      LoadData();

​    }

​    //跳转至输入页号

​    else

​    {

​      nCurrent = pageSize * (pageCurrent - 1);      //当前行数定位

​    }

​    //调用加载数据方法

​    LoadData();

  }

}

//当前页输入字符限制

private void txtCurrentPage_TextChanged(object sender, EventArgs e)

{

  bool IsNum = true;

  foreach (char c in txtCurrentPage.Text.Trim())

  {

​    if (!char.IsNumber(c)) { IsNum = false; break; }

  }

  if (IsNum == false)

  {

​    txtCurrentPage.Text = pageCurrent.ToString();

  }

}

//当前页回车事件调用跳转页的操作

private void txtCurrentPage_KeyPress(object sender, KeyPressEventArgs e)

{

  if (e.KeyChar == 13)

  {

​    btnPage_Click(sender,e);

  }

}

 

//每页显示记录数变更事件

private void txtRecordNumOfPage_TextChanged(object sender, EventArgs e)

{

  bool IsNum = true;

  //输入字符限制

  foreach (char c in txtRecordNumOfPage.Text.Trim())

  {

​    if (!char.IsNumber(c)) { IsNum = false; break; }

  }

  if (IsNum == false)

  {

​    txtRecordNumOfPage.Text = pageSize.ToString();

  }

  //判断输入的每页显示条数是否为空或是否为0，输入长度是否大于4位等情况

  if (txtRecordNumOfPage.Text.Trim() == "" || Convert.ToUInt32(txtRecordNumOfPage.Text.Trim()) == 0 || txtRecordNumOfPage.Text.Trim().Length > 4)

  {

​    txtRecordNumOfPage.Text = pageSize.ToString();

  }

  //规避了特殊情况后直接调用显示数据方法

  LoadDocInfoToDGV();

}

​    至此，winform中对DataGridView控件的分页功能已经实现，该方法是在网上查了相关资料后参照一些常用的分页效果进行了一些扩展，也在组长的要求下，完善了一些细节上的工作：由于屏幕分辨率的不同，用户可自行对每页显示条数进行设置，设置结果可以在关闭窗体的事件中保存至配置文件，下次进行默认读取；对分页控件中按钮操作的优化，判断是否能够使用的功能，代码中已经进行了体现；另外由于显示器分辨率不尽相同，需要在窗体加载时，设置控件显示的位置，也同样需要代码进行控制。

​    需要注意的一点是该分页效果达到的是真分页的功能，要想实现假分页的效果还要进行一些改动。可见实现分页的效果还是要考虑很多方面的，但真正实现后的效果还是很实用的。

 

====================================================================

**C# WinForm中DataGirdView简单分页功能**

```c#
//1.定义变量
　　
int pageSize = 0; //每页显示行数
int nMax = 0; //总记录数
int pageCount = 0; //页数＝总记录数/每页显示行数
int pageCurrent = 0; //当前页号
int nCurrent = 0; //当前记录行
//PS (在bdnInfo中添加上一页, 下一页, lblPageCountText, txtCurrentPage.Text)
//2.在窗体内分别添加BindingNavigator (bdnInfo), BindingSource (bdsInfo), DataGirdView1 //(dgvInfo)
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

