# MongoDB之——存储过程



MongoDB 为很多问题提供了一系列的解决方案，针对于其它数据库的特性，它仍然毫不示弱，表现的非比寻常。
MongoDB 同样支持存储过程。关于存储过程你需要知道的第一件事就是它是用 javascript 来写的。也许这会让你很奇怪，为什么它用 javascript 来写，但实际上它会让你非常满意，MongoDB 存储过程是存储在 db.system.js 表中的，我们想象一个简单的 sql 自定义函数如下：

```plain
function addNumbers( x , y ) {
return x + y;
}1.2.3.
```

下面我们将这个 sql 自定义函数转换为 MongoDB 的存储过程:

```plain
> db.system.js.save({_id:"addNumbers", value:function(x, y){ return x + y; }});1.
```

存储过程可以被查看，修改和删除，所以我们用 find 来查看一下是否这个存储过程已经被创建上了。

```plain
> db.system.js.find()
{ "_id" : "addNumbers", "value" : function cf__1__f_(x, y) {
return x + y;
} }
>1.2.3.4.5.
```

这样看起来还不错，下面我看来实际调用一下这个存储过程:

```plain
> db.eval('addNumbers(3, 4.2)');
7.2
>1.2.3.
```

这样的操作方法简直太简单了，也许这就是 MongoDB 的魅力所在。db.eval()是一个比较奇怪的东西，我们可以将存储过程的逻辑直接在里面并同时调用，而无需事先声明存储过程的逻辑。

```plain
> db.eval( function() { return 3+3; } );
6
>1.2.3.
```

从上面可以看出， MongoDB 的存储过程可以方便的完成算术运算，但其它数据库产品在存储过程中可以处理数据库内部的一些事情，例如取出某张表的数据量等等操作，这些MongoDB 能做到吗？答案是肯定的， MongoDB 可以轻而易举的做到，看下面的实例吧:

```plain
> db.system.js.save({_id:"get_count", value:function(){ return db.c1.count(); }});
> db.eval('get_count()')
21.2.3.
```

可以看到存储过程可以很轻松的在存储过程中操作表。