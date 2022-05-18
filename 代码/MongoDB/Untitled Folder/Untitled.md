# 基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用

 原创

[wuhuacong](https://blog.51cto.com/wuhuacong)2021-07-22 17:09:23©著作权

*文章标签*[MongoDB数据库](https://blog.51cto.com/topic/mongodbshujuku.html)*文章分类*[其他](https://blog.51cto.com/nav/database1)[数据库](https://blog.51cto.com/nav/database)*阅读数*52

## 基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用

在花了不少时间研究学习了MongoDB数据库的相关知识，以及利用C#对MongoDB数据库的封装、测试应用后，决定花一些时间来总结一下最近的研究心得，把这个数据库的应用单独作为一个系列来介绍，希望从各个方面来总结并记录一下这个新型、看似神秘的数据库使用过程。本文是这个系列的开篇，主要介绍一些MongoDB数据库的基础知识、安装过程、C#开发方便的基础使用等内容。

在花了不少时间研究学习了MongoDB数据库的相关知识，以及利用C#对MongoDB数据库的封装、测试应用后，决定花一些时间来总结一下最近的研究心得，把这个数据库的应用单独作为一个系列来介绍，希望从各个方面来总结并记录一下这个新型、看似神秘的数据库使用过程。本文是这个系列的开篇，主要介绍一些MongoDB数据库的基础知识、安装过程、基础使用等方面。

MongoDB是一款由C++编写的高性能、开源、无模式的常用非关系型数据库产品，是非关系数据库当中功能最丰富、最像关系数据库的数据库。它扩展了关系型数据库的众多功能，例如:辅助索引、范围查询、排序等。 

MongoDB主要解决的是海量数据的访问效率问题，它作为分布式数据崛起后，使用较多的一款非结构数据库，必然有其值得称道之处，它的主要功能特性如下:

​    1）面向集合的存储，适合存储对象及JSON形式的数据。
​    2）动态查询，MongoDB支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。
​    3）完整的索引支持，包括文档内嵌对象及数组。MongoDB的查询优化器会分析查询表达式，并生成一个高效的查询计划。
​    4）查询监视，MongoDB包含一个监视工具用于分析数据库操作的性能。
​    5）复制及自动故障转移，MongoDB数据库支持服务器之间的数据复制，支持主-从模式及服务器之间的相互复制。复制的主要目标是提供冗余及自动故障转移。
​    6）高效的传统存储方式，支持二进制数据及大型对象（如图片或视频）。
​    7）自动分片以支持云级别的伸缩性，自动分片功能支持水平的数据库集群，可动态添加额外的机器。 

### 1、MongoDB数据库和传统关系数据库的对比

MongoDB数据库有几个简单的概念需要了解一下。

 

  1）MongoDB中的 `database` 有着和我们熟知的"数据库"一样的概念 (对 Oracle 来说就是 schema)。一个 MongoDB 实例中，可以有零个或多个数据库，每个都作为一个高等容器，用于存储数据。

  2）数据库中可以有零个或多个 `collections` (集合)。集合和传统意义上的 table 基本一致，可以简单的把两者看成是一样的东西。

  3）集合是由零个或多个 `documents` (文档)组成。同样，一个文档可以看成是一 `row`。

  4）文档是由零个或多个 `fields` (字段)组成。,对应的就是关系数据库的 `columns`。

  5）`Indexes` (索引)在 MongoDB 中扮演着和它们在 RDBMS 中一样的角色，都是为了提高查询的效率。

  6）*`Cursors` (游标)和上面的五个概念都不一样，但是它非常重要，并且经常被忽视，其中最重要的你要理解的一点是，游标是当你问 MongoDB 拿数据的时候，它会给你返回一个结果集的指针而不是真正的数据，这个指针我们叫它游标，我们可以拿游标做我们想做的任何事情，比如说计数或者跨行之类的，而无需把真正的数据拖下来，在真正的数据上操作。*

 它们的对比关系图如下所示。

![基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用_MongoDB数据库](http://www.codeproject.com/KB/database/1037052/image002.png)

数据在Mongodb里面都是以Json格式方式进行存储的，如下所示是其中的一个记录内容。

```
{
    _id: ObjectID('4bd9e8e17cefd644108961bb'),
    name:'Vivek',
    class : '12th',
    subjects: [ 'physics', 'chemistry', 'math', 'english', 'computer'],
    address: {
                    house_no: '12B',
                    block: 'B',
                    sector: 12,
                    city : 'noida',
                    },
    grade: [
                    {
                    exam: 'unit test 1',
                    score: '60%'
                    },
                    {
                    exam: 'unit test 2',
                    score: '70%'
                    }
                    
                ]                
}1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.
```

在过去的很长一段时间中，关系型数据库一直是最主流的数据库解决方案，他运用真实世界中事物与关系来解释数据库中抽象的数据架构。然而，在信息技术爆炸式发展的今天，大数据已经成为了继云计算，物联网后新的技术革命，关系型数据库在处理大数据量时已经开始吃力，开发者只能通过不断地优化数据库来解决数据量的问题，但优化毕竟不是一个长期方案，所以人们提出了一种新的数据库解决方案来迎接大数据时代的到来——**NoSQL（非关系型数据库）**，其中MongoDB数据库就是其中的NoSQL的杰出代表。在大数据时代中，大数据量的处理已经成了考量一个数据库最重要的原因之一。而MongoDB的一个主要目标就是尽可能的让数据库保持卓越的性能，这很大程度地决定了MongoDB的设计。

根据MongoDB官网的说明，**MongoDB的适用场景如下**:

1）网站实时数据:MongoDB非常适合实时的插入，更新与查询，并具备网站实时数据存储所需的复制及高度伸缩性。

2）数据缓存:由于性能很高，MongoDB也适合作为信息基础设施的缓存层。在系统重启之后，由MongoDB搭建的持久化缓存层可以避免下层的数据源过载。

3）大尺寸、低价值数据存储:使用传统的关系型数据库存储一些数据时可能会比较昂贵，在此之前，很多时候程序员往往会选择传统的文件进行存储。

**4）高伸缩性场景:MongoDB非常适合由数十或数百台服务器组成的数据库。MongoDB的路线图中已经包含对MapReduce引擎的内置支持。**

*****5）对象或JSON数据存储:MongoDB的BSON数据格式非常适合文档化格式的存储及查询。*****


**MongoDB**不适合使用场景如下:
1）高度事务性系统:例如银行或会计系统。传统的关系型数据库目前还是更适用于需要大量原子性复杂事务的应用程序。

2）传统的商业智能应用:针对特定问题的BI数据库会对产生高度优化的查询方式。
3）需要复杂SQL查询的问题。

MongoDB大多数情况下，可以代替关系型数据库实现数据库业务。它更简单更直接、更快速并且通常对应用开发者的约束更少，不过缺乏事务支持需要慎重考虑业务需要。

 

### 2、MongoDB数据库的安装及基础使用

MongoDB数据的官网为：https://www.mongodb.org/，当前版本为3.2，可以直接下载安装版本在Linux或者Windows进行安装。

一般在Windows，我们默认安装的路径为C:\Program Files\MongoDB，安装后可以手动创建一个放置数据库和日志文件的目录，一般不要放在C盘就好，如下所示：

创建文件夹d:\mongodb\data\db、d:\mongodb\data\log，分别用来安装db和日志文件，我们以后运行数据库后，这个目录就用来放置我们创建的数据库和日志资源了。

一般我们安装后，为了在命令行方便调用Mongodb的命令，我们可以设置一个全局的路径变量，如下所示。

![基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用_MongoDB数据库_02](https://images2015.cnblogs.com/blog/8867/201601/8867-20160105090409559-1210150125.png)

默认情况下，mongodb的工作模式，是启动一个DOS窗口，运行mongodb的数据库服务，一旦这个DOS窗口关闭，也就停止了相关的服务，在Windows平台，我们可以把它寄宿在Windows服务里面，让它随着系统的启动而启动，也不必因为误关闭窗口而停止了数据库服务了。

通过下面命令行执行数据库服务的处理。

```
mongod --dbpath "d:\mongodb\data\db" --logpath "d:\mongodb\data\log\MongoDB.log" --install --serviceName "MongoDB"1.
```

然后使用命令行启动服务

```
NET START MongoDB1.
```

![基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用_MongoDB数据库_03](https://images2015.cnblogs.com/blog/8867/201601/8867-20160105091144215-37054290.jpg)

创建服务并顺利启动成功后，然后就可以在系统的服务列表里查看到了，我们确认把它设置为自动启动的Windows服务即可。

![基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用_MongoDB数据库_04](https://images2015.cnblogs.com/blog/8867/201601/8867-20160105091401918-1338954749.png)

启动后，我们可以在系统【运行】里面直接使用命令mongo打开窗口就可以进行相关的操作了。

![基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用_MongoDB数据库_05](https://images2015.cnblogs.com/blog/8867/201601/8867-20160105093240950-2112739981.png)

上面用了一些常见的命令操作。

- show dbs   显示数据库列表
- use dbname   进入dbname数据库，大小写敏感，没有这个数据库也不要紧
- show collections   显示数据库中的集合，相当于表格
- db.<collection_name>.find(); 集合查找方法，参考上面的方式，使用pretty()函数是排版更好看的意思。

 而其中find方法很强大，可以组合很多条件查询的方式，如下所示：

- db.collection.find({ "key" : value })   查找key=value的数据
- db.collection.find({ "key" : { $gt: value } })   key > value
- db.collection.find({ "key" : { $lt: value } })   key < value
- db.collection.find({ "key" : { $gte: value } })   key >= value
- db.collection.find({ "key" : { $lte: value } })   key <= value
- db.collection.find({ "key" : { $gt: value1 , $lt: value2 } })   value1 < key <value2
- db.collection.find({ "key" : { $ne: value } })   key <> value
- db.collection.find({ "key" : { $mod : [ 10 , 1 ] } })   取模运算，条件相当于key % 10 == 1 即key除以10余数为1的
- db.collection.find({ "key" : { $nin: [ 1, 2, 3 ] } })   不属于，条件相当于key的值不属于[ 1, 2, 3 ]中任何一个
- db.collection.find({ "key" : { $in: [ 1, 2, 3 ] } })   属于，条件相当于key等于[ 1, 2, 3 ]中任何一个
- db.collection.find({ "key" : { $size: 1 } })   $size 数量、尺寸，条件相当于key的值的数量是1（key必须是数组，一个值的情况不能算是数量为1的数组）
- db.collection.find({ "key" : { $exists : true|false } })   $exists 字段存在，true返回存在字段key的数据，false返回不存在字度key的数据
- db.collection.find({ "key": /^val.*val$/i })   正则，类似like；“i”忽略大小写，“m”支持多行
- db.collection.find({ $or : [{a : 1}, {b : 2} ] })   $or或 （注意：MongoDB 1.5.3后版本可用），符合条件a=1的或者符合条件b=2的数据都会查询出来
- db.collection.find({ "key": value , $or : [{ a : 1 } , { b : 2 }] })   符合条件key=value ，同时符合其他两个条件中任意一个的数据
- db.collection.find({ "key.subkey" :value })   内嵌对象中的值匹配，注意："key.subkey"必须加引号
- db.collection.find({ "key": { $not : /^val.*val$/i } })   这是一个与其他查询条件组合使用的操作符，不会单独使用。上述查询条件得到的结果集加上$not之后就能获得相反的集合。

当然还有插入更新的处理语句也是很特别的。

```
db.student.insert({name:'student1',subject:['arts','music']})1.
```

特别是更新操作需要说明一下，支持常规的$set方法（修改）、$unset方法（移除指定的键），还有原子级的$inc方法（数值增减），$rename方法（重命名字段名称）等等，

```
db.users.update({"_id" : ObjectId("51826852c75fdd1d8b805801")},  {"$set" : {"hobby" :["swimming","basketball"]}} )1.
db.users.update({"_id" : ObjectId("51826852c75fdd1d8b805801")},{"$unset" : {"hobby" :1 }} )1.
db.posts.update({"_id" : ObjectId("5180f1a991c22a72028238e4")}, {"$inc":{"pageviews":1}})1.
db.students.update( { _id: 1 }, { $rename: { 'nickname': 'alias', 'cell': 'mobile' } } 1.
```

upsert是一种特殊的更新操作，不是一个操作符。（upsert = up[date]+[in]sert），也就是如果存在则更新，否则就写入一条新的记录操作。这个参数是个布尔类型，默认是false。

```
db.users.update({age :25}, {$inc :{"age" :3}}, true)1.
```

另外，Update可以对Json的集合进行处理，如果对于subject对象是一个集合的话，插入或更新其中的字段使用下面的语句

```
db.student.update({name:'student5'},{$set:{subject:['music']}},{upsert:true});1.
```

如果是记录已经存在，我们可以使用索引数值进行更新其中集合里面的数据，如下所示。

```
db.student.update({name:'student3'},{$set:{'subject.0':'arts'}});1.
```

如果我们先在集合里面增加一个记录，而非替换的话，那么使用$push语句，如下面的语句所示。

```
db.student.update({name:'student3'},{$push:{'subject':'sports'}})1.
```

相反，如果要移除集合里面的某个值，使用**$pop***操作符，那么语句如下所示*

```
db.student.update({name:'student3'},{$pop:{'subject':1}});1.
```

其中索引为1标识最右边的记录，-1标识为最左边的记录。

另外还可以使用**`$pushAll`** 和$pullAll来增加/移除一个或多个集合记录，如下代码所示。

```
db.student.update({name:'student3'},{$pushAll:{'subject':['sports','craft']}})
db.student.update({name:'student3'},{$pullAll:{'subject':['sports','craft']}})1.2.
```

 

mongodb的数据库的操作还是比较容易理解的，具体可以进一步参考官网里面的介绍。

https://docs.mongodb.org/manual/

https://docs.mongodb.org/getting-started/csharp/client/

http://mongodb.github.io/mongo-csharp-driver/2.2/

http://wiki.jikexueyuan.com/project/the-little-mongodb-book/

 

### 3、MongoDB数据库的C#驱动的使用

 数据库的C#驱动使用介绍，可以参考：https://docs.mongodb.org/getting-started/csharp/ 当前已更改为https://docs.mongodb.com/drivers/csharp ，可以下载相关的DLL然后在项目中引用，当前的驱动版本为2.2，一般引入下面几个DLL即可。

- - `MongoDB.Bson.dll`
  - `MongoDB.Driver.dll`
  - `MongoDB.Driver.Core.dll`

也可以使用VS工具的NugGet包进行下载管理，如下所示。

![基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用_MongoDB数据库_06](https://images2015.cnblogs.com/blog/8867/201601/8867-20160104233028606-1172157291.png)

然后在弹出的NugGet程序包管理界面里面搜索mongo，然后添加MongoDB.Driver的数据库驱动就可以使用了。

![基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用_MongoDB数据库_07](https://images2015.cnblogs.com/blog/8867/201601/8867-20160104233256637-365369715.png)

MongoDB数据库驱动在2.2版本（或者是从2.0开始）好像完全改写了API的接口，因此目前这个版本同时支持两个版本的API处理，一个是基于MongoDatabase的对象接口，一个是IMongoDatabase的对象接口，前者中规中矩，和我们使用Shell里面的命令名称差不多，后者IMongoDatabase的接口是基于异步的，基本上和前者差别很大，而且接口都提供了异步的处理操作。后面我会分别对这两个部分进行详细的介绍，本文基于篇幅的原因，介绍一下两者的简单差异就可以了。

我们以Mongodb的数据库连接字符串mongodb://localhost/local来进行构建

1）旧接口MongoDatabase对象的构建

```
            var client = new MongoClient(connectionString);
            var database = client.GetServer().GetDatabase(new MongoUrl(connectionString).DatabaseName);1.2.
```

2）新接口IMongoDatabase对象的构建

```
            var client = new MongoClient(connectionString);
            var database = client.GetDatabase(new MongoUrl(connectionString).DatabaseName);1.2.
```

后者已经没有了***GetServer\***的接口了。

3）旧接口的查找对象处理

```
        /// <summary>
        /// 查询数据库,检查是否存在指定ID的对象
        /// </summary>
        /// <param name="key">对象的ID值</param>
        /// <returns>存在则返回指定的对象,否则返回Null</returns>
        public virtual T FindByID(string id)
        {
            ArgumentValidation.CheckForEmptyString(id, "传入的对象id为空");

            MongoCollection<T> collection = GetCollection();
            return collection.FindOneById(new ObjectId(id)); 
        }1.2.3.4.5.6.7.8.9.10.11.12.
```

3）新接口查找对象的处理

```
        /// <summary>
        /// 查询数据库,检查是否存在指定ID的对象
        /// </summary>
        /// <param name="key">对象的ID值</param>
        /// <returns>存在则返回指定的对象,否则返回Null</returns>
        public virtual T FindByID(string id)
        {
            ArgumentValidation.CheckForEmptyString(id, "传入的对象id为空");

            IMongoCollection<T> collection = GetCollection();
            return collection.Find(s=> s.Id == id).FirstOrDefault();
        }1.2.3.4.5.6.7.8.9.10.11.12.
```

新接口已经没有了FindOneById等接口了，所有的操作基本上都通过Find方法进行处理。旧接口很多通过Query对象进行条件的查询，新接口又换了一个对象进行过滤条件处理了，总的来说，两个接口差异非常大。

例如旧版本接口的Query使用C#代码如下所示：

```
        private void TestQuery()
        {
            Query.All("name", new List<BsonValue> { BsonValue.Create("a"), BsonValue.Create("b") });//通过多个元素来匹配数组
            Query.And(Query.EQ("name", "a"), Query.EQ("title", "t"));//同时满足多个条件
            Query.Or(Query.EQ("name", "a"), Query.EQ("title", "t"));//满足其中一个条件
            Query.EQ("name", "a");//等于
            Query.Exists("type");//判断键值是否存在
            Query.GT("value", 2);//大于>
            Query.GTE("value", 3);//大于等于>=
            Query.In("name", new List<BsonValue> { BsonValue.Create("a"), BsonValue.Create("b") });//包括指定的所有值,可以指定不同类型的条件和值
            Query.LT("value", 9);//小于<
            Query.LTE("value", 8);//小于等于<=
            Query.Mod("value", 3, 1);//将查询值除以第一个给定值,若余数等于第二个给定值则返回该结果
            Query.NE("name", "c");//不等于
            Query.Size("name", 2);//给定键的长度
            Query.Type("_id", BsonType.ObjectId);//给定键的类型
            Query.ElemMatch("children", Query.And( Query.EQ("name", "C3"),   Query.EQ("value", "C")));

            //Query.Nor(Array);//不包括数组中的值
            //Query.Not("name");//元素条件语句
            //Query.NotIn("name", "a", 2);//返回与数组中所有条件都不匹配的文档
            //Query.Where(BsonJavaScript);//执行JavaScript

            //Query.Matches("Title", str);//模糊查询 相当于sql中like  -- str可包含正则表达式
            var keyword = "abc";
            Query.Matches("Title", new BsonRegularExpression("/.*" + keyword + ".*/"));//模糊Like语法

            //通过正则表达式 1开头 第二位数0~9且只能一位数，也包含20
            var queryName = Query.Matches("Name", new BsonRegularExpression("Donma1([0-9]{1,1})$|20"));
            //查找年龄 >=10 且<=20 
            var queryAge = Query.And(Query.GTE("Age", 10), Query.LTE("Age", 20));

            var entityQuery = Query<UserInfo>.EQ(e => e.Name, "wuhuacong");
            var entityQuery2 = Query<UserInfo>.EQ(e => e.Id, "4B414D000000011613CD");
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.25.26.27.28.29.30.31.32.33.34.35.
```

新版本的条件查询，则丢弃了Query这个对象，提供了FilterDefinition<T> 对象的处理，估计是这个可以处理的更好吧，同时新接口全部支持异步的处理操作了。

如插入记录的异步操作代码如下所示。

```
        /// <summary>
        /// 插入指定对象到数据库中
        /// </summary>
        /// <param name="t">指定的对象</param>
        public virtual async Task InsertAsync(T t)
        {
            ArgumentValidation.CheckForNullReference(t, "传入的对象t为空");

            IMongoCollection<T> collection = GetCollection();
            await collection.InsertOneAsync(t);
        }1.2.3.4.5.6.7.8.9.10.11.
```

好了，基于篇幅的原因，把后面介绍的C#开发留到下一篇进行介绍，希望本篇文章对大家了解mongodb数据库，以及如何在C#上面使用该数据库提供了一个简要的指引，希望大家多多支持。

 

![基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用_MongoDB数据库_08](http://www.cnblogs.com/Images/OutliningIndicators/None.gif)主要研究技术：代码生成工具、会员管理系统、客户关系管理软件、病人资料管理软件、Visio二次开发、酒店管理系统、仓库管理系统等共享软件开发
专注于Winform开发框架/混合式开发框架、Web开发框架、Bootstrap开发框架、微信门户开发框架的研究及应用。