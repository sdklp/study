# C# 单例模式和窗体的单例打开方法

第一种最简单，但没有考虑线程安全，在多线程时可能会出问题，不过俺从没看过出错的现象，表鄙视我……

public class Singleton
{
  private static Singleton _instance = null;
  private Singleton(){}
  public static Singleton CreateInstance()
  {
    if(_instance == null)

​    {
​      _instance = new Singleton();
​    }
​    return _instance;
  }
}

第二种考虑了线程安全，不过有点烦，但绝对是正规写法，经典的一叉 

public class Singleton
{
  private volatile static Singleton _instance = null;
  private static readonly object lockHelper = new object();
  private Singleton(){}
  public static Singleton CreateInstance()
  {
    if(_instance == null)
    {
      lock(lockHelper)
      {
        if(_instance == null)
           _instance = new Singleton();
      }
    }
    return _instance;
  }
}

第三种可能是C#这样的高级语言特有的，实在懒得出奇

public class Singleton
{

  private Singleton(){}
  public static readonly Singleton instance = new Singleton();
}  

 

## 一、 单例（Singleton）模式

单例模式的特点：

- 单例类只能有一个实例。
- 单例类必须自己创建自己的唯一实例。
- 单例类必须给所有其它对象提供这一实例。

单例模式应用：

- 每台计算机可以有若干个打印机，但只能有一个Printer Spooler，避免两个打印作业同时输出到打印机。
- 一个具有自动编号主键的表可以有多个用户同时使用，但数据库中只能有一个地方分配下一个主键编号。否则会出现主键重复。

##  二、 Singleton模式的结构：

![img](../../../我的文档/Typora/Pictures/Pic52.gif)

Singleton模式包含的角色只有一个，就是Singleton。Singleton拥有一个私有构造函数，确保用户无法通过new直接实例它。除此之外，该模式中包含一个静态私有成员变量instance与静态公有方法Instance()。Instance方法负责检验并实例化自己，然后存储在静态成员变量中，以确保只有一个实例被创建。（关于线程问题以及C#所特有的Singleton将在后面详细论述）。

##  三、 程序举例：

该程序演示了Singleton的结构，本身不具有任何实际价值。

![img](../../../我的文档/Typora/Pictures/None.gif)// Singleton pattern -- Structural example 
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)using System;
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)// "Singleton"
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)class Singleton
![img](../../../我的文档/Typora/Pictures/ExpandedBlockStart.gif){
![img](../../../我的文档/Typora/Pictures/InBlock.gif) // Fields
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) private static Singleton instance;
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) // Constructor
![img](../../../我的文档/Typora/Pictures/ExpandedSubBlockStart.gif) protected Singleton() {}
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) // Methods
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) public static Singleton Instance()
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif) {
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  // Uses "Lazy initialization"
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  if( instance == null )
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   instance = new Singleton();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  return instance;
![img](../../../我的文档/Typora/Pictures/ExpandedSubBlockEnd.gif) }
![img](../../../我的文档/Typora/Pictures/ExpandedBlockEnd.gif)}
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockStart.gif)/// <summary>
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)/// Client test
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockEnd.gif)/// </summary>
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)public class Client
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockStart.gif){
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) public static void Main()
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif) {
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  // Constructor is protected -- cannot use new
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  Singleton s1 = Singleton.Instance();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  Singleton s2 = Singleton.Instance();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  if( s1 == s2 )
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   Console.WriteLine( "The same instance" );
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif) }
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockEnd.gif)}

##   四、 在什么情形下使用单例模式：

使用Singleton模式有一个必要条件：在一个系统要求一个类只有一个实例时才应当使用单例模式。反过来，如果一个类可以有几个实例共存，就不要使用单例模式。

注意：

不要使用单例模式存取全局变量。这违背了单例模式的用意，最好放到对应类的静态成员中。

不要将数据库连接做成单例，因为一个系统可能会与数据库有多个连接，并且在有连接池的情况下，应当尽可能及时释放连接。Singleton模式由于使用静态成员存储类实例，所以可能会造成资源无法及时释放，带来问题。

##  五、 Singleton模式在实际系统中的实现

下面这段Singleton代码演示了负载均衡对象。在负载均衡模型中，有多台服务器可提供服务，任务分配器随机挑选一台服务器提供服务，以确保任务均衡（实际情况比这个复杂的多）。这里，任务分配实例只能有一个，负责挑选服务器并分配任务。

