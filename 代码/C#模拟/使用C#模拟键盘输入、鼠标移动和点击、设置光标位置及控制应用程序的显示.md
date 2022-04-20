# 使用C#模拟键盘输入、鼠标移动和点击、设置光标位置及控制应用程序的显示

**1.模拟键盘输入(SendKeys)**

------

 功能：将一个或多个按键消息发送到活动窗口，就如同在键盘上进行输入一样。

语法：SendKeys.Send(string keys);SendKeys.SendWait(string keys);

说明：

（1）每个按键由一个或多个字符表示。为了指定单一键盘字符，必须按字符本身的键。例如，为了表示字母 A，可以用 "A" 作为 string。为了表示多个字符，就必须在字符后面直接加上另一个字符。例如，要表示 A、B 及 C，可用 "ABC" 作为 string。 

（2）对 SendKeys 来说，加号 (+)、插入符 (^)、百分比符号 (%)、上划线 (~) 及圆括号 ( ) 都具有特殊意义。为了指定上述任何一个字符，要将它放在大括号 ({}) 当中。例如，要指定正号，可用 {+} 表示。方括号 ([ ]) 对 SendKeys 来说并不具有特殊意义，但必须将它们放在大括号中。在其它应用程序中，方括号有特殊意义，在出现动态数据交换 (DDE) 的时候，它可能具有重要意义。为了指定大括号字符，请使用 {{} 及 {}}。 

（3）为了在按下按键时指定那些不显示的字符，例如 ENTER 或 TAB 以及那些表示动作而非字符的按键，请使用下列代码：

| 按键          | 代码                         |
| ------------- | ---------------------------- |
| BACKSPACE     | {BACKSPACE}, {BS}, 或 {BKSP} |
| BREAK         | {BREAK}                      |
| CAPS LOCK     | {CAPSLOCK}                   |
| DEL or DELETE | {DELETE} 或 {DEL}            |
| DOWN ARROW    | {DOWN}                       |
| END           | {END}                        |
| ENTER         | {ENTER}或 ~                  |
| ESC           | {ESC}                        |
| HELP          | {HELP}                       |
| HOME          | {HOME}                       |
| INS or INSERT | {INSERT} 或 {INS}            |
| LEFT ARROW    | {LEFT}                       |
| NUM LOCK      | {NUMLOCK}                    |
| PAGE DOWN     | {PGDN}                       |
| PAGE UP       | {PGUP}                       |
| PRINT SCREEN  | {PRTSC}                      |
| RIGHT ARROW   | {RIGHT}                      |
| SCROLL LOCK   | {SCROLLLOCK}                 |
| TAB           | {TAB}                        |
| UP ARROW      | {UP}                         |
| F1            | {F1}                         |
| F2            | {F2}                         |
| F3            | {F3}                         |
| F4            | {F4}                         |
| F5            | {F5}                         |
| F6            | {F6}                         |
| F7            | {F7}                         |
| F8            | {F8}                         |
| F9            | {F9}                         |
| F10           | {F10}                        |
| F11           | {F1}                         |
| F12           | {F12}                        |
| F13           | {F13}                        |
| F14           | {F14}                        |
| F15           | {F15}                        |
| F16           | {F16}                        |

（4）为了指定那些与 SHIFT、CTRL 及 ALT 等按键结合的组合键，可在这些按键码的前面放置一个或多个代码，这些代码列举如下：

| 按键  | 代码 |
| ----- | ---- |
| Shift | +    |
| Ctrl  | ^    |
| Alt   | %    |

为了说明在按下其它按键时应同时按下 SHIFT、CTRL、及 ALT 的任意组合键，请把那些按键的码放在括号当中。例如，为了说明按下E与C的时候同时按下Shift键，请使用 "+(EC)"。为了说明在按下E的时候同时按下SHIFT键，但接着按C而不按SHIFT，则使用 "+EC"。 

为了指定重复键，使用 {key number} 的形式。必须在key与number之间放置一个空格。例如，{LEFT 42} 意指42次按下 LEFT ARROW 键；{h 10} 则是指10次按下H键。 

注意：不能用SendKeys将按键消息发送到这样一个应用程序，这个应用程序并没有被设计成在 Microsoft Windows 中运行。Sendkeys也无法将PRINT SCREEN按键{PRTSC}发送到任何应用程序。

