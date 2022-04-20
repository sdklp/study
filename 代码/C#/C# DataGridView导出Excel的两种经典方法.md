# C# DataGridView导出Excel的两种经典方法

第一种是用数据流导出:

```csharp
using System.IO;
#region
            SaveFileDialog saveFileDialog = new SaveFileDialog();
            saveFileDialog.Filter = "Execl files (*.xls)|*.xls";
            saveFileDialog.FilterIndex = 0;
            saveFileDialog.RestoreDirectory = true;
            saveFileDialog.CreatePrompt = true;
            saveFileDialog.Title = "Export Excel File";
            saveFileDialog.ShowDialog();
            if (saveFileDialog.FileName == "")
                return;
            Stream myStream;
            myStream = saveFileDialog.OpenFile();
            StreamWriter sw = new StreamWriter(myStream, System.Text.Encoding.GetEncoding(-0));
            string str = "";
            try
            {
                for (int i = 0; i < dataGridView1.ColumnCount; i++)
                {
                    if (i > 0)
                    {
                        str += "\t";
                    }
                    str += dataGridView1.Columns[i].HeaderText;
                }
                sw.WriteLine(str);
                for (int j = 0; j < dataGridView1.Rows.Count; j++)
                {
                    string tempStr = "";
                    for (int k = 0; k < dataGridView1.Columns.Count; k++)
                    {
                        if (k > 0)
                        {
                            tempStr += "\t";
                        }
                        tempStr += dataGridView1.Rows[j].Cells[k].Value.ToString();
                    }
                    sw.WriteLine(tempStr);
                }
                sw.Close();
                myStream.Close();
            }
            catch (Exception ex)
            {
                //MessageBox.Show(ex.ToString());
            }
            finally
            {
                sw.Close();
                myStream.Close();
            }
#endregion
```



这种方法的优点就是导出比较快，但是excel的表格里面设置标题，字体样式等都不能弄，因为你是用数据流直接导出为excel的，除非你能在数据流中设置这些样式，这个我还没这本事，哪位大哥会的教一下...嘿嘿



第二种方法是直接写一个类，这个类直接操作EXCEL的：

```csharp
using System;
using System.Collections.Generic;
using System.Windows.Forms;
using System.Text;
using System.Diagnostics;
using System.IO;
using Microsoft.Office.Interop.Excel;
namespace Excel
{
    public class Export
    {
        /// <summary>
        /// DataGridView导出Excel
        /// </summary>
        /// <param name="strCaption">Excel文件中的标题</param>
        /// <param name="myDGV">DataGridView 控件</param>
        /// <returns>0:成功;1:DataGridView中无记录;2:Excel无法启动;9999:异常错误</returns>
        public int ExportExcel(string strCaption, DataGridView myDGV, SaveFileDialog saveFileDialog)
        {
            int result = 9999;         
            //保存        
            saveFileDialog.Filter = "Execl files (*.xls)|*.xls";
            saveFileDialog.FilterIndex = 0;
            saveFileDialog.RestoreDirectory = true;
            //saveFileDialog.CreatePrompt = true;
            saveFileDialog.Title = "Export Excel File";
            if ( saveFileDialog.ShowDialog()== DialogResult.OK)
            {
                if (saveFileDialog.FileName == "")
                {
                    MessageBox.Show("请输入保存文件名！");
                    saveFileDialog.ShowDialog();
                }
                    // 列索引，行索引，总列数，总行数
                int ColIndex = 0;
                int RowIndex = 0;
                int ColCount = myDGV.ColumnCount;
                int RowCount = myDGV.RowCount;
                if (myDGV.RowCount == 0)
                {
                    result = 1;
                }
                // 创建Excel对象
                Microsoft.Office.Interop.Excel.Application xlApp = new Microsoft.Office.Interop.Excel.ApplicationClass();
                if (xlApp == null)
                {
                    result = 2;
                }
                try
                {
                    // 创建Excel工作薄
                    Microsoft.Office.Interop.Excel.Workbook xlBook = xlApp.Workbooks.Add(true);
                    Microsoft.Office.Interop.Excel.Worksheet xlSheet = (Microsoft.Office.Interop.Excel.Worksheet)xlBook.Worksheets[1];
                    // 设置标题
                    Microsoft.Office.Interop.Excel.Range range = xlSheet.get_Range(xlApp.Cells[1, 1], xlApp.Cells[1, ColCount]); //标题所占的单元格数与DataGridView中的列数相同
                    range.MergeCells = true;
                    xlApp.ActiveCell.FormulaR1C1 = strCaption;
                    xlApp.ActiveCell.Font.Size = 20;
                    xlApp.ActiveCell.Font.Bold = true;
                    xlApp.ActiveCell.HorizontalAlignment = Microsoft.Office.Interop.Excel.Constants.xlCenter;
                    // 创建缓存数据
                    object[,] objData = new object[RowCount + 1, ColCount];
                    //获取列标题
                    foreach (DataGridViewColumn col in myDGV.Columns)
                    {
                        objData[RowIndex, ColIndex++] = col.HeaderText;
                    }
                    // 获取数据
                    for (RowIndex = 1; RowIndex < RowCount; RowIndex++)
                    {
                        for (ColIndex = 0; ColIndex < ColCount; ColIndex++)
                        {
                            if (myDGV[ColIndex, RowIndex - 1].ValueType == typeof(string)
                                || myDGV[ColIndex, RowIndex - 1].ValueType == typeof(DateTime))//这里就是验证DataGridView单元格中的类型,如果是string或是DataTime类型,则在放入缓存时在该内容前加入" ";
                            {
                                objData[RowIndex, ColIndex] = "" + myDGV[ColIndex, RowIndex - 1].Value;
                            }
                            else
                            {
                                objData[RowIndex, ColIndex] = myDGV[ColIndex, RowIndex - 1].Value;
                            }
                        }
                        System.Windows.Forms.Application.DoEvents();
                    }
                    // 写入Excel
                    range = xlSheet.get_Range(xlApp.Cells[2, 1], xlApp.Cells[RowCount, ColCount]);
                    range.Value2 = objData;
                    xlBook.Saved = true;
                    xlBook.SaveCopyAs(saveFileDialog.FileName);
                }
                catch (Exception err)
                {
                    result = 9999;
                }
                finally
                {
                    xlApp.Quit();
                    GC.Collect(); //强制回收
                }
                //返回值
                result = 0;
            }         
            return result;
        }
    }
}
```



这个优点是能设置样式什么的。缺点就是导出速度慢...

以上两种方法都是参考了很多料的..写在这里以便于相互学习..

补充一下：用第二种方法导出excel会有格式方面的变化，比如身份证号码按科学计算法导出了，不是按原先的模型

改进方法是在写入excel时添加一个格式声明：range.NumberFormatLocal = "@";

如：// 写入Excel

```csharp
range = xlSheet.get_Range(xlApp.Cells[2, 1], xlApp.Cells[RowCount + 2, ColCount]);



range.NumberFormatLocal = "@";



range.Value2 = objData;
```