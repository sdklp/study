前边我们已经使用mongo shell进行增删查改和聚合操作，这一篇简单介绍如何使用C#驱动MongoDB。C#驱动MongoDB的本质是将C#的操作代码转换为mongo shell，驱动的API也比较简单明了，方法名和js shell的方法名基本都保持一致，熟悉mongo shell后学习MongoDB的C#驱动是十分轻松的，直接看几个栗子吧。



## 0.准备测试数据

　　使用js shell添加一些测试数据，如下：



```
use myDb
db.userinfos.insertMany([
   {_id:1, name: "张三", age: 23,level:10, ename: { firstname: "san", lastname: "zhang"}, roles: ["vip","gen" ]},
   {_id:2, name: "李四", age: 24,level:20, ename: { firstname: "si", lastname: "li"}, roles:[ "vip" ]},
   {_id:3, name: "王五", age: 25,level:30, ename: { firstname: "wu", lastname: "wang"}, roles: ["gen","vip" ]},
   {_id:4, name: "赵六", age: 26,level:40, ename: { firstname: "liu", lastname: "zhao"}, roles: ["gen"] },
   {_id:5, name: "田七", age: 27, ename: { firstname: "qi", lastname: "tian"}, address:'北京' },
   {_id:6, name: "周八", age: 28,roles:["gen"], address:'上海' }
])
```





## 1 添加(InsertOne,InsertMany)

　　首先创建一个Console应用程序，使用命令  Install-Package MongoDB.Driver  添加C#的驱动包，然后就可以使用C#操作MongoDB数据库了，看一个添加的栗子：



```
 class Program
    {
        static void Main(string[] args)
        {
            //连接数据库
            var client = new MongoClient("mongodb://192.168.70.131:27017");
            //获取database
            var mydb = client.GetDatabase("myDb");
            //获取collection
            var collection = mydb.GetCollection<BsonDocument>("userinfos");
            //待添加的document
            var doc = new BsonDocument{
                { "_id",7 },
                { "name", "吴九" },
                { "age", 29 },
                { "ename", new BsonDocument
                    {
                        { "firstname", "jiu" },
                        { "lastname", "wu" }
                    }
                }
            };
            //InsertOne()添加单条docment
            collection.InsertOne(doc);
        }
    }
```



　　执行完成后，查看在RoboMongo中查询，看到已经添加成功了：

　　![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706134053250-1005539100.png)

我们也可以是用 collection.InsertMany(IEnumerable<BsonDocument> docs) 来批量添加docment，这里就不再演示了。



## 2  查询(Find,Filter,Sort,Projection)



### 1.简单查询(Find、Filter)

　　栗子：查找name=“吴九"的记录

```
//Fileter用于过滤，如查询name = 吴九的第一条记录
var filter = Builders<BsonDocument>.Filter;
//Find(filter)进行查询
var doc = collection.Find(filter.Eq("name","吴九")).FirstOrDefault();
Console.WriteLine(doc);
```

　　查询结果如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706135610164-1135050805.png)

　　如果我们想查询所有的记录，可以设置过滤器为空，代码为： var docs = collection.Find(filter.Empty).ToList(); 



### 2.AND查询

　栗子：查询年龄大于25且小于28的记录

```
//查询25<age<28的记录
　　var filter = Builders<BsonDocument>.Filter;
　　var docs = collection.Find(filter.Gt("age", 25) & filter.Lt("age", 28)).ToList();
　　docs.ForEach(d => Console.WriteLine(d));
```

　　 查询结果如下：

 ![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706140143540-200856623.png)



### 3 OR查询

　　栗子：查询年龄小于25或年龄大于28的记录

```
//查询age<25或age>28的记录
　　var filter = Builders<BsonDocument>.Filter;
　　var docs = collection.Find(filter.Lt("age", 25) | filter.Gt("age", 28)).ToList();
　　docs.ForEach(d => Console.WriteLine(d));
```

　　查询结果如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706141304804-1273946815.png)



### 4 字段存在(Exists)

　　栗子：查询包含address字段的所有记录

```
//查询存在address字段的记录
　　var filter = Builders<BsonDocument>.Filter;
　　var docs = collection.Find(filter.Exists("address")).ToList();
　　docs.ForEach(d => Console.WriteLine(d));
```

　　查询结果如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706145309268-786959320.png)



### 4 排序(Sort)

　　栗子：查询年龄小于26岁的记录，并按年龄倒序排列

