# C# DataGridView导出excel的几种方法

第一种、首先本form上游datagridview的控件及数据，再建一个button控件作为导出按钮，在按钮click事件中写入以下代码

此乃数据集的方式，即先将数据放入数据集表里 作为对象与excel一一对应



```
 //保存文件对话框
            SaveFileDialog sfd = new SaveFileDialog();
            sfd.Filter = "Excel文件|*.xlsx|Word文件|*.docx";
            sfd.FilterIndex = 0;
            if (sfd.ShowDialog() == DialogResult.OK)
            {
                string search = "select * from 旧备件表 where(0=0)";
                if (this.textBox1.Text.Length > 0)
                {
                    search = search + " and 物料编码=" + "'" + textBox1.Text + "'";
                }
                if (this.textBox2.Text.Length > 0)
                {
                    search = search + " and 设备号=" + "'" + textBox2.Text + "'";
                }
                if (this.textBox3.Text.Length > 0)
                {
                    search = search + " and 物料描述 like" + "'%" + textBox3.Text + "%'";//实现物料描述的模糊查询
                }
                if (this.textBox4.Text.Length > 0)
                {
                    search = search + " and 备件序列号 like" + "'%" + textBox4.Text + "%'";//实现备件序列号的模糊查询
                }
                //调用导出Excel的方法，传入DataTable数据表和路径
                SqlDataAdapter sda = new SqlDataAdapter(search, DataBase.GetSqlConnection());
                System.Data.DataTable dt = new System.Data.DataTable();
                //将数据库中查到的数据填充到DataTable数据表
                sda.Fill(dt);
                ExportExcel(dt, sfd.FileName);
            }
        }
        void ExportExcel(System.Data.DataTable dt, string filepath)
        {
            //创建Excel应用程序类的一个实例，相当于从电脑开始菜单打开Excel
            ApplicationClass xlsxapp = new ApplicationClass();
            //新建一张Excel工作簿
            Workbook wbook = xlsxapp.Workbooks.Add(true);
            //第一个sheet页
            Worksheet wsheet = (Worksheet)wbook.Worksheets.get_Item(1);
            //将DataTable的列名显示在Excel表第一行
            for (int i = 0; i < dt.Columns.Count; i++)
            {
                //注意Excel表的行和列的索引都是从1开始的
                wsheet.Cells[1, i + 1] = dt.Columns[i].ColumnName;
            }
            //遍历DataTable，给Excel赋值
            for (int i = 0; i < dt.Rows.Count; i++)
            {
                for (int j = 0; j < dt.Columns.Count; j++)
                {
                    //从第二行第一列开始写入数据
                    wsheet.Cells[i + 2, j + 1] = dt.Rows[i][j];
                }
            }
            //保存文件
            wbook.SaveAs(filepath);
            //释放资源
            xlsxapp.Quit();
        }
    
```



第二种、此乃直接将datagridview里的数据一一导出放入excel指定的单元格里。



```
private void button3_Click(object sender, EventArgs e)
        {
            string fileName = "";
            string saveFileName = "";
            SaveFileDialog saveDialog = new SaveFileDialog();
            saveDialog.DefaultExt = "xlsx";
            saveDialog.Filter = "Excel文件|*.xlsx";
            saveDialog.FileName = fileName;
            saveDialog.ShowDialog();
            saveFileName = saveDialog.FileName;
            if (saveFileName.IndexOf(":") < 0) return; //被点了取消
            Microsoft.Office.Interop.Excel.Application xlApp = new Microsoft.Office.Interop.Excel.Application();
            if (xlApp == null)
            {
                MessageBox.Show("无法创建Excel对象，您的电脑可能未安装Excel");
                return;
            }
            Microsoft.Office.Interop.Excel.Workbooks workbooks = xlApp.Workbooks;
            Microsoft.Office.Interop.Excel.Workbook workbook = workbooks.Add(Microsoft.Office.Interop.Excel.XlWBATemplate.xlWBATWorksheet);
            Microsoft.Office.Interop.Excel.Worksheet worksheet = (Microsoft.Office.Interop.Excel.Worksheet)workbook.Worksheets[1];//取得sheet1 
            //写入标题             
            for (int i = 0; i < dataGridView1.ColumnCount; i++)
            { worksheet.Cells[1, i + 1] = dataGridView1.Columns[i].HeaderText; }
            //写入数值
            for (int r = 0; r < dataGridView1.Rows.Count; r++)
            {
                for (int i = 0; i < dataGridView1.ColumnCount; i++)
                {
```

　　　　　　　　　　if (i == 3)//将这一列有前导零的加上一个单引号
　　　　　　　　　　{
　　　　　　　　　　　　worksheet.Cells[r + 2, i + 1] = "'" + dataGridView1.Rows[r].Cells[i].Value;
　　　　　　　　　　　　string temp = "'" + dataGridView1.Rows[r].Cells[i].Value;//看下值
　　　　　　　　　　}
　　　　　　　　　　else
　　　　　　　　　　{
　　　　　　　　　　　　worksheet.Cells[r + 2, i + 1] = dataGridView1.Rows[r].Cells[i].Value;
　　　　　　　　　　}

