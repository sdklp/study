# 将图片直接从数据库复制到 Visual C sharp 的 PictureBox 控件

本文介绍如何将数据库中存储的图像直接复制到 Windows Form 上的 PictureBox 控件中，而无需将图像保存到文件中。

*原始产品版本：*  Visual C#
*原始 KB 编号：*  317701

## 摘要

本分步文章介绍如何将数据库中存储的图像直接复制到 Windows Form 上的 PictureBox 控件中，而无需将图像保存到文件中。

在 Visual Basic 6.0 中，在没有将二进制大型对象 {BLOB) 数据另存为文件的中间步骤的情况下，在 PictureBox 控件中显示来自数据库的图像的唯一方法就是将 PictureBox 绑定到数据源，如 ActiveX 数据对象 (ADO) 数据控件或 Recordset。 如果没有数据绑定 (无法) 方式将 BLOB 加载到控件中，而无需将图像保存为供 LoadPicture 语句使用的文件。

本文将使用基类中的 对象将数据库中的图像数据直接复制到 `MemoryStream` `System.IO` PictureBox 控件中。

## 要求

以下列表概述了你将需要的建议硬件、软件、网络基础结构和 Service Pack：

- Visual Studio兼容操作系统上安装的 Windows .NET
- 一个可用的 SQL Server 或一个可用于测试的 Access 数据库

本文假定您熟悉以下主题：

- Visual C# .NET Windows Forms 应用程序
- 二进制大型 (BLOB) 数据库中存储
- ADO.NET 数据访问

## 示例

1. 使用SQL Server创建一个表或 Access 表：

   SQL复制

   ```sql
   CREATE TABLE BLOBTest
   (
       BLOBID INT IDENTITY NOT NULL,
       BLOBData IMAGE NOT NULL
   )
   ```

2. 打开 Visual Studio .NET 并创建新的 Visual C# Windows 应用程序Project。

3. 从工具箱向默认 Form1 添加一个 PictureBox 和两个 Button 控件。 将 `Text` 的 属性 `Button1` 设置为 File to **Database，** 将 的 属性设置为 `Text` Database 设置为 `Button2` **PictureBox**。

4. 在表单的代码模块顶部插入以下 using 语句：

   C#复制

   ```csharp
   using System.Data.SqlClient;
   using System.IO;
   using System.Drawing.Imaging;
   ```

5. 为公共类 Form1： 声明内的数据库连接字符串添加以下声明并 `System.Windows.Forms.Form class` 根据需要调整连接字符串：

   C#复制

   ```csharp
   String strCn = "Data Source=localhost;integrated security=sspi;initial catalog=mydata";
   ```

6. 将以下代码插入到"文件到数据库 `Click` " (`Button1` **事件) 。** 根据需要调整可用示例图像文件的文件路径。 此代码使用对象或 (从磁盘) 映像文件，然后使用参数化的 Command 对象将数据插入 `FileStream` `Byte` 数据库。

   C#复制

   ```csharp
   try
   {
       SqlConnection cn = new SqlConnection(strCn);
       SqlCommand cmd = new SqlCommand("INSERT INTO BLOBTest (BLOBData) VALUES (@BLOBData)", cn);
       String strBLOBFilePath = @"C:\blue hills.jpg";//Modify this path as needed.
   
       //Read jpg into file stream, and from there into Byte array.
       FileStream fsBLOBFile = new FileStream(strBLOBFilePath,FileMode.Open, FileAccess.Read);
       Byte[] bytBLOBData = new Byte[fsBLOBFile.Length];
       fsBLOBFile.Read(bytBLOBData, 0, bytBLOBData.Length);
       fsBLOBFile.Close();
   
       //Create parameter for insert command and add to SqlCommand object.
       SqlParameter prm = new SqlParameter("@BLOBData", SqlDbType.VarBinary, bytBLOBData.Length, ParameterDirection.Input, false,
       0, 0, null, DataRowVersion.Current, bytBLOBData);
       cmd.Parameters.Add(prm);
   
       //Open connection, execute query, and close connection.
       cn.Open();
       cmd.ExecuteNonQuery();
       cn.Close();
   }
   catch(Exception ex)
   {
       MessageBox.Show(ex.Message);
   }
   ```

