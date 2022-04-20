C#中的WinForm开发中，对于控件DataGridView如何实现嵌套对象绑定到列，在Web开发中一般都是：DataGridView对象.DataPropertyName="字段名或对象属性名"；如果集合中包含对象属性的话，使用：**DataGridView对象.DataPropertyName="子对象.属性名";**其中子对象是绑定DataGridView数据源的属性对象。但是在C#的WinForm开发中，是不支持嵌套绑定的，DataGridView只能绑定一层。这样就不能使用**DataGridView对象.DataPropertyName="子对象.属性名"**来进行数据的绑定了。如何进行解决呢？下面给出解决办法！

可以用Linq进行转换下，把嵌套的属性提升到顶层，比如：
var listChange = list.Select(x => new { x.普通属性1, x.普通属性2, 子对象_属性=x.子对象.属性 ... });
然后可以绑定：
dataGridView.DataSource = listChange.ToList();
dataGridView.DataPropertyName="子对象_属性";

具体可以参考下面的代码：

前台的代码：

```
private System.Windows.Forms.DataGridView dgvCheckItemManager;
// 
// Col_ID
// 
this.Col_ID.DataPropertyName = "ID";
this.Col_ID.HeaderText = "检查项编号";
this.Col_ID.Name = "Col_ID";
this.Col_ID.ReadOnly = true;
this.Col_ID.Visible = false;
// 
// Col_CheckItemName
// 
this.Col_CheckItemName.DataPropertyName = "CheckItemName";
this.Col_CheckItemName.HeaderText = "检查项名称";
this.Col_CheckItemName.Name = "Col_CheckItemName";
this.Col_CheckItemName.ReadOnly = true;
this.Col_CheckItemName.Width = 180;
// 
// ColCheckItemPinyin
// 
this.ColCheckItemPinyin.DataPropertyName = "Acronym";
this.ColCheckItemPinyin.HeaderText = "拼音简写";
this.ColCheckItemPinyin.Name = "ColCheckItemPinyin";
this.ColCheckItemPinyin.ReadOnly = true;
this.ColCheckItemPinyin.Width = 180;
// 
// Col_UnitPrice
// 
this.Col_UnitPrice.DataPropertyName = "UnitPrice";
this.Col_UnitPrice.HeaderText = "单价";
this.Col_UnitPrice.Name = "Col_UnitPrice";
this.Col_UnitPrice.ReadOnly = true;
// 
// Col_DoctorProjectName
// 
this.Col_DoctorProjectName.DataPropertyName = "DoctorProjectName";
this.Col_DoctorProjectName.HeaderText = "项目名称";
this.Col_DoctorProjectName.Name = "Col_DoctorProjectName";
this.Col_DoctorProjectName.ReadOnly = true;
// 
// ColCheckItemCategory
// 
this.ColCheckItemCategory.DataPropertyName = "Description";
this.ColCheckItemCategory.HeaderText = "描述";
this.ColCheckItemCategory.Name = "ColCheckItemCategory";
this.ColCheckItemCategory.ReadOnly = true;
this.ColCheckItemCategory.Width = 180;
```



后台的.cs代码如下：

```
//CheckItem实体类如下
public class CheckItem
{
    public CheckItem()
    {
        if (doctorProject == null)
        {
            doctorProject = new DoctorProject();
        }
    }
 
    private int iD = -1;
 
    public int ID
    {
        get { return iD; }
        set { iD = value; }
    }
 
    private string checkItemName = string.Empty;
 
    public string CheckItemName
    {
        get { return checkItemName; }
        set { checkItemName = value; }
    }
 
    /// <summary>
    /// 药品名称拼音首字母
    /// </summary>
    private string acronym = string.Empty;
 
    public string Acronym
    {
        get { return acronym; }
        set { acronym = value; }
    }
 
    private decimal unitPrice = 0;
 
    public decimal UnitPrice
    {
        get { return unitPrice; }
        set { unitPrice = value; }
    }
 
    private string description = string.Empty;
 
    public string Description
    {
        get { return description; }
        set { description = value; }
    }
 
    private DoctorProject doctorProject;
 
    public DoctorProject DoctorProject
    {
        get { return doctorProject; }
        set { doctorProject = value; }
    }
}
```

绑定DataGridView的代码如下：

```
List<CheckItem> checkItemList = CheckItemManager.GetAllCheckItem(ref retMsg);
//注意：DataGridView只能绑定一层。可以用linq转换下，把嵌套的属性提升到顶层
var checkItemListChange = checkItemList.Select(x => new {x.ID, x.CheckItemName, x.Acronym, x.UnitPrice, x.DoctorProject.DoctorProjectName, x.Description });   
dgvCheckItemManager.DataSource = checkItemListChange.ToList();
```

