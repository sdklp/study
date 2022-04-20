## C# 模拟鼠标移动和点击

我们需要用到的mouse_event函数，位于user32.dll这个库文件里面，所以我们要先声明引用。

[![复制代码](../../../我的文档/Typora/Pictures/copycode-16412995308041.gif)](javascript:void(0);)

```
        [System.Runtime.InteropServices.DllImport("user32")]
        private static extern int mouse_event(int dwFlags, int dx, int dy, int dwData, int dwExtraInfo);
        //移动鼠标 
        const int MOUSEEVENTF_MOVE = 0x0001;
        //模拟鼠标左键按下 
        const int MOUSEEVENTF_LEFTDOWN = 0x0002;
        //模拟鼠标左键抬起 
        const int MOUSEEVENTF_LEFTUP = 0x0004;
        //模拟鼠标右键按下 
        const int MOUSEEVENTF_RIGHTDOWN = 0x0008;
        //模拟鼠标右键抬起 
        const int MOUSEEVENTF_RIGHTUP = 0x0010;
        //模拟鼠标中键按下 
        const int MOUSEEVENTF_MIDDLEDOWN = 0x0020;
        //模拟鼠标中键抬起 
        const int MOUSEEVENTF_MIDDLEUP = 0x0040;
        //标示是否采用绝对坐标 
        const int MOUSEEVENTF_ABSOLUTE = 0x8000;
        //模拟鼠标滚轮滚动操作，必须配合dwData参数
        const int MOUSEEVENTF_WHEEL = 0x0800;


        public static void TestMoveMouse()
        {
            Console.WriteLine("模拟鼠标移动5个像素点。");
            //mouse_event(MOUSEEVENTF_MOVE, 50, 50, 0, 0);//相对当前鼠标位置x轴和y轴分别移动50像素
            mouse_event(MOUSEEVENTF_WHEEL, 0, 0, -20, 0);//鼠标滚动，使界面向下滚动20的高度
        }
```

[![复制代码](../../../我的文档/Typora/Pictures/copycode-16412995308041.gif)](javascript:void(0);)

 

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
**dwData：**如果dwFlags为MOUSEEVENTF_WHEEL，则dwData指定鼠标轮移动的数量。正值表明鼠标轮向前转动，即远离用户的方向；负值表明鼠标轮向后转动，即朝向用户。一个轮击定义为WHEEL_DELTA，即120。如果dwFlagsS不是MOUSEEVENTF_WHEEL，则dWData应为零。
**dwExtralnfo：**指定与鼠标事件相关的附加32位值。应用程序调用函数GetMessageExtraInfo来获得此附加信息。
**返回值：**无。

 

 

**程序中我们直接调用mouse_event函数就可以了 mouse_event(MOUSEEVENTF_ABSOLUTE | MOUSEEVENTF_MOVE, 500, 500, 0, 0);**

1、这里是鼠标左键按下和松开两个事件的组合即一次单击： mouse_event (MOUSEEVENTF_LEFTDOWN | MOUSEEVENTF_LEFTUP, 0, 0, 0, 0 )

2、模拟鼠标右键单击事件： mouse_event (MOUSEEVENTF_RIGHTDOWN | MOUSEEVENTF_RIGHTUP, 0, 0, 0, 0 )

3、两次连续的鼠标左键单击事件 构成一次鼠标双击事件： mouse_event (MOUSEEVENTF_LEFTDOWN | MOUSEEVENTF_LEFTUP, 0, 0, 0, 0 ) mouse_event (MOUSEEVENTF_LEFTDOWN | MOUSEEVENTF_LEFTUP, 0, 0, 0, 0 )

4、使用绝对坐标：mouse_event (MOUSEEVENTF_ABSOLUTE | MOUSEEVENTF_MOVE, 500, 500, 0, 0)

   需要说明的是，如果没有使用MOUSEEVENTF_ABSOLUTE，函数默认的是相对于鼠标当前位置的点，如果dx，和dy，用0，0表示，这函数认为是当前鼠标所在的点。

５、直接设定绝对坐标并单击 mouse_event(MOUSEEVENTF_LEFTDOWN, X * 65536 / 1024, Y * 65536 / 768, 0, 0); mouse_event(MOUSEEVENTF_LEFTUP, X * 65536 / 1024, Y * 65536 / 768, 0, 0); 其中X，Y分别是你要点击的点的横坐标和纵坐标

 

**键盘模拟用 Keybd_event函数**

Keybd_event能触发一个按键事 件，也就是说回产生一个WM_KEYDOWN或WM_KEYUP消息。当然也可以用产生这两个消息来模拟按键，但是没有直接用这个函数方便。

