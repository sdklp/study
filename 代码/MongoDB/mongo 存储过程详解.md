# mongo 存储过程详解





## 存储过程

关系型数据库的存储过程描述为： 一组为了完成特定功能的SQL 语句集，经编译后存储在数据库中，用户通过指定存储过程的名字并给出参数（如果该存储过程带有参数）来执行它 。

MongoDB 为很多问题提供了一系列的解决方案，针对于其它数据库的特性，它仍然毫不示 弱，表现的非比寻常。MongoDB 同样支持存储过程,但mongoDB是用javascript来写的 ，这正是mongoDB的魅力。

MongoDB 同样支持存储过程。关于存储过程你需要知道的第一件事就是它是用 javascript 来写的。也许这会让你很奇怪，为什么它用 javascript 来写，但实际上它会让你非常满意，MongoDB 存储过程是存储在 db.system.js 表中的

### 例如：

#### 计算x+y的值

```
function test(x,y){

return x+y;
}
复制代码
```

#### 定义查询总数 存储过程

```
function findTotal(){
return db.user.find().count();
}
复制代码
```

### 编译保存

```
db.system.js.save({"_id":"test","value":function test(x,y){return x+y;}});
复制代码
db.system.js.save({"_id":"findTotal","value":function findTotal(){return db.user.find().count();}）
复制代码
```

#### 执行返回

```
// 1
WriteResult({
	"nMatched" : 1,
	"nUpserted" : 0,
	"nModified" : 0,
	"writeConcernError" : [ ]
})
复制代码
```

### eval 函数

可以将存储过程逻辑直接在里面定义并且 同时调用，无需事先声明！

```
db.eval("function test(x,y){return 2 + 4;}");  // 6 
复制代码
```

##### 查询表中的数据总数

```
db.eval("function total(){ return db.user.find().count();}");  //25
复制代码
```

### 查看所有的存储过程

db.system.js.find();



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/12/15/167b0ba081e90190~tplv-t2oaga2asx-watermark.awebp)



#### 执行结果

总数

db.eval('findTotal()');



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/12/15/167b0ba081d97870~tplv-t2oaga2asx-watermark.awebp)



db.eval('test(2,4)');



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/12/15/167b0ba081ca7169~tplv-t2oaga2asx-watermark.awebp)



### mongodb 执行存储过程

mongodb 存储过程是用js语言写的，java调用js脚本即可！

#### java 执行js脚本文件：

```
/**
 * 调用mongodb存储过程 js脚本文件
 * @return
 */
@Test
public void test(){
    ScriptOperations scriptOps = mongoTemplate.scriptOps();
    Object findTotal = scriptOps.call("findTotal");//执行服务器端脚本
    //call(String script,Object...args)参数放在脚本名称之后
    Object test = scriptOps.call("test",4,4);//执行服务器端脚本

    System.out.println(Double.parseDouble(findTotal.toString()));  //25条
    System.out.println(Double.parseDouble(test.toString()));       // 8 
 
}
复制代码
```

> 注意script脚本不能为null或者“” 不然会异常！

mongoTemplate 中方法返回DefaultScriptOperations对象：

MongoTemplate 实现 MongoOperations接口

MongoTemplate 中方法

```
public ScriptOperations scriptOps() {
    return new DefaultScriptOperations(this);
}
复制代码
```

###### call方法

接口 ScriptOperations

```
@Nullable
Object call(String var1, Object... var2);
复制代码
```

实现类：

class DefaultScriptOperations 实现ScriptOperations 接口

构造方法

```
public DefaultScriptOperations(MongoOperations mongoOperations) {
    Assert.notNull(mongoOperations, "MongoOperations must not be null!");
    this.mongoOperations = mongoOperations;
}
复制代码
```

方法

```
public Object call(final String scriptName, final Object... args) {
    Assert.hasText(scriptName, "ScriptName must not be null or empty!");
    return this.mongoOperations.execute(new DbCallback<Object>() {
        public Object doInDB(MongoDatabase db) throws MongoException, DataAccessException {
            return db.runCommand(new Document("eval", String.format("%s(%s)", scriptName, DefaultScriptOperations.this.convertAndJoinScriptArgs(args)))).get("retval");
        }
    });
}
复制代码
```

通过调用scriptOps（）方法返回MongoOperations引用 ，实际上还是mongoTemplate去执行execute方法 是在 ScriptOperations接口的实现类中DefaultScriptOperations 调用 ScriptOperations接口的call方法，最后根据DefaultScriptOperations 类中的去其他方法 convertAndJoinScriptArgs 传递验证参数类型以及转换。 最后调用最底层runCommand命令执行存储过程。