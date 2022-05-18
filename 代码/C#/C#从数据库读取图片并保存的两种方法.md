# C#从数据库读取图片并保存的两种方法

 更新时间：2021年1月16日 14:46  

这篇文章主要介绍了C#从数据库读取图片并保存的方法，帮助大家更好的理解和使用c#，感兴趣的朋友可以了解下



## 方式一：

数据库用的是SQL 2008，数据表中存放的是图片的二进制数据，现在把图片以一种图片格式（如.jpg）导出，然后存放于指定的文件夹中，实现方式如下：



```
byte``[] bytImg = (``byte``[])myDAL.DbHelperSQL.Query(``"SELECT F_Photo FROM myTable WHERE ID=1"``).Tables[0].Rows[0][0];``if` `(bytImg != ``null``)``{`` ``MemoryStream ms = ``new` `MemoryStream(bytImg);`` ``Image img = Image.FromStream(ms);`` ``img.Save(``"D:\\me.jpg"``);``}
```



## 方式二：

是windowform程序，数据库已经建好，图像以二进制形式存放在数据库的image表中，我想把符合查询条件的图像（大量）从数据库中读出，显示在form窗体上的一个控件（listview或imagelist还是picturebox？这个不知道那个合适），并保存到选择（或新建）的一个文件夹中



```
SqlDataAdapter da = ``new` `SqlDataAdapter(``"select * from newpicture"``, conn);``//数据库连接，修改一下数据库的操作。``DataSet ds = ``new` `DataSet();``da.Fill(ds, ``"pic"``);``//将符合条件的选项保存在数据集的pic表里`` ` `string` `picdotname;``string` `picfilename;``int` `piclength;``int` `i;``//添加新列``DataColumn newcolumn = ds.Tables[``"pic"``].Columns.Add(``"pic_url"``, ``typeof``(``string``));``//给pic表添加新的一列pic_url，保存你的新写出的图片路径``for` `(i = 0; i < Convert.ToInt16(ds.Tables[``"pic"``].Rows.Count); i++)``{`` ``picdotname = ds.Tables[``"pic"``].Rows[i][``"pic_dot"``].ToString();``//图片的拓展名，你数据库要有这一列，如jpg`` ``piclength = Convert.ToInt32(ds.Tables[``"pic"``].Rows[i][``"pic_length"``]);``//数据流的长度`` ``picfilename = Server.MapPath(``"新建的文件夹名/"``) + ``"添加图片名"``+ ``"."` `+ picdotname;`` ``FileStream fs = ``new` `FileStream(picfilename, FileMode.Create, FileAccess.Write);`` ``byte``[] piccontent = ``new` `byte``[piclength];`` ``piccontent = (``byte``[])ds.Tables[``"pic"``].Rows[i][``"pic_content"``];`` ``fs.Write(piccontent, 0, piclength);`` ``fs.Close();``//读出数据流写成图片`` ``//最后把表绑定到控件上。`` ``ds.Tables[``"pic"``].Rows[i][``"pic_url"``] = ``"temp/temp"` `+ i.ToString() + ``"."` `+ picdotname;``//意思给表pic的第i行，pic_url列里添加文件的路径值。``}``//数据源 = ds.Tables["pic"];//数据绑定
```



大体是这样吧，里面表名列名很多细节你按你的表修改吧！