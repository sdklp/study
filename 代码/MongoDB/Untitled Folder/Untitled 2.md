# 基于C#的MongoDB数据库开发应用（3）--MongoDB数据库的C#开发之异步接口

 原创

[wuhuacong](https://blog.51cto.com/wuhuacong)2021-07-26 09:18:37©著作权

*文章标签*[MongoDB数据库](https://blog.51cto.com/topic/mongodbshujuku.html)[编程](https://blog.51cto.com/topic/biancheng.html)*文章分类*[IT业界](https://blog.51cto.com/nav/industry)[其它](https://blog.51cto.com/nav/other)*阅读数*41

## 基于C#的MongoDB数据库开发应用（3）--MongoDB数据库的C#开发之异步接口

在前面的系列博客中，我曾经介绍过，MongoDB数据库的C#驱动已经全面支持异步的处理接口，并且接口的定义几乎是重写了。本篇主要介绍MongoDB数据库的C#驱动的最新接口使用，介绍基于新接口如何实现基础的增删改查及分页等处理，以及如何利用异步接口实现基类相关的异步操作。

在前面的系列博客中，我曾经介绍过，MongoDB数据库的C#驱动已经全面支持异步的处理接口，并且接口的定义几乎是重写了。本篇主要介绍MongoDB数据库的C#驱动的最新接口使用，介绍基于新接口如何实现基础的增删改查及分页等处理，以及如何利用异步接口实现基类相关的异步操作。

MongoDB数据库驱动在2.2版本（或者是从2.0开始）好像完全改写了API的接口，因此目前这个版本同时支持两个版本的API处理，一个是基于MongoDatabase的对象接口，一个是IMongoDatabase的对象接口，前者中规中矩，和我们使用Shell里面的命令名称差不多，后者IMongoDatabase的接口是基于异步的，基本上和前者差别很大，而且接口都提供了异步的处理操作。

### 1、MongoDB数据库C#驱动的新接口

新接口也还是基于数据库，集合，文档这样的处理概念进行封装，只是它们的接口不再一样了，我们还是按照前面的做法，定义一个数据库访问的基类，对MongoDB数据库的相关操作封装在基类里面，方便使用，同时基类利用泛型对象，实现更强类型的约束及支持，如基类BaseDAL的定义如下所示。

```
    /// <summary>
    /// 数据访问层的基类
    /// </summary>
    public partial class BaseDAL<T> where T : BaseEntity, new()1.2.3.4.
```

利用泛型的方式，把数据访问层的接口提出来，并引入了数据访问层的基类进行实现和重用接口，如下所示。

![基于C#的MongoDB数据库开发应用（3）--MongoDB数据库的C#开发之异步接口_MongoDB数据库](https://s5.51cto.com/images/blog/202107/26/a751bcfef1c6dc682604f286becfda16.png?x-oss-process=image/watermark,size_14,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=)

![基于C#的MongoDB数据库开发应用（3）--MongoDB数据库的C#开发之异步接口_编程_02](https://s7.51cto.com/images/blog/202107/26/f42dd04ae12a6aa9b5bfda4433ecd310.png?x-oss-process=image/watermark,size_14,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=)

基于新接口，如获取数据库对象的操作，则利用了IMongoDatabase的接口了，如下所示。

```
            var client = new MongoClient(connectionString);
            var database = client.GetDatabase(new MongoUrl(connectionString).DatabaseName);1.2.
```

相对以前的常规接口，MongoClient对象已经没有了**GetServer**的接口了。如果对创建数据库对象的操作做更好的封装，可以利用配置文件进行指定的话，那么方法可以封装如下所示。

```
        /// <summary>
        /// 根据数据库配置信息创建MongoDatabase对象，如果不指定配置信息，则从默认信息创建
        /// </summary>
        /// <param name="databaseName">数据库名称，默认空为local</param>
        /// <returns></returns>
        protected virtual IMongoDatabase CreateDatabase()
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
            var database = client.GetDatabase(new MongoUrl(connectionString).DatabaseName);

            return database;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.
```

根据IMongoDatabase 接口，那么其获取集合对象的操作如下所示，它使用了另外一个定义IMongoCollection了。

```
        /// <summary>
        /// 获取操作对象的IMongoCollection集合,强类型对象集合
        /// </summary>
        /// <returns></returns>
        public virtual IMongoCollection<T> GetCollection()
        {
            var database = CreateDatabase();
            return database.GetCollection<T>(this.entitysName);
        }1.2.3.4.5.6.7.8.9.
```

### 2、查询单个对象实现封装处理

基于新接口的查询处理，已经没有FindOne的方法定义了，只是使用了Find的方法，而且也没有了Query的对象可以作为条件进行处理，而是采用了新的定义对象FilterDefinition，例如对于根据ID查询单个对象，接口的实现如下所示。

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

对于利用FilterDefinition进行查询的操作，如下所示。

```
        /// <summary>
        /// 根据条件查询数据库,如果存在返回第一个对象
        /// </summary>
        /// <param name="filter">条件表达式</param>
        /// <returns>存在则返回指定的第一个对象,否则返回默认值</returns>
        public virtual T FindSingle(FilterDefinition<T> filter)
        {
            IMongoCollection<T> collection = GetCollection();
            return collection.Find(filter).FirstOrDefault();
        } 1.2.3.4.5.6.7.8.9.10.
```

我们可以看到，这些都是利用Find方法的不同重载实现不同条件的处理的。

对于这个新接口，异步是一个重要的改变，那么它的异步处理是如何的呢，我们看看上面两个异步的实现操作，具体代码如下所示。

```
        /// <summary>
        /// 查询数据库,检查是否存在指定ID的对象（异步）
        /// </summary>
        /// <param name="key">对象的ID值</param>
        /// <returns>存在则返回指定的对象,否则返回Null</returns>
        public virtual async Task<T> FindByIDAsync(string id)
        {
            ArgumentValidation.CheckForEmptyString(id, "传入的对象id为空");

            IMongoCollection<T> collection = GetCollection();
            return await collection.FindAsync(s=>s.Id == id).Result.FirstOrDefaultAsync(); 
        }

        /// <summary>
        /// 根据条件查询数据库,如果存在返回第一个对象（异步）
        /// </summary>
        /// <param name="query">条件表达式</param>
        /// <returns>存在则返回指定的第一个对象,否则返回默认值</returns>
        public virtual async Task<T> FindSingleAsync(FilterDefinition<T> query)
        {
            return await GetQueryable(query).SingleOrDefaultAsync();
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.
```

我们看到，上面的Collection或者GetQueryable(query)返回的对象，都提供给了以Async结尾的异步方法，因此对异步的封装也是非常方便的，上面的GetQueryable(query)是另外一个公共的实现方法，具体代码如下所示。

```
        /// <summary>
        /// 返回可查询的记录源
        /// </summary>
        /// <param name="query">查询条件</param>
        /// <returns></returns>
        public virtual IFindFluent<T, T> GetQueryable(FilterDefinition<T> query)
        {
            return GetQueryable(query, this.SortPropertyName, this.IsDescending);
        }1.2.3.4.5.6.7.8.9.
        /// <summary>
        /// 根据条件表达式返回可查询的记录源
        /// </summary>
        /// <param name="query">查询条件</param>
        /// <param name="sortPropertyName">排序表达式</param>
        /// <param name="isDescending">如果为true则为降序，否则为升序</param>
        /// <returns></returns>
        public virtual IFindFluent<T,T> GetQueryable(FilterDefinition<T> query, string sortPropertyName, bool isDescending = true)
        {
            IMongoCollection<T> collection = GetCollection();
            IFindFluent<T, T> queryable = collection.Find(query);

            var sort = this.IsDescending ? Builders<T>.Sort.Descending(this.SortPropertyName) : Builders<T>.Sort.Ascending(this.SortPropertyName);
            return queryable.Sort(sort);
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.
```

我们可以看到，它返回了IFindFluent<T, T>的对象，这个和以前返回的IMongoQuery对象又有不同，基本上，使用最新的接口，所有的实现都不太一样，这也是因为MongoDB还在不停变化之中有关。

### 3、GetQueryable几种方式

为了简化代码，方便使用，我们对获取MongoDB的LINQ方式的处理做了简单的封装，提供了几个GetQueryable的方式，具体代码如下所示。

```
        /// <summary>
        /// 返回可查询的记录源
        /// </summary>
        /// <returns></returns>
        public virtual IQueryable<T> GetQueryable()
        {
            IMongoCollection<T> collection = GetCollection();
            IQueryable<T> query = collection.AsQueryable();

            return query.OrderBy(this.SortPropertyName, this.IsDescending);
        }1.2.3.4.5.6.7.8.9.10.11.
        /// <summary>
        /// 根据条件表达式返回可查询的记录源
        /// </summary>
        /// <param name="match">查询条件</param>
        /// <param name="orderByProperty">排序表达式</param>
        /// <param name="isDescending">如果为true则为降序，否则为升序</param>
        /// <returns></returns>
        public virtual IQueryable<T> GetQueryable<TKey>(Expression<Func<T, bool>> match, Expression<Func<T, TKey>> orderByProperty, bool isDescending = true)
        {
            IMongoCollection<T> collection = GetCollection();
            IQueryable<T> query = collection.AsQueryable();

            if (match != null)
            {
                query = query.Where(match);
            }

            if (orderByProperty != null)
            {
                query = isDescending ? query.OrderByDescending(orderByProperty) : query.OrderBy(orderByProperty);
            }
            else
            {
                query = query.OrderBy(this.SortPropertyName, isDescending);
            }
            return query;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.25.26.27.
```

以及基于FilterDefinition的条件处理，并返回IFindFluent<T,T>接口对象的代码如下所示。

```
        /// <summary>
        /// 根据条件表达式返回可查询的记录源
        /// </summary>
        /// <param name="query">查询条件</param>
        /// <param name="sortPropertyName">排序表达式</param>
        /// <param name="isDescending">如果为true则为降序，否则为升序</param>
        /// <returns></returns>
        public virtual IFindFluent<T,T> GetQueryable(FilterDefinition<T> query, string sortPropertyName, bool isDescending = true)
        {
            IMongoCollection<T> collection = GetCollection();
            IFindFluent<T, T> queryable = collection.Find(query);

            var sort = this.IsDescending ? Builders<T>.Sort.Descending(this.SortPropertyName) : Builders<T>.Sort.Ascending(this.SortPropertyName);
            return queryable.Sort(sort);
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.
```

### 4、集合的查询操作封装处理

基于上面的封装，对结合的查询，也是基于不同的条件进行处理，返回对应的列表的处理方式， 最简单的是利用GetQueryable方式进行处理，代码如下所示。

```
        /// <summary>
        /// 根据条件查询数据库,并返回对象集合
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <returns>指定对象的集合</returns>
        public virtual IList<T> Find(Expression<Func<T, bool>> match)
        {
            return GetQueryable(match).ToList();
        }1.2.3.4.5.6.7.8.9.
```

或者如下所示

```
        /// <summary>
        /// 根据条件查询数据库,并返回对象集合
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <returns>指定对象的集合</returns>
        public virtual IList<T> Find(FilterDefinition<T> query)
        {
            return GetQueryable(query).ToList();
        }1.2.3.4.5.6.7.8.9.
```

以及对排序字段，以及升降序的处理操作如下所示。

```
        /// <summary>
        /// 根据条件查询数据库,并返回对象集合
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <param name="orderByProperty">排序表达式</param>
        /// <param name="isDescending">如果为true则为降序，否则为升序</param>
        /// <returns></returns>
        public virtual IList<T> Find<TKey>(Expression<Func<T, bool>> match, Expression<Func<T, TKey>> orderByProperty, bool isDescending = true)
        {
            return GetQueryable<TKey>(match, orderByProperty, isDescending).ToList();
        }

        /// <summary>
        /// 根据条件查询数据库,并返回对象集合
        /// </summary>
        /// <param name="query">条件表达式</param>
        /// <param name="orderByProperty">排序字段</param>
        /// <param name="isDescending">如果为true则为降序，否则为升序</param>
        /// <returns></returns>
        public virtual IList<T> Find<TKey>(FilterDefinition<T> query, string orderByProperty, bool isDescending = true)
        {
            return GetQueryable(query, orderByProperty, isDescending).ToList();
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.
```

以及利用这些条件进行分页的处理代码如下所示。

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
        }

        /// <summary>
        /// 根据条件查询数据库,并返回对象集合(用于分页数据显示)
        /// </summary>
        /// <param name="query">条件表达式</param>
        /// <param name="info">分页实体</param>
        /// <returns>指定对象的集合</returns>
        public virtual IList<T> FindWithPager(FilterDefinition<T> query, PagerInfo info)
        {
            int pageindex = (info.CurrenetPageIndex < 1) ? 1 : info.CurrenetPageIndex;
            int pageSize = (info.PageSize <= 0) ? 20 : info.PageSize;

            int excludedRows = (pageindex - 1) * pageSize;

            var find = GetQueryable(query);
            info.RecordCount = (int)find.Count();

            return find.Skip(excludedRows).Limit(pageSize).ToList();
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.25.26.27.28.29.30.31.32.33.34.35.36.37.
```

对于异步的封装处理，基本上也和上面的操作差不多，例如对于基础的查询，异步操作封装如下所示。

```
        /// <summary>
        /// 根据条件查询数据库,并返回对象集合
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <returns>指定对象的集合</returns>
        public virtual async Task<IList<T>> FindAsync(Expression<Func<T, bool>> match)
        {
            return await Task.FromResult(GetQueryable(match).ToList());
        }

        /// <summary>
        /// 根据条件查询数据库,并返回对象集合
        /// </summary>
        /// <param name="query">条件表达式</param>
        /// <returns>指定对象的集合</returns>
        public virtual async Task<IList<T>> FindAsync(FilterDefinition<T> query)
        {
            return await GetQueryable(query).ToListAsync();
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.
```

复杂一点的分页处理操作代码封装如下所示。

```
        /// <summary>
        /// 根据条件查询数据库,并返回对象集合(用于分页数据显示)
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <param name="info">分页实体</param>
        /// <returns>指定对象的集合</returns>
        public virtual async Task<IList<T>> FindWithPagerAsync(Expression<Func<T, bool>> match, PagerInfo info)
        {
            int pageindex = (info.CurrenetPageIndex < 1) ? 1 : info.CurrenetPageIndex;
            int pageSize = (info.PageSize <= 0) ? 20 : info.PageSize;

            int excludedRows = (pageindex - 1) * pageSize;

            IQueryable<T> query = GetQueryable(match);
            info.RecordCount = query.Count();

            var result = query.Skip(excludedRows).Take(pageSize).ToList();
            return await Task.FromResult(result);
        }

        /// <summary>
        /// 根据条件查询数据库,并返回对象集合(用于分页数据显示)
        /// </summary>
        /// <param name="query">条件表达式</param>
        /// <param name="info">分页实体</param>
        /// <returns>指定对象的集合</returns>
        public virtual async Task<IList<T>> FindWithPagerAsync(FilterDefinition<T> query, PagerInfo info)
        {
            int pageindex = (info.CurrenetPageIndex < 1) ? 1 : info.CurrenetPageIndex;
            int pageSize = (info.PageSize <= 0) ? 20 : info.PageSize;

            int excludedRows = (pageindex - 1) * pageSize;

            var queryable = GetQueryable(query);
            info.RecordCount = (int)queryable.Count();

            return await queryable.Skip(excludedRows).Limit(pageSize).ToListAsync();
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.25.26.27.28.29.30.31.32.33.34.35.36.37.38.
```

### 5、增删改方法封装处理

对于常规的增删改操作，在新的MongoDB数据库驱动里面也修改了名称，使用的时候也需要进行调整处理了。

```
        /// <summary>
        /// 插入指定对象到数据库中
        /// </summary>
        /// <param name="t">指定的对象</param>
        public virtual void Insert(T t)
        {
            ArgumentValidation.CheckForNullReference(t, "传入的对象t为空");

            IMongoCollection<T> collection = GetCollection();
            collection.InsertOne(t);
        }1.2.3.4.5.6.7.8.9.10.11.
```

异步的操作实现如下所示。

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

批量插入记录的操作如下所示。

```
        /// <summary>
        /// 插入指定对象集合到数据库中
        /// </summary>
        /// <param name="list">指定的对象集合</param>
        public virtual void InsertBatch(IEnumerable<T> list)
        {
            ArgumentValidation.CheckForNullReference(list, "传入的对象list为空");

            IMongoCollection<T> collection = GetCollection();
            collection.InsertMany(list);
        }1.2.3.4.5.6.7.8.9.10.11.
```

对应的异步操作处理如下所示，这些都是利用原生支持的异步处理接口实现的。

```
        /// <summary>
        /// 插入指定对象集合到数据库中
        /// </summary>
        /// <param name="list">指定的对象集合</param>
        public virtual async Task InsertBatchAsync(IEnumerable<T> list)
        {
            ArgumentValidation.CheckForNullReference(list, "传入的对象list为空");

            IMongoCollection<T> collection = GetCollection();
            await collection.InsertManyAsync(list);
        }1.2.3.4.5.6.7.8.9.10.11.
```

更新操作，有一种整个替换更新，还有一个是部分更新，它们两者是有区别的，如对于替换更新的操作，它的接口封装处理如下所示

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
            IMongoCollection<T> collection = GetCollection();
            //使用 IsUpsert = true ，如果没有记录则写入
            var update = collection.ReplaceOne(s => s.Id == id, t, new UpdateOptions() { IsUpsert = true });
            result = update != null && update.ModifiedCount > 0;

            return result;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.
```

如果对于部分字段的更新，那么操作如下所示 ，主要是利用UpdateDefinition对象来指定需要更新那些字段属性及值等信息。

```
        /// <summary>
        /// 封装处理更新的操作(部分字段更新)
        /// </summary>
        /// <param name="id">主键的值</param>
        /// <param name="update">更新对象</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c></returns>
        public virtual bool Update(string id, UpdateDefinition<T> update)
        {
            ArgumentValidation.CheckForNullReference(update, "传入的对象update为空");
            ArgumentValidation.CheckForEmptyString(id, "传入的对象id为空");

            IMongoCollection<T> collection = GetCollection();
            var result = collection.UpdateOne(s => s.Id == id, update, new UpdateOptions() { IsUpsert = true });
            return result != null && result.ModifiedCount > 0;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.
```

上面的异步更新操作如下所示。

```
        /// <summary>
        /// 封装处理更新的操作(部分字段更新)
        /// </summary>
        /// <param name="id">主键的值</param>
        /// <param name="update">更新对象</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c></returns>
        public virtual async Task<bool> UpdateAsync(string id, UpdateDefinition<T> update)
        {
            ArgumentValidation.CheckForNullReference(update, "传入的对象update为空");
            ArgumentValidation.CheckForEmptyString(id, "传入的对象id为空");

            IMongoCollection<T> collection = GetCollection();
            var result = await collection.UpdateOneAsync(s => s.Id == id, update, new UpdateOptions() { IsUpsert = true });

            var sucess = result != null && result.ModifiedCount > 0;
            return await Task.FromResult(sucess);
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.
```

删除的操作也是类似的了，基本上和上面的处理方式接近，顺便列出来供参考学习。

```
        /// <summary>
        /// 根据指定对象的ID,从数据库中删除指定对象
        /// </summary>
        /// <param name="id">对象的ID</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c>。</returns>
        public virtual bool Delete(string id)
        {
            ArgumentValidation.CheckForEmptyString(id, "传入的对象id为空");

            IMongoCollection<T> collection = GetCollection();
            var result = collection.DeleteOne(s=> s.Id == id);
            return result != null && result.DeletedCount > 0;
        }

        /// <summary>
        /// 根据指定对象的ID,从数据库中删除指定指定的对象
        /// </summary>
        /// <param name="idList">对象的ID集合</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c>。</returns>
        public virtual bool DeleteBatch(List<string> idList)
        {
            ArgumentValidation.CheckForNullReference(idList, "传入的对象idList为空");

            IMongoCollection<T> collection = GetCollection();
            var query = Query.In("_id", new BsonArray(idList));
            var result = collection.DeleteMany(s => idList.Contains(s.Id));
            return result != null && result.DeletedCount > 0;
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.25.26.27.28.
```

如果根据条件的删除，也可以利用条件定义的两种方式，具体代码如下所示。

```
        /// <summary>
        /// 根据指定条件,从数据库中删除指定对象
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c>。</returns>
        public virtual bool DeleteByExpression(Expression<Func<T, bool>> match)
        {
            IMongoCollection<T> collection = GetCollection();
            collection.AsQueryable().Where(match).ToList().ForEach(s => collection.DeleteOne(t => t.Id == s.Id));
            return true;
        }

        /// <summary>
        /// 根据指定条件,从数据库中删除指定对象
        /// </summary>
        /// <param name="match">条件表达式</param>
        /// <returns>执行成功返回<c>true</c>，否则为<c>false</c>。</returns>
        public virtual bool DeleteByQuery(FilterDefinition<T> query)
        {
            IMongoCollection<T> collection = GetCollection();
            var result = collection.DeleteMany(query);
            return result != null && result.DeletedCount > 0;
        } 1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.
```

### 6、数据访问子类的封装和方法调用

好了，基本上上面大多数使用的方法都发布出来了，封装的原则就是希望数据访问层子类能够简化代码，减少不必要的复制粘贴，而且必要的时候， 也可以对具体的接口进行重写，实现更强大的处理控制。

例如对于上面的基类，我们在具体的集合对象封装的时候，需要继承于BaseDAL<T>这样的方式，这样可以利用基类丰富的接口，简化子类的代码，如User集合类的代码如下所示。

```
   /// <summary>
    /// User集合（表）的数据访问类
    /// </summary>
    public class User : BaseDAL<UserInfo>
    {
        /// <summary>
        /// 默认构造函数
        /// </summary>
        public User() 
        {
            this.entitysName = "users";//对象在数据库的集合名称
        }

        /// <summary>
        /// 为用户增加岁数
        /// </summary>
        /// <param name="id">记录ID</param>
        /// <param name="addAge">待增加的岁数</param>
        /// <returns></returns>
        public bool IncreaseAge(string id, int addAge)
        {
            var collection = GetCollection();
            var update = Builders<UserInfo>.Update.Inc(s => s.Age, addAge);
            var result = collection.UpdateOne(s => s.Id == id, update);
            return result != null && result.ModifiedCount > 0;
        }

        /// <summary>
        /// 单独修改用户的名称
        /// </summary>
        /// <param name="id">记录ID</param>
        /// <param name="newName">用户新名称</param>
        /// <returns></returns>
        public bool UpdateName(string id, string newName)
        {
            var collection = GetCollection();
            var update = Builders<UserInfo>.Update.Set(s => s.Name, newName);
            var result = collection.UpdateOne(s => s.Id == id, update);
            return result != null && result.ModifiedCount > 0;
        }
    }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.25.26.27.28.29.30.31.32.33.34.35.36.37.38.39.40.41.
```

在界面层使用的时候，只需要声明一个对应的User数据访问类dal对象，就可以利用它的相关接口进行对应的数据操作了，如下代码所示。

```
            IList<UserInfo> members = dal.Find(s => s.Name.StartsWith("Test"));
            foreach (UserInfo info in members)
            {
                Console.WriteLine(info.Id + ", " + info.Name);
            }1.2.3.4.5.
            var user = dal.FindSingle(s => s.Id == "56815e6634ab091e1406ec68");
            if(user != null)
            {
                Console.WriteLine(user.Name);
            }1.2.3.4.5.
```

对于部分字段的更新处理，在界面上，我们可以利用封装好的接口进行处理，如下所示。

```
        /// <summary>
        /// 测试部分字段修改的处理
        /// </summary>
        private void btnAddAge_Click(object sender, EventArgs e)
        {
            UserInfo info = dal.GetAll()[0];
            if(info != null)
            {
                Console.WriteLine("Age before Incr:" + info.Age);

                int addAge = 10;
                dal.IncreaseAge(info.Id, addAge);

                info = dal.FindByID(info.Id);
                Console.WriteLine("Age after Incr:" + info.Age);


                Console.WriteLine("Name before modify:" + info.Name);
                var update = Builders<UserInfo>.Update.Set(s => s.Name, info.Name + DateTime.Now.Second);
                dal.Update(info.Id, update);

                info = dal.FindByID(info.Id);
                Console.WriteLine("Name after modify:" + info.Name);
            }
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.25.
```

对于异步接口的调用代码，如下所示。

```
        /// <summary>
        /// 异步操作的调用
        /// </summary>
        private async void btnAsync_Click(object sender, EventArgs e)
        {
            UserInfo newInfo = new UserInfo();
            newInfo.Name = "Ping" + DateTime.Now.ToString();
            newInfo.Age = DateTime.Now.Minute;
            newInfo.Hobby = "乒乓球";
            await dal.InsertAsync(newInfo);

            var list = await dal.FindAsync(s => s.Age < 30);
            foreach (UserInfo info in list)
            {
                Console.WriteLine(info.Id + ", " + info.Name);
            }
            Console.WriteLine(newInfo.Id);
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.
```

 

![基于C#的MongoDB数据库开发应用（3）--MongoDB数据库的C#开发之异步接口_MongoDB数据库_03](http://www.cnblogs.com/Images/OutliningIndicators/None.gif)主要研究技术：代码生成工具、会员管理系统、客户关系管理软件、病人资料管理软件、Visio二次开发、酒店管理系统、仓库管理系统等共享软件开发