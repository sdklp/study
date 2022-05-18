# 基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发

 转载

[mb5ff2f21b6d2a1](https://blog.51cto.com/u_15075510)2017-02-10 15:12:00©著作权

*文章标签*[数据库](https://blog.51cto.com/topic/the-database-1.html)[封装](https://blog.51cto.com/topic/fengzhuang.html)[分页](https://blog.51cto.com/topic/fenye.html)[mongodb数据库](https://blog.51cto.com/topic/mongodbshujuku.html)[字段](https://blog.51cto.com/topic/ziduan.html)*文章分类*[Java](https://blog.51cto.com/nav/java)[编程语言](https://blog.51cto.com/nav/program)*阅读数*33

在上篇博客《基于C#的MongoDB数据库开发应用（1）--MongoDB数据库的基础知识和使用》里面，我总结了MongoDB数据库的一些基础信息，并在最后面部分简单介绍了数据库C#驱动的开发 ，本文继续这个主题，重点介绍MongoDB数据库C#方面的使用和封装处理过程，利用泛型和基类对象针对数据访问层进行的封装处理。

前面介绍到，当前2.2版本的数据库C#驱动的API，支持两种不同的开发接口，一个是基于MongoDatabase的对象接口，一个是IMongoDatabase的对象接口，前者中规中矩，和我们使用Shell里面的命令名称差不多；后者IMongoDatabase的接口是基于异步的，基本上和前者差别很大，而且接口都提供了异步的处理操作。

本文主要介绍基于MongoDatabase的对象接口的封装处理设置。

### 1、数据访问层的设计

在结合MongoDB数据库的C#驱动的特点，使用泛型和继承关系，把常规的处理接口做了抽象性的封装，以便封装适合更多业务的接口，减少子类代码及统一API的接口名称。

首先我们来看看大概的设计思路，我们把实体类抽象一个实体基类，方便使用。

 ![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_mongodb数据库](https://s7.51cto.com/images/blog/202108/14/1a064f4e50486c1e16396f645554969f.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

我们知道，在MongoDB数据库的集合里面，都要求文档有一个_id字段，这个是强制性的，而且这个字段的存储类型为ObjectId类型，这个值考虑了分布式的因素，综合了机器码，进程，时间戳等等方面的内容，它的构造如下所示。



ObjectId是一个12字节的 [ BSON](http://docs.mongodb.org/manual/reference/glossary/#term-bson) 类型字符串。按照字节顺序，依次代表：

- 4字节：UNIX时间戳
- 3字节：表示运行MongoDB的机器
- 2字节：表示生成此_id的进程
- 3字节：由一个随机数开始的计数器生成的值

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_mongodb数据库_02](https://s6.51cto.com/images/blog/202108/14/c4c25b8679b973dff9639618a2d818f7.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

实体基类BaseEntity包含了一个属性Id，这个是一个字符串型的对象（也可以使用ObjectId类型，但是为了方便，我们使用字符型，并声明为ObjectId类型即可），由于我们声明了该属性对象为ObjectId类型，那么我们就可以在C#代码里面使用字符串的ID类型了，代码如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
    /// <summary>
    /// MongoDB实体类的基类
    /// </summary>
    public class BaseEntity
    {        
        /// <summary>
        /// 基类对象的ID，MongoDB要求每个实体类必须有的主键
        /// </summary>
        [BsonRepresentation(BsonType.ObjectId)]        
        public string Id { get; set; }
    }1.2.3.4.5.6.7.8.9.10.11.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

然后利用泛型的方式，把数据访问层的接口提出来，并引入了数据访问层的基类进行实现和重用接口，如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_mongodb数据库_05](https://s3.51cto.com/images/blog/202108/14/6b93a5f4b6a0872ed9ff5d32b5184fad.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

其中，上面几个类的定义如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_分页_06](https://s8.51cto.com/images/blog/202108/14/fe1cf271e1799fdd453f0e0cf97be2a4.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

数据访问层基类BaseDAL的类定义如下所示，主要就是针对上面的IBaseDAL<T>接口进行实现。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_封装_07](https://s4.51cto.com/images/blog/202108/14/96429fb7ae6bb576b77629b6657a8e3e.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

有了这些基类的实现，我们对于实体类的处理就非常方便、统一了，基本上不需要在复制大量的代码来实现基础的增删改查分页实现了。

例如上面的User集合（表对象）的数据访问类定义如下所示，在对象的定义的时候，指定对应的实体类，并在构造函数里面指定对应的集合名称就可以实例化一个数据访问类了。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
    /// <summary>
    /// 数据表User对应的具体数据访问类
    /// </summary>
    public class User : BaseDAL<UserInfo>, IBaseDAL<UserInfo>
    {
        /// <summary>
        /// 默认构造函数
        /// </summary>
        public User() 
        {
            this.entitysName = "users";//对象在数据库的集合名称
        }

        .................1.2.3.4.5.6.7.8.9.10.11.12.13.14.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

### 2、基类各种接口的实现

前面小节我们介绍了实体基类，数据访问层基类接口和基类实现，以及具体集合对象的实现类的定义关系，通过泛型和继承关系，我们很好的抽象了各种对象的增删改查、分页等操作，子类继承了BaseDAL基类后，就自然而然的具有了非常强大的接口处理功能了。下面我们来继续详细介绍基于C#驱动的MongoDB数据库是如何进行各种增删改查等封装的。

1）构造MongoDatabase对象

首先我们需要利用连接字符串来构建MongoDatabase对象，因为所有的接口都是基于这个对象进行处理的，代码如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据数据库配置信息创建MongoDatabase对象，如果不指定配置信息，则从默认信息创建
        /// </summary>
        /// <param name="databaseName">数据库名称，默认空为local</param>
        /// <returns></returns>
        protected virtual MongoDatabase CreateDatabase()
        {
            string connectionString = null;

            if (!string.IsNullOrEmpty(dbConfigName))
            {
                //从配置文件中获取对应的连接信息
                connectionString = ConfigurationManager.ConnectionStrings[dbConfigName].ConnectionString;                
            }
            else
            {
                connectionString = defaultConnectionString;
            }

            var client = new MongoClient(connectionString);
            var database = client.GetServer().GetDatabase(new MongoUrl(connectionString).DatabaseName);

            return database;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

2）构建MongoCollection对象

上面构建了MongoDatabase对象后，我们需要基于这个基础上再创建一个对象的MongoCollection对象，这个就是类似我们关系数据库里面的表对象的原型了。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 获取操作对象的MongoCollection集合,强类型对象集合
        /// </summary>
        /// <returns></returns>
        protected virtual MongoCollection<T> GetCollection()
        {
            MongoDatabase database = CreateDatabase();
            return database.GetCollection<T>(this.entitysName);
        }1.2.3.4.5.6.7.8.9.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

3）查询单个对象

利用MongoCollection对象，我们可以通过API接口获取对应的对象，单个对象的接口为FindOneById（也可以用FindOne接口，如注释部分的代码），我们具体的处理代码如下所示

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

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
            return collection.FindOneById(new ObjectId(id)); //FindOne(Query.EQ("_id", new ObjectId(id)));
        }1.2.3.4.5.6.7.8.9.10.11.12.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

如果基于条件的单个记录查询，我们可以使用Expression<Func<T, bool>>和IMongoQuery的参数进行处理，如下代码所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据条件查询数据库,如果存在返回第一个对象
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <returns>存在则返回指定的第一个对象,否则返回默认值</returns>
        public virtual T FindSingle(Expression<Func<T, bool>> match)
        {
            MongoCollection<T> collection = GetCollection();
            return collection.AsQueryable().Where(match).FirstOrDefault();
        }

        /// <summary>
        /// 根据条件查询数据库,如果存在返回第一个对象
        /// </summary>
        /// <param name="query">条件表达式</param>
        /// <returns>存在则返回指定的第一个对象,否则返回默认值</returns>
        public virtual T FindSingle(IMongoQuery query)
        {
            MongoCollection<T> collection = GetCollection();
            return collection.FindOne(query);
        } 1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

4）IQueryable的接口利用

使用过EF的实体框架的话，我们对其中的IQueryable<T>印象很深刻，它可以给我提供很好的LINQ语法获取对应的信息，它可以通过使用Expression<Func<T, bool>>和IMongoQuery的参数来进行条件的查询操作，MongoCollection对象有一个AsQueryable()的API进行转换，如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 返回可查询的记录源
        /// </summary>
        /// <returns></returns>
        public virtual IQueryable<T> GetQueryable()
        {
            MongoCollection<T> collection = GetCollection();
            IQueryable<T> query = collection.AsQueryable();

            return query.OrderBy(this.SortPropertyName, this.IsDescending);
        }1.2.3.4.5.6.7.8.9.10.11.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

如果是通过使用Expression<Func<T, bool>>和IMongoQuery的参数，那么处理的接口代码如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据条件表达式返回可查询的记录源
        /// </summary>
        /// <param name="match">查询条件</param>
        /// <param name="sortPropertyName">排序表达式</param>
        /// <param name="isDescending">如果为true则为降序，否则为升序</param>
        /// <returns></returns>
        public virtual IQueryable<T> GetQueryable(Expression<Func<T, bool>> match, string sortPropertyName, bool isDescending = true)
        {
            MongoCollection<T> collection = GetCollection();
            IQueryable<T> query = collection.AsQueryable();
            if (match != null)
            {
                query = query.Where(match);
            }
            return query.OrderBy(sortPropertyName, isDescending);
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据条件表达式返回可查询的记录源
        /// </summary>
        /// <param name="query">查询条件</param>
        /// <param name="sortPropertyName">排序表达式</param>
        /// <param name="isDescending">如果为true则为降序，否则为升序</param>
        /// <returns></returns>
        public virtual IQueryable<T> GetQueryable(IMongoQuery query, string sortPropertyName, bool isDescending = true)
        {
            MongoCollection<T> collection = GetCollection();
            IQueryable<T> queryable = collection.Find(query).AsQueryable();

            return queryable.OrderBy(sortPropertyName, isDescending);
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

5）集合的查询处理

通过利用上面的IQueryable<T>对象，以及使用Expression<Func<T, bool>>和IMongoQuery的参数，我们很好的进行集合的查询处理操作的了，具体代码如下所示

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据条件查询数据库,并返回对象集合
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <returns>指定对象的集合</returns>
        public virtual IList<T> Find(Expression<Func<T, bool>> match)
        {
            return GetQueryable(match).ToList();
        }

        /// <summary>
        /// 根据条件查询数据库,并返回对象集合
        /// </summary>
        /// <param name="query">条件表达式</param>
        /// <returns>指定对象的集合</returns>
        public virtual IList<T> Find(IMongoQuery query)
        {
            MongoCollection<T> collection = GetCollection();
            return collection.Find(query).ToList();
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

对于分页，我们是非常需要的，首先在大数据的集合里面，我们不可能一股脑的把所有的数据全部返回，因此根据分页参数返回有限数量的集合处理就是我们应该做的，分页的操作代码和上面很类似，只是利用了Skip和Take的接口，返回我们需要的记录数量就可以了。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据条件查询数据库,并返回对象集合(用于分页数据显示)
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <param name="info">分页实体</param>
        /// <returns>指定对象的集合</returns>
        public virtual IList<T> FindWithPager(Expression<Func<T, bool>> match, PagerInfo info)
        {
            int pageindex = (info.CurrenetPageIndex < 1) ? 1 : info.CurrenetPageIndex;
            int pageSize = (info.PageSize <= 0) ? 20 : info.PageSize;

            int excludedRows = (pageindex - 1) * pageSize;

            IQueryable<T> query = GetQueryable(match);
            info.RecordCount = query.Count();

            return query.Skip(excludedRows).Take(pageSize).ToList();
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

或者是下面的代码

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据条件查询数据库,并返回对象集合(用于分页数据显示)
        /// </summary>
        /// <param name="query">条件表达式</param>
        /// <param name="info">分页实体</param>
        /// <returns>指定对象的集合</returns>
        public virtual IList<T> FindWithPager(IMongoQuery query, PagerInfo info)
        {
            int pageindex = (info.CurrenetPageIndex < 1) ? 1 : info.CurrenetPageIndex;
            int pageSize = (info.PageSize <= 0) ? 20 : info.PageSize;

            int excludedRows = (pageindex - 1) * pageSize;

            IQueryable<T> queryable = GetQueryable(query);
            info.RecordCount = queryable.Count();

            return queryable.Skip(excludedRows).Take(pageSize).ToList();
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

6）对象的写入操作

对象的写入可以使用save，它是根据_id的来决定插入还是更新的，如下代码所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 保存指定对象到数据库中，根据Id的值，决定是插入还是更新
        /// </summary>
        /// <param name="t">指定的对象</param>
        /// <returns>执行成功指定对象信息</returns>
        public virtual T Save(T t)
        {
            ArgumentValidation.CheckForNullReference(t, "传入的对象t为空");

            MongoCollection<T> collection = GetCollection();
            var result = collection.Save(t);
            return t;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

插入记录就可以利用insert方法进行处理的，代码如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 插入指定对象到数据库中
        /// </summary>
        /// <param name="t">指定的对象</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c></returns>
        public virtual bool Insert(T t)
        {
            ArgumentValidation.CheckForNullReference(t, "传入的对象t为空");

            MongoCollection<T> collection = GetCollection();
            var result = collection.Insert(t);
            return result != null && result.DocumentsAffected > 0;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

如果是批量插入，可以利用它的insertBatch的方法进行处理，具体代码如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 插入指定对象集合到数据库中
        /// </summary>
        /// <param name="list">指定的对象集合</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c></returns>
        public virtual bool InsertBatch(IEnumerable<T> list)
        {
            ArgumentValidation.CheckForNullReference(list, "传入的对象list为空");

            MongoCollection<T> collection = GetCollection();
            var result = collection.InsertBatch(list);
            return result.Any(s => s != null && s.DocumentsAffected > 0); //部分成功也返回true
        }1.2.3.4.5.6.7.8.9.10.11.12.13.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

7）对象的更新操作

更新操作分为了两个不同的部分，一个是全部的记录更新，也就是整个JSON的替换操作了，一般我们是在原来的基础上进行更新的，如下代码所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 更新对象属性到数据库中
        /// </summary>
        /// <param name="t">指定的对象</param>
        /// <param name="id">主键的值</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c></returns>
        public virtual bool Update(T t, string id)
        {
            ArgumentValidation.CheckForNullReference(t, "传入的对象t为空");
            ArgumentValidation.CheckForEmptyString(id, "传入的对象id为空");

            bool result = false;
            MongoCollection<T> collection = GetCollection();
            var existing = FindByID(id);
            if (existing != null)
            {
                var resultTmp = collection.Save(t);
                result = resultTmp != null && resultTmp.DocumentsAffected > 0;
            }

            return result;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

还有一种方式是部分更新，也就是更新里面的指定一个或几个字段，不会影响其他字段，也就不会全部替换掉其他内容的操作了。这里利用了一个UpdateBuilder<T>的对象，用来指定那些字段需要更新，以及这些字段的值内容的，具体的更新代码如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 封装处理更新的操作
        /// </summary>
        /// <param name="id">主键的值</param>
        /// <param name="update">更新对象</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c></returns>
        public virtual bool Update(string id, UpdateBuilder<T> update)
        {
            ArgumentValidation.CheckForNullReference(update, "传入的对象update为空");
            ArgumentValidation.CheckForEmptyString(id, "传入的对象id为空");

            var query = Query.EQ("_id", new ObjectId(id));
            MongoCollection<T> collection = GetCollection();
            var result = collection.Update(query, update);
            return result != null && result.DocumentsAffected > 0;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

部分更新，可以结合使用Inc和Set方法来进行处理，如下是我在子类里面利用到上面的Update部分更新的API进行处理个别字段的更新操作。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 为用户增加岁数
        /// </summary>
        /// <param name="id">记录ID</param>
        /// <param name="addAge">待增加的岁数</param>
        /// <returns></returns>
        public bool IncreaseAge(string id, int addAge)
        {
            //增加指定的岁数
            var query = Query<UserInfo>.EQ(s => s.Id, id);
            var update = Update<UserInfo>.Inc(s => s.Age, addAge);

            var collection = GetCollection();
            var result = collection.Update(query, update);
            return result != null && result.DocumentsAffected > 0;
        }

        /// <summary>
        /// 单独修改用户的名称
        /// </summary>
        /// <param name="id">记录ID</param>
        /// <param name="newName">用户新名称</param>
        /// <returns></returns>
        public bool UpdateName(string id, string newName)
        {
            //增加指定的岁数
            var query = Query<UserInfo>.EQ(s => s.Id, id);
            var update = Update<UserInfo>.Set(s => s.Name, newName);

            var collection = GetCollection();
            var result = collection.Update(query, update);
            return result != null && result.DocumentsAffected > 0;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.25.26.27.28.29.30.31.32.33.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

8）对象的删除操作

对象的删除，一般可以利用条件进行删除，如单个删除可以使用_id属性进行处理，也可以利用批量删除的接口进行删除操作，代码如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据指定对象的ID,从数据库中删除指定对象
        /// </summary>
        /// <param name="id">对象的ID</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c>。</returns>
        public virtual bool Delete(string id)
        {
            ArgumentValidation.CheckForEmptyString(id, "传入的对象id为空");

            MongoCollection<T> collection = GetCollection();
            //var result = collection.Remove(Query<T>.EQ(s => s.Id, id));
            var result = collection.Remove(Query.EQ("_id", new ObjectId(id)));
            return result != null && result.DocumentsAffected > 0;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

其中上面注释的var result = collection.Remove(Query<T>.EQ(s => s.Id, id));代码，就是利用了强类型的对象属性和值进行移除，一样可以的。

对于批量删除，可以利用Query的不同进行处理。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据指定对象的ID,从数据库中删除指定指定的对象
        /// </summary>
        /// <param name="idList">对象的ID集合</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c>。</returns>
        public virtual bool DeleteBatch(List<string> idList)
        {
            ArgumentValidation.CheckForNullReference(idList, "传入的对象idList为空");

            MongoCollection<T> collection = GetCollection();
            var query = Query.In("_id", new BsonArray(idList));
            var result = collection.Remove(query);
            return result != null && result.DocumentsAffected > 0;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

或者基于IMongoQuery的条件进行处理。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据指定条件,从数据库中删除指定对象
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c>。</returns>
        public virtual bool DeleteByQuery(IMongoQuery query)
        {
            MongoCollection<T> collection = GetCollection();
            var result = collection.Remove(query);
            return result != null && result.DocumentsAffected > 0;
        } 1.2.3.4.5.6.7.8.9.10.11.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

9）其他相关接口

一般除了上面的接口，还有一些其他的接口，如获取记录的总数、判断条件的记录是否存在等也是很常见的，他们的代码封装如下所示。

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 获取表的所有记录数量
        /// </summary>
        /// <returns></returns>
        public virtual int GetRecordCount()
        {
            return GetQueryable().Count();
        }1.2.3.4.5.6.7.8.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
        /// <summary>
        /// 根据查询条件，判断是否存在记录
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <returns></returns>
        public virtual bool IsExistRecord(Expression<Func<T, bool>> match)
        {
            return GetQueryable(match).Any();//.Count() > 0
        }

        /// <summary>
        /// 根据查询条件，判断是否存在记录
        /// </summary>
        /// <param name="query">条件表达式</param>
        /// <returns></returns>
        public virtual bool IsExistRecord(IMongoQuery query)
        {
            return GetQueryable(query).Any();//.Count() > 0
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.
```

![基于C#的MongoDB数据库开发应用（2）--MongoDB数据库的C#开发_数据库_03](https://s7.51cto.com/images/blog/202108/14/73375688cedc126f22138890168521ea.gif?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

 

非常感谢您的详细阅读，以上基本上就是我对整个MongoDB数据库的各个接口的基类封装处理了，其中已经覆盖到了基础的增删改查、分页等操作接口，以及一些特殊的条件处理接口的扩展，我们利用这些封装好的基类很好的简化了子类的代码，而且可以更方便的在基类的基础上进行特殊功能的扩展处理。

当然，以上介绍的都不是最新的接口，是2.0（或2.2）版本之前的接口实现，虽然在2.2里面也还可以利用上面的MongoDatabase对象接口，但是IMongoDatabase最新的接口已经全面兼容异步的操作，但也是一个很大的跳跃，基本上引入了不同的接口命名和处理方式，利用异步可以支持更好的处理体验，但是也基本上是需要对所有的接口进行了全部的重写了。