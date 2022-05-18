# 为C#自定义控件添加自定义事件

这里的自定义控件是由普通控件组合而成的。

希望事件响应代码推迟到使用自定义控件的窗体里写。

步骤一：新建一个用户控件，放两个按钮,Tag分别是btn1,btn2.

这两个按钮的共用单击事件处理代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Drawing;
using System.Data;
using System.Linq;
using System.Text;
using System.Windows.Forms;

namespace UcDll
{
  public partial class UcTest : UserControl
  {
    public UcTest()
    {
      InitializeComponent();
    }

​    //定义委托
​    public delegate void BtnClickHandle(object sender, EventArgs e);
​    //定义事件
​    public event BtnClickHandle UserControlBtnClicked;
​    private void btn_Click(object sender, EventArgs e)
​    {
​      if (UserControlBtnClicked != null)
​        UserControlBtnClicked(sender, new EventArgs());//把按钮自身作为参数传递
​    }
  }
}

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

步骤二：当用户拖一个自定义控件在窗体的时候，

在事件里可以找到UserControlBtnClicked事件。

private void ucTest1_UserControlBtnClicked(object sender, EventArgs e)
{
  Button btn = sender as Button;
  MessageBox.Show(btn.Tag.ToString());
}

这个操作很有用。


### c# 自定义控件如何在属性栏添加自定义事件？可以双击生成＋＝代码？ 

用户控件的实现比较简单，直接从System.Windows.Forms.UserControl继承。

public class UserControl1 : System.Windows.Forms.UserControl

 

为了便于测试我在上面添加了一个TextBox,并注册TextBox的TextChanged事件，

this.textBox1.TextChanged += new System.EventHandler(this.textBox1_TextChanged);

事件处理函数，

private void textBox1_TextChanged(object sender, System.EventArgs e)

{

  MessageBox.Show(this.textBox1.Text);

}

这里演示如果控件中文本框的内容改变就会用MessageBox显示当前的文本框内容。

窗体中添加上面的用户控件，当我们改变textBox的文本时，可以看到跳出一个对话框，很简单吧。

下面来看看对控件添加属性。

这里定义一个私有变量。

private string customValue;

添加访问他的属性

public string CustomValue

{

  get{return customValue;}

  set{customValue =value;}

}

在窗体中使用的时候像普通控件一样进行访问，

userControl11.CustomValue = "用户控件自定义数据";

通过事件可以传递消息到窗体上，在定义之前我们先来写一个简单的参数类。

public class TextChangeEventArgs : EventArgs

{

  private string message;

  public TextChangeEventArgs(string message)

  {

​    this.message = message;

  }

public string Message

  {

​    get{return message;}

  }

}

定义委托为，

public delegate void TextBoxChangedHandle(object sender,TextChangeEventArgs e);

接下去在用户控件中添加事件，

//定义事件

public event TextBoxChangedHandle UserControlValueChanged;

为了激发用户控件的新增事件，修改了一下代码，

private void textBox1_TextChanged(object sender, System.EventArgs e)

{

  if(UserControlValueChanged != null)

​    UserControlValueChanged(this,new TextChangeEventArgs(this.textBox1.Text));

​      

}

好了，为了便于在Csdn上回答问题，把完整的代码贴了出来：

using System;

using System.Collections;

using System.ComponentModel;

using System.Drawing;

using System.Data;

using System.Windows.Forms;

 

namespace ZZ.WindowsApplication1

{

  public class UserControl1 : System.Windows.Forms.UserControl

  {

​    private System.Windows.Forms.TextBox textBox1;

​    private string customValue;

​    

private System.ComponentModel.Container components = null;

 

​    public string CustomValue

​    {

​      get{return customValue;}

​      set{customValue =value;}

​    }

 

​    //定义事件

​    public event TextBoxChangedHandle UserControlValueChanged;

 

​    public UserControl1()

​    {

​      InitializeComponent();

​    }

 

​    protected override void Dispose( bool disposing )

​    {

​      if( disposing )

​      {

​        if(components != null)

​        {

​          components.Dispose();

​        }

​      }

​      base.Dispose( disposing );

​    }

 

​    \#region组件设计器生成的代码

​    private void InitializeComponent()

​    {

​      this.textBox1 = new System.Windows.Forms.TextBox();

​      this.SuspendLayout();

​      this.textBox1.Location = new System.Drawing.Point(12, 36);

​      this.textBox1.Name = "textBox1";

​      this.textBox1.TabIndex = 0;

​      this.textBox1.Text = "textBox1";

​      this.textBox1.TextChanged += new System.EventHandler(this.textBox1_TextChanged);

​      this.Controls.Add(this.textBox1);

​      this.Name = "UserControl1";

​      this.Size = new System.Drawing.Size(150, 92);

​      this.ResumeLayout(false);

 

​    }

​    \#endregion

 

​    private void textBox1_TextChanged(object sender, System.EventArgs e)

​    {

​      if(UserControlValueChanged != null)

​        UserControlValueChanged(this,new TextChangeEventArgs(this.textBox1.Text));

​      

​    }

  }

