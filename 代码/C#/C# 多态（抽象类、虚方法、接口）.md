# C# 多态（抽象类、虚方法、接口）

多态：让一个对象能够变出多种状态（类型），使用父类类型调用子类中实现的方法。

实现多态的手段：抽象类、虚方法、接口

# 抽象类:

1、抽象方法只能出现在抽象类中，但是抽象类中可以包含普通方法（普通方法可以由非抽象类的子类调用）。

2、在父类中定义的抽象方法不能实现（也就是没有方法体，定义的时候去掉大括号{}）。
例如：
public abstract class Function{



```csharp
    public void A(){
        Console.WriteLine ("A方法是普通方法");
    }
    public abstract void B(); // 没有方法体 只有方法名定义
```

}

3、抽象类不能实例化（也就是不能new出来）。

4、抽象类与抽象方法需要添加abstract关键字（如：public abstract Class Person{}，public abstract void Test（）；）。

5、子类实现父类的抽象方法时，需要添加override关键字。

6、如果抽象类的子类不是抽象类，那么子类中必须重写父类抽象类的所有抽象方法。
public abstract class Person{



```csharp
    public void A(){
        Console.WriteLine ("A方法是普通方法");
    }
    public abstract void B(); // 没有方法体 只有方法名定义
```

}

public abstract class Teacher:Person{



```csharp
    public abstract void C ();
```

}

public class Student:Teacher{



```csharp
    public override void B ()// 这里是利用override重写父类Person中的抽象B方法
    {
        Console.WriteLine ("这是B方法");
    }
    public override void C ()// 这里是利用override重写父类Teacher中的抽象C方法
    {
        // 因为Student是普通方法 Teacher是抽象方法 而Teacher的父类Person又是一个抽象函数
        // 所以Student类必须实现Teacher类和Person中定义的抽象方法
        Console.WriteLine ("这是C方法");
    }
```

}

# 虚方法

方法替换：（这只是方法的替换 并非是虚方法）
子类继承父类之后，可以隐藏父类中的方法，在子类中重新实现（使用关键词new）
public class Person{



```c#
    public void A(){
        Console.WriteLine ("A方法是父类方法");
    }
```

}

public class Teacher:Person{



```c#
    public new void  A (){//这里使用new关键词将父类的A方法替换为子类的A方法
        Console.WriteLine ("A方法是子类方法");
    }
```

}

举个栗子：



```c#
        Person p1 = new Person ();
        p1.A ();// 如果等号前后定义的类型一致，那么这里的A方法为父类的方法

        Teacher t1 = new Teacher ();
        t1.A ();// 如果等号前后定义的类型一致，那么这里的A方法为子类的方法

        Person p2 = new Teacher ();
        p2.A ();// 如果使用父类类型变量接收子类类型的对象，那么此时方法为父类的方法
```

运行结果显示：

![img](https://upload-images.jianshu.io/upload_images/5276281-fca2c91426b8da6e.png?imageMogr2/auto-orient/strip|imageView2/2/w/807/format/webp)

# 虚方法和重写：

1、用virtual修饰的方法叫虚方法，用override修饰的方法为重写。
2、只有方法和属性才能是虚，字段不能虚。
运行结果显示：



![img](https://upload-images.jianshu.io/upload_images/5276281-1d7d1e3e58c2f5d9.png?imageMogr2/auto-orient/strip|imageView2/2/w/804/format/webp)

虚方法与抽象类的区别：
（1）抽象方法必须在抽象类中，而虚方法不用。
（2）抽象方法在父类中不能实现，而虚方法可以实现。
（3）抽象方法在非抽象子类中必须实现（至少一个子类要实现，否则，没意义），而虚方法不用（虚方法可以使用父类自身的方法）。

# 接口

接口的格式可以记成用interface替换Class来定义（interface接口默认的访问修饰符是internal），如 ：public interface A { }

在接口中定义方法：
1、不能给 方法和属性 添加访问修饰符，默认都是public。
2、接口中可以包含方法和属性，不能包含字段。
3、在接口中的方法/属性不能实现，如：
public interface A{



```csharp
    void Eat();  //这里是定义一个方法
    float Prise { get ; set; } //这里是定义一个属性
```

}
4、一旦某个类实现了接口，就必须实现接口中定义的全部成员。
5、不能直接实例化接口，可以使用接口实现多态。

抽象类与接口的相同点：
（1） 两者都包含可以由子类继承的抽象成员；
（2） 两者都不直接实例化。
抽象类与接口的区别：
（1） 抽象类除拥有抽象成员之外，还可以拥有非抽象成员；而接口所有的成员都是抽象的。
（2） 抽象成员可以是私有的，而接口的成员默认是公有的。
（3） 接口中不能含有构造函数、析构函数、静态成员和常量。
（4） C#只支持单继承，即子类只能继承一个父类，而一个子类却能够实现多个接口。



```csharp
public interface inf1{ //接口1
    void Eat();  //这里是定义一个方法
}

public interface inf2{ //接口2
    float Prise { get ; set; } //这里是定义一个属性 未对属性做特别的限制
}

public class Fruit{ //普通类
    public string name;
}
//Fruit是Apple的父类（如果有父类  位置一定排在第一个）  inf1和inf2是Apple实现的接口  可以有多个接口
public class Apple:Fruit,inf1,inf2{ 
    private float _prise;
    #region inf1 implementation
    public void Eat ()
    {
        Console.WriteLine ("{0}的价格为{1}元一斤",this.name,this.Prise);//这里的name用到了父类的name字段
    }
    #endregion
    #region inf2 implementation
    public float Prise {
        get {
            return _prise;
        }
        set {
            _prise = value;
        }
    }
    #endregion
    public Apple (string name,float prise){
        this.name = name;
        this._prise = prise;
    }
}

    class MainClass
    {
         public static void Main (string[] args)
         {
              //inf1是接口1 可以接收实现接口1的类的对象
              inf1 f1 = new Apple ("苹果",10f);
              f1.Eat ();
          }
    }
```

运行结果显示：



![img](https://upload-images.jianshu.io/upload_images/5276281-1eb063020937cc6c.png?imageMogr2/auto-orient/strip|imageView2/2/w/585/format/webp)



