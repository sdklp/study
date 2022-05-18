# C#泛型和泛型约束

#### **一、泛型：**

　　　　所谓泛型，即通过参数化类型来实现在同一份代码上操作多种数据类型。泛型编程是一种编程范式，它利用“参数化类型”将类型抽象化，从而实现更为灵活的复用。


　　　　 在定义泛型类时，可以对客户端代码能够在实例化类时用于类型参数的类型种类施加限制。如果客户端代码尝试使用某个约束所不允许的类型来实例化类，则会产生编译时错误。这些限制称为约束。约束是使用 where 上下文关键字指定的。

　　　　下表列出了五种类型的约束：

|     约束      |                             说明                             |
| :-----------: | :----------------------------------------------------------: |
|   T：struct   | 类型参数必须是值类型。可以指定除 Nullable 以外的任何值类型。 |
|   T：class    |  类型参数必须是引用类型，包括任何类、接口、委托或数组类型。  |
|   T：new()    | 类型参数必须具有无参数的公共构造函数。当与其他约束一起使用时，new() 约束必须最后指定。 |
|  T：<基类名>  |         类型参数必须是指定的基类或派生自指定的基类。         |
| T：<接口名称> | 类型参数必须是指定的接口或实现指定的接口。可以指定多个接口约束。约束接口也可以是泛型的。 |
|     T：U      | 为 T 提供的类型参数必须是为 U 提供的参数或派生自为 U 提供的参数。这称为裸类型约束. |

#### **一.派生约束**

**1.常见的**

```
 public class MyClass5<T> where T :IComparable { }
2.约束放在类的实际派生之后
public class B { }          
public class MyClass6<T> : B where T : IComparable { }
 3.可以继承一个基类和多个接口，且基类在接口前面
public class B { }
public class MyClass7<T> where T : B, IComparable, ICloneable { }
```

#### **二.构造函数约束**

**1.常见的**

```
public class MyClass8<T> where T :  new() { }
2.可以将构造函数约束和派生约束组合起来,前提是构造函数约束出现在约束列表的最后
　　　　public class MyClass8<T> where T : IComparable, new() { }
```

#### **三.值约束**

**1.常见的**

```
public class MyClass9<T> where T : struct { }
```

**2.与接口约束同时使用，在最前面(不能与基类约束,构造函数约束一起使用)**

```
　public class MyClass11<T> where T : struct, IComparable { }
```

#### **四.引用约束**

**1.常见的**　

```
public class MyClass10<T> where T : class { }
```

#### **五.多个泛型参数**

```
public class MyClass12<T, U> where T : IComparable  where U : class { }
```

#### **六.继承和泛型**

```
　　　　public class B<T>{ }
```

\1. 在从泛型基类派生时,可以提供类型实参,而不是基类泛型参数

```
  　　  public class SubClass11 : B<int>
   　　 { }
```

2.如果子类是泛型,而非具体的类型实参,则可以使用子类泛型参数作为泛型基类的指定类型

```
 　　   public class SubClass12<R> : B<R>
  　　  { }
```

**3.在子类重复基类的约束(在使用子类泛型参数时,必须在子类级别重复在基类级别规定的任何约束)**

```
public class B<T> where T : ISomeInterface { } 
public class SubClass2<T> : B<T> where T : ISomeInterface { }
4.构造函数约束
public class B<T> where T : new() 
 { 
      public T SomeMethod()
{  return new T();  }   　　  
}    　　 
public class SubClass3<T> : B<T> where T : new(){ }
```