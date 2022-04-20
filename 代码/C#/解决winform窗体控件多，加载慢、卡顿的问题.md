winform窗体加载慢 

 

```
 protected override CreateParams CreateParams
  {
       get
       {
           CreateParams cp = base.CreateParams;
           cp.ExStyle |= 0x02000000; // 用双缓冲绘制窗口的所有子控件
           return cp;
       }
  }
```

将此方法放在需要优化的窗体代码中即可。如下图：

![在这里插入图片描述](../../../我的文档/Typora/Pictures/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjI4ODQzMg==,size_16,color_FFFFFF,t_70.png)

## 另一篇文章

C# 窗体程序，窗体上控件过多，会导致打开程序时窗体闪烁，下面有个不错的方法，大家可以试一试，在你的主窗体中加入如下代码：

```c#
 protected override CreateParams CreateParams
        {
            get
            {
                CreateParams cp = base.CreateParams;
                cp.ExStyle |= 0x02000000;
                return cp;
            }
        }
```

