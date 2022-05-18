## MongoDB存储过程

### 1 什么是存储过程

​     存储过程（Stored Procedure）是在大型数据库系统中，一组为了完成特定功能的SQL 语句集，存储在数据库中，经过第一次编译后再次调用不需要再次编译，用户通过指定存储过程的名字并给出参数（如果该存储过程带有参数）来执行它。

​    个人理解存储过程相当于用户自定义了一些函数，存储在了DB里，后续可以在DB里轻松的调用。

### 2 MongoDB中的存储过程

​    mongDB中的存储过程是使用JavaScript实现，本质上，是将一段JS脚本存储到admin 的 system.js集合中。

####  2.1添加存储过程

   db.system.js.save({_id:存储过程名称,value:存储过程体}) //添加一个新的存储过程或者更新一个已经存在的存储过程

```
db.system.js.insert({_id:存储过程名称,value:存储过程体}) //添加一个新的存储过程
```

#### 2.2修改存储过程

 

```
  db.system.js.update({_id:存储过程名称},{value:存储过程体})
```



#### 2.3.执行存储过程

```
db.eval('存储过程名称(参数)')
```

### 3 存储过程实例

 

#### 3.1 Mongo中的JavaScript

   Mongo中引用了JS语法，可以通过直接 在MongoShell中执行，和脚本文件的方式执行。

```
conn =  new  Mongo( "nj01-build-junheng2-bak053.nj01.baidu.com:8829" );

db = conn.getDB( "testDB" );

db.courses.save({name: "英语" ,time: '周二 上午 8:00-9:50' });

db.courses.save({name: "数学" ,time: '周二 上午 10:00-12:00' });

db.courses.save({name: "语文" ,time: '周二 下午午 2:00-4:00' });

db.people.save({name: '刘湘' ,type: 'teacher' ,age: 28 });

db.people.save({name: '王莎莎' ,type: 'student' ,age: 12 });

// 如果脚本执行 则可直接使用db 不需要获取链接，链接在外层获取

 

//1.添加存储过程

    //db.system.js.save({_id:存储过程名称,value:存储过程体})

db.system.js.save({_id: "getCoursesCount" ,value:function(){ return  db.courses.count();},description: '获取课程数' });

 

    //db.system.js.insert({_id:存储过程名称,value:存储过程体})

db.system.js.insert({

     _id: "getPeople" ,

     value:function(type){

       return  type;

     },

     description: '显示根据类型显示人员'

  });

 

//2.修改存储过程

   //db.system.js.update({_id:存储过程名称},{value:存储过程体})

db.system.js.update({

     _id: "getPeople" },{

     value:function(type){

         if (type== null ){

             return  db.people.find().toArray();

         } else  {

             return  db.people.find({type:type}).toArray();

         }

     }

});

 

//3.执行存储过程

   //db.eval('存储过程名称(参数)')

var obj=db.eval( "getPeople()" );

print(JSON.stringify(obj));

var obj1=db.eval( "getPeople('student')" );

print(JSON.stringify(obj1));

print(db.eval( "getCoursesCount()" ));

 

//4.查找存储过程

   //db.system.js.find();

var obj2= db.system.js.find();

print(JSON.stringify(obj2.toArray()));
```

