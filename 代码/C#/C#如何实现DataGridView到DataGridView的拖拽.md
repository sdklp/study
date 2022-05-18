# C#如何实现DataGridView到DataGridView的拖拽

今天工作中遇到一个问题，需要将一个DataGridView中的某一行拖拽到另一个DataGridView中，在网上搜了一遍，大多是从DataGridView拖拽到TextBox等控件，没有拖拽到
DataGridView中的。拖拽到TextBox很容易，但拖拽到DataGridView就有一个问题：如何决定拖拽到DataGridView中的哪一个Cell？
为此研究了两个小时，终于找到了答案。
例如要实现从gridSource到gridTarget的拖拽，需要一个设置和三个事件：
1、设置gridTarget的属性AllowDrop为True
2、实现gridSource的MouseDown事件，在这里进行要拖拽的Cell内容的保存，保存到剪贴板。
3、实现gridTarget的DragDrop和DragEnter事件，DragDrop事件中的一个难点就是决定拖拽到哪一个Cell

代码如下：

gridSource的MouseDown事件：


private void gridSource_MouseDown(object sender, MouseEventArgs e)
{
   if (e.Button == MouseButtons.Left)
   {
     DataGridView.HitTestInfo info = this.gridSource.HitTest(e.X, e.Y);
     if (info.RowIndex >= 0)
     {
       if (info.RowIndex >= 0 && info.ColumnIndex >= 0)
       {
         string text = (String)this.gridSource.Rows[info.RowIndex].Cells[info.ColumnIndex].Value;
          if (text != null)
          {
            this.gridSource.DoDragDrop(text, DragDropEffects.Copy);
           }
        }
      }
    }
 }



 

gridTarget的DragDrop事件：


private void gridTarget_DragDrop(object sender, DragEventArgs e)
{
    //得到要拖拽到的位置
   Point p = this.gridTarget.PointToClient(new Point(e.X, e.Y));
   DataGridView.HitTestInfo hit = this.gridTarget.HitTest(p.X, p.Y);
   if (hit.Type == DataGridViewHitTestType.Cell)
   {
      DataGridViewCell clickedCell = this.gridTarget.Rows[hit.RowIndex].Cells[hit.ColumnIndex];
      clickedCell.Value = (System.String)e.Data.GetData(typeof(System.String));
  　　　//如果只想允许拖拽到某一个特定列，比如Target Field Expression，则先要判断列是否为Target Field Expression，如下：
       //if (0 == string.Compare(clickedCell.OwningColumn.Name, "Target Field Expression"))
       //{
       //  clickedCell.Value = (System.String)e.Data.GetData(typeof(System.String));
       //}
    }
}



 

gridTarget的DragEnter事件：


private void gridTarget_DragEnter(object sender, DragEventArgs e)
{
   e.Effect = DragDropEffects.Copy;
}