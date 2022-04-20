# C#之判断Mysql数据库表是否存在

涉及到的SQL语句如下：

判断表是否存在：

```csharp
select count(*) as A from information_schema.tables where table_name = 'test' and table_schema ='test1'
```

创建表：

```sql
CREATE TABLE `test` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1
```

部分代码：

```csharp
            DBMysql dBMysql = new DBMysql();
            dBMysql.ConnectDB("127.0.0.1", 3306, "root", "sa","test1");
            string strSql = string.Format(@"select count(*) as A from information_schema.tables where table_name = 'test' and table_schema ='test1'");
            System.Data.DataSet dataSet = new DataSet();
            var bRet = dBMysql.SqlExe(strSql, ref dataSet);
            if (false == bRet)
            {
                return;
            }
            if (1 != dataSet.Tables[0].Rows.Count)
            {
                return;
            }
            DataRow row = dataSet.Tables[0].Rows[0];
            var a = row["A"];
            if ("0" == a.ToString())
            {
                string strCreat = string.Format(@"CREATE TABLE `test` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1");
                Console.WriteLine(dBMysql.SqlExe(strCreat) + " " + strCreat);
            }
```

效果如下：

![img](../../../我的文档/Typora/Pictures/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2cwNDE1c2hlbnc=,size_16,color_FFFFFF,t_70.png)