（5）输入汉字用SendKeys.Send("汉字"); 

原博客地址：http://www.cnblogs.com/sydeveloper/archive/2013/02/25/2932571.html

 

**2.模拟鼠标移动和点击**

------

 我们需要用到的mouse_event函数，位于user32.dll这个库文件里面，所以我们要先声明引用。

[![复制代码](../../../我的文档/Typora/Pictures/copycode-164129967450414.gif)](javascript:void(0);)

```
 1 [DllImport("user32",CharSet=CharSet.Unicode)] 
 2 private static extern int mouse_event(int dwFlags, int dx, int dy, int dwData, int dwExtraInfo); 
 3 //移动鼠标 
 4 const int MOUSEEVENTF_MOVE = 0x0001;      
 5 //模拟鼠标左键按下 
 6 const int MOUSEEVENTF_LEFTDOWN = 0x0002; 
 7 //模拟鼠标左键抬起 
 8 const int MOUSEEVENTF_LEFTUP = 0x0004; 
 9 //模拟鼠标右键按下 
10 const int MOUSEEVENTF_RIGHTDOWN = 0x0008; 
11 //模拟鼠标右键抬起 
12 const int MOUSEEVENTF_RIGHTUP = 0x0010; 
13 //模拟鼠标中键按下 
14 const int MOUSEEVENTF_MIDDLEDOWN = 0x0020; 
15 //模拟鼠标中键抬起 
16 const int MOUSEEVENTF_MIDDLEUP = 0x0040; 
17 //标示是否采用绝对坐标 
18 const int MOUSEEVENTF_ABSOLUTE = 0x8000;
```

[![复制代码](../../../我的文档/Typora/Pictures/copycode-164129967450414.gif)](javascript:void(0);)

**dwFlags：**标志位集，指定点击按钮和鼠标动作的多种情况。此参数里的各位可以是下列值的任何合理组合：

- MOUSEEVENTF_ABSOLUTE：表明参数dX，dy含有规范化的绝对坐标。如果不设置此位，参数含有相对数据：相对于上次位置的改动位置。此标志可被设置，也可不设置，不管鼠标的类型或与系统相连的类似于鼠标的设备的类型如何。要得到关于相对鼠标动作的信息，参见下面备注部分。
- MOUSEEVENTF_MOVE：表明发生移动。
- MOUSEEVENTF_LEFTDOWN：表明接按下鼠标左键。
- MOUSEEVENTF_LEFTUP：表明松开鼠标左键。
- MOUSEEVENTF_RIGHTDOWN：表明按下鼠标右键。
- MOUSEEVENTF_RIGHTUP：表明松开鼠标右键。
- MOUSEEVENTF_MIDDLEDOWN：表明按下鼠标中键。
- MOUSEEVENTF_MIDDLEUP：表明松开鼠标中键。
- MOUSEEVENTF_WHEEL：在Windows NT中如果鼠标有一个轮，表明鼠标轮被移动。移动的数量由dwData给出。

**dx：**指定鼠标沿x轴的绝对位置或者从上次鼠标事件产生以来移动的数量，依赖于MOUSEEVENTF_ABSOLUTE的设置。给出的绝对数据作为鼠标的实际X坐标；给出的相对数据作为移动的mickeys数。一个mickey表示鼠标移动的数量，表明鼠标已经移动。

**dy：**指定鼠标沿y轴的绝对位置或者从上次鼠标事件产生以来移动的数量，依赖于MOUSEEVENTF_ABSOLUTE的设置。给出的绝对数据作为鼠标的实际y坐标，给出的相对数据作为移动的mickeys数。

**dwData：**如果dwFlags为MOUSEEVENTF_WHEEL，则dwData指定鼠标轮移动的数量。正值表明鼠标轮向前转动，即远离用户的方向；负值表明鼠标轮向后转动，即朝向用户。一个轮击定义为WHEEL_DELTA，即120。

如果dwFlags不是MOUSEEVENTF_WHEEL，则dWData应为零。

**dwExtralnfo：**指定与鼠标事件相关的附加32位值。应用程序调用函数GetMessageExtraInfo来获得此附加信息。

**返回值：**无。

**程序中我们直接调用mouse_event函数就可以了 mouse_event(MOUSEEVENTF_ABSOLUTE | MOUSEEVENTF_MOVE, 500, 500, 0, 0);**