![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)// Singleton pattern -- Real World example 
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)using System;
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)using System.Collections;
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)using System.Threading;
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)// "Singleton"
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)class LoadBalancer
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockStart.gif){
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) // Fields
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) private static LoadBalancer balancer;
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) private ArrayList servers = new ArrayList();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) private Random random = new Random();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) // Constructors (protected)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) protected LoadBalancer()
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif) {
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  // List of available servers
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  servers.Add( "ServerI" );
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  servers.Add( "ServerII" );
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  servers.Add( "ServerIII" );
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  servers.Add( "ServerIV" );
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  servers.Add( "ServerV" );
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif) }
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) // Methods
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) public static LoadBalancer GetLoadBalancer()
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif) {
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  // Support multithreaded applications through
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  // "Double checked locking" pattern which avoids
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  // locking every time the method is invoked
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  if( balancer == null )
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif)  {
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   // Only one thread can obtain a mutex
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   Mutex mutex = new Mutex();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   mutex.WaitOne();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   if( balancer == null )
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)    balancer = new LoadBalancer();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   mutex.Close();
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif)  }
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  return balancer;
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif) }
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) // Properties
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) public string Server
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif) {
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  get
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif)  {
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   // Simple, but effective random load balancer
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   int r = random.Next( servers.Count );
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   return servers[ r ].ToString();
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif)  }
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif) }
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockEnd.gif)}
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockStart.gif)/// <summary>
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)/// SingletonApp test
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)/// </summary>
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockEnd.gif)///
![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)public class SingletonApp
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockStart.gif){
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) public static void Main( string[] args )
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif) {
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  LoadBalancer b1 = LoadBalancer.GetLoadBalancer();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  LoadBalancer b2 = LoadBalancer.GetLoadBalancer();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  LoadBalancer b3 = LoadBalancer.GetLoadBalancer();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  LoadBalancer b4 = LoadBalancer.GetLoadBalancer();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  // Same instance?
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  if( (b1 == b2) && (b2 == b3) && (b3 == b4) )
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)   Console.WriteLine( "Same instance" );
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  // Do the load balancing
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  Console.WriteLine( b1.Server );
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  Console.WriteLine( b2.Server );
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  Console.WriteLine( b3.Server );
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  Console.WriteLine( b4.Server );
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif) }
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockEnd.gif)}

##   六、 C#中的Singleton模式

C#的独特语言特性决定了C#拥有实现Singleton模式的独特方法。这里不再赘述原因，给出几个结果：

**方法一：**

下面是利用.NET Framework平台优势实现Singleton模式的代码：

![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)sealed class Singleton
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockStart.gif){
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  private Singleton();
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  public static readonly Singleton Instance=new Singleton();
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockEnd.gif)}

这使得代码减少了许多，同时也解决了线程问题带来的性能上损失。那么它又是怎样工作的呢？

注意到，Singleton类被声明为sealed，以此保证它自己不会被继承，其次没有了Instance的方法，将原来_instance成员变量变成public readonly，并在声明时被初始化。通过这些改变，我们确实得到了Singleton的模式，原因是在JIT的处理过程中，如果类中的static属性被任何方法使用时，.NET Framework将对这个属性进行初始化，于是在初始化Instance属性的同时Singleton类实例得以创建和装载。而私有的构造函数和readonly(只读)保证了Singleton不会被再次实例化，这正是Singleton设计模式的意图。
（摘自：http://www.cnblogs.com/huqingyu/archive/2004/07/09/22721.aspx ）

不过这也带来了一些问题，比如无法继承，实例在程序一运行就被初始化，无法实现延迟初始化等。

详细情况可以参考微软MSDN文章：[《Exploring the Singleton Design Pattern》](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dnbda/html/singletondespatt.asp)

**方法二：**

既然方法一存在问题，我们还有其它办法。

 

