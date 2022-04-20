##  1 mongoDB副本集



### 1 副本集简介

　　前边我们介绍都是单机MongoDB的使用，在实际开发中很少会用单机MongoDB，因为使用单机会有数据丢失的风险，同时单台服务器无法做到高可用性(即当服务器宕机时，没有替代的服务器顶上来，我们的业务也就挂了)，MongoDB中的副本集可以完美地解决上边的两个问题。

　　MongoDB的副本集本质上就是一组mongod进程。复制集的成员有:
　　　　1.Primary:主节点，负责所有的写操作；
　　　　2.Secondaries:从节点，同步主节点的数据，保存数据副本；
　　　　3.Arbiter:仲裁节点，不保存数据，只有一个投票的作用；
　　副本集运行过程：主节点是集群中唯一一个负责写操作的节点，主节点的写操作都会记录在其操作日志(oplog，是一个 [capped collection](https://docs.mongodb.com/manual/core/capped-collections/))中，从节点复制主节点的oplog日志并执行日志中的命令，以此保持数据和主节点一致。副本集的所有节点都可以进行读操作，但是应用程序默认从主节点读取数据。当主节点一段时间(当前默认为10s)不和从节点通信,集群就会开始投票选取新的主节点。下图来自官网，描述了一个一主两从的副本集的结构，应用程序的读写操作默认都是通过主节点进行的。

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190708165842417-1506987260.png)

 



### 2 副本集搭建

　　MongoDB的副本集搭建并不复杂，这里简单演示一下搭建过程。搭建mongoDB副本集时，节点的个数最好是奇数，这主要是为了保证投票顺利进行。这里演示搭建一个一主两从的副本集，搭建副本集时，每个节点最好部署在不同的设备上，因为我没有那么多电脑，所以就采用三台centos7虚拟机来搭建。

#### 第一步 安装mongoDB　　

　　为了方便几台设备通信，我们在每台设备上使用 vim /etc/hosts 命令注册一下主机信息(注意要改成自己设备的ip)，配置如下：

```
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.70.129 mongo01
192.168.70.131 mongo02
192.168.70.133 mongo03
```

　　在三台centos虚拟机上都安装mongoDB，安装可以参考第一篇中的安装方法，注意副本集的配置相比单机多了一个replication节点，这里设置副本集的名字为MongoXset，使用命令 vim /usr/local/mongodb/bin/mongodb.conf 编辑配置文件如下：



```
systemLog:
  destination: file
  logAppend: true
  path: /usr/local/mongodb/logs/mongodb.log
storage:
  dbPath: /usr/local/mongodb/data
  journal:
    enabled: true
processManagement:
  fork: true
net:
  port: 27017
  bindIp: 0.0.0.0
replication:
  replSetName: MongoXset
```



#### 第二步 初始化副本集　

　　安装完成后，在Robomongo中连接任意一个节点，执行以下命令初始化副本集：



```
//配置
config = { _id:"MongoXset", members:[
  {_id:0,host:"192.168.70.129:27017"},
  {_id:1,host:"192.168.70.131:27017"},
  {_id:2,host:"192.168.70.133:27017"}]
}
use admin
//初始化
rs.initiate(config)
```



 　　执行上边的命令后，我们的副本集就搭建完成了，执行 rs.status() 查看副本集的状态，我们看到192.168.70.133:27017的mongodb是primary（主节点）,其他两个节点为secondary（从节点）:



```
{
    "set" : "MongoXset",
    "date" : ISODate("2019-06-30T08:13:34.677Z"),
    "myState" : 1,
    "term" : NumberLong(1),
    "syncingTo" : "",
    "syncSourceHost" : "",
    "syncSourceId" : -1,
    "heartbeatIntervalMillis" : NumberLong(2000),
    "optimes" : {
        "lastCommittedOpTime" : {
            "ts" : Timestamp(1561882407, 1),
            "t" : NumberLong(1)
        },
        "readConcernMajorityOpTime" : {
            "ts" : Timestamp(1561882407, 1),
            "t" : NumberLong(1)
        },
        "appliedOpTime" : {
            "ts" : Timestamp(1561882407, 1),
            "t" : NumberLong(1)
        },
        "durableOpTime" : {
            "ts" : Timestamp(1561882407, 1),
            "t" : NumberLong(1)
        }
    },
    "lastStableCheckpointTimestamp" : Timestamp(1561882387, 1),
    "members" : [
        {
            "_id" : 0,
            "name" : "192.168.70.129:27017",
            "health" : 1,
            "state" : 2,
            "stateStr" : "SECONDARY",
            "uptime" : 219,
            "optime" : {
                "ts" : Timestamp(1561882407, 1),
                "t" : NumberLong(1)
            },
            "optimeDurable" : {
                "ts" : Timestamp(1561882407, 1),
                "t" : NumberLong(1)
            },
            "optimeDate" : ISODate("2019-06-30T08:13:27Z"),
            "optimeDurableDate" : ISODate("2019-06-30T08:13:27Z"),
            "lastHeartbeat" : ISODate("2019-06-30T08:13:33.585Z"),
            "lastHeartbeatRecv" : ISODate("2019-06-30T08:13:34.465Z"),
            "pingMs" : NumberLong(0),
            "lastHeartbeatMessage" : "",
            "syncingTo" : "192.168.70.133:27017",
            "syncSourceHost" : "192.168.70.133:27017",
            "syncSourceId" : 2,
            "infoMessage" : "",
            "configVersion" : 1
        },
        {
            "_id" : 1,
            "name" : "192.168.70.131:27017",
            "health" : 1,
            "state" : 2,
            "stateStr" : "SECONDARY",
            "uptime" : 219,
            "optime" : {
                "ts" : Timestamp(1561882407, 1),
                "t" : NumberLong(1)
            },
            "optimeDurable" : {
                "ts" : Timestamp(1561882407, 1),
                "t" : NumberLong(1)
            },
            "optimeDate" : ISODate("2019-06-30T08:13:27Z"),
            "optimeDurableDate" : ISODate("2019-06-30T08:13:27Z"),
            "lastHeartbeat" : ISODate("2019-06-30T08:13:33.604Z"),
            "lastHeartbeatRecv" : ISODate("2019-06-30T08:13:34.458Z"),
            "pingMs" : NumberLong(0),
            "lastHeartbeatMessage" : "",
            "syncingTo" : "192.168.70.133:27017",
            "syncSourceHost" : "192.168.70.133:27017",
            "syncSourceId" : 2,
            "infoMessage" : "",
            "configVersion" : 1
        },
        {
            "_id" : 2,
            "name" : "192.168.70.133:27017",
            "health" : 1,
            "state" : 1,
            "stateStr" : "PRIMARY",
            "uptime" : 1281,
            "optime" : {
                "ts" : Timestamp(1561882407, 1),
                "t" : NumberLong(1)
            },
            "optimeDate" : ISODate("2019-06-30T08:13:27Z"),
            "syncingTo" : "",
            "syncSourceHost" : "",
            "syncSourceId" : -1,
            "infoMessage" : "",
            "electionTime" : Timestamp(1561882205, 1),
            "electionDate" : ISODate("2019-06-30T08:10:05Z"),
            "configVersion" : 1,
            "self" : true,
            "lastHeartbeatMessage" : ""
        }
    ],
    "ok" : 1,
    "operationTime" : Timestamp(1561882407, 1),
    "$clusterTime" : {
        "clusterTime" : Timestamp(1561882407, 1),
        "signature" : {
            "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
            "keyId" : NumberLong(0)
        }
    }
}
```



####  3 测试副本集

　　我们知道副本集的主节点负责所有写操作，从节点不能执行写操作，只会同步主节点的数据。这里简单测试一下：连接主节点192.168.70.133:27017，执行以下命令插入一条命令：

![img](https://img2018.cnblogs.com/blog/1007918/201906/1007918-20190630163404411-1778200195.png)

　　连接从节点192.168.70.129:27017，上执行下边的命令，我们看到从节点是不能插入数据的，但是我们可以从从节点查到主节点插入的数据（注意：必须先执行 rs.slaveOk() 后才能进行read操作)：

![img](https://img2018.cnblogs.com/blog/1007918/201906/1007918-20190630162725196-346129090.png)

　　**测试高可用性**：连接主节点192.168.70.133:27017，执行命令 use admin db.shutdownServer() 关闭主节点，然后连接一个其他节点执行 rs.status() 查看副本集状态如下，我们看到192.168.70.133:27017节点显示不可用，而192.168.70.129:27017被选举为新的主节点：



```
"members" : [
        {
            "_id" : 0,
            "name" : "192.168.70.129:27017",
            "health" : 1,
            "state" : 1,
            "stateStr" : "PRIMARY",
            "uptime" : 2919,
            "optime" : {
                "ts" : Timestamp(1561885110, 1),
                "t" : NumberLong(2)
            },
            "optimeDurable" : {
                "ts" : Timestamp(1561885110, 1),
                "t" : NumberLong(2)
            },
            "optimeDate" : ISODate("2019-06-30T08:58:30Z"),
            "optimeDurableDate" : ISODate("2019-06-30T08:58:30Z"),
            "lastHeartbeat" : ISODate("2019-06-30T08:58:35.900Z"),
            "lastHeartbeatRecv" : ISODate("2019-06-30T08:58:34.979Z"),
            "pingMs" : NumberLong(0),
            "lastHeartbeatMessage" : "",
            "syncingTo" : "",
            "syncSourceHost" : "",
            "syncSourceId" : -1,
            "infoMessage" : "",
            "electionTime" : Timestamp(1561884658, 1),
            "electionDate" : ISODate("2019-06-30T08:50:58Z"),
            "configVersion" : 1
        },
        {
            "_id" : 1,
            "name" : "192.168.70.131:27017",
            "health" : 1,
            "state" : 2,
            "stateStr" : "SECONDARY",
            "uptime" : 3892,
            "optime" : {
                "ts" : Timestamp(1561885110, 1),
                "t" : NumberLong(2)
            },
            "optimeDate" : ISODate("2019-06-30T08:58:30Z"),
            "syncingTo" : "192.168.70.129:27017",
            "syncSourceHost" : "192.168.70.129:27017",
            "syncSourceId" : 0,
            "infoMessage" : "",
            "configVersion" : 1,
            "self" : true,
            "lastHeartbeatMessage" : ""
        },
        {
            "_id" : 2,
            "name" : "192.168.70.133:27017",
            "health" : 0,
            "state" : 8,
            "stateStr" : "(not reachable/healthy)",
            "uptime" : 0,
            "optime" : {
                "ts" : Timestamp(0, 0),
                "t" : NumberLong(-1)
            },
            "optimeDurable" : {
                "ts" : Timestamp(0, 0),
                "t" : NumberLong(-1)
            },
            "optimeDate" : ISODate("1970-01-01T00:00:00Z"),
            "optimeDurableDate" : ISODate("1970-01-01T00:00:00Z"),
            "lastHeartbeat" : ISODate("2019-06-30T08:58:36.291Z"),
            "lastHeartbeatRecv" : ISODate("2019-06-30T08:50:59.539Z"),
            "pingMs" : NumberLong(0),
            "lastHeartbeatMessage" : "Error connecting to 192.168.70.133:27017 :: caused by :: Connection refused",
            "syncingTo" : "",
            "syncSourceHost" : "",
            "syncSourceId" : -1,
            "infoMessage" : "",
            "configVersion" : -1
        }
    ]
```



####  4 设置节点的优先级

　　在部署的时候，我们一般更愿意让稳定且性能好的设备在选举时优先作为主节点，让性能差的服务器不能被选举为主节点。这就要用到优先级了，各个节点的默认优先级都是1，我们可以更改各个节点的优先级，优先级越高，被选举为主节点的几率就越大，优先级为0的节点不能被选举为主节点。使用mongo shell执行下边的命令就可以更改各个节点的优先级，这里就不再具体演示了。



```
//获取集群配置
　　cfg=rs.config()
//设置优先级
　　cfg.members[0].priority=1
　　cfg.members[1].priority=100
　　cfg.members[1].priority=0
//重新加载配置
　　rs.reconfig(cfg)
```



### 3 副本集管理的常用函数

　　这里汇总了一些管理副本集的相关命令，有兴趣的小伙伴可以自己测试一下：

| 方法                           | 描述                     |
| ------------------------------ | ------------------------ |
| rs.status()                    | 查看副本集状态           |
| rs.initiate(cfg)               | 初始化副本集             |
| rs.conf()                      | 获取副本集的配置         |
| rs.reconfig(cfg)               | 重新加载配置             |
| rs.add(ip:port)                | 添加一个节点             |
| rs.addArb(ip:port)             | 添加一个仲裁节点         |
| rs.remove(ip:port)             | 删除一个节点             |
| rs.isMaster()                  | 查看是否是主节点         |
| rs.slaveOk()                   | 让从节点可以执行read操作 |
| rs.printReplicationInfo()      | 查看oplog的大小和时间    |
| rs.printSlaveReplicationInfo() | 查看从节点的数据同步情况 |



### 4 C#驱动之读写分离实现

　　前边我们已经搭建了一个一主两从的副本集，状态为：192.168.70.129:27017(主节点)，192.168.70.131:27017(从节点)，192.168.70.133:27017(从节点)，现在我们简单演示一下怎么使用C#操作副本集，并实现读写分离。

　　首先添加一些测试数据：连接主节点192.168.70.129:27017，执行以下命令插入一些测试数据，接着到两个从节点分别执行命令 rs.slaveOk() 让节点可以进行read操作：



```
use myDb
//清空students中的记录
db.students.drop()
//在students集合中添加测试数据
db.students.insertMany([
    {"no":1, "stuName":"jack", "age":23, "classNo":1},
    {"no":2, "stuName":"tom", "age":20, "classNo":2},
    {"no":3, "stuName":"hanmeimei", "age":22, "classNo":1},
    {"no":4, "stuName":"lilei", "age":24, "classNo":2}
])
```



　　然后写一个控制台程序，使用 Install-Package MongoDB.Driver 添加驱动包，具体代码如下：



```
    class Program
    {
        static void Main(string[] args)
        {
            //连接数据库
            var client = new MongoClient("mongodb://192.168.70.133:27017, 192.168.70.131:27017, 192.168.70.129:27017");
            //获取database
            var mydb = client.GetDatabase("myDb");
            //设置优先从从节点读取数据
            mydb.WithReadPreference(ReadPreference.Secondary);
            //mydb.WithReadConcern(ReadConcern.Majority);
            //mydb.WithWriteConcern(WriteConcern.WMajority);//这里可以设置写入确认级别
            //获取collection
            var stuCollection = mydb.GetCollection<Student>("students");
            //插入一条数据
            stuCollection.InsertOne(new Student()
            {
                no = 5,
                stuName = "jim",
                age = 25
            });

            //读取学生列表
            List<Student> stuList = stuCollection.AsQueryable().ToList();
            stuList.ForEach(s => Console.WriteLine($"学号:{s.no}  ,姓名：{s.stuName}  ,年龄：{s.age}"));
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
        [BsonExtraElements]
        public BsonDocument others { get; set; }
    }
```



　　注意一点：使用  var client = new MongoClient("mongodb://192.168.70.133:27017, 192.168.70.131:27017, 192.168.70.129:27017"); 获取client时，**驱动程序能够自动判断哪个节点是主节点**。执行结果如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190708164941356-541271794.png)



##  2 Sharing分片简介

　　除了副本集，在Mongodb里面存在另一种集群：分片集群。所谓分片简单来说就是将一个完整的数据分割后存储在不同的节点上。当MongoDB存储海量的数据时，一台机器不足以存储数据，或者数据过多造成读写吞吐量不能满足我们的需求时，可以考虑使用分片集群。

　　举一个栗子：例如我们有1个亿的用户信息，选择用户的name列作为**分片键(shard key),**将用户信息存储到两个Shard Server中，mongoDB会**自动根据分片键**将用户数据进行分片，假如分片后第一个片(shard1)存储了名字首字母为a~m的用户信息，第二个片(shard2)存储了名字首字母为n~z的用户信息。当我们要查询name=jack的用户时，因为jack的首字母**j**在a和m之间，所以分片集群会直接到shard1中查找，这样查询效率就会提高很多；如果我们要插入一个name=tom的用户，因为t在n~z之间，所以tom的信息会插入shard2中。这个栗子的分片键是name，当我们要查询生日为某一天的用户时(出生日期不是分片键)，mongoDB还是会去查询所有分片服务器的数据。

　　分片集群的基本结构如下：

![img](https://img2018.cnblogs.com/blog/1007918/201907/1007918-20190713171437656-1079860844.png)

　　分片集群主要包含三个组件(都是mongod进程)：

**1 Shard Server**

　   存储角色，真实的业务数据都保存在该角色中。在生产环境中每一个shard server都应该使用副本集，用于防止数据丢失，实现高可用。

**2 Config Server**

　　配置角色，存储sharing集群的元数据和配置信息。分片集群判断我们查询的数据在哪个shard中，或者要将数据插入到哪一个shard中就是由Config Server中的配置决定的。Config Server也要使用副本集充当，不然Config Server宕机，配置信息就无从获取了。

**3 mongos**

　　路由角色，这是应用程序访问分片集群的入口，我们通过连接mongos来访问分片集群，mongos让整个集群看起来就像一个单独的数据库。mongos同样推荐配置成副本集，不然路由角色宕机，应用程序就无法访问集群。

　　分片集群各个角色一般都要配置为副本集，所以需要较多的mongod进程，如sharing 集群中的三个角色都使用一主两从的副本集就需要9个mongod进程，这里就不再具体演示怎么去搭建分片集群，有兴趣的小伙伴可以按照[官网文档](https://docs.mongodb.com/manual/tutorial/deploy-sharded-cluster-ranged-sharding/)搭建，

　　对于中小型应用，使用副本集就可以满足业务需求，没必要使用分片集群。当数据量非常大时我们可以考虑使用分片技术。在开发中使用分片集群时，只需要把mongos作为一个简单的mongoDB实例连接即可，至于怎么去分片存储和分片查询会由集群自动完成。