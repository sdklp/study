主要是前两天，有个同学问我这个方面的内容，当时利用了委托事件的方法来解决的，感觉效果还是挺好的。于是便记录了下来，以作备忘。

本例中，主要实现的是向Access数据库中添加记录的功能。其中，主窗体负责显示数据，而弹出的子窗体负责添加数据，数据添加完毕，需要刷新主窗体。

父窗体代码如下：

```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;

namespace WindowsFormsApplication1
{
publicpartialclass mainFrm : Form
{
public mainFrm()
{
InitializeComponent();
}

childFrm childfrm;

privatevoid mainFrm_Load(object sender, EventArgs e)
{
string sql ="select [userName] as 姓名,[userPass] as 编制,[userSex] as 性别,[userQQ] as QQ,[userAddress] as 住址 from UserData";
DataTable dt = DB.GenDT(sql);
this.dataGridView1.DataSource = dt;
}

privatevoid button1_Click(object sender, EventArgs e)
{
if (childfrm ==null|| childfrm.IsDisposed ==true)
{
childfrm =new childFrm();
childfrm.raiseCallBackRefreshEvent +=new raiseCallBackRefreshDelegate(childfrm_raiseCallBackRefreshEvent); //注册回调事件，也即将子窗体抛出的事件接收，然后处理
childfrm.ShowDialog();
}
else
{
childfrm.raiseCallBackRefreshEvent +=new raiseCallBackRefreshDelegate(childfrm_raiseCallBackRefreshEvent);
childfrm.ShowDialog();
}
}

privatevoid childfrm_raiseCallBackRefreshEvent()
{
mainFrm_Load(null,null); //刷新主窗体
}
}
}
```

子窗体代码如下：

```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;

namespace WindowsFormsApplication1
{

publicdelegatevoid raiseCallBackRefreshDelegate(); //全局委托申明

publicpartialclass childFrm : Form
{
public childFrm()
{
InitializeComponent();
}

publicevent raiseCallBackRefreshDelegate raiseCallBackRefreshEvent; //公开的事件

privatevoid childFrm_Load(object sender, EventArgs e)
{

}

privatevoid button1_Click(object sender, EventArgs e)
{
string sql =@"insert into UserData(userName,userPass,userSex,userQQ,userAddress)
values('"+textBox1.Text+"','"+textBox2.Text+"','"+textBox3.Text+"','"+textBox4.Text+"','"+textBox5.Text+"')";
DB.RunSQL(sql);
raiseCallBackRefreshEvent(); //将事件抛出去，在主窗体中接收，并作相应处理
this.Close();
}
}
}
```