![img](https://www.cnblogs.com/Images/OutliningIndicators/None.gif)public sealed class Singleton
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockStart.gif){
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) Singleton()
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif) {
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif) }
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) public static Singleton GetInstance()
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif) {
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  return Nested.instance;
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif) }
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif) class Nested
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif) {
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  // Explicit static constructor to tell C# compiler
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  // not to mark type as beforefieldinit
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  static Nested()
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockStart.gif)  {
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif)  }
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)
![img](https://www.cnblogs.com/Images/OutliningIndicators/InBlock.gif)  internal static readonly Singleton instance = new Singleton();
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedSubBlockEnd.gif) }
![img](https://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockEnd.gif)}

这实现了延迟初始化，并具有很多优势，当然也存在一些缺点。详细内容请访问：《Implementing the Singleton Pattern in C#》。文章包含五种Singleton实现，就模式、线程、效率、延迟初始化等很多方面进行了详细论述。

 

参考链接：http://blog.csdn.net/zhuangzhineng/article/details/3927455

 

 

单 例模式是广为流传的设计模式中的一种。本质上，单例模式是一个只允许创建一个实例，并提供对这个实例简单的访问途径的类。一般而言，单例模式在创建实例时 不允许传递任何参数－否则不同参数导致不同的实例创建，就会出现问题！（如果同一个实例可以被同参的不同请求所访问，那么工厂模式会更适合。）这篇文章只 针对无参创建的请求进行讨论。典型的，单例模式的应用往往是延后创建的（created lazily）－－－只有在第一次被用到的时候才会被创建。在C#中有实现单例模式有很多种方法。我将在这里一一展现给大家，从最常见的、线程不安全的到 延后创建的、线程安全的、再到简洁高效的版本。注意在下面的代码中，我忽略了所有私有域，因为私有域是默认的类的成员。In many other languages such as Java, there is a different default, and private should be used.
所有的这些实现都有以下四个特征：
  1.只有一个构造函数，而且是私有的，不带参数的。这是为了防止其他类对其实例化（这和模式本身有冲突）。同时也防止了子类化－－如果一个单例能被子类化 一次，就能被子类化两次，而如果每个子类可以创建一个实例，这与模式本身又产生了冲突。如果你（遇到这样的情况）：只有在运行期才能知道实际的类型，因此 你需要一个父类的单例，你可以使用工厂模式。
 2.类是密封的。这并不是必须的，严格的说，即如上一点所说的原因，可以提高JIT（Just-In-Time , 运行时编译执行的技术）的效率。
  3.一个静态变量用来保存单例的引用。
  4.一个用以访问单例引用的公用静态方法。
注意：所有这些实现都用到一个公用静态属性Instance，作为访问实例的方法。当然都可以替换为方法，这对线程安全和性能都没有影响。

 

版本1－非线程安全

// Bad code! Do not use!
public sealed class Singleton
{
  static Singleton instance=null;
  Singleton()
   {
  }
  public static Singleton Instance
   {
    get
     {
      if (instance==null)
       {
        instance = new Singleton();
      }
      return instance;
    }
  }
}
 
如 上提示，上面的代码是非线程安全的。两个线程可能同时判断“if (instance==null)”，发现为TRUE,于是都创建了实例，这又违背了单例模式。注意：事实上在表达式反应前实例已经被创建了，内存模型并 不保证新的实例的值被其他的线程看到，除非对应的内存屏障已经通过了。（CPU越过内存屏障后，将刷新自已对存储器的缓冲状态，这样其他线程才能同步自己 的copy）

 

版本2 － 简单的线程安全

public sealed class Singleton
{
  static Singleton instance=null;
  static readonly object padlock = new object();
  Singleton()
   {
  }
  public static Singleton Instance
   {
    get
     {
      lock (padlock)
       {
        if (instance==null)
         {
          instance = new Singleton();
        }
        return instance;
      }
    }
  }
}
这 个实现是线程安全的。线程首先对共用的对象进行锁定，然后判断实例是否在之前已经创建。这里要小心内存屏障问题（在锁定的时候确保所有的读操作发生在所获 得之后，在解锁的时候确保所有的写操作发生在锁释放之前），确保只有一个线程创建了实例（因为在同一时刻只有一个线程可以在执行那段代码－－到了第二个线 程进入的时候，第一个线程已经完成了实例的创建，这样表达式返回false）。不幸的是，由于在每次访问的时候都要进行锁定，所以影响了性能。（这对于多 线程并发的高性能要求的应用显得尤为重要）。注意有些该版本的实现对typeof(Singleton)进行锁定，但我是对类的私有静态变量进行锁定。对 那些可被其他类访问的对象进行锁定或对类型进行锁定会导致性能问题甚至引起死锁。这是一个地雷－任何地方都有可能发生，只有对本就为锁定而创建的对象进行 锁定或是那些因为某些目的而被锁定的文档才是安全的。通常这些对象都是私有的。这样有助于写出线程安全的程序。

 

版本3 －－ 用双重检测机制的线程安全

// Bad code! Do not use!
public sealed class Singleton
{
  static Singleton instance=null;
  static readonly object padlock = new object();
  Singleton()
   {
  }
  public static Singleton Instance
   {
    get
     {
      if (instance==null)
       {
        lock (padlock)
         {
          if (instance==null)
           {
            instance = new Singleton();
          }
        }
      }
      return instance;
    }
  }
}

 

# C#单例模式的几种实现方式

 

*文章总结自张波老师的视频教程*

> 单例模式

- 动机(Motivation)
  - 在软件系统中，经常有这样一些特殊的类，必须保证它们在系统中只存在一个实例，才能确保它们的逻辑正确性、以及良好的效率。
  - 如何绕过常规的构造器，提供一种机制来保证一个类只有一个实例？
  - 这应该是类设计者的责任，而不是使用者的责任
- 意图(Intent)
  - 保证一个类仅有一个实例，并提供一个该实例的全局访问点。——《设计模式》GoF

> 简单实现

```
public sealed class Singleton
{
    private static Singleton _instance = null;

    private Singleton()
    {

    }

    public static Singleton Instance
    {
        get { return _instance ?? (_instance = new Singleton()); }
    }
}
```



说明：

- 对于线程来说不安全
- 单线程中已满足要求
- 优点： 
  - 由于实例是在 Instance 属性方法内部创建的，因此类可以使用附加功能
  - 直到对象要求产生一个实例才执行实例化；这种方法称为“惰性实例化”。惰性实例化避免了在应用程序启动时实例化不必要的 singleton。

> 线程安全的

```
public sealed class Singleton
{
    private static Singleton _instance = null;
    private static readonly object Padlock = new object();

    private Singleton()
    {
    }

    public static Singleton Instance
    {
        get
        {
            lock (Padlock)
            {
                if (_instance == null)
                {
                    _instance = new Singleton();
                }

            }
            return _instance;
        }
    }
}
```



说明：

- 同一个时刻加了锁的那部分程序只有一个线程可以进入
- 对象实例由最先进入的那个线程创建
- 后来的线程在进入时（instence == null）为假，不会再去创建对象实例
- 增加了额外的开销，损失了性能

> 双重锁定

```
public sealed class Singleton
{
    private static Singleton _instance = null;
    private static readonly object Padlock = new object();

    private Singleton()
    {
    }

    public static Singleton Instance
    {
        get
        {
            if (_instance == null)
            {
                lock (Padlock)
                {
                    if (_instance == null)
                    {
                        _instance = new Singleton();
                    }

                }
            }

            return _instance;
        }
    }
}
```



说明：

- 多线程安全
- 线程不是每次都加锁
- 允许实例化延迟到第一次访问对象时发生

> 静态初始化

```
public sealed class Singleton
{
    private static readonly Singleton _instance = null;

    static Singleton()
    {
        _instance = new Singleton();
    }

    private Singleton()
    {
    }

    public static Singleton Instance
    {
        get
        {
            return _instance;
        }
    }
}
```



说明：

- 依赖公共语言运行库负责处理变量初始化
- 公共静态属性为访问实例提供了一个全局访问点
- 对实例化机制的控制权较少(.NET代为实现)
- 静态初始化是在 .NET 中实现 Singleton 的首选方法

> 延迟初始化

```
public sealed class Singleton
{
    private Singleton()
    {
    }

    public static Singleton Instance
    {
        get
        {
            return Nested.instance;
        }
    }

    private class Nested
    {
        internal static readonly Singleton instance = null;
        static Nested()
        {
            instance = new Singleton();
        }
    }
}
```



说明：

- 初始化工作由Nested类的一个静态成员来完成，这样就实现了延迟初始化

> 注意事项

- Singleton模式中的实例构造器可以设置为protected以允许子类派生。
- Singleton模式一般不要支持ICloneable接口，因为这可能会导致多个对象实例，与Singleton模式的初衷违背。
- Singleton模式一般不要支持序列化，因为这也有可能导致多个对象实例，同样与Singleton模式的初衷违背。
- Singletom模式只考虑到了对象创建的管理，没有考虑对象销毁的管理。就支持垃圾回收的平台和对象的开销来讲，我们一般没有必要对其销 毁进行特殊的管理。

> 总结

- Singleton模式是限制而不是改进类的创建。
- 理解和扩展Singleton模式的核心是“如何控制用户使用new对一个类的构造器的任意调用”。
- 可以很简单的修改一个Singleton，使它有少数几个实例，这样做是允许的而且是有意义的。