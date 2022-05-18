# C#接口/泛型

## 一.接口

**1.1简介:**

接口是一种特殊的引用类型, 和类很相似. 它定义了属性, 方法, 事件, 这些都作为接口的成员/ 接口中只包含成员的声明, 成员的定义是派生类的责任(谁继承谁实现).

接口在某种程度上与抽象类类似, 但是接口大多数只用在当只有少数方法由基类声明由派生类实现时.

**其实接口主要就是用来解决多继承问题的;**

**1.2 接口的定义**

接口使用 interface 关键字声明,它与类的声明类似, 声明默认为 public .

```csharp
interface 接口名
{
    属性定义;   
    // 注意方法默认是公共的抽象方法, 不能加访问修饰符和 abstract 关键字 
    // 方法只声明, 不实现
    返回值类型 方法名(参数);
}
```

栗子

```csharp
// 定义一个陆地生物接口
// 接口名通常用 I 开头
interface ITerrestrial
{
    void Run();
}

// 定义一个水生接口
interface IAquatic
{
    void Swim();
}
```

再次注意:方法默认就是抽象的, 所以不用加abstract关键字, 加了反而会报错;

**1.3 接口实例**

让 Dog 类继承 ITerrestrial 接口

```csharp
class Dog : ITerrestrial
{
    // 接口的方法前不能加访问修饰符, 但是由派生类实现时需要在前面加上访问修饰符 public
    public void Run()
    {
        Console.WriteLine("狗在奔跑...");
    }
}
```

Fish 类继承 IAquatic 接口

```csharp
class Fish : IAquatic
{
    public void Swim()
    {
        Console.WriteLine("鱼在水中游");
    }
}
```

执行代码

```csharp
static void Main(string[] args)
{
    // 接口不能直接创建接口实例, 比如 ITerrestrial terrestrial = new ITerrestrial(); 这样子就是错误的;
    // 但是他可以间接实例化, 通过隶属原则把创建的子类对象赋值给接口对象;
    ITerrestrial dog = new Dog();
    Fish fish = new Fish();

    fish.Swim();
    dog.Run();
}
```

