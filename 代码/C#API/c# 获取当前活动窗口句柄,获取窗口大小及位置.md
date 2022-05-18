## c# 获取当前活动窗口句柄,获取窗口大小及位置

需调用API函数

需在开头引入命名空间
using System.Runtime.InteropServices;

 

获取当前窗口句柄:GetForegroundWindow()

[DllImport("user32.dll", CharSet = CharSet.Auto, ExactSpelling = true)]
public static extern IntPtr GetForegroundWindow();

 

返回值类型是IntPtr,即为当前获得焦点窗口的句柄

使用方法 :  IntPtr myPtr=GetForegroundWindow();

获取到该窗口句柄后,可以对该窗口进行操作.比如,关闭该窗口或在该窗口隐藏后,使其显示

[DllImport("user32.dll", CharSet = CharSet.Auto, ExactSpelling = true)]
public static extern int ShowWindow(IntPtr hwnd, int nCmdShow);

 

其中ShowWindow(IntPtr hwnd, int nCmdShow);

nCmdShow的含义

0  关闭窗口

1  正常大小显示窗口

2  最小化窗口

3  最大化窗口

使用实例:  ShowWindow(myPtr, 0);

 

获取窗口大小及位置:需要调用方法GetWindowRect(IntPtr hWnd, ref RECT lpRect)

​    [DllImport("user32.dll")]
​    [return: MarshalAs(UnmanagedType.Bool)]
​    static extern bool GetWindowRect(IntPtr hWnd, ref RECT lpRect);

​    [StructLayout(LayoutKind.Sequential)]
​    public struct RECT
​    {
​      public int Left;               //最左坐标
​      public int Top;               //最上坐标
​      public int Right;              //最右坐标
​      public int Bottom;            //最下坐标
​    }

 

示例:

​          InPtr awin = GetForegroundWindow();  //获取当前窗口句柄
​          RECT rect = new RECT();
​          GetWindowRect(awin, ref rect);
​          int width = rc.Right - rc.Left;            //窗口的宽度
​          int height = rc.Bottom - rc.Top;          //窗口的高度
​          int x = rc.Left;                       
​          int y = rc.Top;

 

 

 

 

\------------------------------------------------------------------------

C#中的FindWindow
 [System.Runtime.InteropServices.DllImport("user32.dll", EntryPoint="FindWindow")]
 public static extern int FindWindow (
      string lpClassName,
      string lpWindowName
      );
已知窗口标题"abc",怎么得到窗口句柄?

IntPtr hWnd = FindWindow(null, "abc");

\-------------------------------------------------------

C#使用FindWindow()函数：

​      [DllImport("coredll.dll", EntryPoint = "FindWindow")]
​      private extern static IntPtr FindWindow(string lpClassName, string lpWindowName);
​    这个函数有两个参数，第一个是要找的窗口的类，第二个是要找的窗口的标题。在搜索的时候不一定两者都知道，但至少要知道其中的一个。有的窗口的标题是比较容易得到的，如"计算器"，所以搜索时应使用标题进行搜索。但有的软件的标题不是固定的，如"记事本"，如果打开的文件不同，窗口标题也不同，这时使用窗口类搜索就比较方便。如果找到了满足条件的窗口，这个函数返回该窗口的句柄，否则返回0。 看例子

​      IntPtr ParenthWnd = new IntPtr(0);

​      ParenthWnd = FindWindow(null,"Word Mobile");
​      //判断这个窗体是否有效
​      if (ParenthWnd != IntPtr.Zero)
​      {
​        MessageBox.Show("找到窗口");
​      }

​      else

​        MessageBox.Show("没有找到窗口");

​    从上面的讨论中可以看出，如果要搜索的外部程序的窗口标题比较容易得到，问题是比较简单的。可如果窗口的标题不固定或者根本就没有标题，怎么得到窗口的类呢？如果你安装了Visual C++，你可以使用其中的Spy，在Spy++中有一个FindWindow工具，它允许你使用鼠标选择窗口，然后Spy++会显示这个窗口的类。
  在Win32 API中还有一个FindWindowEx，它非常适合寻找子窗口。

 

