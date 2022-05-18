# 基于C#的MongoDB数据库开发应用（4）--Redis的安装及使用

 原创

[wuhuacong](https://blog.51cto.com/wuhuacong)2021-07-22 16:43:09©著作权

*文章标签*[MongoDB数据库](https://blog.51cto.com/topic/mongodbshujuku.html)*文章分类*[其他](https://blog.51cto.com/nav/program1)[编程语言](https://blog.51cto.com/nav/program)*阅读数*25

## 基于C#的MongoDB数据库开发应用（4）--Redis的安装及使用

在前面介绍了三篇关于MongoDB数据库的开发使用文章，严格来讲这个不能归类于MongoDB数据库开发，不过Redis又有着和MongoDB数据库非常密切的关系，它们两者很接近，Redis主要是内存中的NoSQL数据库，用来提高性能的；MongoDB数据库则是文件中的NoSQL数据库，做数据序列号存储使用的，它们两者关系密切又有所区别。本篇主要介绍Redis的安装及使用，为后面Redis和MongoDB数据库的联合使用先铺下基础。

在前面介绍了三篇关于MongoDB数据库的开发使用文章，严格来讲这个不能归类于MongoDB数据库开发，不过Redis又有着和MongoDB数据库非常密切的关系，它们两者很接近，Redis主要是内存中的NoSQL数据库，用来提高性能的；MongoDB数据库则是文件中的NoSQL数据库，做数据序列号存储使用的，它们两者关系密切又有所区别。本篇主要介绍Redis的安装及使用，为后面Redis和MongoDB数据库的联合使用先铺下基础。

### 1、Redis基础及安装

Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

Redis的代码遵循ANSI-C编写，可以在所有POSIX系统（如Linux, `*`BSD, Mac OS X, Solaris等）上安装运行。而且Redis并不依赖任何非标准库，也没有编译参数必需添加。

**1）Redis支持两种持久化方式：**

  (1):snapshotting(快照)也是默认方式.(把数据做一个备份，将数据存储到文件)

  (2)Append-only file(缩写aof)的方式 

  快照是默认的持久化方式，这种方式是将内存中数据以快照的方式写到二进制文件中，默认的文件名称为dump.rdb.可以通过配置设置自动做快照持久化的方式。我们可以配置redis在n秒内如果超过m个key键修改就自动做快照.

  aof方式:由于快照方式是在一定间隔时间做一次的，所以如果Redis意外down掉的话，就会丢失最后一次快照后的所有修改。aof比快照方式有更好的持久化性，是由于在使用aof时，redis会将每一个收到的写命令都通过write函数追加到文件中，当redis重启时会通过重新执行文件中保存的写命令来在内存中重建整个数据库的内容。 

**2）Redis数据结构**

Redis 的作者antirez曾称其为一个数据结构服务器（**data structures server**），这是一个非常准确的表述，Redis的所有功能就是将数据以其固有的几种结构保存，并提供给用户操作这几种结构的接口。我们可以想象我们在各种语言中的那些固有数据类型及其操作。

Redis目前提供四种数据类型：**string**,**list**,**set**及**zset**(sorted set)和**Hash**。

- **string**是最简单的类型，你可以理解成与Memcached一模一个的类型，一个key对应一个value，其上支持的操作与Memcached的操作类似。但它的功能更丰富。
- **list**是一个链表结构，主要功能是push、pop、获取一个范围的所有值等等。操作中key理解为链表的名字。
- **set**是集合，和我们数学中的集合概念相似，对集合的操作有添加删除元素，有对多个集合求交并差等操作。操作中key理解为集合的名字。
- **zset**是set的一个升级版本，他在set的基础上增加了一个顺序属性，这一属性在添加修改元素的时候可以指定，每次指定后，zset会自动重新按新的值调整顺序。可以理解了有两列的mysql表，一列存value，一列存顺序。操作中key理解为zset的名字。
- **Hash**数据类型允许用户用Redis存储对象类型,Hash数据类型的一个重要优点是,当你存储的数据对象只有很少几个key值时,数据存储的内存消耗会很小.更多关于Hash数据类型的说明请见: http://code.google.com/p/redis/wiki/Hashes

**3）Redis数据存储**

Redis的存储分为内存存储、磁盘存储和log文件三部分，配置文件中有三个参数对其进行配置。

**save seconds updates**，**save**配置，指出在多长时间内，有多少次更新操作，就将数据同步到数据文件。这个可以多个条件配合，比如默认配置文件中的设置，就设置了三个条件。

**appendonly yes**/**no** ，**appendonly**配置，指出是否在每次更新操作后进行日志记录，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为redis本身同步数据文件是按上面的save条件来同步的，所以有的数据会在一段时间内只存在于内存中。

**appendfsync no**/**always**/**everysec** ，**appendfsync**配置，**no**表示等操作系统进行数据缓存同步到磁盘，**always**表示每次更新操作后手动调用**fsync**()将数据写到磁盘，**everysec**表示每秒同步一次。

 

**4）Redis的安装**

Redis可以在不同的平台运行，不过我主要基于Windows进行开发工作，所以下面主要是基于Windows平台进行介绍。

Redis可以安装以DOS窗口启动的，也可以安装为Windows服务的，一般为了方便，我们更愿意把它安装为Windows服务，这样可以比较方便管理。下载地址：https://github.com/MSOpenTech/redis/releases下载，安装为Windows服务即可。

当前可以下载到最新的Windows安装版本为3.0，安装后作为Windows服务运行，安装后可以在系统的服务里面看到Redis的服务在运行了，如下图所示。

![基于C#的MongoDB数据库开发应用（4）--Redis的安装及使用_MongoDB数据库](https://images2015.cnblogs.com/blog/8867/201601/8867-20160112103903944-361706197.png)

安装好Redis后，还有一个Redis伴侣Redis Desktop Manager需要安装，这样可以实时查看Redis缓存里面有哪些数据，具体地址如下：http://redisdesktop.com/download

下载属于自己平台的版本即可

下载安装后，打开运行界面，如果我们往里面添加键值的数据，那么可以看到里面的数据了。

![基于C#的MongoDB数据库开发应用（4）--Redis的安装及使用_MongoDB数据库_02](https://images2015.cnblogs.com/blog/8867/201601/8867-20160112104254225-1096787368.png)

 

### 2、Redis的C#使用

Redis目前提供四种数据类型：**string**,**list**,**set**及**zset**(sorted set)和**Hash**。因此它在C#里面也有对应的封装处理，而且有很多人对他进行了封装，提供了很多的响应开发包，具体可以访问http://redis.io/clients#c 进行了解。一般建议用ServiceStack.Redis的封装驱动比较好，具体的使用可以参考https://github.com/ServiceStack/ServiceStack.Redis。

我们开发C#代码的时候，可以在NuGet程序包上面进行添加对应的ServiceStack.Redis引用，如下所示。

![基于C#的MongoDB数据库开发应用（4）--Redis的安装及使用_MongoDB数据库_03](https://images2015.cnblogs.com/blog/8867/201601/8867-20160112105929882-814202873.png)

 

在弹出的NuGet程序包里面，输入ServiceStack.Redis进行搜索，并添加下面的驱动引用即可。

![基于C#的MongoDB数据库开发应用（4）--Redis的安装及使用_MongoDB数据库_04](https://images2015.cnblogs.com/blog/8867/201601/8867-20160112105948632-2098636953.png)

这样会在项目引用里面添加了几个对应的程序集，如下所示。

![基于C#的MongoDB数据库开发应用（4）--Redis的安装及使用_MongoDB数据库_05](https://images2015.cnblogs.com/blog/8867/201601/8867-20160112110123116-791277534.png)

在C#里面使用Redis，首先需要实例化一个Redis的客户端类，如下所示。

```
        //创建一个Redis的客户端类
        RedisClient client = new RedisClient("127.0.0.1", 6379);1.2.
```

在使用前，我们需要清空所有的键值存储，使用FushAll方法即可，如下所示

```
            //Redis FlushAll 命令用于清空整个 Redis 服务器的数据(删除所有数据库的所有 key )。 
            client.FlushAll();1.2.
```

根据上面的驱动，可以为不同类型的处理编写一些演示代码，下面代码是摘录网上的案例进行介绍。

```
            #region string类型的测试代码

            client.Add<string>("StringValueTime", "带有有效期的字符串", DateTime.Now.AddMilliseconds(10000));

            while (true)
            {
                if (client.ContainsKey("StringValueTime"))
                {
                    Console.WriteLine("String.键:StringValue, 值:{0} {1}", client.Get<string>("StringValueTime"), DateTime.Now);
                    Thread.Sleep(10000);
                }
                else
                {
                    Console.WriteLine("键:StringValue, 值:已过期 {0}", DateTime.Now);
                    break;
                }
            }

            client.Add<string>("StringValue", " String和Memcached操作方法差不多");
            Console.WriteLine("数据类型为：String.键:StringValue, 值:{0}", client.Get<string>("StringValue"));

            Student stud = new Student() { id = "1001", name = "李四" };
            client.Add<Student>("StringEntity", stud);
            Student Get_stud = client.Get<Student>("StringEntity");
            Console.WriteLine("数据类型为：String.键:StringEntity, 值:{0} {1}", Get_stud.id, Get_stud.name);
            #endregion

            #region Hash类型的测试代码

            client.SetEntryInHash("HashID", "Name", "张三");
            client.SetEntryInHash("HashID", "Age", "24");
            client.SetEntryInHash("HashID", "Sex", "男");
            client.SetEntryInHash("HashID", "Address", "上海市XX号XX室");

            List<string> HaskKey = client.GetHashKeys("HashID");
            foreach (string key in HaskKey)
            {
                Console.WriteLine("HashID--Key:{0}", key);
            }

            List<string> HaskValue = client.GetHashValues("HashID");
            foreach (string value in HaskValue)
            {
                Console.WriteLine("HashID--Value:{0}", value);
            }

            List<string> AllKey = client.GetAllKeys(); //获取所有的key。
            foreach (string Key in AllKey)
            {
                Console.WriteLine("AllKey--Key:{0}", Key);
            }
            #endregion

            #region List类型的测试代码
            /*
             * list是一个链表结构，主要功能是push,pop,获取一个范围的所有的值等，操作中key理解为链表名字。 
             * Redis的list类型其实就是一个每个子元素都是string类型的双向链表。我们可以通过push,pop操作从链表的头部或者尾部添加删除元素，
             * 这样list既可以作为栈，又可以作为队列。Redis list的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销，
             * Redis内部的很多实现，包括发送缓冲队列等也都是用的这个数据结构 
             */
            client.EnqueueItemOnList("QueueListId", "1.张三");  //入队
            client.EnqueueItemOnList("QueueListId", "2.张四");
            client.EnqueueItemOnList("QueueListId", "3.王五");
            client.EnqueueItemOnList("QueueListId", "4.王麻子");
            long q = client.GetListCount("QueueListId");

            Console.WriteLine(client.GetItemFromList("QueueListId", 0));
            for (int i = 0; i < q; i++)
            {
                Console.WriteLine("QueueListId出队值：{0}", client.DequeueItemFromList("QueueListId"));   //出队(队列先进先出)
            }

            q = client.GetListCount("QueueListId");
            Console.WriteLine(q);

            client.PushItemToList("StackListId", "1.张三");  //入栈
            client.PushItemToList("StackListId", "2.张四");
            client.PushItemToList("StackListId", "3.王五");
            client.PushItemToList("StackListId", "4.王麻子");
            long p = client.GetListCount("StackListId");
            for (int i = 0; i < p; i++)
            {
                Console.WriteLine("StackListId出栈值：{0}", client.PopItemFromList("StackListId"));   //出栈(栈先进后出)
            }
            q = client.GetListCount("StackListId");
            Console.WriteLine(q);

            #endregion

            #region Set无序集合的测试代码
            /*
             它是string类型的无序集合。set是通过hash table实现的，添加，删除和查找,对集合我们可以取并集，交集，差集
             */
            client.AddItemToSet("Set1001", "小A");
            client.AddItemToSet("Set1001", "小B");
            client.AddItemToSet("Set1001", "小C");
            client.AddItemToSet("Set1001", "小D");
            HashSet<string> hastsetA = client.GetAllItemsFromSet("Set1001");
            foreach (string item in hastsetA)
            {
                Console.WriteLine("Set无序集合ValueA:{0}", item); //出来的结果是无须的
            }

            client.AddItemToSet("Set1002", "小K");
            client.AddItemToSet("Set1002", "小C");
            client.AddItemToSet("Set1002", "小A");
            client.AddItemToSet("Set1002", "小J");
            HashSet<string> hastsetB = client.GetAllItemsFromSet("Set1002");
            foreach (string item in hastsetB)
            {
                Console.WriteLine("Set无序集合ValueB:{0}", item); //出来的结果是无须的
            }

            HashSet<string> hashUnion = client.GetUnionFromSets(new string[] { "Set1001", "Set1002" });
            foreach (string item in hashUnion)
            {
                Console.WriteLine("求Set1001和Set1002的并集:{0}", item); //并集
            }

            HashSet<string> hashG = client.GetIntersectFromSets(new string[] { "Set1001", "Set1002" });
            foreach (string item in hashG)
            {
                Console.WriteLine("求Set1001和Set1002的交集:{0}", item);  //交集
            }

            HashSet<string> hashD = client.GetDifferencesFromSet("Set1001", new string[] { "Set1002" });  //[返回存在于第一个集合，但是不存在于其他集合的数据。差集]
            foreach (string item in hashD)
            {
                Console.WriteLine("求Set1001和Set1002的差集:{0}", item);  //差集
            }

            #endregion

            #region  SetSorted 有序集合的测试代码
            /*
             sorted set 是set的一个升级版本，它在set的基础上增加了一个顺序的属性，这一属性在添加修改.元素的时候可以指定，
             * 每次指定后，zset(表示有序集合)会自动重新按新的值调整顺序。可以理解为有列的表，一列存 value,一列存顺序。操作中key理解为zset的名字.
             */
            client.AddItemToSortedSet("SetSorted1001", "1.刘仔");
            client.AddItemToSortedSet("SetSorted1001", "2.星仔");
            client.AddItemToSortedSet("SetSorted1001", "3.猪仔");
            List<string> listSetSorted = client.GetAllItemsFromSortedSet("SetSorted1001");
            foreach (string item in listSetSorted)
            {
                Console.WriteLine("SetSorted有序集合{0}", item);
            }
            #endregion1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.25.26.27.28.29.30.31.32.33.34.35.36.37.38.39.40.41.42.43.44.45.46.47.48.49.50.51.52.53.54.55.56.57.58.59.60.61.62.63.64.65.66.67.68.69.70.71.72.73.74.75.76.77.78.79.80.81.82.83.84.85.86.87.88.89.90.91.92.93.94.95.96.97.98.99.100.101.102.103.104.105.106.107.108.109.110.111.112.113.114.115.116.117.118.119.120.121.122.123.124.125.126.127.128.129.130.131.132.133.134.135.136.137.138.139.140.141.142.143.144.145.146.147.
```

对于具体类型的类对象，也可以使用As方法进行转换为对应的处理对象进行处理，如下所示

```
IRedisTypedClient<Phone> phones = client.As<Phone>();1.
```

具体的测试代码如下所示。

```
        /// <summary>
        /// Redis对对象类的处理例子
        /// </summary>
        private void btnTypeValue_Click(object sender, EventArgs e)
        {
            IRedisTypedClient<Phone> phones = client.As<Phone>();
            Phone phoneFive = phones.GetValue("5");
            if (phoneFive == null)
            {
                Thread.Sleep(50);
                phoneFive = new Phone
                {
                    Id = 5,
                    Manufacturer = "Apple",
                    Model = "xxxxx",
                    Owner = new Person
                    {
                        Id = 1,
                        Age = 100,
                        Name = "伍华聪",
                        Profession = "计算机",
                        Surname = "wuhuacong"
                    }
                };

                phones.SetEntry(phoneFive.Id.ToString(), phoneFive);
            }

            client.Store<Phone>(
                new Phone
                {
                    Id = 2,
                    Manufacturer = "LG",
                    Model = "test-xxx",
                    Owner = new Person
                    {
                        Id = 2,
                        Age = 40,
                        Name = "test",
                        Profession = "teacher",
                        Surname = "wuhuacong"
                    }
                });

            var message = "Phone model is " + phoneFive.Manufacturer + ",";
            message += "Phone Owner Name is: " + phoneFive.Owner.Name;
            Console.WriteLine(message);
        }1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.18.19.20.21.22.23.24.25.26.27.28.29.30.31.32.33.34.35.36.37.38.39.40.41.42.43.44.45.46.47.48.
```

以上就是关于Redis的安装以及简单的例子使用说明，在具体中，我们可以利用Redis的高性能特性，来构建我们的缓存数据，并且可以利用Redis和MongoDB数据库的完美衔接，可以整合一起做的更好，为相关的后台提供更高效的数据处理操作，毕竟在互联网的大环境下，性能是非常重要的。