![img](https://pic1.zhimg.com/80/v2-cc72bfb84e0589cd4ffcc26b25cf3dec_720w.jpg)

**1.4 接口多继承**

- 派生类可以继承多个接口, 但是对继承过来的接口方法需要实现;
- 派生类可以同时继承类跟接口, 但是基类只能有一个, 接口不受限制;
- 多继承中间用逗号分隔连接;

例如 让青蛙类继承 ITerrestrial 接口和 IAquatic 接口

```csharp
// 继承多个接口直接用逗号隔开即可
class Frog : ITerrestrial, IAquatic
{
    // 继承两个接口就要重写两个接口中的所有方法;
    public void Run()
    {
        Console.WriteLine("青蛙在陆地上跳");
    }

    public void Swim()
    {
        Console.WriteLine("青蛙在水里游");
    }
}
```

实现:

```csharp
// Main 函数
Frog frog = new Frog();
frog.Run();
frog.Swim();
```

![img](https://pic4.zhimg.com/80/v2-9d1544388e5d3f70bec8157b44a0df77_720w.jpg)



**接口的特点:**

1. 可以多继承. 当继承同时有类和接口时, 类放在前面, 接口放在后面;
2. 接口不允许直接实例化(可以通过子类间接实例化);
3. 接口都是抽象的, 方法前不允许加访问修饰符(默认就是公开, 也只能是公开的);
4. B接口继承A接口, C继承B接口 C要实现A B中的所有方法;
5. 接口中不允许有构造方法, 析构方法, 静态成员和常量;
6. 接口中可以有抽象属性;

**接口和抽象类的区别(重点!!!):**

**相同点:**

1. 都包含抽象成员, 可以由子类继承;
2. 两者不能直接实例化;
3. 都属于引用类型;
4. 它们都有多态性;

**不同点:**

1. 接口中所有成员都是抽象的(且不加abstract 关键字), 而抽象类除了抽象成员还有非抽象成员;
2. 抽象类是类, 在C#中只能单继, 而接口一次可以继承多个;
3. 接口中只能声明属性, 方法, 事件, 索引器, 而抽象类还可以有字段, 静态成员和常量, 具体方法, 构造函数, 析构函数;
4. 接口中所有成员都是公开的(且不允许加访问修饰符), 抽象类不一定;

## 二.泛型

我们在编写程序时，经常遇到两个模块的功能非常相似(例如排序)，只是一个是处理int数据，另一个是处string数据，或者其他自定义的数据类型，虽然代码几乎一样,然还是得写好遍;

这时你可能会想到用object 来解决这个问题。但是他是有缺陷的：

1. 会出现装箱、折箱操作，这将在托管堆上分配和回收大量的变量，若数据量大，则性能损失非常严重;
2. 在处理引用类型时，虽然没有装箱和折箱操作，但将用到数据类型的强制转换操作，增加处理器的负担;

![img](https://pic4.zhimg.com/80/v2-ffac4c3b377ba0baf9f7d7b18b096a27_720w.jpg)



**2.1 泛型方法**

不过好在C#提供了泛型功能, 它能够将类型作为参数来传递, 即在创建类型时用一个特定的符号如 " T" 作为一个占位符, 代替实际的类型, 等待在实例化时用一个实际的类型来代替.

在方法名后面加<T>, T 是类型占位符; 这个是你自己定义的, 也可以是U,E等, 但一般都是大写字母.

**2.1.1 泛型方法的定义:**

> 1> 参数类型不确定, 返回值确定;
> public void ShowT<T>(T t){ 代码段; }



> 2> 参数类型确定, 返回值类型不确定;
> public T ShowT<T>(string name){ 代码段; }

使用情景: Unity 游戏开发中通过名字从资源文件夹中获取对象(比如模型);



> 3> 参数类型和返回值类型都不确定;
> public T ShowT<T>(T t){ 代码段; }



```csharp
        // 栗子: 将参数的值打印出来
        // 在这里写成了静态方法方便调用
        // 方法的返回值类型和参数类型中必须至少有一个泛型类型;
        public static void Print<T>(T a, T b)
        {
            Console.WriteLine(a);
            Console.WriteLine(b);
        }

            // Main 函数中使用
            Print<int>(20, 30);
            Print<string>("Hello ", "World!");
```

![img](https://pic3.zhimg.com/80/v2-f7ede777bf8fa07f00586669c99da9de_720w.jpg)

**2.1.2 泛型的优点:**

1. 使用泛型类型可以最大限度地重用代码、保护类型的安全以及提高性能。
2. 降低了强制转换或装箱操作的成本或风险。
3. 可以对泛型类进行约束以访问特定数据类型的方法。

**2.1.3 注意事项:**

1. 泛型成员因类型不确定(可能是类、结构体、字符、枚举……),所以不能使用算术运算符、比较运算符等进行运算！
2. 可以使用赋值运算符。

**2.2 泛型类型参数**

在泛型类型或方法定义中，类型参数是客户端在实例化泛型类型的变量时指定的特定类型的占位符, 可以将多个类型进行泛化。

例如:

```csharp
public static void Test<T, U>(T t, U u) // 多个占位符
{
}
```

**说明:**

1. 多个类型参数在<>中用逗号隔开;
2. 类型参数可以是编译器识别的任何类型;
3. 类型参数的名字不能重复;
4. 泛型类中的泛型方法后面不能跟<类型占位符>

**2.3 泛型类型参数的约束**

定义泛型类或泛型方法时, 可以对泛型类型进行约束限制客户端的传值. 如果客户端代码尝试使用某个约束不允许的类型来实例化类或方法, 则会产生编译错误. 这些限制成为约束.

1. struct 代表只能传递值类型数据;
2. class 代表只能传递引用类型;
3. where T : 接口名 表示只能传递实现该接口的类型;
4. where T : 类名 表示只能传递实现该类的类型
5. where T:new() 表示T 类型必须要有一个无参数的构造方法 , 当多个限定一起使用时, new() 必须放在最后;



**2.3 泛型类**

泛型类封装不特定数据类型的操作. 通常, 创建泛型类是从现有具体类开始, 然后每次逐个将类型更改为类型参数.

练习1: 将任意类型的数组拼接成字符串

```csharp
        // 定义一个泛化方法
        public static string Joins<T>(T[] arr)
        {
            string res = "";
            for (int i = 0; i < arr.Length; i++)
            {
                res += arr[i];
            }
            return res;
        }

            // Main函数中调用
            int[] arr = new int[] { 12, 52, 3 };
            char[] charr = new char[] { 'a', 'b', 'c' };
            调用泛型方法的时候只需要将具体的类型写出来即可;
            Console.WriteLine(Joins<int>(arr));
            Console.WriteLine(Joins<char>(charr));
```

![img](https://pic2.zhimg.com/80/v2-7e24cc1c09c09f0a6976b7e1cb55ad65_720w.jpg)

练习2 : 将任意类型的数组进行冒泡排序(考虑 对类和结构体排序)

```csharp
   // 定义一个Person类, 继承 IComparable接口是因为类本身是不能进行比较的, 但是继承该接口就可以重写接口中的CompareTo方法;自己定义比较运算
    public class Person:IComparable
    {
        public string name;
        public int age;
        // 构造方法
        public Person(string name, int age)
        {
            this.name = name;
            this.age = age;
        }
        // 重写 IComparable 的 CompareTo方法
        public int CompareTo(object obj)
        {
             // 用里氏转换将 object类型转换为 Person类型;
            Person per = obj as Person;
            // 自定义比较运算的规则
            if(this.age > per.age)
            {
                return 1;
            }else if(this.age < per.age)
            {
                return -1;
            }
            else
            {
                return 0;
            }
        }
    }
```

同理 结构体类型和类非常类似

```csharp
    // 定义 Student 结构体继承IComparable 接口
    public struct Student: IComparable
    {
        public string name;
        public int score;
        // 结构体的构造方法
        public Student(string name, int score)
        {
            this.name = name;
            this.score = score;
        }
        // 重写 CompareTo 方法
        public int CompareTo(object obj)
        {
            // 将 object 类型强制转化为 Student类型
            Student stu = (Student)obj;
            // 自定义比较规则
            if(this.score > stu.score)
            {
                return 1;
            }else if(this.score < stu.score)
            {
                return -1;
            }
            else
            {
                return 0;
            }
        }
    }
```

泛型的冒泡排序:

```csharp
        // 这里注意 泛型类型的参数是不能进行运算符的运算的, 要通过CompareTo方法来实现, 所以就要规定一下T要遵循IComparable接口
        public static void BubbleSort<T>(T[] arr) where T:IComparable
        {
            bool tag = true;
            for (int i = 0; i < arr.Length-1 && tag; i++)
            {
                tag = false;
                for (int j = 0; j < arr.Length - i - 1; j++)
                {
                    if (arr[j].CompareTo(arr[j + 1]) > 0)
                    {
                        tag = true;
                        T temp = arr[j];
                        arr[j] = arr[j + 1];
                        arr[j + 1] = temp;
                    }
                }
            }
        }

            // Main 函数中使用
            // int 类型数组
            int[] arr = new int[] { 5, 4, 3, 2, 1 };
            BubbleSort<int>(arr);
            foreach (int item in arr)
            {
                Console.WriteLine(item);
            }
            // Person 类型数组
            Person p1 = new Person("a", 19);
            Person p2 = new Person("b", 18);
            Person p3 = new Person("c", 17);
            Person[] parr = new Person[] { p1, p2, p3 };
            BubbleSort<Person>(parr);
            foreach (Person item in parr)
            {
                Console.WriteLine(item.name);
            }
```

![img](https://pic4.zhimg.com/80/v2-1769be9adb5cf9c55222db32e6a648c7_720w.jpg)



=======================================================



思维导图:

![img](https://pic3.zhimg.com/80/v2-46904f31aadad70775c4ea69149fd662_720w.jpg)

