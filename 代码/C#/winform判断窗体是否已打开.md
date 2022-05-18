# winform判断窗体是否已打开

在用父子窗体

#region 判断窗体是否打开，如已打开，显示；如未打开创建并显示；
        /// <summary>
        /// 判断窗体是否打开，如已打开，显示；如未打开创建并显示；
        /// </summary>
        /// <param name="frm">需要打开的窗体</param>
        /// <param name="splitContainer">父级SplitContainer名称</param>
        /// <param name="panel">父级SplitContainer的panel名称</param>
        /// <param name="ifPanel2Collapse">父级SplitContainer的panel2是否隐藏，默认不隐藏</param>

        private void ShowForm(Form frm,SplitContainer splitContainer, SplitterPanel panel, bool ifPanel2Collapse = false)
        {
            panel.Controls.Clear();
            foreach (Form f in this.MdiChildren)
            {
                if (f.Text == frm.Text)
                {
                    f.Activate();
                    return;
                }
            }
            frm.MdiParent = this;
            if (ifPanel2Collapse)
            {
                splitContainer.Panel2Collapsed = true;
            }
            else
            {
                splitContainer.Panel2Collapsed = false;
            }
            panel.Controls.Add(frm);
            frm.Dock = DockStyle.Fill;
            frm.Show();
            frm.TopMost = true;
        }
    
        #endregion











方式1:

```csharp
 foreach (Form frm in Application.OpenForms)
 {
    if (frm is youForm)
    {
       youForm.Activate();
       youForm.WindowState = FormWindowState.Normal;
       return;
     }
 }
 Form youForm = new Form();
 youForm.Show();
```

方式2：

```csharp
Form1 F1 ;
if(F1 == null || F1.IsDisposed)
{
   F1 = new Form1();
   F1.Show();//未打开，直接打开。
}
else
{
   F1.Activate();//已打开，获得焦点，置顶。
}
```

方式2 个人认为 如果说子窗体很多，也得实例化很多个，总感觉不太好