```
//查询age<26的记录，按年龄倒序排列
    var filter = Builders<BsonDocument>.Filter;
    var sort = Builders<BsonDocument>.Sort;
    var docs = collection.Find(filter.Lt("age",26))//过滤
                         .Sort(sort.Descending("age")).ToList();//按age倒序
    docs.ForEach(d => Console.WriteLine(d));
```

　　查询结果如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706142343044-231185970.png)



### 5 查询指定字段(Projection)

　　MongoDB查询会默认返回_id字段，如果要不返回_id字段可以使用Exclude("_id")将其排除。栗子：查询年龄小于26岁记录的name和age



```
//查询age<26的记录
    var project = Builders<BsonDocument>.Projection;
    var filter = Builders<BsonDocument>.Filter;
    var docs = collection.Find(filter.Lt("age", 26))//过滤
                         .Project(project.Include("name")//包含name
                                         .Include("age")//包含age
                                         .Exclude("_id")//不包含_id
                               ).ToList();
docs.ForEach(d => Console.WriteLine(d))        
```



　　查询结果：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706144204439-1031358373.png)



##  3 修改(UpdateOne,UpdateMany)



### 1 修改单条记录(UpdateOne)

　　修改符合过滤条件的第一条记录，栗子：将张三的年龄改成18岁



```
            var filter = Builders<BsonDocument>.Filter;
            var update = Builders<BsonDocument>.Update;
            var project = Builders<BsonDocument>.Projection;
            //将张三的年龄改成18
            collection.UpdateOne(filter.Eq("name", "张三"), update.Set("age", 18));
            //查询张三的记录
            var doc = collection.Find(filter.Eq("name", "张三"))
                                .Project(project.Include("age").Include("name"))
                                .FirstOrDefault();
            Console.WriteLine(doc);
```



　　执行结果：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706151449633-242556122.png)



### 2 修改多条记录(UpdateMany)

　　UpdateMany会修改所有符合过滤条件的记录，栗子：将所有年龄小于25的记录标记为young（如果没有mark字段会自动添加）



```
            var filter = Builders<BsonDocument>.Filter;
            var update = Builders<BsonDocument>.Update;
            var project = Builders<BsonDocument>.Projection;
            //将所有年龄小于25的记录标记为young（如果没有mark字段会自动添加）
            UpdateResult reulst=collection.UpdateMany(filter.Lt("age",25), update.Set("mark", "young"));
            if (reulst.IsModifiedCountAvailable)
            {
                Console.WriteLine($"符合条件的有{reulst.MatchedCount}条记录");
                Console.WriteLine($"一共修改了{reulst.ModifiedCount}条记录");
               //查询修改后的记录
                var docs = collection.Find(filter.Empty)
                                .Project(project.Include("age").Include("name").Include("mark"))
                                .ToList();
                docs.ForEach(d => Console.WriteLine(d));
            }
            else
            {
                Console.WriteLine("无修改操作！");
            }
```



　　查询结果如下，可以看到age<25的记录的mark字段值为young：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706153643710-968538836.png)



## 4 删除(DeleteOne和DeleteMany)

　　删除操作比较简单，DeleteOne用于删除符合过滤条件的第一条记录，DeleteMany用于删除所有符合过滤条件的记录。



### 1 删除单条记录(DeleteOne)

　　栗子：删除名字为张三的记录



```
            var filter = Builders<BsonDocument>.Filter;
            var project = Builders<BsonDocument>.Projection;
            //删除名字为张三的记录
            collection.DeleteOne(filter.Eq("name", "张三"));
            var docs = collection.Find(filter.Empty)
                            .Project(project.Include("age").Include("name").Include("mark"))
                            .ToList();
            docs.ForEach(d => Console.WriteLine(d));
```



　　执行结果如下，我们看到张三的记录已经被删除了：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706155450284-488096566.png)



### 2  删除多条记录(DeleteMany)

　　栗子：删除所有年龄大于25岁的记录



```
            var filter = Builders<BsonDocument>.Filter;
            var project = Builders<BsonDocument>.Projection;
            //删除age>25的记录
            DeleteResult result= collection.DeleteMany(filter.Gt("age", 25));
            Console.WriteLine($"一共删除了{result.DeletedCount}条记录");
            var docs = collection.Find(filter.Empty)
                            .Project(project.Include("age").Include("name").Include("mark"))
                            .ToList();
            docs.ForEach(d => Console.WriteLine(d));
```



　　执行结果如下，所有年龄大于25岁的记录都被删除了：

 ![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190706160146714-1652883312.png)



## 5 类型映射



### 1 简单栗子

