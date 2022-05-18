1、将文件以二进制流的格式写入数据库

首先获得文件路径，然后将文件以二进制读出保存在一个二进制数组中，与数据库建立连接，在SQL语句中将二进制数组赋值给相应的参数，完成向数据库中写入文件的操作

```c#
    /// 将文件流写入数据库  
    /// </summary>  
    /// <param name="filePath">存入数据库文件的路径</param>  
    /// <param name="id">数据库中插入文件的行标示符ID</param>  
    /// <returns></returns>  
    public int UploadFile(string filePath, string id)  
    {  
        byte[] buffer = null;  
        int result = 0;  
        if (!string.IsNullOrEmpty(filePath))  
        {  
            String file = HttpContext.Current.Server.MapPath(filePath);   
            buffer = File.ReadAllBytes(file);  
            using (SqlConnection conn = new SqlConnection(DBOperator.ConnString))  
            {  
                using (SqlCommand cmd = conn.CreateCommand())  
                {  
                    cmd.CommandText = "update DomesticCompanyManage_Main_T set ZBDocumentFile = @fileContents where MainID ='" + id + "'";;  
                    cmd.Parameters.AddRange(new[]{  
                    new SqlParameter("@fileContents",buffer)  
                });  
                    conn.Open();  
                    result = cmd.ExecuteNonQuery();  
                    conn.Close();  
                }  
            }  
            return result;  
        }  
        else  
            return 0;  
    }  
```


2、从数据库中将文件读出并建立相应格式的文件

从数据库中读取文件，只需根据所需的路径建立相应的文件，然后将数据库中存放的二进制流写入新建的文件就可以了

如果该目录下有同名文件，则会将原文件覆盖掉

```c#
    //从数据库中读取文件流  
    //shipmain.Rows[0]["ZBDocument"],文件的完整路径  
    //shipmain.Rows[0]["ZBDocumentFile"],数据库中存放的文件流  
    if (shipmain.Rows[0]["ZBDocumentFile"] != DBNull.Value)  
    {  
        int arraySize = ((byte[])shipmain.Rows[0]["ZBDocumentFile"]).GetUpperBound(0);  
        FileStream fs = new FileStream(HttpContext.Current.Server.MapPath(shipmain.Rows[0]["ZBDocument"].ToString()), FileMode.OpenOrCreate, FileAccess.Write);//由数据库中的数据形成文件  
        fs.Write((byte[])shipmain.Rows[0]["ZBDocumentFile"], 0, arraySize);  
        fs.Close();  
    }   
```