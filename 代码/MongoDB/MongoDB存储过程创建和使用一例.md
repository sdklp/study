## MongoDB存储过程创建和使用一例

mongo的脚本是js语法，所以存储过程也是js语法。

 

创建：

```
db.system.js.save(
  {
    _id: "saveAndCount",      
    value : function(x) { 
        for(var i=0;i<x;i++){
           db.[表名].save(
           {
                "status" : "200",
                "msg" : "登陆状态"
            }
           );
        }
        return db.getCollection('xxx').find({}).count(); 
        }    
  })
```

 

查询：

```
 db.system.js.find()
```

 

删除：

```
 db.system.js.remove({"_id" : "saveAndCounts(10)"});
```

 

执行：

```
use [库名]
db.eval("saveAndCount(1)")
```