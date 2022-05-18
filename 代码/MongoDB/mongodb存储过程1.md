# mongodb存储过程

  MongoDB支持存储过程的使用，它的存储过程是用javascript实现的，被存在于system.js表中，可以接收和输出参数，返回执行存储过程的状态值，也可以嵌套调用。

  **所以我理解的MongoDB的存储过程就是**：

  把javascript变量，存储到MongoDB的数据库的特殊集合:system.js表中,然后这些变量可以在何MongoDB的javascript上下文中调用,包括"$where"子句,db.eval调用,MapReduce作业。

  1.添加存储过程

```
  db.system.js.save({_id:存储过程名称,value:存储过程体})  //添加一个新的存储过程或者更新一个已经存在的存储过程

  或者
  db.system.js.insert({_id:存储过程名称,value:存储过程体}) //添加一个新的存储过程
 其中：_id和value属性是必须的，如果没有_id这个属性，会导致无法调用。也可以增加其他的属性来描述这个存储过程。比如：
  db.system.js.insert({_id:存储过程名称,value:存储过程体，discrption："这是存储过程"})
2.修改存储过程
  db.system.js.update({_id:存储过程名称},{value:存储过程体})
3.执行存储过程
  db.eval('存储过程名称(参数)')
4.查找存储过程   
  db.system.js.find();

以上命令都可以在mongodb shell命令窗口下执行，也可以写在javascript文件里执行。
下面的示例写在javascript文件里。
连接数据库有两种方法，如下，示例里面用的方法1
方法1：
db = connect("localhost:port/myDatabase");

方法2：
new Mongo() 或者 new Mongo(<host>) 或者 new Mongo(<host:port>)
conn = new Mongo();
db = conn.getDB("myDatabase");
新建test.js，代码如下： 
```



```
var db = connect( 'school' ); //连接的数据库名字为：school
db.courses.save({name:"英语",time:'周二 上午 8:00-9:50'});
db.courses.save({name:"数学",time:'周二 上午 10:00-12:00'});
db.courses.save({name:"语文",time:'周二 下午午 2:00-4:00'});
db.people.save({name:'刘湘',type:'teacher',age:28});
db.people.save({name:'王莎莎',type:'student',age:12});

//1.添加存储过程
   //db.system.js.save({_id:存储过程名称,value:存储过程体})
db.system.js.save({_id:"getCoursesCount",value:function(){return db.courses.count();},description:'获取课程数'});

   //db.system.js.insert({_id:存储过程名称,value:存储过程体})
db.system.js.insert({
    _id:"getPeople",
    value:function(type){
      return type;
    },
    description:'显示根据类型显示人员'
 });

//2.修改存储过程
  //db.system.js.update({_id:存储过程名称},{value:存储过程体})
db.system.js.update({
    _id:"getPeople"},{
    value:function(type){
        if(type==null){
            return db.people.find().toArray();
        }else {
            return db.people.find({type:type}).toArray();
        }
    }
});

//3.执行存储过程
  //db.eval('存储过程名称(参数)')
var obj=db.eval("getPeople()");
print(JSON.stringify(obj));
var obj1=db.eval("getPeople('student')");
print(JSON.stringify(obj1));
print(db.eval("getCoursesCount()"));

//4.查找存储过程
  //db.system.js.find();
var obj2= db.system.js.find();
print(JSON.stringify(obj2.toArray()));
```



 

找到test.js的文件位置，我的文件位置如下图，然后按住shift键，点击鼠标右键弹出一个菜单，选择 【在此处打开命令窗口（W）】

 

![img](https://images2015.cnblogs.com/blog/137142/201602/137142-20160202142109007-1521588429.png)

弹出命令窗口，接下来运行test.js,会有两种方法

方法1：

在命令窗口输入mongo test.js，按回车键，test.js就会被运行，如下图

![img](https://images2015.cnblogs.com/blog/137142/201602/137142-20160202142715085-2117616175.png)

方法2：在命令窗口输入 mongo 按回车键，然后输入load('test.js'),这样test.js也会被运行，如下图：

![img](https://images2015.cnblogs.com/blog/137142/201602/137142-20160202143047882-1790410887.png)