FindWindowEx用法
 函数功能：该函数获得一个窗口的句柄，该窗口的类名和窗口名与给定的字符串相匹配。这个函数查找子窗口，从排在给定的子窗口后面的下一个子窗口开始。在查找时不区分大小写。

  函数原型：HWND FindWindowEx（HWND hwndParent，HWND hwndChildAfter，LPCTSTR lpszClass，LPCTSTR lpszWindow）；

  参数：

  hwndParent：要查找子窗口的父窗口句柄。

  如果hwnjParent为NULL，则函数以桌面窗口为父窗口，查找桌面窗口的所有子窗口。

  Windows NT5.0 and later：如果hwndParent是HWND_MESSAGE，函数仅查找所有消息窗口。

  hwndChildAfter ：子窗口句柄。查找从在Z序中的下一个子窗口开始。子窗口必须为hwndPareRt窗口的直接子窗口而非后代窗口。如果HwndChildAfter为NULL，查找从hwndParent的第一个子窗口开始。如果hwndParent 和 hwndChildAfter同时为NULL，则函数查找所有的顶层窗口及消息窗口。

  lpszClass：指向一个指定了类名的空结束字符串，或一个标识类名字符串的成员的指针。如果该参数为一个成员，则它必须为前次调用theGlobaIAddAtom函数产生的全局成员。该成员为16位，必须位于lpClassName的低16位，高位必须为0。

  lpszWindow：指向一个指定了窗口名（窗口标题）的空结束字符串。如果该参数为 NULL，则为所有窗口全匹配。返回值：如果函数成功，返回值为具有指定类名和窗口名的窗口句柄。如果函数失败，返回值为NULL。

C#中使用该函数首先导入命名空间：

 
01.using System.Runtime.InteropServices; 
 

然后写API引用部分的代码，放入 class 内部

 

 
01.[DllImport("user32.dll", EntryPoint = "FindWindow")] 
02.private static extern IntPtr FindWindowEx( IntPtr hwndParent, IntPtr hwndChildAfter, string lpszClass, string lpszWindow )
 

例如：

 

 
01.const int BM_CLICK = 0xF5; 
02.IntPtr maindHwnd = FindWindow(null, "QQ用户登录"); //获得QQ登陆框的句柄 
03.if (maindHwnd != IntPtr.Zero) 
04.{ 
\05.  IntPtr childHwnd = FindWindowEx(maindHwnd, IntPtr.Zero, null, "登录");  //获得按钮的句柄 
\06.  if (childHwnd != IntPtr.Zero) 
\07.  { 
\08.    SendMessage(childHwnd, BM_CLICK, 0, 0);   //发送点击按钮的消息 
\09.  } 
\10.  else 
\11.  { 
\12.    MessageBox.Show("没有找到子窗口"); 
\13.  } 
14.} 
15.else 
16.{ 
\17.  MessageBox.Show("没有找到窗口"); 
18.} 
 


 从此学习网 http://item.congci.com/item/findwindowfindwindowex

\---------------------------------------------------------------------------------------

DllImport("user32", SetLastError = true)] 
public static extern int GetWindowText( 
  IntPtr hWnd,//窗口句柄 
  StringBuilder lpString,//标题 
  int nMaxCount //最大值 
  );

//获取类的名字 
[DllImport("user32.dll")] 
private static extern int GetClassName( 
  IntPtr hWnd,//句柄 
  StringBuilder lpString, //类名 
  int nMaxCount //最大值 
  );

//根据坐标获取窗口句柄 
[DllImport("user32")] 
private static extern IntPtr WindowFromPoint( 
Point Point //坐标 
);

 

private void timer1_Tick(object sender, EventArgs e) 
{ 
  int x = Cursor.Position.X; 
  int y = Cursor.Position.Y; 
  Point p = new Point(x, y); 
  IntPtr formHandle = WindowFromPoint(p);//得到窗口句柄 
  StringBuilder title = new StringBuilder(256); 
  GetWindowText(formHandle, title, title.Capacity);//得到窗口的标题 
  StringBuilder className = new StringBuilder(256); 
  GetClassName(formHandle, className, className.Capacity);//得到窗口的句柄 
  this.textBox1.Text = title.ToString(); 
  this.textBox2.Text = formHandle.ToString(); 
  this.textBox3.Text = className.ToString(); 
}

 

\--------------------------------------

 

文章不错，收藏备用。

设计初衷：
　　公司为了安全性考虑，不让密码被太多人知道，所以想实现一个自动登录的模块。