```
             }
                System.Windows.Forms.Application.DoEvents();
            }
            worksheet.Columns.EntireColumn.AutoFit();//列宽自适应
            MessageBox.Show(fileName + "资料保存成功", "提示", MessageBoxButtons.OK);
            if (saveFileName != "")
            {
                try
                {
                    workbook.Saved = true;
                    workbook.SaveCopyAs(saveFileName);  //fileSaved = true;                 
                }
                catch (Exception ex)
                {//fileSaved = false;                      
                    MessageBox.Show("导出文件时出错,文件可能正被打开！\n" + ex.Message);
                }
            }
            xlApp.Quit();
            GC.Collect();//强行销毁           }
        }
    
```



 第三种：网上的



```
SaveFileDialog SaveDialog = new SaveFileDialog();
                    SaveDialog.Filter = "Excel 文件(*.xls)|*.xls|Excel 文件(*.xlsx)|*.xlsx|所有文件(*.*)|*.*";
                    SaveDialog.RestoreDirectory = true;
                    if (SaveDialog.ShowDialog() == DialogResult.OK)
                    {
                        GenerateAttachment(SaveDialog.FileName,dtExport);
                    }
 try
            {
                //需要添加 Microsoft.Office.Interop.Excel引用 
                Microsoft.Office.Interop.Excel.Application app = new Microsoft.Office.Interop.Excel.Application();
                if (app == null)//服务器上缺少Excel组件，需要安装Office软件
                {
                    return;
                }
                app.Visible = false;
                app.UserControl = true;
                string strTempPath = System.Windows.Forms.Application.StartupPath + "\\Template\\Form.xls";
                Microsoft.Office.Interop.Excel.Workbooks workbooks = app.Workbooks;
                Microsoft.Office.Interop.Excel._Workbook workbook = workbooks.Add(strTempPath); //加载模板
                Microsoft.Office.Interop.Excel.Sheets sheets = workbook.Sheets;
                Microsoft.Office.Interop.Excel._Worksheet worksheet = (Microsoft.Office.Interop.Excel._Worksheet)sheets.get_Item(1); //第一个工作薄。
                if (worksheet == null)//工作薄中没有工作表
                {
                    return;
                }
 
                //1、获取数据
                int rowCount = DT.Rows.Count;
                if (rowCount < 1)//没有取到数据
                {
                    return;
                }
                //2、写入数据，Excel索引从1开始
                for (int i = 1; i <= rowCount; i++)
                {
                    int row_ = 1 + i;  //Excel模板上表头占了1行
                    int dt_row = i - 1; //dataTable的行是从0开始的 
                    worksheet.Cells[row_, 1] = DT.Rows[dt_row]["itemname"].ToString();
                    worksheet.Cells[row_, 2] = DT.Rows[dt_row]["Color"].ToString();
                    worksheet.Cells[row_, 3] = DT.Rows[dt_row]["Grade1"].ToString();
                   // worksheet.Cells[row_, 4] = DT.Rows[dt_row]["ProAreaName"].ToString();
                    worksheet.Cells[row_, 4] = DT.Rows[dt_row]["Quantity"].ToString();
                    worksheet.Cells[row_, 5] = DT.Rows[dt_row]["Unit_name"].ToString();
                    worksheet.Cells[row_, 6] = DT.Rows[dt_row]["TotalAmt"].ToString();
                    
                }
                worksheet.Cells[DT.Rows.Count + 2, 1] = "合计";
                worksheet.Cells[DT.Rows.Count + 2, 4] = DT.Compute("sum(Quantity)", "");
                worksheet.Cells[DT.Rows.Count + 2, 6] = DT.Compute("sum(TotalAmt)", "");
                //调整Excel的样式。
                //Microsoft.Office.Interop.Excel.Range rg = worksheet.Cells.get_Range("A3", worksheet.Cells[rowCount + 2, 32]);
                //rg.Borders.LineStyle = 1; //单元格加边框
                //worksheet.Columns.AutoFit(); //自动调整列宽
 
                //隐藏某一行
                //选中部分单元格，把选中的单元格所在的行的Hidden属性设为true
                //worksheet.get_Range(app.Cells[2, 1], app.Cells[2, 32]).EntireRow.Hidden = true;
 
                //删除某一行
               // worksheet.get_Range(app.Cells[2, 1], app.Cells[2, 32]).EntireRow.Delete(Microsoft.Office.Interop.Excel.XlDirection.xlUp);
 
                //3、保存生成的Excel文件
                //Missing在System.Reflection命名空间下
                //string savePath = System.Windows.Forms.Application.StartupPath+"/Temp/T1_" + DateTime.Now.ToString("yyyyMMddHHmmss") + ".xls";
 
                workbook.SaveAs(FileName, Missing.Value, Missing.Value, Missing.Value, Missing.Value, Missing.Value, Microsoft.Office.Interop.Excel.XlSaveAsAccessMode.xlNoChange, Missing.Value, Missing.Value, Missing.Value, Missing.Value, Missing.Value);
                //workbook.SaveAs(FileName,FileFormat,Password,WriteResPassword,ReadOnlyRecommended,CreateBackup, AccessMode, ConflictResolution, AddToMru, TextCodepage, TextVisualLayout, Local) 
                //4、按顺序释放资源
                NAR(worksheet);
                NAR(sheets);
                NAR(workbook);
                NAR(workbooks);
                app.Quit();
                NAR(app);
                MessageBox.Show("保存成功", "提示", MessageBoxButtons.OK, MessageBoxIcon.Warning);
            }
            catch (Exception ex)
            {
               MessageBox.Show("异常，异常信息为："+ex.ToString(),"");
            }
```

