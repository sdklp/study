## MongoDB 数据库的备份与恢复

## 数据备份

```bash
./mongodump -h 127.0.0.1:10001 -d lietou -o /usr/local/data
```

- -h：MongDB所在服务器地址，例如：127.0.0.1，当然也可以指定端口号：127.0.0.1:10001

- -d：需要备份的数据库实例，例如：lietou

- -o：备份的数据存放位置，例如：/usr/local/data ，在备份完成后，系统自动在dump目录下建立一个lietou目录，这个目录里面存放该数据库实例的备份数据。

-  C#代码：

  ```
          string strCmd = string.Format(
                    @"""{0}mongodump.exe"" -h 127.0.0.1 -d {1} -o {2}%date:~0,4%-%date:~5,2%-%date:~8,2%\%time:~0,2%-%time:~3,2%",
                    SysParameter.MongoDBPath,
                    SysParameter.Database,
                    SysParameter.MongoDBBackupPath);
  ```

## 数据还原

```bash
./mongorestore -h 127.0.0.1:10001 -d test  --directoryperdb /usr/local/data/lietou/
```

- -h：MongoDB所在服务器地址

- -d：需要恢复的数据库实例，例如：test，当然这个名称也可以和备份时候的不一样，比如test2

- --directoryperdb：备份数据所在位置，例如：/usr/local/data/lietou/，这里为什么要多加一个lietou，而不是备份时候的dump，读者自己查看提示吧！

- --drop：恢复的时候，先删除当前数据，然后恢复备份的数据。就是说，恢复后，备份后添加修改的数据都会被删除，慎用哦！

- C#代码：

  ```
            string strCmd = string.Format(
                      @"""{0}mongorestore.exe"" -h 127.0.0.1 -d {1} {2}{3}",
                      SysParameter.MongoDBPath,
                      SysParameter.Database,
                      SysParameter.MongoDBBackupPath,
                      SysParameter.Database);
  ```



MongoDB数据库备份方式：

　　1、整库备份

　　2、单表备份

 

 1、整库备份

备份整个数据库：

```
mongodump -h 127.0.0.1:27000 -d park --authenticationDatabase park -u USERNAME -p PASSWORD
```

恢复整个数据库：

```
mongorestore -h 127.0.0.1:27000 -d park park --authenticationDatabase park -u USERNAME -p PASSWORD　　
```

 

 2、单表备份

备份单个表：

```
mongoexport -h 127.0.0.1:27000 --db park --collection user_order -o /tmp/mongodb-test --authenticationDatabase park -u USERNAME -p PASSWORD　　
```

备份单个表每个时间点以内的数据：

```
mongoexport -h 127.0.0.1:27000 --db park --collection user_order -o /tmp/mongodb-test --authenticationDatabase park -u USERNAME -p PASSWORD --query '{"pay_time":{$lt:ISODate("2017-05-01T00:00:00Z")}}'
```

还原单个表数据到数据库：

```
mongoimport -h 127.0.0.1:27000 -d park -c user_order --file mongodb-test --authenticationDatabase park -u USERNAME -p PASSWORD　　
```

###  ###

要**备份**使用**mongodump**:

```
% mongodump -h ds012345.mongolab.com:56789 -d dbname -u dbuser -p dbpassword -o dumpdir
```

要**恢复**使用**mongorestore**:

```
% mongorestore -h ds023456.mongolab.com:45678 -d dbname -u dbuser -p dbpassword dumpdir/*
```

### C#调用可执行文件

    // 需要的头文件
    using System.Diagnostics;
    
    // 这里是要调用的可执行文件的文件夹目录
    string targetPath = string.Format(@"文件夹路径");
    
    // Process:提供对本地和远程进程的访问并使你能够启动和停止本地系统进程
    Process process = new Process();
    
    // 初始化可执行文件的一些基础信息
    process.StartInfo.WorkingDirectory = targetPath; // 初始化可执行文件的文件夹信息
    process.StartInfo.FileName = "可执行文件名称.后缀"; // 初始化可执行文件名
    
    // 当我们需要给可执行文件传入参数时候可以设置这个参数
    // "para1 para2 para3" 参数为字符串形式，每一个参数用空格隔开
    process.StartInfo.Arguments = "para1 para2 para3";
    process.StartInfo.UseShellExecute = true;        // 使用操作系统shell启动进程
    
    // 启动可执行文件
    process.Start();





如果您只需要自動備份，有一種方法比使用成熟的程式語言更簡單：
http://docs.mongodb.org/manual/tutorial/backup-databases-with-filesystem-snapshots/
如連結所示，下面的命令就足夠了。您可以將其放入statup指令碼/守護程式中，以定期執行它：
備份：

```
lvcreate --size 100M --snapshot --name mdb-snap01 /dev/vg0/mongodb
```


恢復：

```
lvcreate --size 1G --name mdb-new vg0
gzip -d -c mdb-snap01.gz | dd of=/dev/vg0/mdb-new
mount /dev/vg0/mdb-new /srv/mongodb
```