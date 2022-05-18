C#将文件保存到数据库中或者从数据库中读取文件
 
在编程中我们常常会遇到“将文件保存到数据库中”这样一个问题，虽然这已不是什么高难度的问题，但对于一些刚刚开始编程的朋友来说可能是有一点困难。其实，方法非常的简单，只是可能由于这些朋友刚刚开始编程不久，一时没有找到方法而已。
 
下面介绍一下使用C#来完成此项任务。
 
首先，介绍一下保存文件到数据库中。
将文件保存到数据库中，实际上是将文件转换成二进制流后，将二进制流保存到数据库相应的字段中。在SQL Server中该字段的数据类型是Image，在Access中该字段的数据类型是OLE对象。

 //保存文件到SQL Server数据库中
 FileInfo fi=new FileInfo(fileName);
 FileStream fs=fi.OpenRead();
 byte[] bytes=new byte[fs.Length];
 fs.Read(bytes,0,Convert.ToInt32(fs.Length));
 SqlCommand cm=new SqlCommand();
 cm.Connection=cn;
 cm.CommandType=CommandType.Text;
 if(cn.State==0) cn.Open();
 cm.CommandText="insert into "+tableName+"("+fieldName+") values(@file)";
 SqlParameter spFile=new SqlParameter("@file",SqlDbType.Image);
 spFile.Value=bytes;
 cm.Parameters.Add(spFile);
 cm.ExecuteNonQuery()

 //保存文件到Access数据库中

 FileInfo fi=new FileInfo(fileName);
 FileStream fs=fi.OpenRead();
 byte[] bytes=new byte[fs.Length];
 fs.Read(bytes,0,Convert.ToInt32(fs.Length));
 OleDbCommand cm=new OleDbCommand();
 cm.Connection=cn;
 cm.CommandType=CommandType.Text;
 if(cn.State==0) cn.Open();
 cm.CommandText="insert into "+tableName+"("+fieldName+") values(@file)";
 OleDbParameter spFile=new OleDbParameter("@file",OleDbType.Binary);
 spFile.Value=bytes;
 cm.Parameters.Add(spFile);
 cm.ExecuteNonQuery()
 
代码中的fileName是文件的完整名称，tableName是要操作的表名称，fieldName是要保存文件的字段名称。
 
两段代码实际上是一样的，只是操作的数据库不同，使用的对象不同而已。
 
接着，在说说将文件从数据库中读取出来，只介绍从SQL Server中读取。

 SqlDataReader dr=null;
 SqlConnection objCn=new SqlConnection();
 objCn.ConnectionString="Data Source=(local);User ID=sa;Password=;Initial Catalog=Test";
 SqlCommand cm=new SqlCommand();
 cm.Connection=cn;
 cm.CommandType=CommandType.Text;
 cm.CommandText="select "+fieldName+" from "+tableName+" where ID=1";
 dr=cm.ExecuteReader();
 byte[] File=null; 
 if(dr.Read())
 {
 File=(byte[])dr[0];
 }
 FileStream fs;
 FileInfo fi=new System.IO.FileInfo(fileName);
 fs=fi.OpenWrite();
 fs.Write(File,0,File.Length);
 fs.Close();
 
上面的代码是将保存在数据库中的文件读取出来并保存文fileName指定的文件中。
 
在使用上面的代码时，别忘了添加System.Data.SqlClient和System.IO引用。


修改:
将读文件的下面部分的代码
 FileStream fs;
 FileInfo fi=new System.IO.FileInfo(fileName);
 fs=fi.OpenWrite();
 fs.Write(File,0,File.Length);
 fs.Close();
修改为
 FileStream fs=new FileStream(fileName,FileMode.CreateNew);
 BinaryWriter bw=new BinaryWriter(fs);
 bw.Write(File,0,File.Length);
 bw.Close();
 fs.Close();
这样修改后，就可以解决朋友们提出的“如果想从数据库中取出，另存为相应的文件时。如WORD文件另存为XXX.DOC('XXX'为文件名) ”问题了。