设计思想： 
　　主要是通过调用Windows API中的一些方法，找到目标窗口和进程之后把保存在数据库中的用户名密码自动填入输入框中，并登录。

设计步骤：
一、调用Windows API。
　C#下调用Windows API方法如下：
　1、引入命名空间：using System.Runtime.InteropServices;
　2、引用需要使用的方法，格式：[DllImport("DLL文件")]方法的声明;
　[DllImport("user32.dll")]private static extern bool ShowWindow(IntPtr hWnd, int nCmdShow);
　[DllImport("user32.dll")]private static extern bool SetForegroundWindow(IntPtr hWnd);
　[DllImport("user32.dll")]private static extern IntPtr FindWindow(string lpClassName,string lpWindowName);
　[DllImport("user32.dll")]private static extern int SendMessage(IntPtr hWnd,int Msg,int wParam,int lParam);
　[DllImport("user32.dll")]private static extern bool SetCursorPos(int X, int Y);
　[DllImport("user32.dll")]private static extern void mouse_event(int dwFlags, int dx, int dy, int dwData, int dwExtraInfo);
　[DllImport("user32.dll")]private static extern void keybd_event(byte bVk, byte bScan, uint dwFlags, uint dwExtraInfo);
　 [DllImport("user32.dll")]private static extern bool SetWindowPos(IntPtr hWnd, IntPtr hWndlnsertAfter, int X, int Y, int cx, int cy, uint Flags); 
　//ShowWindow参数
　private const int SW_SHOWNORMAL = 1;
　private const int SW_RESTORE = 9;
　private const int SW_SHOWNOACTIVATE = 4;
　//SendMessage参数
　private const int WM_KEYDOWN = 0X100;
　private const int WM_KEYUP = 0X101;
　private const int WM_SYSCHAR = 0X106;
　private const int WM_SYSKEYUP = 0X105;
　private const int WM_SYSKEYDOWN = 0X104;
　private const int WM_CHAR = 0X102;

二、找到目标窗口
1)、根据窗口的标题得到句柄
　IntPtr myIntPtr = FindWindow(null,"窗口名"); //null为类名，可以用Spy++得到，也可以为空
　ShowWindow(myIntPtr, SW_RESTORE); //将窗口还原
　SetForegroundWindow(myIntPtr); //如果没有ShowWindow，此方法不能设置最小化的窗口
2)、遍历所有窗口得到句柄
1 定义委托方法CallBack，枚举窗口API（EnumWindows），得到窗口名API（GetWindowTextW）和得到窗口类名API（GetClassNameW）
　public delegate bool CallBack(int hwnd, int lParam);
　[DllImport("user32")]public static extern int EnumWindows(CallBack x, int y);
　 [DllImport("user32.dll")]private static extern int GetWindowTextW(IntPtr hWnd, [MarshalAs(UnmanagedType.LPWStr)]StringBuilder lpString, int nMaxCount);
　[DllImport("user32.dll")]private static extern int GetClassNameW(IntPtr hWnd, [MarshalAs(UnmanagedType.LPWStr)]StringBuilder lpString, int nMaxCount);
2 调用EnumWindows遍历窗口
　CallBack myCallBack = new CallBack(Recall);
　EnumWindows(myCallBack, 0);
3 回调方法Recall
　public bool Recall(int hwnd, int lParam)
　{
　　StringBuilder sb = new StringBuilder(256);
　　IntPtr PW = new IntPtr(hwnd);

　　GetWindowTextW(PW,sb,sb.Capacity); //得到窗口名并保存在strName中
　　string strName = sb.ToString();

　　GetClassNameW(PW,sb,sb.Capacity); //得到窗口类名并保存在strClass中
　　string strClass = sb.ToString();

　　if (strName.IndexOf("窗口名关键字") >= 0 && strClass.IndexOf("类名关键字") >= 0)
　　{
　　　return false; //返回false中止EnumWindows遍历
　　}
　　else
　　{
　　　return true; //返回true继续EnumWindows遍历
　　}
　}
3)、打开窗口得到句柄
1 定义设置活动窗口API（SetActiveWindow），设置前台窗口API（SetForegroundWindow）
　[DllImport("user32.dll")]static extern IntPtr SetActiveWindow(IntPtr hWnd);
　[DllImport("user32.dll")][return: MarshalAs(UnmanagedType.Bool)]static extern bool SetForegroundWindow(IntPtr hWnd);
2 打开窗口
　Process proc = Process.Start(@"目标程序路径");
　SetActiveWindow(proc.MainWindowHandle);
　SetForegroundWindow(proc.MainWindowHandle);

