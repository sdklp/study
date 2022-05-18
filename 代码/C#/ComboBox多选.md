**ComboBox多选**
新建一个类ComboBoxEx.cs编译后在工具箱里会出现自定义控件。
拖放上去即可和普通ComboBox一样使用，按住Ctrl键可以多选。
![img](https://images.cnblogs.com/cnblogs_com/greatverve/combobox.PNG)
代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

using System;
using System.Collections;
using System.ComponentModel;
using System.Drawing;
using System.Data;
using System.Windows.Forms;
namespace ComboBoxEx
{
 public class ComboBoxEx:ComboBox
 {
 ListBox lst=new ListBox();
 public ComboBoxEx()
 {
  lst.SelectionMode=SelectionMode.MultiExtended;
  this.DrawMode=DrawMode.OwnerDrawFixed;//只有设置这个属性为OwnerDrawFixed才可能让重画起作用
  lst.KeyUp+=new KeyEventHandler(lst_KeyUp);
  lst.MouseUp+=new MouseEventHandler(lst_MouseUp);
  lst.KeyDown+=new KeyEventHandler(lst_KeyDown);
 }
 \#region Property
 [Description("选定项的值"),Category("Data")]
 public ListBox.SelectedObjectCollection SelectedItems
 {
  get
  {
  return lst.SelectedItems;
  }
 }
 \#endregion
 \#region override
 protected override void OnKeyUp(KeyEventArgs e)
 {
  base.OnKeyDown(e);
  bool Pressed=(e.Control && ((e.KeyData & Keys.A)==Keys.A));
  if(Pressed)
  {
  for(int i=0;i<lst.Items.Count;i++)
   lst.SetSelected(i,true);
  }
 }
 protected override void OnMouseDown(MouseEventArgs e)
 {
  this.DroppedDown=false;
  
 }
 protected override void OnMouseUp(MouseEventArgs e)
 {
  this.DroppedDown=false;
  lst.Focus();
 }
 protected override void OnDropDown(EventArgs e)
 {
  lst.Items.Clear();
  lst.Show();
  lst.ItemHeight=this.ItemHeight;
  lst.BorderStyle=BorderStyle.FixedSingle;
  lst.Size=new Size(this.DropDownWidth,this.ItemHeight*(this.MaxDropDownItems-1)-(int)this.ItemHeight/2);
  lst.Location=new Point(this.Left,this.Top+this.ItemHeight+6);
  lst.BeginUpdate();
  for(int i=0;i<this.Items.Count;i++)  
  lst.Items.Add(this.Items[i]);
  lst.EndUpdate();
  
  this.Parent.Controls.Add(lst);
 }
 \#endregion
 private void lst_KeyUp(object sender, KeyEventArgs e)
 {
  this.OnKeyUp(e);
 }
 private void lst_MouseUp(object sender, MouseEventArgs e)
 {
  try
  {
  this.Text="";
  for(int i=0;i<lst.SelectedItems.Count;i++)
  {
   if(i==0)
   this.Text=lst.SelectedItems[i].ToString();
   else 
   {
   this.Text=this.Text+","+lst.SelectedItems[i].ToString();
   }
  }
  }
  catch
  {
  this.Text="";
  }
  bool isControlPressed=(Control.ModifierKeys==Keys.Control);
  bool isShiftPressed=(Control.ModifierKeys==Keys.Shift);
  if(isControlPressed || isShiftPressed)
  lst.Show();
  else
  lst.Hide();
 }
 private void lst_KeyDown(object sender, KeyEventArgs e)
 {
  switch(e.KeyData)
  {
  case Keys.Down:
   if(lst.SelectedItems.Count!=0)
   {
   this.Text=lst.SelectedItem.ToString();
   }
   else
   this.Text=this.Items[0].ToString();
   break;
  case Keys.Up:
   if(lst.SelectedItems.Count!=0)
   {
   this.Text=lst.SelectedItem.ToString();
   }
   else
   this.Text=this.Items[0].ToString();
   break;
  }
 }
 }
}

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**在ComboBox下拉列表显示图片**

定义 private System.Windows.Forms.ComboBox comboBox1;
 private System.Windows.Forms.ImageList imageList1;comboBox1下面两个属性一定要设为下面的值。DrawMode：OwnerDrawFixed； 
DropDownStyle：DropDownList；imageList1中一定要包函与所添加的项相同数目的图关键方法，此方法为comboBox1的DrawItem事件所引起的方法。private void comboBox1_DrawItem(object sender, System.Windows.Forms.DrawItemEventArgs e)
 {
  Graphics g = e.Graphics ; 
  Rectangle r = e.Bounds ; 
  Size imageSize = imageList1.ImageSize; 
  Font fn = null ; 
  if ( e.Index >= 0 ) 
  { 
  fn = (Font)fontArray[0]; 
  string s = (string)comboBox1.Items[e.Index]; 
  StringFormat sf = new StringFormat(); 
  sf.Alignment = StringAlignment.Near; 
  if ( e.State == ( DrawItemState.NoAccelerator | DrawItemState.NoFocusRect)) 
  { 
   //画条目背景 
   e.Graphics.FillRectangle(new SolidBrush(Color.Red) , r); 
   //绘制图像 
   imageList1.Draw(e.Graphics, r.Left, r.Top,e.Index); 
   //显示字符串 
   e.Graphics.DrawString( s , fn , new SolidBrush(Color.Black), r.Left+imageSize.Width ,r.Top); 
   //显示取得焦点时的虚线框 
   e.DrawFocusRectangle(); 
  } 
  else 
  { 
   e.Graphics.FillRectangle(new SolidBrush(Color.LightBlue) , r); 
   imageList1.Draw(e.Graphics, r.Left, r.Top,e.Index); 
   e.Graphics.DrawString( s , fn , new SolidBrush(Color.Black),r.Left+imageSize.Width ,r.Top); 
   e.DrawFocusRectangle(); 
  } 
  } } 向combobox中添加数据comboBox1.Items.Add("小车"); 
  comboBox1.Items.Add("视频"); 
  comboBox1.Items.Add("信号灯"); 