  //定义委托

  public delegate void TextBoxChangedHandle(object sender,TextChangeEventArgs e);

 

  public class TextChangeEventArgs : EventArgs

  {

​    private string message;

​    public TextChangeEventArgs(string message)

​    {

​      this.message = message;

​    }

​    public string Message

​    {

​      get{return message;}

​    }

  }

}

使用时要在窗体中注册上面的事件，比较简单都贴源代码了，

using System;

using System.Drawing;

using System.Collections;

using System.ComponentModel;

using System.Windows.Forms;

using System.Data;

 

namespace ZZ.WindowsApplication1

{

  public class Form1 : System.Windows.Forms.Form

  {

​    private WindowsApplication1.UserControl1 userControl11;

​    private System.ComponentModel.Container components = null;

 

​    public Form1()

​    {

​      InitializeComponent();

​      userControl11.CustomValue = "用户控件自定义数据";

​      userControl11.UserControlValueChanged += newTextBoxChangedHandle(userControl11_UserControlValueChanged);

​    }

 

​    protected override void Dispose( bool disposing )

​    {

​      if( disposing )

​      {

​        if (components != null)

​        {

​          components.Dispose();

​        }

​      }

​      base.Dispose( disposing );

​    }

 

​    \#region Windows 窗体设计器生成的代码

​    private void InitializeComponent()

​    {

​      this.userControl11 = new WindowsApplication1.UserControl1();

​      this.SuspendLayout();

​      this.userControl11.Location = new System.Drawing.Point(8, 8);

​      this.userControl11.Name = "userControl11";

​      this.userControl11.Size = new System.Drawing.Size(150, 84);

​      this.userControl11.TabIndex = 0;

​      this.AutoScaleBaseSize = new System.Drawing.Size(6, 14);

​      this.ClientSize = new System.Drawing.Size(292, 193);

​      this.Controls.Add(this.userControl11);

​      this.Name = "Form1";

​      this.Text = "Form1";

​      this.ResumeLayout(false);

 

​    }

​    \#endregion

 

​     [STAThread]

​    static void Main()

​    {

​      Application.Run(new Form1());

​    }

 

​    private void userControl11_UserControlValueChanged(object sender, TextChangeEventArgs e)

​    {

​      MessageBox.Show("当前控件的值为:" + e.Message);

​    }

  }

}

另外需要动态加载，就把控件添加在容器的Controls集合就行了，下面是在构造函数中添加控件，

public Form1()

{

  InitializeComponent();

  UserControl1 uc = new UserControl1();

  uc.CustomValue = "动态加载的用户控件";

  uc.UserControlValueChanged += new TextBoxChangedHandle(userControl11_UserControlValueChanged);

  this.Controls.Add(uc);

}

另外从VS.net中的工具箱中拖动用户控件到窗体上，如果是第一次需要编译一下项目。

 

//如果我有一个写好的控件，想在Form中使用如何？？？？？？？

在控件中： 
    public delegate void OnSubBureauSelectChanged();//定义委托
    public event OnSubBureauSelectChanged onSubBureauSelectChanged;//定义事件

//以下代码放在你要用在窗体中调用的事件中，可以是控件中有的也可以自己写的

 if ( ( subBureaus.Count > 0 ) && ( onSubBureauSelectChanged != null ) )
        onSubBureauSelectChanged ();

//以下写在窗体构造中

searchPanel.onSubBureauSelectChanged += new SearchPanel.OnSubBureauSelectChanged ( OnSubBureauSelectChanged );

//以下再写一个自己写的事件

 private void OnSubBureauSelectChanged ( )
    {这样就可以了}