　有时候我们要让查询的结果映射到我们的实体类中去，实现这个需求也十分简单，mongoDB支持自动映射，直接使用泛型即可，看一个栗子：



```
    class Program
    {
        static void Main(string[] args)
        {
            //连接数据库
            var client = new MongoClient("mongodb://192.168.70.133:27017");
            //获取database
            var mydb = client.GetDatabase("myDb");
            //获取collection
            var collection = mydb.GetCollection<Userinfo>("userinfos");
            var filter = Builders<Userinfo>.Filter;
            var sort = Builders<Userinfo>.Sort;
            List<Userinfo> userinfos = collection.Find(filter.Lt("age", 25))    //查询年龄小于25岁的记录
                                                 .Sort(sort.Descending("age"))  //按年龄进行倒序
                                         .ToList();
            //遍历结果
            userinfos.ForEach(u => Console.WriteLine($"姓名：{u.name},年龄：{u.age},英文名：{u.ename.firstname} {u.ename.lastname}"));
            Console.ReadKey();
        }
    }
    /// <summary>
    /// 用户类
    /// </summary>
    public class Userinfo
    {
        public int _id { get; set; }//id
        public string name { get; set; }//姓名
        public int age { get; set; }//年龄
        public int level { get; set; }//等级
        public Ename ename { get; set; }//英文名
        public string[] roles { get; set; }//角色
        public string address { get; set; }//地址
    }
    /// <summary>
    /// 英文名
    /// </summary>
    public class Ename
    {
        public string firstname { get; set; }
        public string lastname { get; set; }
    }
```



执行结果如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190707145425866-1309140409.png)



### 2 常用属性

　　上边的栗子仅仅用了基本的自动化映射，使用基本的自动化映射时：类和Bson中的字段必须严格一致(_id除外，可以自动映射到_id/id/Id)，且Bson中的每一个字段在实体类中都必须有一个对应的字段，不然就会抛出异常，这就造成我们可能要写一个非常庞大的实体类，而且类中的字段命名也要严格和Bson中的字段一致。这些限制对我们开发来说是不能接受的，这里我们采用mongoDriver中的一些属性改进一下上边的代码，如下：



```
    class Program
    {
        static void Main(string[] args)
        {//连接数据库
            var client = new MongoClient("mongodb://192.168.70.133:27017");
            //获取database
            var mydb = client.GetDatabase("myDb");
            //获取collection
            var collection = mydb.GetCollection<Userinfo>("userinfos");

            var filter = Builders<Userinfo>.Filter;
            var sort = Builders<Userinfo>.Sort;
            List<Userinfo> userinfos = collection.Find(filter.Lt("age", 25))    //查询年龄小于25岁的记录
                                                 .Sort(sort.Descending("age"))  //按年龄进行倒序
                                         .ToList();
            //遍历结果
            userinfos.ForEach(u =>
            {   
                Console.WriteLine($"编号：{u.userId},姓名：{u.name},年龄：{u.age},英文名：{u.ename?.ming} {u.ename?.xing},性别：{u.gender}");
                Console.WriteLine($"其他属性：{u.otherprops}");
                Console.WriteLine();
            });
            
            Console.ReadKey();
        }
    }
    /// <summary>
    /// 用户类
    /// </summary
    //[BsonIgnoreExtraElements]
    public class Userinfo
    {
        [BsonId]
        public int userId { get; set; }//id
        public string name { get; set; }//姓名
        public int age { get; set; }//年龄
        public Ename ename { get; set; }//英文名
        [BsonDefaultValue('男')]
        public char gender { get; set; }
        [BsonIgnore]
        public string nickname { get; set; }//昵称
        [BsonExtraElements]
        public BsonDocument otherprops { get; set; }//其他属性
    }
    /// <summary>
    /// 英文名
    /// </summary>
    public class Ename
    {
        [BsonElement("firstname")]
        public string ming { get; set; }
        [BsonElement("lastname")]
        public string xing { get; set; }
    }
```



　　执行结果如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190707161746411-1024244044.png)

这里用到了几个常用的属性，作用如下：

　　**BsonId**修饰的字段对应BsonDocument中的_id；

　　**BsonDefaultValue(value)**用于指定默认值；

　　**BsonIgnore**表示不映射，即使BsonDocument中包含该字段也不会赋值给属性；

　　**BsonExtraElements**修饰的字段用于存储没有映射到类中的其他属性；

　　**BsonElement**可以指定修饰的属性映射到BsonDocument中的哪个字段，

还有一些其他的属性，具体可以参考官方文档。