1、这里是鼠标左键按下和松开两个事件的组合即一次单击： mouse_event (MOUSEEVENTF_LEFTDOWN | MOUSEEVENTF_LEFTUP, 0, 0, 0, 0 )

2、模拟鼠标右键单击事件： mouse_event (MOUSEEVENTF_RIGHTDOWN | MOUSEEVENTF_RIGHTUP, 0, 0, 0, 0 )

3、两次连续的鼠标左键单击事件 构成一次鼠标双击事件： mouse_event (MOUSEEVENTF_LEFTDOWN | MOUSEEVENTF_LEFTUP, 0, 0, 0, 0 ) mouse_event (MOUSEEVENTF_LEFTDOWN | MOUSEEVENTF_LEFTUP, 0, 0, 0, 0 )

4、使用绝对坐标 (MOUSEEVENTF_ABSOLUTE | MOUSEEVENTF_MOVE, 500, 500, 0, 0)

需要说明的是，如果没有使用MOUSEEVENTF_ABSOLUTE，函数默认的是相对于鼠标当前位置的点，如果dx，和dy，用0，0表示，这函数认为是当前鼠标所在的点。

5、直接设定绝对坐标并单击 mouse_event(MOUSEEVENTF_LEFTDOWN, X * 65536 / 1024, Y * 65536 / 768, 0, 0); mouse_event(MOUSEEVENTF_LEFTUP, X * 65536 / 1024, Y * 65536 / 768, 0, 0); 其中X，Y分别是你要点击的点的横坐标和纵坐标

6、键盘模拟用 Keybd_event()

Keybd_event能触发一个按键事件，也就是说回产生一个WM_KEYDOWN或WM_KEYUP消息。当然也可以用产生这两个消息来模拟按键，但是没有直接用这个函数方便。 Keybd_event共有四个参数，第一个为按键的虚拟键值，如回车键为vk_return,　tab键为vk_tab。第二个参数为扫描码，一般不用设置，用0代替就行。第三个参数为选项标志，如果为keydown则置0即可，如果为keyup则设成“KEYEVENTF_KEYUP”，第四个参数一 般也是置0即可。

转载至博客(略有修正)：http://www.cnblogs.com/blackice/p/3418414.html

DllImport的其他属性，参见MSDN(字段部分)：https://msdn.microsoft.com/zh-cn/library/system.runtime.interopservices.dllimportattribute.aspx

 

**3.设置光标位置**

------

 声明引用，导入方法

```
1 [DllImport("User32.dll")]  
2 public extern static bool GetCursorPos(ref Point pot); 
3 
4 [DllImport("User32.dll")] 
5 public extern static void SetCursorPos(int x, int y);
```

调用方法设置光标位置

```
1 SetCursorPos(int.Parse(pointX), (int.Parse(pointY));   
```

 

**4.控制应用程序的显示**

------

 声明引用，导入方法

```
1 [DllImport("User32.dll")]
2 public static extern bool SetForegroundWindow(IntPtr hWnd);
3 
4 [DllImport("User32.dll")]
5 public static extern bool ShowWindow(IntPtr hWnd, int nCmdShow);
```

调用方法将正在打开的网页(IE) 窗口激活并显示到桌面

[![复制代码](../../../我的文档/Typora/Pictures/copycode-164129967450414.gif)](javascript:void(0);)

```
 1 static void Main(string[] args)
 2 {
 3     Process[] localByName = Process.GetProcessesByName("iexplore");
 4     foreach (Process proc in localByName)
 5     {
 6         //internet explorer uses a hosting model - one iexplore.exe instance hosts the internet explorer frame, 
 7         //the other iexplore.exe instances just display the contents of tabs(网页标题--MainWindowTitle不是空字符串)
 8         if (proc.MainWindowTitle != string.Empty)
 9         {
10             IntPtr handle = proc.MainWindowHandle;
11             SetForegroundWindow(handle);
12             ShowWindow(handle, 3);
13         }
14     }
15 }
```

[![复制代码](../../../我的文档/Typora/Pictures/copycode-164129967450414.gif)](javascript:void(0);)

此方法的作用相当于：

![img](../../../我的文档/Typora/Pictures/552940-20161223192845261-800690741.png) 