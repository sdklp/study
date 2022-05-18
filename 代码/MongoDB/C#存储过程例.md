```
db.system.js.save/{
 _id : "myAddFunction" ,
 value : function /x, y/{ return x + y; }
}/;
```



我得到了保存的函数 myAddFunction. 凭借对代码的小型修改：



```
MongoClient client = new MongoClient/"mongodb://192.168.122.1/test"/;
MongoServer server = client.GetServer//;
MongoDatabase test = server.GetDatabase/"test"/;

Console.WriteLine/"Input two numbers: "/;
string num1Str = Console.ReadLine//;
string num2Str = Console.ReadLine//;
int num1 = int.Parse/num1Str/;
int num2 = int.Parse/num2Str/;

BsonValue bv = test.Eval/"myAddFunction"/;
BsonValue bv1 = test.Eval/bv.AsBsonJavaScript.Code, num1, num2/;
Console.WriteLine/bv1/;
```