三、向指定的窗口输入数据
1 利用发送消息API（SendMessage）向窗口发送数据
　InputStr(myIntPtr, _GameID); //输入游戏ID
　SendMessage(myIntPtr, WM_SYSKEYDOWN, 0X09, 0); //输入TAB（0x09）
　SendMessage(myIntPtr, WM_SYSKEYUP, 0X09, 0);
　InputStr(myIntPtr, _GamePass); //输入游戏密码
　SendMessage(myIntPtr, WM_SYSKEYDOWN, 0X0D, 0); //输入ENTER（0x0d）
　SendMessage(myIntPtr, WM_SYSKEYUP, 0X0D, 0);

　/// <summary>
　/// 发送一个字符串
　/// </summary>
　/// <param name="myIntPtr">窗口句柄</param>
　/// <param name="Input">字符串</param>
　public void InputStr(IntPtr myIntPtr, string Input)
　{
　　byte[] ch = (ASCIIEncoding.ASCII.GetBytes(Input));
　　for (int i = 0; i < ch.Length; i++)
　　{ 
　　　SendMessage(PW, WM_CHAR, ch, 0);
　　}
　}
2 利用鼠标和键盘模拟向窗口发送数据
　SetWindowPos(PW, (IntPtr)(-1), 0, 0, 0, 0, 0x0040 | 0x0001); //设置窗口位置
　SetCursorPos(476, 177); //设置鼠标位置
　mouse_event(0x0002, 0, 0, 0, 0); //模拟鼠标按下操作
　mouse_event(0x0004, 0, 0, 0, 0); //模拟鼠标放开操作
　SendKeys.Send(_GameID);  //模拟键盘输入游戏ID
　SendKeys.Send("{TAB}"); //模拟键盘输入TAB
　SendKeys.Send(_GamePass); //模拟键盘输入游戏密码
　SendKeys.Send("{ENTER}"); //模拟键盘输入ENTER

另：上面还提到了keybd_event方法，用法和mouse_event方法类似，作用和SendKeys.Send一样。

C# 别人软件里边做好的文本框,我如何给他赋值并且提交，最好有源码可供参考，如有合适的，将高额追加分
可以用WINDOWS api函数实现。
下面的WINDIWS API引用部分的代码，放入 class 内部

[DllImport ( "user32.dll", EntryPoint = "FindWindow", SetLastError = true )]
private static extern IntPtr FindWindow( string lpClassName, string lpWindowName );//查找窗口句柄
[DllImport ( "user32.dll", EntryPoint = "FindWindowEx", SetLastError = true )]
private static extern IntPtr FindWindowEx( IntPtr hwndParent, uint hwndChildAfter, string lpszClass, string lpszWindow );//查找窗口内控件句柄
[DllImport ( "user32.dll", EntryPoint = "SendMessage", SetLastError = true, CharSet = CharSet.Auto )]
private static extern int SendMessage( IntPtr hwnd, uint wMsg, int wParam, int lParam );//发送消息
[DllImport ( "user32.dll", EntryPoint = "SetForegroundWindow", SetLastError = true )]
private static extern void SetForegroundWindow( IntPtr hwnd );// 设置窗口为激活状态

哈哈，现在可以开工了。我就用QQ的自动登录为列子
下面是你winfrom窗口的按钮事件：
private void button1_Click( object sender, EventArgs e )
{
const uint WM_SETTEXT = 0x000C;//设置文本框内容的消息
const uint BM_CLICK = 0xF5; //鼠标点击的消息，对于各种消息的数值，你还是得去查查API手册
IntPtr hwndCalc = FindWindow ( null, "QQ2011" ); //查找QQ2011的窗口句柄
if ( hwndCalc != IntPtr.Zero )//找到啦
{
IntPtr hwndLogin= FindWindowEx ( hwndCalc, 0, null, "安全登录" ); //获取登陆按钮的句柄
IntPtr hwndQ = FindWindowEx ( hwndCalc, 0, “ComboBox”, "" ); //获取QQ号码输入框的控件句柄
IntPtr hwndP = FindWindowEx ( hwndCalc, 0,"Edit", “” ); //获取密码输入框的控件句柄 SetForegroundWindow ( hwndCalc );  //将QQ窗口设置为激活
System.Threading.Thread.Sleep ( 1000 );  //暂停1秒让你看到效果
SendMessage ( hwndQ, WM_SETTEXT, TextBox1.Text, 0 );//发送文本框1里面的内容（QQ号啦）
System.Threading.Thread.Sleep ( 1000 );  //暂停1秒让你看到效果
SendMessage( hwndP, WM_SETTEXT, TextBox2.Text, 0 );//发送文本框2里面的内容（QQpassword）

System.Threading.Thread.Sleep ( 1000);  //暂停1秒让你看到效果
SendMessage ( hwndLogin, BM_CLICK, 0, 0 );//点击登录
}
else
{
MessageBox.Show ("没有启动 [QQ2011]");
}
}

