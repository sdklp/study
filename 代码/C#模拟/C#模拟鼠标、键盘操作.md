## C#模拟鼠标、键盘操作

### C语言 在程序中打开网页，模拟鼠标点击、键盘输入

**一、简述**

​     **记--使用C语言 打开指定网页，并模拟鼠标点击、键盘输入。实现半自动填写账号密码，并登录网站（当然现在的大部分网站都有验证码，或有检测"非人为"操作，以防止恶意注册、登录)。**

​    **例子打包：链接: https://pan.baidu.com/s/1eStV0lAcmr8kmEA0n3LRcg 提取码: 7kvj** 

**二、效果 (程序填写账号密码，实现半自动登录)**

![img](../../../我的文档/Typora/Pictures/20181222103515688.gif)

**三、工程结构**

![img](../../../我的文档/Typora/Pictures/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25hbmZlaWJ1eWk=,size_16,color_FFFFFF,t_70.png)

**四、源文件**

​    **main.c文件**

[![复制代码](../../../我的文档/Typora/Pictures/copycode-16412995906656.gif)](javascript:void(0);)

```
#include <stdio.h>
#include <stdlib.h>
#include <Windows.h> //ShellExecuteA()
 
//打开某个网址:website （使用默认浏览器） 
void open_web(char *website)
{
    ShellExecuteA(0,"open", website,0,0,1);
}
 
 
//模拟鼠标点击  （x,y）是要点击的位置 
void click(int x, int y)
{
    //将鼠标光标移动到 指定的位置     例子中屏幕分辨率1600x900  在鼠标坐标系统中，屏幕在水平和垂直方向上均匀分割成65535×65535个单元
    mouse_event(MOUSEEVENTF_ABSOLUTE|MOUSEEVENTF_MOVE, x*65535/1600, y*65535/900, 0, 0);
    
    Sleep(50);//稍微延时50ms 
    mouse_event(MOUSEEVENTF_LEFTDOWN,0,0,0,0);//鼠标左键按下 
    mouse_event(MOUSEEVENTF_LEFTUP,0,0,0,0);//鼠标左键抬起
 }
 
//模拟键盘输入 keybd_event(要按下的字符,0,动作,0);动作为0是按下，动作为2是抬起 
void input()
{
    char user[]="1234567890123";//账号 
    char pwd[]="1234567890";//密码 
    
    click(823,392); //点击"用户名输入框"的位置     
    
    int i;
    //输入账号 
    for(i=0;i<sizeof(user);i++)
    {
        keybd_event(user[i],0,0,0);
        keybd_event(user[i],0,2,0);
        Sleep(30);    
    }
    
    //tab键 对应的编号是0x09  让密码输入框 获取焦点 
    keybd_event(0x09,0,0,0);//按下 
    keybd_event(0x09,0,2,0); //松开 
    Sleep(30);    
    
    //输入密码 
    for(i=0;i<sizeof(pwd);i++)
    {
        keybd_event(pwd[i],0,0,0);
        keybd_event(pwd[i],0,2,0);
        Sleep(30);
    }
    
    //模拟按下tab键 让登录按钮获取焦点 
    click(824,530);//点击"登录按钮" 
    Sleep(30);
}
 
 
//将chrome.exe进程杀掉，在例子中尚未使用 
void close()
{
    system("taskkill  /f  /im chrome.exe");
}
 
int main(int argc,char *argv[])
{
    open_web("https://www.baidu.com/");//打开某个网址 
    Sleep(4000);//延时4秒，等待网页打开完毕，再进行其它操作。根据实际情况（浏览器打开速度，网速） 
    click(1454, 126);//点击"登录"（1454,126） 
    Sleep(150);
    click(712,658);//点击"用户名登录"
    Sleep(150);
    input();//模拟鼠标动作，键盘输入 
    return 0;
}
```

[![复制代码](../../../我的文档/Typora/Pictures/copycode-16412995906656.gif)](javascript:void(0);)

 

**五、总结**

   **5.1 ShellExecute()函数** 

| 功能         | 对指定的文件执行操作。（可以实现调用第三方程序）             |                                                    |
| ------------ | ------------------------------------------------------------ | -------------------------------------------------- |
| 头文件       | Windows.h                                                    |                                                    |
| 原型         | HINSTANCE ShellExecuteA( HWND hwnd, LPCSTR lpOperation, LPCSTR lpFile, LPCSTR lpParameters, LPCSTR lpDirectory, INT nShowCmd ); |                                                    |
| 参数         | hwnd                                                         | 父窗口的句柄。如果操作与窗口不关联，则此值可以为空 |
| lpOperation  | 指定要执行的操作（谓词）**edit**：启动编辑器并打开文档进行编辑。要打开的文档文件由lpFile指定**explore**：浏览由参数lpFile指定的文件夹**find：**搜索由参数lpDirectory指定的目录**open**：打开lpFile参数指定的项。可以是文件或文件夹，或者是网页。**print**：打印由lpFile指定的文件。**NULL**：默认操作。如果没有，则使用“**open**”动词。如果“open”不可用，系统将使用注册表中列出的第一个谓词。 |                                                    |
| lpFile       | 操作对象（文件等。。。）                                     |                                                    |
| lpParameters | 如果lpFile指定可执行文件，则此参数是指向以-结束的字符串的指针，该字符串指定要传递给应用程序的参数。此字符串的格式由要调用的谓词决定。如果lpFile指定文档文件，则lpParameters应为空。 |                                                    |
| lpDirectory  | 指定操作的默认(工作)目录。如果此值为NULL，则使用当前工作目录。 |                                                    |
| nShowCmd     | 指定打开应用程序时如何显示的标志。如果lpFile指定文档文件，则只需将标志传递给关联的应用程序。应该由应用程序来决定如何处理它。这些值是定义的。SW_HIDE：隐藏SW_MAXIMIZE ：最大化SW_MINIMIZE ：最小化。。。 |                                                    |
| 返回值       | 如果函数成功，则返回大于32的值。如果函数失败，它将返回一个错误值 |                                                    |
| 备注         | 更多详见：https://docs.microsoft.com/en-us/windows/desktop/api/Shellapi/nf-shellapi-shellexecutea![img](../../../我的文档/Typora/Pictures/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25hbmZlaWJ1eWk=,size_16,color_FFFFFF,t_70-16412995906633.png) |                                                    |

 

