## 1 GridFS简介

　　当前Bson能存储的最大尺寸是16M，我们想把大于16M的文件存入mongoDB中怎么办呢？mongoDB提供的GridFS就是专门做这个的。使用GridFS存储大文件时，文件被分成一个个的块(默认大小是255 kb)，将每一块存放在一个单独的document中。GridFS将文件存储在两个collection中：chunks collection和files collection，其中chunks collection保存文件块，files collection保存文件的元数据。



## 2 使用mongofiles进行大文件管理

　　mongofiles是mongoDB内置的文件操作工具，提供了十分简单的API让我们可以通过命令行实现文件的上传、下载、查找和删除。我们使用一个视频文件做测试。



### 1 上传文件(put)

　　这里准备将/data/videos下的电影绿皮书(文件名:”lvpishu.mkv“)上传到mongoDB数据库myfiles中下，只需要使用一条命令就可以完成文件的上传：在mongoDb的bin目录下执行命令   mongofiles -d myfiles -l /data/videos/lvpishu.mkv put lvpishu.mkv ----host 192.168.70.131:27017  ,如果标明--host的话默认上传到localhost。上传完成后使用robomongo查看文件信息，如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190713114142470-1818069124.png)

 　　使用robomongo查看上传的文件信息，如下图：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190713131239208-920824653.png)



### 2 下载文件(get)

　　下载GridFS中的文件使用命令Get，如我们要将刚才上传的电影，下载到/data/videos2目录下，执行命令 mongofiles -d myfiles -l /data/videos2/lvpishu.mkv get lvpishu.mkv 即可，效果如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190713123923256-1760237483.png)



### 3 查找文件(list、search)

　　查询 GridFS中的文件可以使用search查询文件名包含某字符串的文件信息，使用list查询以某字符串开头的文件列表，因为我们只上传了一个文件所以这里的文件列表也只展示一条文件信息，执行命令效果如下：

 ![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190713125208388-1378178772.png)



### 4 删除文件(delete)

　　如果我们想删除GridFS中的某一文件，使用delete <filename>命令

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190713124606854-1713058226.png)



## 3 使用C#驱动操作GridFS

　　前边我们已经使用mongoDB自带的命令行工具mongofiles实现了大文件的增删查操作，但是实际开发中我们更常用的方式是使用各种语言驱动来管理文件，这里展示怎么通过C#驱动来实现大文件的管理。添加GridFS的包 Install-Package MongoDB.Driver.GridFS ，C#驱动中提供了GridFSBucket(GridFS桶)对象来保存文件，它是fs.files和fs.chunks的组合，我们在使用时，最好使用GridFSBucket来和GridFS交互，尽量不要直接使用底层的fs.files和fs.chunks)。

　　C#驱动mongoDB的上传和下载文件有两种形式：①通过字节数组byte[]上传和下载，这种方式适用于文件不大的情况，②使用stream的方式进行上传和下载，这种形式适用于各种场合，这里就采用stream的形式做文件的上传和下载演示，代码如下：



```
    class Program
    {
        static void Main(string[] args)
        {
            //连接数据库
            var client = new MongoClient("mongodb://192.168.70.133:27017, 192.168.70.131:27017, 192.168.70.129:27017");
            //获取database
            var mydb = client.GetDatabase("myfilesDb");
            //初始化GridFSBucket
            var bucket = new GridFSBucket(mydb, new GridFSBucketOptions
            {
                BucketName = "lvpishu",         //设置根节点名
                ChunkSizeBytes = 1024 * 1024,   //设置块的大小为1M
                WriteConcern = WriteConcern.WMajority,     //写入确认级别为majority
                ReadPreference = ReadPreference.Secondary  //优先从从节点读取
            });
            //上传文件
            //上传的配置项，可以添加文件元数据
            var options = new GridFSUploadOptions
            {
                //ChunkSizeBytes = 1048000, 
                Metadata = new BsonDocument
                {
                    { "format", "mkv" },
                    { "country", "USA" }
                }
            };
            //通过stream形式上传文件
            ObjectId fileId;
            Console.WriteLine("开始文件上传---------------->");
            string sourceFile = @"D:\迅雷下载\lvpishu.mkv";
            using (var fs = new FileStream(sourceFile, FileMode.Open))
            {
                //mongodb中的文件名为“绿皮书”
                Console.WriteLine("上传中...");
                 fileId = bucket.UploadFromStream(filename: "绿皮书", source: fs, options: options);
            }
            Console.WriteLine("<----------------文件上传完成");
            Console.WriteLine();

            //查看文件
            var filter = Builders<GridFSFileInfo>.Filter;
            
            using (var cursor = bucket.Find(filter.Eq(x => x.Filename, "绿皮书")))
            {
                var fileInfo = cursor.FirstOrDefault();
                fileId = fileInfo.Id;
                Console.WriteLine($"文件名：{fileInfo?.Filename}， 文件大小：{fileInfo?.Length}字节, 文件上传时间：{fileInfo?.UploadDateTime.AddHours(8)}");
                Console.WriteLine($"自定义的元数据：{fileInfo?.Metadata}");
            }
            Console.WriteLine();

            //下载文件
           //文件下载的位置
            Console.WriteLine("开始文件下载---------------->");
            string tagrgetPath = @"D:/mongoDownLoad/绿皮书下载.mkv";
            using (var mongoStream = bucket.OpenDownloadStream(id: fileId))
            {
                Console.WriteLine("下载中...");
                //通过FileStream写文件
                using (FileStream fsWrite = new FileStream(tagrgetPath, FileMode.Create)) 
                {
                    //开辟临时缓存内存
                    byte[] buffer = new byte[1024 * 1024]; 
                    while (true)
                    {
                        //readCount是真正读取到的字节数
                        int readCount = mongoStream.Read(buffer, 0, buffer.Length);
                        //写入目标文件
                        fsWrite.Write(buffer, 0, readCount);
                        //判断是否读取完成
                        if (readCount < buffer.Length)
                        {
                            break; 
                        }
                    }
                }
            }
            //最好比较一下mongodb中的文件和下载文件的Md5值，如果md5相同表示下载完成
            //这里为了简单起见，就简单判断以下文件是否存在
            if (File.Exists(@"D:/mongoDownLoad/绿皮书下载.mkv"))
            {
                Console.WriteLine("<----------------文件下载完成！");
            }
            Console.WriteLine();

            //删除文件
            bucket.Delete(id: fileId);
            Console.WriteLine("文件已删除！");

            Console.ReadKey();
        }
    }
```



　　初始化GridFSBucket时可以设置一些参数：BucketName用于设置files和chunks的根节点名，如设置BucketName="lvpishu"，那么在数据库中保存文件的两个collection的名字为lvpishu.files和lvpishu.chunks。ChunkSizeBytes用于设置数据块的大小，这里设置数据块大小为1M。

代码的注释比较详细，这里就不多介绍了，程序运行结果如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190713161609074-1730687194.png)

**小结** 　　

　　本节介绍了GridFS的概念，并简单演示了怎样使用mongofile和C#驱动进行大文件的上传、查询、下载、删除操作。如果文中有错误的话，希望大家可以指出，我会及时修改，谢谢！