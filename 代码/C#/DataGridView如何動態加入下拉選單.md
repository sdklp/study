### [C#][WinForm]DataGridView如何動態加入下拉選單

[C#][WinForm]DataGridView如何動態加入下拉選單



初始畫面:

[![image](https://dotblogsfile.blob.core.windows.net/user/ricochen/1006/CWinFormDataGridViewCombobox_27F9/image_thumb.png)](https://dotblogsfile.blob.core.windows.net/user/ricochen/1006/CWinFormDataGridViewCombobox_27F9/image_2.png)

User希望ZIPCODE可以改成下拉選單，並顯示地區名稱。

 

完成畫面:

[![image](https://dotblogsfile.blob.core.windows.net/user/ricochen/1006/CWinFormDataGridViewCombobox_27F9/image_thumb_3.png)](https://dotblogsfile.blob.core.windows.net/user/ricochen/1006/CWinFormDataGridViewCombobox_27F9/image_8.png)

[![image](https://dotblogsfile.blob.core.windows.net/user/ricochen/1006/CWinFormDataGridViewCombobox_27F9/image_thumb_1.png)](https://dotblogsfile.blob.core.windows.net/user/ricochen/1006/CWinFormDataGridViewCombobox_27F9/image_4.png)

 

 

[![image](https://dotblogsfile.blob.core.windows.net/user/ricochen/1006/CWinFormDataGridViewCombobox_27F9/image_thumb_2.png)](https://dotblogsfile.blob.core.windows.net/user/ricochen/1006/CWinFormDataGridViewCombobox_27F9/image_6.png)

 

initData:

```
 private void initData(string strconn)
        {
          DataTable dt=new DataTable ();
          using( SqlConnection conn = new SqlConnection( strconn ) )
          {
              using( SqlDataAdapter da = new SqlDataAdapter( "select * from dbo.userm", conn ) )
              {
                  try
                  {                           
                      da.Fill( dt );
                  }
                  catch( SqlException sqlex )
                  {
                      throw new Exception( sqlex.Message );
                  }
                  catch( Exception ex )
                  {
                      throw new Exception( ex.Message );
                  }         
              }                    
          }
          dataGridView1.DataSource = dt;           
        }
```

 



 

FormatColumn:

```
private void FormatColumn( string strconn )
        {
            dataGridView1.Columns.Remove( "ZIPCODE" ); 
            DataGridViewComboBoxColumn combo = new DataGridViewComboBoxColumn();
            DataTable dt = new DataTable();  
            using( SqlConnection conn = new SqlConnection( strconn ) )
            {
                using( SqlDataAdapter da = new SqlDataAdapter( "select * from dbo.codes", conn ) )
                {
                    try
                    {                             
                        da.Fill( dt );
 
                    }
                    catch( SqlException sqlex )
                    {
                        throw new Exception( sqlex.Message );
                    }
                    catch( Exception ex )
                    {
                        throw new Exception( ex.Message );
                    }     
                }
                           
            }
            combo.DisplayIndex = 1;
            combo.HeaderText = "ZIPCODE";
            combo.DataPropertyName = "ZIPCODE";//資料行名稱
            combo.DisplayMember = "ZONENAME";//顯示清單選項內容
            combo.ValueMember = "ZIPCODE"; //清單選項對應的值
            combo.DataSource = dt;
            dataGridView1.Columns.Insert( 1, combo );           
        }        
```

 