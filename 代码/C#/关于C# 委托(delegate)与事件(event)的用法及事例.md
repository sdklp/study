C#中的委托和事件对于新手可能会有一点难理解，所以先从一个小例子入手，以便能更好的理解其如何使用。有一个学生每天定闹钟在早上6点起床，所以当每天早上6点的时候，闹钟就会响起来，从而学生才会按时起床。

上面例子实际上包括2个类，一个是学生类(Student)，一个是闹钟类(Ring)。此时，让我们仔细想想，当闹钟到点后如何通知学生呢？当然不要说，闹钟响了，学生能听到这样的话23333，现在是写程序，一切用程序说话。也就是说当时间到了，闹钟类里应该有个给学生发消息的方法(OnSendMessage())，学生类里会有处理这个消息方法(HandleEvent())，比如说起床。看似蛮对的，但是有一个问题，这不是实时通信程序，学生如何检测到闹钟发来的信息，从而去调用起床的方法呢？实际在程序中，我们是通过在闹钟类的发消息方法中调用学生类中的起床方法实现的。是不是有点乱了？那换句话说，就是当响铃这一事件被触发后，起床会自动发生。呃，不小心就把事件给说出来了，感觉自己有点啰嗦了。

那什么是委托，委托其实是一种编程技术，事件机制是委托这种技术的应用。简单的说，通过在声明个委托delegate，将HandleEvent()方法交给delegate，这样在闹钟类中就可以通过委托调用HandleEvent()方法了。

到这里有的朋友可能读懵了。不用担心，不懂可以先继续往下看。说下委托和事件各自的声明格式：

**委托：**[修饰符] delegate 返回类型 委托名 (参数列表)
**举例：**

```c#
public delegate void RingHandler();
```

**事件：**[修饰符] event 委托名 事件名

**举例：**

```c#
public event RingHandler SendMessage;
```

根据上面的小例子，我们把代码实现，然后大家细细体会。

------

#### 1、需要声明一个委托

```c#
public delegate void RingHandler();
```

**需要注意2点:**
\1) 委托的声明在类外与类的声明并列;
\2) 委托的返回类型和参数列表必须与需要被委托方法(HandleEvent())的返回类型和参数列表相同。

#### 2、创建一个定义事件的类，即消息的发送方(Ring)。

需要包含：
(1)与委托关联的事件；
(2)事件的触发方法。

```c#
public delegate void RingHandler();//注意返回类型和参数列表与事件处理方法返回类型和参数列表一致

public class Ring
{
    public event RingHandler SendMessage;//与委托关联的事件，此时不懂没关系，知道是个事件就行。
    public void OnSendMessage()//事件触发时调用的方法 
    {
        SendMessage();
    }
}
```

#### 3、定义一个将方法连接到接收事件的上的类(Student)。

需要包含：
(1)事件处理方法；
(2)将事件与事件处理方法相关联。

```c#
public class Student
{
    public void HandleEvent()//事件处理方法
    {
        Console.WriteLine("该起床了");
    }
    public void Register(Ring ring)
    {
        //此处注意事件注册或移除只能用+=/-=符号，不能用其他。括号里只需写上方法名即可
        ring.SendMessage += new RingHandler(HandleEvent);
    }
}
```

#### 4、最后创建实例使程序运行，代码如下：

```c#
class Program
{
    static void Main(string[] args)
    {
        Ring ring = new Ring();
        Student student = new Student();
        student.Register(ring);

        if(GetTime() == 6)//如果时间是6点，就触发响铃方法。GetTime()不给实现了。
        {
            ring.OnSendMessage();
        }
    }
}
```

------

总结一下，事件的使用简单就是三点：
(1)定义事件；
(2)注册事件；
(3)触发事件。

大概就是这样使用的，但是在实际工作中，通常我们只需要完成事件处理方法中的代码，不必关心事件和委托的定义。