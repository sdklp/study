# windows下c#模拟鼠标点击

## c#模拟鼠标点击

### 须要引用的dll

C#自己带的类库中没有关于鼠标操做的函数库，须要引用微软的dll，在visual studio中使用 nuget添加 mshtml 便可（Microsoft.mshtml）html

### 主要函数，及其方法参数释义

```
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Runtime.InteropServices;

    namespace workhelper
    {
        class MouseHelper
        {
            [System.Runtime.InteropServices.DllImport("user32")]
            public static extern int mouse_event(int dwFlags, int dx, int dy, int cButtons, int dwExtraInfo);
            //移动鼠标 
            public const int MOUSEEVENTF_MOVE = 0x0001;
            //模拟鼠标左键按下 
            public const int MOUSEEVENTF_LEFTDOWN = 0x0002;
            //模拟鼠标左键抬起 
            public const int MOUSEEVENTF_LEFTUP = 0x0004;
            //模拟鼠标右键按下 
            public const int MOUSEEVENTF_RIGHTDOWN = 0x0008;
            //模拟鼠标右键抬起 
            public const int MOUSEEVENTF_RIGHTUP = 0x0010;
            //模拟鼠标中键按下 
            public const int MOUSEEVENTF_MIDDLEDOWN = 0x0020;
            //模拟鼠标中键抬起 
            public const int MOUSEEVENTF_MIDDLEUP = 0x0040;
            //标示是否采用绝对坐标 
            public const int MOUSEEVENTF_ABSOLUTE = 0x8000;
            [DllImport("user32.dll")]
            public static extern bool SetCursorPos(int X, int Y);
        }
    }
```

#### SetCursorPos 函数

把光标移到屏幕的指定位置。（ps：是整个屏幕的坐标,相对于屏幕左上角的绝对位置）c#

*参数*函数

- X 指定光标的新的X坐标，以屏幕坐标表示。

- Y 指定光标的新的Y坐标，以屏幕坐标表示。
  *返回值*

- 若是成功，返回非0值

- 若是失败，返回值是0spa

  #### mouse_event 函数

  综合鼠标移动和按钮点击。该方法包含鼠标左右移动及点击操做。

  参数

- dwFlags 标志位集，指定点击按钮和鼠标动做的多种状况。此参数能够是下列值的某种组合：code

| VALUE                  | MEANING                                                      |
| :--------------------- | :----------------------------------------------------------- |
| MOUSEEVENTF_ABSOLUTE   | dX和dY参数含有规范化的绝对坐标。若是不设置，这些参数含有相对数据：相对于上次位置的改动位置。此标志可设置，也可不设置，无论鼠标的类型或与系统相连的相似于鼠标的设备的类型如何。要获得关于相对鼠标动做的信息，参见下面备注部分 |
| MOUSEEVENTF_MOVE       | 鼠标移动                                                     |
| MOUSEEVENTF_LEFTDOWN   | 鼠标左键按下                                                 |
| MOUSEEVENTF_LEFTUP     | 鼠标左键松开                                                 |
| MOUSEEVENTF_RIGHTDOWN  | 鼠标右键按下                                                 |
| MOUSEEVENTF_RIGHTUP    | 鼠标右键松开                                                 |
| MOUSEEVENTF_MIDDLEDOWN | 鼠标中键按下                                                 |
| MOUSEEVENTF_MIDDLEUP   | 鼠标中键松开                                                 |
| MOUSEEVENTF_WHEEL      | 鼠标轮被滚动，若是鼠标有一个滚轮。滚动的数量由dwData给出     |

- dx
  指定鼠标沿x轴的绝对位置或者从上次鼠标事件产生以来移动的数量，依赖于MOUSEEVENTF_ABSOLUTE的设置。给出的绝对数据做为鼠标的实际X坐标；给出的相对数据做为移动的mickeys数。一个mickey表示鼠标移动的数量，代表鼠标已经移动。
- dy
  指定鼠标沿y轴的绝对位置或者从上次鼠标事件产生以来移动的数量，依赖于MOUSEEVENTF_ABSOLUTE的设置。给出的绝对数据做为鼠标的实际y坐标，给出的相对数据做为移动的mickeys数。
- dwData
  若是dwFlags为MOUSEEVENTF_WHEEL，则dwData指定鼠标轮移动的数量。正值代表鼠标轮向前转动，即远离用户的方向；负值代表鼠标轮向后转动，即朝向用户。一个轮击定义为WHEEL_DELTA，即120。若是dwFlagsS不是MOUSEEVENTF_WHEEL，则dWData应为零。
- dwExtraInfo
  指定与鼠标事件相关的附加32位值。应用程序调用函数GetMessageExtraInfo来得到此附加信息

#### 使用示例

```
    MouseHelper.SetCursorPos(Form1.point.X, Form1.point.Y);
    MouseHelper.mouse_event(MouseHelper.MOUSEEVENTF_LEFTDOWN, 0, 0, 0, 0);
    MouseHelper.mouse_event(MouseHelper.MOUSEEVENTF_LEFTUP, 0, 0, 0, 0);
```