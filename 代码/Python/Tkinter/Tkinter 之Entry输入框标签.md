一、参数说明

| 语法                            | 作用                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| Entry（root,width=20）          | 组件的宽度（所占字符个数）                                   |
| Entry（root,fg='blue'）         | 前景字体颜色                                                 |
| Entry（root,bg='blue'）         | 背景颜色                                                     |
| Entry（root,show="*"）          | 将Entry框中的文本替换为指定字符                              |
| Entry（root,state=readonly）    | 设置组件状态，默认为normal，可设置为：disabled—禁用组件，readonly—只读 |
| Entry（root,textvariable=text） | 指定变量，需要事先定义一个变量，在Entry进行绑定获取变量的值 text=tk.StringVar() |

二、代码示例

```python
import tkinter as tk

window = tk.Tk()

def main():
    global window
    # 设置主窗体大小
    winWidth = 600
    winHeight = 400
    # 获取屏幕分辨率
    screenWidth = window.winfo_screenwidth()
    screenHeight = window.winfo_screenheight()
    # 计算主窗口在屏幕上的坐标
    x = int((screenWidth - winWidth)/ 2)
    y = int((screenHeight - winHeight) / 2)
    
    # 设置主窗口标题
    window.title("Entry输入框参数说明")
    # 设置主窗口大小
    window.geometry("%sx%s+%s+%s" % (winWidth, winHeight, x, y))
    # 设置窗口宽高固定
    window.resizable(0,0)
    # 设置窗口图标
    window.iconbitmap("./image/icon.ico")
    
    """entry参数.

        Valid resource names: background, bd, bg, borderwidth, cursor,
        exportselection, fg, font, foreground, highlightbackground,
        highlightcolor, highlightthickness, insertbackground,
        insertborderwidth, insertofftime, insertontime, insertwidth,
        invalidcommand, invcmd, justify, relief, selectbackground,
        selectborderwidth, selectforeground, show, state, takefocus,
        textvariable, validate, validatecommand, vcmd, width,
        xscrollcommand."""
    var = tk.StringVar()
    var.set("请输入密码")
    # 当鼠标移入输入框时，执行validatecommand
    tk.Entry(window, width=30, borderwidth=1, fg="#f00",insertwidth=1,
             insertbackground="#333", state=tk.NORMAL,
             textvariable=var, validate="focus", validatecommand=valid).pack()

    window.mainloop()

def valid():
    print("valid")

if __name__ == '__main__':
    main()
```





　　

三、效果图

![Tkinter 之Entry输入框标签_Python](https://s3.51cto.com/images/blog/202107/21/fda83c73aa4e4f3c42347d457d498afd.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

 