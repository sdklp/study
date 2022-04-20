## 1.mongoDB简介

　　mongoDB 是由C++语言编写的，是一种分布式的面向文档存储的开源nosql数据库。nosql是Not Only SQL的缩写，是对不同于传统的关系型数据库的数据库管理系统的统称。

　　mongoDB是无模式的文档数据库,在关系型数据库中，数据表的每一行都拥有一样的字段，字段的名字和数据类型在创建table的时候就基本确定了，如student表的每一行都有学生编号、学生姓名、年龄等字段；而在mongoDB中，存储数据的格式类似于Json(格式为[Bson](https://docs.mongodb.com/manual/reference/bson-types/))，每一个document的字段的名字和数据类型可以完全不同，如在一个collection下，第一个document可以存储学生信息(学生编号、姓名、年龄、性别等)，第二个document可以存储班级信息(班级编号，班级名等)。正是因为无模式的特点，让我们可以无需多余操作就能完成数据的横向扩展。下表是mongoDB和传统数据库术语的对应关系。

| SQL术语     | MongoDB     | 解释/说明                           |
| ----------- | ----------- | ----------------------------------- |
| database    | database    | 数据库                              |
| table       | collection  | 数据库表/集合                       |
| row         | document    | 数据记录行/文档                     |
| column      | field       | 数据字段/域                         |
| index       | index       | 索引                                |
| join        | $lookup     | 表连接                              |
| primary key | primary key | 主键,MongoDB自动将_id字段设置为主键 |

 

## 2. mongoDB安装 



### 1.安装mongoDB

　　mongoDB的安装步骤十分简单，下载地址：<https://www.mongodb.com/download-center#community>。如果我们想在Windows上安装mongoDB直接下载msi文件，双击安装即可。如果要将mongoDB安装在Linux系统上，步骤如下：



```
####第1步 下载解压mongdb
#下载解压二进制包，解压即安装
　　cd /usr/local/src/
　　curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-4.0.9.tgz
　　tar -zxvf mongodb-linux-x86_64-4.0.9.tgz
　　mv mongodb-linux-x86_64-4.0.9 /usr/local/mongodb

####第2步 添加配置文件
　　vim /usr/local/mongodb/bin/mongodb.conf
配置文件内容如下：
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

 #配置文件中指定的dbpath和log要自己添加，不然会报错，执行命令 
　　mkdir -p /usr/local/mongodb/data; mkdir -p /usr/local/mongodb/logs/;cd /usr/local/mongodb/logs/; touch mongodb.log

 ####第3步 加载配置文件运行
　　/usr/local/mongodb/bin/mongod -f /usr/local/mongodb/bin/mongodb.conf 

####第4步 添加环境变量，用于可以在任意目录下执行mongo命令 
　　vim ~/.bash_profile #修改当前用户下的环境变量 PATH=$PATH:$HOME/bin:/usr/local/mongodb/bin  
　　source ~/.bash_profile
```



 　安装完成后，在任意目录下使用命令 mongo 192.168.70.133:27017 (mongo命令会默认启动127.0.0.1:27017)启动mongodb，mongoDB使用的是javascript shell，我们简单测试一下，如果出现下边的界面表示安装成功了

![img](https://img2018.cnblogs.com/blog/1007918/201905/1007918-20190525151151628-164071995.png)　

 　　这里简单使用一下mongoDB的shell来添加、删除数据库和collection:

![img](https://img2018.cnblogs.com/blog/1007918/201906/1007918-20190616150530057-737696353.png)



### 2.安装Robomongo并连接数据库

　　mongoDB的GUI有MongoDB Compass、studio 3T等，这里使用的是Robomongo，下载地址：<https://robomongo.org/>，下载完成后一直Next安装即可，连接mongoDB效果如下，Robomongo是傻瓜式的用法，可以通过客户端添加collection,document，执行shell等，具体使用方法就不再详细介绍了。

·　　![img](https://img2018.cnblogs.com/blog/1007918/201905/1007918-20190525153533351-367984082.png)

　　我们也可以通过shell脚本连接数据库，在Robomongo执行shell命令如下：



```
//连接远程数据库
　　var mongo=new Mongo("192.168.70.133:27017")
//找到数据库
　　var db=mongo.getDB("myDB");
//找到collecttion
　　var collection=db.getCollection("userinfo");
//查询colletion中所有数据
　　var list=collection.find().toArray();
//json形式打印结果
　　printjson(list);
```



## 3 mongoDB的shell

　　我们已经知道mongoDB是面向文档的nosql数据库，因为其无模式的特点，造成它的操作要比关系型数据库复杂一些，这里简单介绍一下mongoDB的CRUD操作。注意：从3.2版本开始，mongoDB添加了一些xxxOne()和xxxMany()方法，我们尽量使用这些新的方法。



### 1 添加(insert)

　　　添加数据的指令是insert，使用方法如下：

　

 　　![img](https://img2018.cnblogs.com/blog/1007918/201906/1007918-20190613211708734-1714194013.png)

　　insert方法的参数也可以是数组，用于批量添加数据，如下：

```
db.userinfos.insert([
   {_id:1, name: "张三", age: 23},
   {_id:2, name: "李四", age: 24}
]);
```

　　从3.2版本，mongoDB添加了insertOne和insertMany方法，分别用于单条插入和批量插入，用法很简单，如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//insertOne用于单条添加
　　db.userinfos.insertOne(
   　　{_id:1, name: "张三", age: 23}
  　 );

//insertMany用于批量添加
　　 db.userinfos.insertMany([
  　　 {_id:1, name: "张三", age: 23},
  　　 {_id:2, name: "李四", age: 24}
　　]);
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)



### 2 查询(find)

　　mongoDB查询使用find函数，语法如下：

 　![img](https://img2018.cnblogs.com/blog/1007918/201906/1007918-20190613220902900-1323060044.png)

　　mongoDB使用find查询时，默认会返回主键_id，如果不想返回主键的话设置_id=0即可。mongoDB的查询语法是比较简单的，但是因为其无模式的特点，且field的值可以是对象和数组，造成mongoDB的运算符要比传统的关系型数据库多很多，如运算符$exists可用于查询field是否存在、$type用于判断filed的类型等等，这里汇总了一些常用的查询相关的运算符，有兴趣的小伙伴可以测试一下：

```
db.userinfos.insertMany([
   {_id:1, name: "张三", age: 23,level:10, ename: { firstname: "san", lastname: "zhang"}, roles: ["vip","gen" ]},
   {_id:2, name: "李四", age: 24,level:20, ename: { firstname: "si", lastname: "li"}, roles:[ "vip" ]},
   {_id:3, name: "王五", age: 25,level:30, ename: { firstname: "wu", lastname: "wang"}, roles: ["gen","vip" ]},
   {_id:4, name: "赵六", age: 26,level:40, ename: { firstname: "liu", lastname: "zhao"}, roles: ["gen"] },
   {_id:5, name: "田七", age: 27, ename: { firstname: "qi", lastname: "tian"}, address:'北京' },
   {_id:6, name: "周八", age: 28,roles:["gen"], address:'上海' }
]);
```



| **类别**                                                     | **运算符**                                                   | **说明**                                                     | **实例**                                                     | **执行结果**                                                 |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **比较运算符**                                               | $gt($gte)                                                    | 大于(大于等于)                                               | db.userinfos.find( 　　{ age:{$gt:25}}, 　　{ name:1 })      | 查找age>25的文档的name结果:　　[{ "_id" : 4, "name" : "赵六" },　　 { "_id" : 5, "name" : "田七" }] |
| $lt($lte)                                                    | 小于(小于等于)                                               | db.userinfos.find(　　{ age:{$lt:25}}, 　　{ name:1 })       | 查找age<25的文档的name结果： 　　[ { "_id" : 1, "name" : "张三" },　　{ "_id" : 2, "name" : "李四" } ] |                                                              |
| $eq                                                          | 等于                                                         | db.userinfos.find(　　{ age:{$eq:25}}, 　　{ name:1 })       | 查询age=25的文档的name 结果：　　[ { "_id" : 3, "name" : "王五" } ] |                                                              |
| $ne                                                          | 不等于                                                       | db.userinfos.find( 　　{ age:{$ne:25}}, 　　{ name:1 } )     | 查询age!=25的文档的name结果：　　[{"_id" : 1,"name" : "张三"}, 　　{"_id" : 2,"name" : "李四"}, 　　{"_id" : 4,"name" : "赵六"}, 　　{"_id" : 5,"name" : "田七"}] |                                                              |
| $in                                                          | 包含                                                         | db.userinfos.find(　　{ age:{$in:[24,25]}}, 　　{ name:1 } ) | 查询age在[24,25]中的文档的name结果：　　 [ { "_id" : 2, "name" : "李四" },　　{ "_id" : 3, "name" : "王五" } ] |                                                              |
| $nin                                                         | 不包含                                                       | db.userinfos.find(　　{ age:{$nin:[24,25]}}, 　　{ name:1 } ) | 查询age不在[24,25]中的文档的name结果：　　[{"_id" : 1,"name" : "张三"}, 　　{"_id" : 4,"name" : "赵六"}, 　　{"_id" : 5,"name" : "田七"}] |                                                              |
| **逻辑运算符**                                               | $and                                                         | 与                                                           | db.userinfos.find(　　{$and: [　　　　{name:{$eq:'张三'}},　　　　{age:{$eq:23}}　　]},　　{name:1}) | 查询name='张三'且age=23的文档 结果：　　[ { "_id" : 1, "name" : "张三" } ] |
| $not                                                         | 非                                                           | db.userinfos.find(　　{age:{$not:{$in:[23,24,25]}}}, 　　{name:1} ) | 查询age不在[23,24,24]中的文档结果：　　[ { "_id" : 4, "name" : "赵六" },　　{ "_id" : 5, "name" : "田七" } ] |                                                              |
| $or                                                          | 或                                                           | db.userinfos.find(　　{$or: [　　　　{name:{$eq:'张三'}},　　　　{age:{$eq:24}}　　]},　　{name:1}) | 查询name='张三'或者age=24的文档 结果：　　[ { "_id" : 1, "name" : "张三" },　　{ "_id" : 2, "name" : "李四" } ] |                                                              |
| $nor                                                         | 或的取反                                                     | db.userinfos.find(　　{$nor: [　　　　{name:{$eq:'张三'}},　　　　{age:{$eq:24}}　　]},　　{name:1}) | 上边栗子的取反操作结果：　　[ {"_id" : 3,"name" : "王五"}, 　　 {"_id" : 4,"name" : "赵六"}, 　　 {"_id" : 5,"name" : "田七"} ] |                                                              |
| **评估运算符**                                               | $mod                                                         | 取余                                                         | db.userinfos.find(　　{age:{$mod:[10,3]}}, 　　{name:1} )    | 查询name%10=3的文档结果：[ { "_id" : 1, "name" : "张三" } ]  |
| $regex                                                       | 正则                                                         | db.userinfos.find(　　{name:{$regex:/^张/i}}, 　　{name:1} ) | 查询name以张开头的文档结果：[ { "_id" : 1, "name" : "张三" } ] |                                                              |
| db.userinfos.find(　　{name:{$in:[/^张/,/四$/]}}, 　　{name:1} ) | 查询name以张开头或者以四结尾的文档结果：[ { "_id" : 1, "name" : "张三" },{ "_id" : 2, "name" : "李四" } ] |                                                              |                                                              |                                                              |
| $where                                                       | where过滤                                                    | db.userinfos.find( 　　{$where :function(){return this.name=='张三';}}, 　　{name:1}) | 查询名字为张三的记录结果：　　[ { "_id" : 1, "name" : "张三" } ]注意：where可以实现所有的过滤，但是效率不高。这是因为where采用逐行判断而不使用索引 |                                                              |
| $expr                                                        | 表达式过滤                                                   | db.userinfos.find( 　　{ $expr:{$lt:["$age","$level"]}}, 　　{ name:1 }) | 查询age<level的记录结果：[ { "_id" : 3, "name" : "王五" },{ "_id" : 4, "name" : "赵六" } ] |                                                              |
| **元素运算符**                                               | $exists                                                      | field是否存在                                                | db.userinfos.find( 　　{ address:{$exists:true}}, 　　{ name:1 }) | 查询存在address字段的文档结果：[ { "_id" : 5, "name" : "田七" },{ "_id" : 6, "name" : "周八" } ] |
| $type                                                        | field类型                                                    | db.userinfos.find(　　{name:{$type:'string'}},　　{name:1})  | 查询name为string的文档结果：　　所有文档都匹配               |                                                              |
| **数组运算符**                                               | $all                                                         | 包含所有元素才匹配成功                                       | db.userinfos.find(　　{roles:{$all:['vip','gen']}},　　{name:1}) | 查询roles中包含vip和gen的文档结果： [ { "_id" : 1, "name" : "张三" },{ "_id" : 3, "name" : "王五" } ] |
| $eleMatch                                                    | 只要有一个元素符合就匹配成功                                 | db.userinfos.find(　　{roles:{$elemMatch:{ $eq: 'vip', $ne: 'gen' }}},　　{name:1}) | 查询roles中有元素等于vip，或有元素不等于gen的文档结果：　　[{"_id" : 1,"name" : "张三"}, 　　{"_id" : 2,"name" : "李四"}, 　　{"_id" : 3,"name" : "王五"}] |                                                              |
| $size                                                        | 元素个数相同的匹配成功                                       | db.userinfos.find(　　{roles:{$size:2}},　　{name:1})        | 查询roles中有两个元素的文档结果：[ { "_id" : 1, "name" : "张三" },{ "_id" : 3, "name" : "王五" } ] |                                                              |

### 3 修改(update)

　　mongoDB修改documen使用的命令是update，语法如下：

![img](https://img2018.cnblogs.com/blog/1007918/201906/1007918-20190616114751086-971432921.png)

　　mongoDB的update默认只修改一条document，如果想修改所有符合条件的documet的话，可以设置multi:true。upsert表示当没有符合过滤条件的文档时，就添加一条文档，并将修改的内容作为新增document的值。mongoDB的update功能比较丰富，如可以修改field的名字，删除field，以及对数组进行增删改。从3.2版本开始，mongoDB添加了updateOne()和updateMany()方法，用于修改单条或者多条数据，推荐使用新的方法，语法如下：



```
 //将age<25的记录的level修改为50，只修改一条。updateOne相当于update设置multi:false
db.userinfos.updateOne(
   {age:{$lt:25}},
   {$set:{level:50}}
   )
 //将age<25的记录的level修改为50，所有符合条件的记录都修改。updateMany相当于update设置multi:true
db.userinfos.updateMany(
   {age:{$lt:25}},
   {$set:{level:50}}
   )
```



　　这里汇总了mongoDB中关于update的相关运算符，有兴趣的小伙伴可以测试一下：

```
db.userinfos.insertMany([
   {_id:1, name: "张三", age: 23,level:10, ename: { firstname: "san", lastname: "zhang"}, roles: ["vip","gen" ]},
   {_id:2, name: "李四", age: 24,level:20, ename: { firstname: "si", lastname: "li"}, roles:[ "vip" ]},
   {_id:3, name: "王五", age: 25,level:30, ename: { firstname: "wu", lastname: "wang"}, roles: ["gen","vip" ]},
   {_id:4, name: "赵六", age: 26,level:40, ename: { firstname: "liu", lastname: "zhao"}, roles: ["gen"] },
   {_id:5, name: "田七", age: 27, ename: { firstname: "qi", lastname: "tian"}, address:'北京' },
   {_id:6, name: "周八", age: 28,roles:["gen"], address:'上海' }
]);
```

| **运算符**                                                   | $currentDate                                        | 修改field值为当前时间,如果field不存在则添加field             | db.userinfos.update( 　　{name:'张三'}, 　　{$currentDate:{　　　　createtime:{$type:'timestamp'}}　　} ) | 将张三的createtime字段值修改为当前时间　　格式为时间戳("createtime" : Timestamp(1560663270, 1))补充:如果不设置$type，默认的格式为date　　格式为date("createtime" : ISODate("2019-06-16T05:38:21.119Z")) |
| ------------------------------------------------------------ | --------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| $set                                                         | 修改值                                              | db.userinfos.update( 　　{name:'张三'}, 　　{$set:　　　　{level:20}　　} ) | 将张三的level修改为20。如果要修改的field不存在，不会添加新的field。 |                                                              |
| $setOnInsert                                                 | 只有在新增document时进行赋值，一定要设置upsert:true | db.userinfos.update( 　　{name:'张三'}, 　　{$setOnInsert:　　　　{level:30}　　}, 　　{upsert:true} ) | 因为已经有name=张三的document，所以不做任何操作              |                                                              |
| db.userinfos.update( 　　{name:'吴九'}, 　　{$setOnInsert:{level:30}}, 　　{upsert:true} ) | 添加一个name=吴九的document，并设置level为30        |                                                              |                                                              |                                                              |
| $inc                                                         | 自增                                                | db.userinfos.update( 　　{name:'张三'}, 　　{$inc:　　　　{age:10}　　} ) | 张三的age自增10，age修改为23+10=33                           |                                                              |
| $mul                                                         | 自乘                                                | db.userinfos.update( 　　{name:'张三'}, 　　{$mul:　　　　{age:2}　　} ) | 张三的age自乘2，age修改为23*2=46                             |                                                              |
| $min                                                         | 取小                                                | db.userinfos.update( 　　{name:'张三'}, 　　{$min:　　　　{age:13}　　} ) | 张三的age取小值，因为23>13,所以修改age为13。如果修改的值比23大，那么不做操作。 |                                                              |
| $max                                                         | 取大                                                | db.userinfos.update( 　　{name:'张三'}, 　　{$max:　　　　{age:33}　　} ) | 张三的age取大值，因为23<33,所以修改age为33。如果修改的值比23小，那么不做操作。 |                                                              |
| **字段操作运算符**                                           | $rename                                             | 修改filed的名字                                              | db.userinfos.update(　　{name:'张三'}, 　　{$rename:　　　　{age:'年龄'}　　} ) | 将张三的age字段名改成年龄，值不变，年龄=23                   |
| $unset                                                       | 删除field                                           | db.userinfos.update(　　{name:'张三'}, 　　{$unset:　　　　{level:''}　　} ) | 将张三的level字段删除                                        |                                                              |

 

####  　　数组相关的update运算符：

```
 db.students.insertMany(
        [{ "_id" : 1, "grades" : [ 85, 80, 80 ] },
　　      { "_id" : 2, "grades" : [ 88, 90, 92 ] }]
   )
```



| **数组运算符**                 | $                                           | 单个占位符                                                   | db.students.updateOne( 　　{ _id: 1, grades: 80 }, 　　{ $set: { "grades.$" : 82 } } ) | 修改_id=1的文档graders中第一个值为80的元素，值改成82结果：　　[{ "_id" : 1, "grades" : [ 85, 82, 80 ] }, 　　{ "_id" : 2, "grades" : [ 88, 90, 92 ] }] |
| ------------------------------ | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| $[]                            | 所有元素占位符                              | db.students.updateOne( 　　{ _id: 1}, 　　{ $set: { "grades.$[]" : 100 } } ) | 修改_id=1的文档graders中所有元素，值改成100结果：　　[{ "_id" : 1, "grades" : [ 100, 100, 100 ] }, 　　{ "_id" : 2, "grades" : [ 88, 90, 92 ] }] |                                                              |
| $[<identifier>]                | 符合arrayFilter过滤条件的元素占位符         | db.students.updateOne( 　　{ _id: 2}, 　　{ $set: { "grades.$[element]" : 100 } }, 　　{ arrayFilters: [ { "element": { $gte: 90 } } ]} ) | 修改_id=2的文档graders中大于等于90的元素，值改成100结果：　　[{ "_id" : 1, "grades" : [  85, 82, 80  ] }, 　　{ "_id" : 2, "grades" : [ 88, 100, 100 ] }] |                                                              |
| $push                          | 添加元素                                    | db.students.updateOne( 　　{ _id: 1 }, 　　{ $push: { "grades" : 98 } } ) | 在_id=1的文档graders中添加元素结果：　　[{ "_id" : 1, "grades" : [  85, 80, 80 ,98 ] }, 　　{ "_id" : 2, "grades" : [ 88, 90, 92 ] }] |                                                              |
| $addToSet                      | 添加不存在的元素，如果元素已经存在则无操作  | db.students.updateOne( 　　{ _id: 1 }, 　　{ $addToSet: { "grades" : 100 } } ) | 在_id=1的文档graders中添加元素结果：　　[{ "_id" : 1, "grades" : [  85, 80, 80 ,98,100 ] }, 　　{ "_id" : 2, "grades" : [ 88, 90, 92 ] }]补充：如果添加的元素是85，因为85已经存在，所以不执行操作 |                                                              |
| $pop                           | 弹出(移除)元素                              | db.students.updateOne( 　　{ _id: 1 }, 　　{ $pop: { "grades" : 1 } } ) | 移除最后一个元素结果：　　[{ "_id" : 1, "grades" : [  85, 80 ] }, 　　{ "_id" : 2, "grades" : [ 88, 90, 92 ] }]补充：如果{ $pop: { "grades" : -1 } }表示从前边弹出，移除第一个元素 |                                                              |
| $pullAll                       | 根据值移除数组中的元素                      | db.students.update( 　　{_id:2}, 　　{$pullAll:{"grades":[88,90]}} ) | 移除_id=2的文档的graders中值为88,90的所有元素结果：　　[{ "_id" : 1, "grades" : [  85, 80, 80  ] }, 　　{ "_id" : 2, "grades" : [ 92 ] }] |                                                              |
| $pull                          | 移除符合条件的元素                          | db.students.update( 　　{_id:2}, 　　{$pull:{"grades":{$gt:90}}} ) | 移除_id=2的文档的graders中大于90的所有元素结果：　　[{ "_id" : 1, "grades" : [  85, 80, 80  ] }, 　　{ "_id" : 2, "grades" : [ 88,90 ] }] |                                                              |
| **数组批量添加****相关运算符** | $each                                       | 和$push,$addToSet配合使用，用于批量添加元素                  | db.students.update( 　　{_id:1}, 　　{$push:　　　　{grades:{$each:[99,100]}}　　}) | 在_id=1的文档graders中添加元素[99,100]结果：　　[{ "_id" : 1, "grades" : [  85, 80, 80 ,99,100 ] }, 　　{ "_id" : 2, "grades" : [ 88,90 ,92] }] |
| $slice                         | 和$push,$each配合使用，用于限制元素个数     | db.students.update( 　　{_id:1}, 　　{$push:　　　　{grades:{$each:[99,100],$slice:4}}　　} ) | 在_id=1的文档graders中添加元素[99,100]结果：　　[{ "_id" : 1, "grades" : [  85, 80, 80 ,99] }, 　　{ "_id" : 2, "grades" : [ 88,90 ,92] }]如果使用$slice:-4，则保留后4个元素 |                                                              |
| $sort                          | 和$push,$each配合使用，在添加元素后进行排序 | db.students.update( 　　{_id:1}, 　　{$push:　　　　{grades:{$each:[70,100],$sort:1}}　　} ) | 在_id=1的文档graders中添加元素[99,100]，并排序结果：　　[{ "_id" : 1, "grades" : [70,80,80,85,100]}, 　　{ "_id" : 2, "grades" : [ 88,90 ,92] }]如果使用$sort:-1，则倒序排序 |                                                              |
| $position                      | 和$push,$each配合使用，指定插入元素的位置   | db.students.update( 　　{_id:1}, 　　{$push:　　　　{grades:{$each:[70,100],$position:1}}　　} ) | 在_id=1的文档graders中,从索引1开始插入元素[99,100]结果：　　[{ "_id" : 1, "grades" : [85,70,100,80,80]}, 　　{ "_id" : 2, "grades" : [ 88,90 ,92] }] |                                                              |

### 4.删除(remove/delete)

　　在3.2以前的版本中，mongoDB使用remove方法来删除文档，用法如下：

　　![img](https://img2018.cnblogs.com/blog/1007918/201906/1007918-20190616143017259-1159581044.png)

　　从3.2版本开始，提供了deleteOne()和deleteMany()方法，分别用于删除单条和多条document，语法如下：

```
//删除单条document，功能类似于remove设置justOne:true
　　db.userinfos.deleteOne({age:{$gt:25}})
//删除所有符合条件的document，功能类似于remove设置justOne:false
　　db.userinfos.deleteMany({age:{$gt:25}})
```

　　