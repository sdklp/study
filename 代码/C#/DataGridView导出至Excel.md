 public class ExportDateGridViewToExcel
    {
        #region 导出dataGridView至Excel
        public void ExportDataGridViewToExcel(DataGridView dv)
        {

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
                for (int i = 0; i < dv.ColumnCount; i++)
                {
                    if (i > 0)
                    {
                        str += "\t";
                    }
                    str += dv.Columns[i].HeaderText;
                }
                sw.WriteLine(str);
                for (int j = 0; j < dv.Rows.Count; j++)
                {
                    string tempStr = "";
                    for (int k = 0; k < dv.Columns.Count; k++)
                    {
                        if (k > 0)
                        {
                            tempStr += "\t";
                        }
                        tempStr += dv.Rows[j].Cells[k].Value.ToString();
                    }
                    sw.WriteLine(tempStr);
                }
                sw.Close();
                myStream.Close();
            }
    
            catch
            {
    
            }
            finally
            {
                sw.Close();
                myStream.Close();
            }
    
        }
        #endregion