\---------------------------------------------------

# [C#查找指定窗口的子窗口的句柄](http://blog.csdn.net/iwteih/article/details/1483743)

2009年更新
通过实验得知，FindWindowEx可以通过classname或caption（也就是窗口的title）查找窗口，且如果第一个参数传IntPtr.Zero的话，将从Windows最顶层窗口开始查找，但是窗口很多的话这样会非常的慢，所以加入Timeout的判断，如果超时还没找到，返回false。

用法：FindWindow fw = new FindWindow(IntPtr.Zero, null, "ThunderDFrame", 10);//查找Title为ThunderDFrame的窗口，如果10秒内还没找到，返回false

代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Runtime.InteropServices;

namespace Util
{
    class FindWindow
    {
        [DllImport("user32")]
        [return: MarshalAs(UnmanagedType.Bool)]
        //IMPORTANT : LPARAM  must be a pointer (InterPtr) in VS2005, otherwise an exception will be thrown
        private static extern bool EnumChildWindows(IntPtr window, EnumWindowProc callback, IntPtr i);
        //the callback function for the EnumChildWindows
        private delegate bool EnumWindowProc(IntPtr hWnd, IntPtr parameter);

        //if found  return the handle , otherwise return IntPtr.Zero
        [DllImport("user32.dll", EntryPoint = "FindWindowEx")]
        private static extern IntPtr FindWindowEx(IntPtr hwndParent, IntPtr hwndChildAfter, string lpszClass, string lpszWindow);

        private string m_classname; // class name to look for
        private string m_caption; // caption name to look for

        private DateTime start;
        private int m_timeout;//If exceed the time. Indicate no windows found.

        private IntPtr m_hWnd; // HWND if found
        public IntPtr FoundHandle
        {
            get { return m_hWnd; }
        }

        private bool m_IsTimeOut;
        public bool IsTimeOut
        {
            get{return m_IsTimeOut;}
            set { m_IsTimeOut = value; }
        }

        // ctor does the work--just instantiate and go
        public FindWindow(IntPtr hwndParent, string classname, string caption, int timeout)
        {
            m_hWnd = IntPtr.Zero;
            m_classname = classname;
            m_caption = caption;
            m_timeout = timeout;
            start = DateTime.Now;
            FindChildClassHwnd(hwndParent, IntPtr.Zero);
        }

        /**/
        /// <summary>
        /// Find the child window, if found m_classname will be assigned 
        /// </summary>
        /// <param name="hwndParent">parent's handle</param>
        /// <param name="lParam">the application value, nonuse</param>
        /// <returns>found or not found</returns>
        //The C++ code is that  lParam is the instance of FindWindow class , if found assign the instance's m_hWnd
        private bool FindChildClassHwnd(IntPtr hwndParent, IntPtr lParam)
        {
            EnumWindowProc childProc = new EnumWindowProc(FindChildClassHwnd);
            IntPtr hwnd = FindWindowEx(hwndParent, IntPtr.Zero, m_classname, m_caption);
            if (hwnd != IntPtr.Zero)
            {
                this.m_hWnd = hwnd; // found: save it
                m_IsTimeOut = false;
                return false; // stop enumerating
            }

            DateTime end = DateTime.Now;

            if (start.AddSeconds(m_timeout) < end)
            {
                m_IsTimeOut = true;
                return false;
            }

            EnumChildWindows(hwndParent, childProc, IntPtr.Zero); // recurse  redo FindChildClassHwnd
            return true;// keep looking
        }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)