### 3 MongoDB使用Linq查询

　　MongoDB的驱动支持Linq查询，用法十分简单，引用  usingMongoDB.Driver.Linq;  后，使用  collection.AsQueryable()  获取**IMongoQueryable<T>**实例，然后就可以使用Linq对这个IMongoQueryable<T>进行查询了。

　　使用过EF的小伙伴应该都了解IQueryable+Linq的查询默认不是将整个结果集立即加载到内存中，而是**延迟加载**的，即只有使用结果时（如 在执行First,Last,Single,Count,ToList等）才会将Linq转换成Sql，在数据库中执行查询操作。类似的，在使用IMongoQueryable<T>+Linq进行查询时默认也是延迟加载的，在需要使用查询结果时，驱动会将Linq转换成聚合管道命令(aggregation pipeline)，如Linq中的Where被转换成$watch，Join转换为$lookup，Skip和Take转换为$skip和$limit等等，然后在Mongodb中执行这些管道命令获取结果，看一个栗子我们就会轻松地掌握了。

　　首先添加一些测试数据：



```
//添加学生数据
db.students.insertMany([
    {"no":1, "stuName":"jack", "age":23, "classNo":1},
    {"no":2, "stuName":"tom", "age":20, "classNo":2},
    {"no":3, "stuName":"hanmeimei", "age":22, "classNo":1},
    {"no":4, "stuName":"lilei", "age":24, "classNo":2}
    ])
        
//添加班级数据
db.classxes.insertMany([
    {"no" : 1,"clsName" : "A班"},
    {"no" : 2,"clsName" : "B班"}
    ])
```



　　这里查找了两组数据：①基本查询：查找年龄大于22岁的学生；②连接查询：查询各个学生的学号、姓名、班级名。栗子比较简单，直接看代码吧



```
        static void Main(string[] args)
        {
            //连接数据库
            var client = new MongoClient("mongodb://192.168.70.133:27017");
            //获取database
            var mydb = client.GetDatabase("myDb");
            //获取collection
            var stuCollection = mydb.GetCollection<Student>("students");
            var clsCollection = mydb.GetCollection<Classx>("classxes");
            //查找年龄大于22的学生
            Console.WriteLine("-------------查找年龄大于22的学生列表--------------");
            //1.query语法
            List<Student> stuList1 = (from stu in stuCollection.AsQueryable()
                        where stu.age > 22
                        select stu).ToList();
            //2.点语法
            List<Student> stuList2 = stuCollection.AsQueryable().Where(s => s.age > 22).ToList();
            stuList1.ForEach(stu => Console.WriteLine($"姓名:{stu?.stuName},  年龄:{stu?.age}"));
            Console.WriteLine();



            //表连接查询，查询各个学生的班级名
            Console.WriteLine("-------------表连接，查询学生的班级名----------------");
            //1.query语法
            var result1 = from stu in stuCollection.AsQueryable()
                          join cls in clsCollection.AsQueryable()
                            on stu.classNo equals cls.no
                          select new { stuno = stu.no, stu.stuName, cls.clsName };
            //2.点语法
            var result2 = stuCollection.AsQueryable().Join(
                            clsCollection.AsQueryable(),
                            stu => stu.classNo,
                            cls => cls.no,
                            (stu, cls) => new { stuno=stu.no, stu.stuName, cls.clsName }
                         );
            //遍历结果
            foreach (var item in result1)
            {
                Console.WriteLine($"学号:{item.stuno}, 姓名:{item.stuName}, 班级:{item.clsName}");
            }

            Console.ReadKey();
        }
    }
    /// <summary>
    /// 学生类
    /// </summary
    public class Student
    {
        public int no { get; set; }//学号
        public string stuName { get; set; }//姓名
        public int age { get; set; }//年龄
        public int classNo { get; set; }//班级编号
        [BsonExtraElements]
        public BsonDocument others { get; set; }
    }
    /// <summary>
    /// 班级类
    /// </summary>
    public class Classx
    {
        public int no { get; set; }//班级编号
        public string clsName { get; set; }//班级名
        [BsonExtraElements]
        public BsonDocument others { get; set; }
    }
```



 　执行结果如下：

　![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190708114248881-307694742.png)

**小结**

　　本篇简单介绍了C#驱动mongoDB进行CRUD操作，C#驱动功能十分丰富，几乎实现了所有的Mongo shell功能，这里就不再过多介绍了，有兴趣的小伙伴可以看看官方的文档。如果文中有错误的话，希望大家可以指出，我会及时修改，谢谢。