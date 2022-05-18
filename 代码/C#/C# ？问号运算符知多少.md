# C# ？问号运算符知多少

***\*总结一下C#中问号有三种\****

***\*第一：三目操作运算符【 ? : 】\****

**问号前面的是条件，后面的是结果，条件满足返回冒号前面的值否则后面的值**

**事例**



```csharp
<span style="white-space:pre">	</span>public int WhoBig(int a, int b)

        {
            return a > b ? a : b;
        }
        public int WhoSmall(int a, int b)
        {
            return a < b ? a : b;
            //等价于
            /*if (a < b)
                return a;
            else
                return b;
             * */
        }
```

**第二：基本数据类型可空标识符【?】**

**声明的变量可以为空，比如int,string,但是布尔值为空依然报错**

**事例**

```csharp
<span style="white-space:pre">	</span>int i = null;//报错
        bool j = null; //报错
        int? k = null;//通过
        bool? m = null; //报错
```


***\*第三：null合并运算符【??】\****



**赋值的结果中的变量如果为空则用??后面的值替代前面的变量，否则直接用前面的变量**

***\**\*如果此运算符的左操作数不为 null，则此运算符将返回左操作数；否则返回右操作数\*\**\*
**

**事例**

```csharp
<span style="white-space:pre">	</span>public string Hongyan(string a)
        {
            string res = a;
            if (a == null)
                res = "";
            //等价于
            res = a ?? "";
            return res;
        }
```

如果a为空就选择？？后面的值否则前面的值

***\*第四：null条件运算符【?.】\****

用于在执行成员访问 (?.) 或索引 (?[) 操作之前，测试是否存在 NULL。 这些运算符可帮助编写更少的代码来处理 null 检查，尤其是对于下降到数据结构。

```csharp
int? length = customers?.Length; // null if customers is null 
Customer first = customers?[0];  // null if customers is null
int? count = customers?[0]?.Orders?.Count();  // null if customers, the first customer, or Orders is null
```

最后一个示例演示 NULL 条件运算符会短路。 如果条件成员访问和索引操作链中的某个操作返回 NULL，则该链其余部分的执行将停止。 表达式中优先级较低的其他操作将继续。 例如，以下的示例中的 E 将始终执行，?? 和 == 操作将执行。

```csharp
A?.B?.C?[0] ?? E
A?.B?.C?[0] == E
```

NULL 条件成员访问的另一个用途是使用非常少的代码以线程安全的方式调用委托。 旧方法需要如下所示的代码：

```csharp
var handler = this.PropertyChanged;
if (handler != null)
    handler(…)
```

新的方法是要简单得多：

```csharp
PropertyChanged?.Invoke(e)
```

新方法是线程安全的，因为编译器生成代码以评估 PropertyChanged（仅一次），从而使结果保持在临时变量中。
你需要显式调用 Invoke 方法，因为不存在 NULL 条件委托调用语法 PropertyChanged?(e)。 有太多不明确的分析情况来允许它。