7. 在将数据库插入 `Click` PictureBox (`Button2` **事件过程中插入**) 。 此代码将数据库中的表中的行检索到 中，将最近添加的图像复制到数组中，然后复制到对象中，然后将 加载至 `BLOBTest` `DataSet` PictureBox 控件的 `Byte` `MemoryStream` `MemoryStream` `Image` 属性中。

   C#复制

   ```csharp
   try
   {
       SqlConnection cn = new SqlConnection(strCn);
       cn.Open();
   
       //Retrieve BLOB from database into DataSet.
       SqlCommand cmd = new SqlCommand("SELECT BLOBID, BLOBData FROM BLOBTest ORDER BY BLOBID", cn);
       SqlDataAdapter da = new SqlDataAdapter(cmd);
       DataSet ds = new DataSet();
       da.Fill(ds, "BLOBTest");
       int c = ds.Tables["BLOBTest"].Rows.Count;
   
       if(c>0)
       {
           //BLOB is read into Byte array, then used to construct MemoryStream,
           //then passed to PictureBox.
           Byte[] byteBLOBData = new Byte[0];
           byteBLOBData = (Byte[])(ds.Tables["BLOBTest"].Rows[c - 1]["BLOBData"]);
           MemoryStream stmBLOBData = new MemoryStream(byteBLOBData);
           pictureBox1.Image= Image.FromStream(stmBLOBData);
       }
       cn.Close();
   }
   catch(Exception ex)
   {
       MessageBox.Show(ex.Message);
   }
   ```

8. 按 **F5** 编译并运行项目。

9. 单击" **文件到数据库"** 按钮，将至少一个示例映像加载到数据库中。

10. 单击" **数据库到图片框"** 按钮，在 PictureBox 控件中显示保存的图像。

11. 如果要将 PictureBox 控件中的图像直接插入数据库，请添加第三个 Button 控件，并在其事件过程中插入 `Click` 以下代码。 此代码将 PictureBox 控件中的图像数据检索到对象中，将 复制到数组中，然后使用参数化的 Command 对象将数组保存到 `MemoryStream` `MemoryStream` `Byte` `Byte` 数据库中。

    C#复制

    ```csharp
    try
    {
        SqlConnection cn = new SqlConnection(strCn);
        SqlCommand cmd = new SqlCommand("INSERT INTO BLOBTest (BLOBData) VALUES (@BLOBData)", cn);
    
        //Save image from PictureBox into MemoryStream object.
        MemoryStream ms = new MemoryStream();
        pictureBox1.Image.Save(ms, ImageFormat.Jpeg);
    
        //Read from MemoryStream into Byte array.
        Byte[] bytBLOBData = new Byte[ms.Length];
        ms.Position = 0;
        ms.Read(bytBLOBData, 0, Convert.ToInt32(ms.Length));
    
        //Create parameter for insert statement that contains image.
        SqlParameter prm = new SqlParameter("@BLOBData", SqlDbType.VarBinary, bytBLOBData.Length, ParameterDirection.Input, false,
        0, 0,null, DataRowVersion.Current, bytBLOBData);
        cmd.Parameters.Add(prm);
        cn.Open();
        cmd.ExecuteNonQuery();
        cn.Close();
    }
    catch(Exception ex)
    {
        MessageBox.Show(ex.Message);
    }
    ```

12. 运行项目。 单击" **数据库到图片框"** 按钮，在 PictureBox 控件中显示以前保存的图像。 单击新添加的按钮，将 PictureBox 中的图像保存到数据库中。 然后再次 **单击"数据库到 PictureBox"** 按钮以确认图像已正确保存。

## 错误

- 此测试将不能与随 Access 和 SQL Server 分发的示例 Northwind 数据库的 Employees 表中的 Photo 列一SQL Server。 存储在"照片"列中的位图图像用由"6.0 OLE 容器"控件Visual Basic标题信息包装。
- 如果需要使用 Access 数据库测试此代码，则需要在 Access 表中创建 OLE 对象类型列，并使用命名空间和 `System.Data.OleDb` Jet 4.0 Provider 来表示命名空间。 `System.Data.SqlClient`