函数原型：void keybd_event( BYTE bVk, BYTE bScan, DWORD dwFlags, ULONG_PTR dwExtraInfo );

参数说明：
bVk 虚拟按键代码编号
bScan 按键的的硬件扫描代码
dwFlags 控制功能操作的各个方面；**KEYEVENTF_EXTENDEDKEY**：如果指定，扫描代码前面有一个前缀字节，其值为0xE0；**KEYEVENTF_KEYUP**：如果指定，则抬起按键。
dwExtraInfo 按键动作的附加信息

返回值：无返回值

Keybd_event共有四个参数：
第一个为按键的虚拟键值，如回车键为vk_return,　tab键为vk_tab。
第二个参数为扫描码，一般不用 设置，用0代替就行。
第三个参数为选项标志，如果为keydown则置0即可，如果为keyup则设成“KEYEVENTF_KEYUP”，
第四个参数一 般也是置0即可。例如：实现模拟按下i键，其中的 0x49 表示 i 键的虚拟键值。

 

键盘键码对照表：

| 按键      | 键码 | 按键        | 键码 | 按键        | 键码 | 按键        | 键码 |
| --------- | ---- | ----------- | ---- | ----------- | ---- | ----------- | ---- |
| A         | 65   | 6(数字键盘) | 102  | ;           | 59   | :           | 58   |
| B         | 66   | 7(数字键盘) | 103  | =           | 61   | +           | 43   |
| C         | 67   | 8(数字键盘) | 104  | ,           | 44   | <           | 60   |
| D         | 68   | 9(数字键盘) | 105  | -           | 45   | _           | 95   |
| E         | 69   | *           | 106  | .           | 46   | >           | 62   |
| F         | 70   | !           | 33   | /           | 47   | ?           | 63   |
| G         | 71   | Enter       | 13   | `           | 96   | ~           | 126  |
| H         | 72   | @           | 64   | [           | 91   | {           | 123  |
| I         | 73   | #           | 35   | \           | 92   | \|          | 124  |
| J         | 74   | $           | 36   | }           | 125  | ]           | 93   |
| K         | 75   | F1          | 112  | a           | 97   | b           | 98   |
| L         | 76   | F2          | 113  | c           | 99   | d           | 100  |
| M         | 77   | F3          | 114  | e           | 101  | f           | 102  |
| N         | 78   | F4          | 115  | g           | 103  | h           | 104  |
| O         | 79   | F5          | 116  | i           | 105  | j           | 106  |
| P         | 80   | F6          | 117  | k           | 107  | l           | 108  |
| Q         | 81   | F7          | 118  | m           | 109  | n           | 110  |
| R         | 82   | F8          | 119  | o           | 111  | p           | 112  |
| S         | 83   | F9          | 120  | q           | 113  | r           | 114  |
| T         | 84   | F10         | 121  | s           | 115  | t           | 116  |
| U         | 85   | F11         | 122  | u           | 117  | v           | 118  |
| V         | 86   | F12         | 123  | w           | 119  | x           | 120  |
| W         | 87   | Backspace   | 8    | y           | 121  | z           | 122  |
| X         | 88   | Tab         | 9    | 0(数字键盘) | 96   | Up Arrow    | 38   |
| Y         | 89   | Clear       | 12   | 1(数字键盘) | 97   | Right Arrow | 39   |
| Z         | 90   | Shift       | 16   | 2(数字键盘) | 98   | Down Arrow  | 40   |
| 0(小键盘) | 48   | Control     | 17   | 3(数字键盘) | 99   | Insert      | 45   |
| 1(小键盘) | 49   | Alt         | 18   | 4(数字键盘) | 100  | Delete      | 46   |
| 2(小键盘) | 50   | Cap Lock    | 20   | 5(数字键盘) | 101  | Num Lock    | 144  |
| 3(小键盘) | 51   | Esc         | 27   | 2(数字键盘) | 98   | Down Arrow  | 40   |
| 4(小键盘) | 52   | Spacebar    | 32   | 3(数字键盘) | 99   | Insert      | 45   |
| 5(小键盘) | 53   | Page Up     | 33   | 4(数字键盘) | 100  | Delete      | 46   |
| 6(小键盘) | 54   | Page Down   | 34   | 5(数字键盘) | 101  | Num Lock    | 144  |
| 7(小键盘) | 55   | End         | 35   |             |      |             |      |
| 8(小键盘) | 56   | Home        | 36   |             |      |             |      |
| 9(小键盘) | 57   | Left Arrow  | 37   |             |      |             |      |

 