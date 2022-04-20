 C#窗体传值代码方法

一、前言
我们在做Winform窗体程序开发的时候，会经常遇到窗体之间相互传值。假设有下面的一个场景：一个主窗体和一个子窗体，点击主窗体上面的按钮给子窗体传值，并在子窗体上面显示出来，一般会有如下几种方式实现。

二、公共属性

我们可以在子窗体里面定义一个公共的属性，然后在父窗体里面给公共属性赋值，这样可以实现窗体之间传值，子窗体代码如下：



```c#
using System;
using System.Windows.Forms;

namespace DelegateDemo
{
 public partial class frmChild : Form
 {
 public frmChild()
 {
  InitializeComponent();
 }

 // 定义一个公共属性，接收传递的值
 public string strMessage { get; set; }

 /// <summary>
 /// 窗体加载
 /// </summary>
 /// <param name="sender"></param>
 /// <param name="e"></param>
 private void frmChild_Load(object sender, EventArgs e)
 {
  // 将接收到的值显示在窗体上
  this.lblMessage.Text = strMessage;
 }
 }
}
```


父窗体代码：

```c#
using System;
using System.Windows.Forms;

namespace DelegateDemo
{
 public partial class frmParent : Form
 {
 public frmParent()
 {
  InitializeComponent();
 }

 /// <summary>
 /// 单击事件
 /// </summary>
 /// <param name="sender"></param>
 /// <param name="e"></param>
 private void btn_Value_Click(object sender, EventArgs e)
 {
  frmChild child = new frmChild();
  // 给窗体的公共属性赋值
  child.strMessage = this.txtMessage.Text.Trim();
  // 显示子窗体
  child.Show();
 }
 }
}
```


这种方式有一个缺点：属性需要设置为public，不安全。

二、公共方法
我们还可以在子窗体里面定义一个方法，通过调用方法传值，子窗体代码如下：

```c#
using System;
using System.Windows.Forms;

namespace DelegateDemo
{
 public partial class frmChild : Form
 {
 public frmChild()
 {
  InitializeComponent();
 }

 // 定义一个公共属性，接收传递的值
 //public string strMessage { get; set; }

 // 定义属性为private
 private string strMessage { get; set; }

 /// <summary>
 /// 给私有属性赋值
 /// </summary>
 /// <param name="strText"></param>
 public void SetText(string strText)
 {
  strMessage = strText;
 }

 /// <summary>
 /// 窗体加载
 /// </summary>
 /// <param name="sender"></param>
 /// <param name="e"></param>
 private void frmChild_Load(object sender, EventArgs e)
 {
  // 将接收到的值显示在窗体上
  this.lblMessage.Text = strMessage;
 }
 }
}
```


父窗体代码：

```c#
using System;
using System.Windows.Forms;

namespace DelegateDemo
{
 public partial class frmParent : Form
 {
 public frmParent()
 {
  InitializeComponent();
 }

 /// <summary>
 /// 单击事件
 /// </summary>
 /// <param name="sender"></param>
 /// <param name="e"></param>
 private void btn_Value_Click(object sender, EventArgs e)
 {
  #region 调用公共属性赋值
  //frmChild child = new frmChild();
  //// 给窗体的公共属性赋值
  //child.strMessage = this.txtMessage.Text.Trim();
  //// 显示子窗体
  //child.Show(); 
  #endregion


  #region 调用方法赋值
  frmChild child = new frmChild();
  // 给窗体的公共属性赋值
  child.SetText(this.txtMessage.Text.Trim());
  // 显示子窗体
  child.Show(); 
  #endregion
 }
 }
}
```


这种方式同样也有缺点：属性虽然是private的了，但是方法还是public的。

三、委托
上述两种方式都是不安全，下面我们使用委托来实现窗体之间传值。

1、定义一个委托
我们在主窗体里面定义一个有参无返回值的委托：

```c#
// 定义一个有参无返回值的委托
private delegate void SendMessage(string strMessage);
```

2、实例化一个此委托类型的事件
在父窗体里面定义一个委托类型的事件：

```c#
// 定义一个委托类型的事件
public event SendMessage sendMessageEvent;
```

委托与事件的关系，事件相对于委托更安全，更低耦合。委托是一个类型，事件是委托类型的一个实例。

3、定义要执行的方法
这里其实就是在子窗体里面定义一个给控件赋值的方法：


/// <summary>
/// 给控件赋值的方法
/// </summary>
/// <param name="strValue"></param>
public void SetValue(string strValue)
{
 this.lblMessage.Text = strValue;
}
4、将方法绑定到事件

```c#
frmChild child = new frmChild();
// 将方法绑定到事件上
sendMessageEvent += new SendMessage(child.SetValue);
// 也可以使用下面的简写形式
// sendMessageEvent += child.SetValue;
child.Show();
```

5、触发委托
在按钮的点击事件里面触发委托：

```c#
if(sendMessageEvent!=null)
{
  sendMessageEvent.Invoke(this.txtMessage.Text.Trim());
}
```

上面的代码中使用的是自定义的委托，我们也可以使用.Net 框架里面自带的Action泛型委托：

```c#
using System;
using System.Windows.Forms;

namespace DelegateDemo
{

 public partial class frmParent : Form
 {
  // 定义一个有参无返回值的委托
  public delegate void SendMessage(string strMessage);

  // 定义一个委托类型的事件
  public event SendMessage sendMessageEvent;


  public event Action<string> actionEvent;
  public frmParent()
  {
   InitializeComponent();
  }

  /// <summary>
  /// 单击事件
  /// </summary>
  /// <param name="sender"></param>
  /// <param name="e"></param>
  private void btn_Value_Click(object sender, EventArgs e)
  {
   #region 调用公共属性赋值
   //frmChild child = new frmChild();
   //// 给窗体的公共属性赋值
   //child.strMessage = this.txtMessage.Text.Trim();
   //// 显示子窗体
   //child.Show(); 
   #endregion


   #region 调用方法赋值
   //frmChild child = new frmChild();
   //// 给窗体的公共属性赋值
   //child.SetText(this.txtMessage.Text.Trim());
   //// 显示子窗体
   //child.Show(); 
   #endregion

   #region 通过委托传值
   //frmChild child = new frmChild();
   //// 将方法绑定到事件上
   //// sendMessageEvent += new SendMessage(child.SetValue);
   //// 也可以使用下面的简写形式
   //sendMessageEvent += child.SetValue;
   //child.Show();
   #endregion

   #region 使用Action
   frmChild child = new frmChild();
   // 将方法绑定到事件上
   actionEvent += child.SetValue;
   child.Show();
   #endregion

   // 使用自定义委托
   //if (sendMessageEvent!=null)
   //{
   // sendMessageEvent.Invoke(this.txtMessage.Text.Trim());
   //}

   // 使用Action委托
   if (actionEvent != null)
   {
    actionEvent.Invoke(this.txtMessage.Text.Trim());
   }
  }
 }
}
```


以上就是本次介绍的全部相关知识点，感谢大家的学习和对脚本之家的支持。