​    打开指定网页：

```
ShellExecute(0, "open", "https://www.baidu.com/",0, 0, 1);//最后的参数是控制最大化、最小化，隐藏
```

 

​    打开某个可执行文件：

[![复制代码](../../../我的文档/Typora/Pictures/copycode-16412995906656.gif)](javascript:void(0);)

```
#include <stdio.h>
#include <windows.h> //ShellExecute() 
 
int main(int argc, char *argv[])
{
    ShellExecute(0, "open", "C:\\Users\\newuser\\Desktop\\串口助手.exe",0, 0, 1);//最后的参数是控制最大化、最小化
    printf("Hello World!\n");
    return 0;
}
```

[![复制代码](../../../我的文档/Typora/Pictures/copycode-16412995906656.gif)](javascript:void(0);)

 

 ![img](../../../我的文档/Typora/Pictures/20181222110154260.gif)

​     

   **5.2 mouse_event()函数** 

| 功能        | 合成鼠标运动和按钮单击。(模拟鼠标动作)                       |                                                              |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 头文件      | Windows.h                                                    |                                                              |
| 原型        | void mouse_event(  DWORD   dwFlags,  DWORD   dx,  DWORD   dy,  DWORD   dwData,  ULONG_PTR dwExtraInfo ); |                                                              |
| 参数        | dwFlags                                                      | 控制鼠标运动和按钮点击的各个方面（鼠标动作类型）**MOUSEEVENTF_LEFTDOWN：鼠标左键按下****MOUSEEVENTF_LEFTUP：鼠标左键抬起****MOUSEEVENTF_RIGHTDOWN：鼠标右键按下****MOUSEEVENTF_RIGHTUP：鼠标右键抬起****MOUSEEVENTF_WHEEL：鼠标滚轮，数值由参数***dwData指定***MOUSEEVENTF_ABSOLUTE：鼠标光标位置，由参数dx,dy指定。** |
| dx          | x坐标                                                        |                                                              |
| dy          | y坐标                                                        |                                                              |
| dwData      | 滚轮滚动值                                                   |                                                              |
| dwExtraInfo | 与鼠标事件关联的附加值。调用GetMessageExtraInfo()以获取此额外信息 |                                                              |
| 返回值      | 无返回值                                                     |                                                              |
| 备注        | 详见：https://docs.microsoft.com/en-us/windows/desktop/api/winuser/nf-winuser-mouse_event![img](../../../我的文档/Typora/Pictures/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25hbmZlaWJ1eWk=,size_16,color_FFFFFF,t_70-16412995906644.png) |                                                              |

示例一：将鼠标移动到指定(绝对)位置（x,y）

```
//例子中屏幕分辨率1600x900  在鼠标坐标系统中，屏幕在水平和垂直方向上均匀分割成65535×65535个单元
mouse_event(MOUSEEVENTF_ABSOLUTE|MOUSEEVENTF_MOVE, x*65535/1600, y*65535/900, 0, 0);
```

示例二：按下鼠标左键，然后抬起

```
mouse_event(MOUSEEVENTF_LEFTDOWN,0,0,0,0);//鼠标左键按下 
mouse_event(MOUSEEVENTF_LEFTUP,0,0,0,0);//鼠标左键抬起
```

 

 

   **5.3 keybd_event()函数**

| 功能        | 合成击键。（模拟键盘输入）                                   |                  |
| ----------- | ------------------------------------------------------------ | ---------------- |
| 头文件      | Windows.h                                                    |                  |
| 原型        | void keybd_event( BYTE bVk, BYTE bScan, DWORD dwFlags, ULONG_PTR dwExtraInfo ); |                  |
| 参数        | bVk                                                          | 虚拟按键代码编号 |
| bScan       | 按键的的硬件扫描代码                                         |                  |
| dwFlags     | 控制功能操作的各个方面**KEYEVENTF_EXTENDEDKEY：**如果指定，扫描代码前面有一个前缀字节，其值为0xE0**KEYEVENTF_KEYUP：**如果指定，则抬起按键。 |                  |
| dwExtraInfo | 按键动作的附加信息                                           |                  |
| 返回值      | 无返回值                                                     |                  |
| 备注        | 详见：https://docs.microsoft.com/en-us/windows/desktop/api/winuser/nf-winuser-keybd_event![img](../../../我的文档/Typora/Pictures/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25hbmZlaWJ1eWk=,size_16,color_FFFFFF,t_70-16412995906655.png) |                  |

 

示例三：模拟按下数字按键"9":    （‘9’的和0x39都表示数字按键9）

[![复制代码](../../../我的文档/Typora/Pictures/copycode-16412995906656.gif)](javascript:void(0);)

```
keybd_event('9',0,0,0);//按下按键 ‘9’
keybd_event('9',0,2,0);//抬起按键 ‘9’
 
或  0x39
 
keybd_event(0x39,0,0,0);//按下按键 ‘9’
keybd_event(0x39,0,2,0);//抬起按键 ‘9’
```

[![复制代码](../../../我的文档/Typora/Pictures/copycode-16412995906656.gif)](javascript:void(0);)

 