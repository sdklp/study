mongoDB存储过程

简介： 存储过程 关系型数据库的存储过程描述为：一组为了完成特定功能的SQL 语句集，经编译后存储在数据库中，用户通过指定存储过程的名字并给出参数（如果该存储过程带有参数）来执行它。 mongoDB也有存储过程，但是mongoDB是用javascript来写的，这正是mongoDB的魅力。
存储过程

关系型数据库的存储过程描述为：一组为了完成特定功能的SQL 语句集，经编译后存储在数据库中，用户通过指定存储过程的名字并给出参数（如果该存储过程带有参数）来执行它。

mongoDB也有存储过程，但是mongoDB是用javascript来写的，这正是mongoDB的魅力。

保存存储过程
mongodb的存储过程是存放在db.system.js表中，我们先来一个简单的例子：

1 function add(x,y){  
2     return x+y;  
3 }  
现在我们将这个存储过程保存到db.system.js的表中：

创建存储过程代码

1 > db.system.js.save({"_id":"myAdd",value:function add(x,y){ return x+y; }});  
其中：_id和value属性是必须的，如果没有_id这个属性，会导致以后无法调用（到目前为止我还没有找到调用的方式方法，如果大家有什么办法，请回复我。）。你可以增加其他的属性来描述这个存储过程。比如：

1 > db.system.js.save({"_id":"myAdd1",value:function add(x,y){ return x+y; },"discrption":"x is number ,and y is number"});  
增加了discrption来描述这个函数。

查询存储过程
可以使用find来查询存储过程，和之前MongoDB查询文档中描述一样例如：

查询存储过程代码

 1 //直接查询所有的存储过程  
 2 > db.system.js.find();  
 3 { "_id" : "myAdd", "value" : function __cf__13__f__add(x, y) {  
 4     return x + y;  
 5 } }  
 6 { "_id" : "myAdd1", "value" : function __cf__14__f__add(x, y) {  
 7     return x + y;  
 8 }, "discrption" : "x is number ,and y is number" }  
 9 { "_id" : ObjectId("5343686ba6a21def9951af1c"), "value" : function __cf__15__f__  
10 add(x, y) {  
11     return x + y;  
12 } }  
13 //查询_id为myAdd1的存储过程  
14 > db.system.js.find({"_id":"myAdd1"});  
15 { "_id" : "myAdd1", "value" : function __cf__16__f__add(x, y) {  
16     return x + y;  
17 }, "discrption" : "x is number ,and y is number" }  
18 >  
执行存储过程
保存好的存储是如何执行的呢？

这里有个牛逼的函数，eval；如果对js了解的人肯定知道这个eval。用来执行一段字符串（描述的比较肤浅，呵呵），在mongodb中使用db.eval("函数名(参数1，参数2...)")，来执行存储过程（函数名找的是_id）：

执行存储过程代码

1 > db.eval('myAdd(1,2)');  
2 3 
eval会找到对应_id属性执行存储过程。

db.eval()是一个比较奇怪的东西，我们可以将存储过程的逻辑直接在里面并同时调用，而无需事先声明存储过程的逻辑。

执行存储过程代码

1 > db.eval(function(){return 3+3;});  
2 6  