## C# 判断窗口是否打开

## 推荐

```c#
        #region 判断窗体是否加载，如已加载激活并显示能前端，如未加载，实例并显示到前端
        /// <summary>
        /// 打开MDI子窗口并附加到MDI主窗口中，如果MDI主窗口中已经存在相同类型的子窗口，则直接激活
        /// </summary>
        /// <typeparam name="T">MDI子窗体类型</typeparam>
        /// <param name="mdiParent">MDI主窗体引用</param>
        /// <returns>当前创建或得到的MDI子窗体类型实例的引用</returns>
        private T ShowChildForm<T>(Form mdiParent=null) where T : Form, new()
        {
            /*调用方法

              * ShowChildForm<DeviceFrm>(this);MDI窗口调用
              * ShowChildForm<DeviceFrm>();普通窗体调用
              */

                foreach (Form subForm in Application.OpenForms)
                {
                        if (subForm.GetType().Equals(typeof(T)))
                        {
                            subForm.Activate();
                            subForm.BringToFront();
                            return subForm as T;
                        }
        		}
                T newForm = new T();
                if (mdiParent!=null)
                {
                    newForm.MdiParent = mdiParent;
                    this.splMain.Panel2.Controls.Add(newForm);
                }           
                newForm.Show();
                newForm.BringToFront();
                return newForm;
    	}
    	#endregion
```













/// <summary>
    /// 检查窗口是否已经打开
    /// </summary>
    /// <param name="asFormName">窗口名称</param>
    /// <returns></returns>
    private bool CheckFormIsOpen(string asFormName)
    {
      bool bResult = false;
      foreach (Form frm in Application.OpenForms)
      {
        if (frm.Name == asFormName)
        {
          bResult = true;
          break;
        }
      }
      return bResult;
    }

\--------------------------------------------------------------------------------------------

如果是子窗体：



1.定义一个全局变量，定义窗体的全局变量Form  form=null。

2判断窗体是否为null；如果是则new 窗体，不为null，则show（）；

```c#
private bool HaveOpened(Form myMdi,string windowName) {
//查看窗口是否已经被打开
bool bReturn=true;
for(int i=0;i<myMdi.MdiChildren.Length;i++)
{
//MessageBox.Show(myMdi.MdiChildren[i].Name);
if(myMdi.MdiChildren[i].Name==windowName)
{
myMdi.MdiChildren[i].BringToFront();
bReturn=false;
break;
}
}
return bReturn;
}
```

打开窗口的时候：

```c#
ProcessesManage w0=new ProcessesManage();
if(HaveOpened(myMdi,"ProcessesManage"))
{
w0.MdiParent=myMdi;
w0.Show();
}
```

这里的myMdi，是Form类型的，传递的是mdi窗口



编程的时候经常碰到这个问题，而且在网上看到许多人问，自己也不是很清楚C#如何编程，刚好遇到了就调试了一下：
注：看似简单其实我也调了十几分钟，因为两次调用login不是在同一个窗体中；所以要注意先顶以一个公共类在主窗体mainform中：

```c#
public class aa //定义全局变量
    {
       public static login sqlconnect;

​    }
```

创建一个窗体login，在loading时把 login实例化 ，然后打开login， loading窗体关闭  loading里的代码：
     MainForm.aa.sqlconnect = new login();//login是一个windows窗体，在项目——添加winsows窗体
      MainForm.aa.sqlconnect.MdiParent = this.MdiParent;//将login实例的父窗体设置和loading一样
      MainForm.aa.sqlconnect.Show();//show 实例过的login

在另外一个窗口中判断sqlconnect是否打开 的代码：

//因为MainForm.aa.sqlconnect已经实例化了，就不用在实例化了
    

```c#
  if (MainForm.aa.sqlconnect.IsDisposed )
      {
        MainForm.aa.sqlconnect = new login();
        MainForm.aa.sqlconnect.MdiParent = this;
        MainForm.aa.sqlconnect.Show();
      }
      else
      {
        //sqlconnect.Dispose ();
        MainForm.aa.sqlconnect.Show ();

​      }  
```

