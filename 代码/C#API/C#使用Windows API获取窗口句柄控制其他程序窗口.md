# C#使用Windows API获取窗口句柄控制其他程序窗口



很多时候，编写程序模拟鼠标和键盘操作可以方便的实现你需要的功能，而不需要对方程序为你开放接口。比如，操作飞信定时发送短信等。我之前开发过飞信耗子，用的是对飞信协议进行抓包，然后分析协议，进而模拟协议的执行，开发出了客户端，与移动服务器进行通信，但是这有一些缺点。如果移动的服务器对接口进行变更，我所编写的客户端也要进行相应的升级。如果服务器的协议进行了更改，甚至个人编写的这种第三方客户端需要重写。而我个人也没有这个时间和精力，或者说没有足够的利益支撑我继续去重构飞信耗子。因此，这款还算优秀的软件，现在就束之高阁了，我自己也觉得遗憾。上周，某项目验收，需要修改界面，但是零时找不到源码了。我在两三个小时内要解决这个问题，时间紧迫。我突然想起室友以前做过模拟鼠标键盘去发送飞信消息的小程序。于是我赶紧电话咨询了一下。然后掌握了这个技巧，按时解决了问题。我觉得这个技巧还是很有用的，现总结如下：

首先，引入如下三个API接口：



```c#
[DllImport("user32.dll")]
public static extern IntPtr FindWindow(string lpClassName, string lpWindowName); 
[DllImport("User32.dll", EntryPoint = "SendMessage")]
private static extern int SendMessage(IntPtr hWnd, int Msg, IntPtr wParam, stringlParam); [DllImport("User32.dll ")]
public static extern IntPtr FindWindowEx(IntPtr parent, IntPtr childe, string strclass, string FrmText);
```

第一个与第三个是用于查找窗口句柄的，凡运行于Windows上的窗口，都具有句柄。窗口上的文本框，按钮之类的，也有其句柄（可看作子窗口句柄）。这些句柄的类型可以通过

Spy++进行查询。比如C语言编写的程序中，文本框的句柄类型一般为“EDIT”，C#写的程序则不是，可以具体去查。第二个接口则是用于向窗口发送各种消息，比如向文本框发送

字符串，或者向按钮发送按下与弹起的消息等。详细解释如下：

```c#
IntPtr hwnd = FindWindow(null, "无标题 - 记事本");
```

这是用于查找操作系统中打开的窗口中标题名为无标题 - 记事本的窗口。第一个参数是此窗口的类型。这两个参数知道一

个即可，另一个可以填null。但是如果是用窗口类型查找，则可能只能得到其中的一个窗口。因此通过标题进行查找是非常方便的。

```c#
IntPtr htextbox = FindWindowEx(hwnd, IntPtr.Zero, "EDIT", null);
```

这个函数用于获得窗口中子窗口的句柄，子窗口指的其实就是窗口中的各种控件。第一个参数是父窗口的句柄，第二个参数指示获得的是同一类型中的第几个子窗口。填

IntPtr.Zero则表示获得第一个子窗口。第三个参数表示你需要找的子窗口的类型，第四个参数一般为null。如果一个窗口中有两个文本框，那么可以用如下操作获得第二个文本框

的句柄。

```c#
IntPtr htextbox = FindWindowEx(hwnd, IntPtr.Zero, "EDIT", null);IntPtr htextbox2 = FindWindowEx(hwnd, htextbox, "EDIT", null);//填上次获得的句柄，可以得到下一个的句柄。
```

这里只是先将第二个参数填为IntPtr.Zero，获取第一个EDIT类型的文本框，然后第二次调用时，再将第二参数填为第一个文本框的句柄，那么执行返回的就是下一个文本框的句柄

了。因此htextbox2得到的就是第二文本框的句柄。
在可以自由获得各种窗口及其上控件的句柄后，我们就可以向其发送各种消息进行鼠标和键盘的模拟了。比如：

```c#
SendMessage(htextbox, WM_SETTEXT, IntPtr.Zero, name);
```

这句是为文本框填写相应的字符串name。

```c#
IntPtr hbutton = FindWindowEx(hwnd, IntPtr.Zero, "BUTTON", null);SendMessage(hbutton, WM_LBUTTONDOWN, IntPtr.Zero, null);
SendMessage(hbutton, WM_LBUTTONUP, IntPtr.Zero, null);
```

这三句是获得了窗口的一个button，然后发送按下，弹起消息给它，模拟了点击鼠标的动作。
SendMessage函数的第一个参数是窗口句柄，或者窗口中控件的句柄，第二个参数是消息的类型Flag，这些值是在API的一些头文件中定义好的。你要是在C#中用，就自己去定义他们，比如

```c#
constint WM_SETTEXT =0x000C;constint WM_LBUTTONDOWN =0x0201;constint WM_LBUTTONUP =0x0202;
constint WM_CLOSE =0x0010;
```

还有其他的类型Flag，可以参考上一篇Blog查询，也可以去查MSDN。第三个参数和第四个参数都是消息的具体内容。一般我们用的是最后一个参数。第三个参数填为IntPtr.Zero。

当然如果是鼠标的动作，那么最后一个参数就是null。

```cs
SendMessage(htextbox, WM_SETTEXT, IntPtr.Zero, name);//填写文本框。SendMessage(hbutton, WM_LBUTTONDOWN, IntPtr.Zero, null);//鼠标按下按钮
```