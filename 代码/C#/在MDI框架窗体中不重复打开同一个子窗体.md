在MDI框架窗体中不重复打开同一个子窗体：

```
     FormChild formChild = new FormChild();//实例化FormChild子窗体
     bool isOpened = false;//定义子窗体打开标记，默认值为false
     foreach (Form form in this.MdiChildren)//循环MDI中的所有子窗体
     {
         if (formChild.Name == form.Name)//若该子窗体已被打开
         {
             formChild.Activate();//激活该窗体
             formChild.StartPosition = FormStartPosition.CenterParent;
             formChild.WindowState = FormWindowState.Normal;
             isOpened = true;//设置子窗体的打开标记为true
             formChild.Dispose();//销毁formChild实例
             break;
         }
     }
     if (!isOpened)//若该子窗体未打开，则显示该子窗体
     {
         formChild.MdiParent = this;
         formChild.Show();
     }
```