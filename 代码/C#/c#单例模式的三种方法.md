

```c#
 class User
 {       
     //1.私用化构造方法
     private User() { }
    //方法一：
    //2.提供一个私有的静态的当前对象，程序加载就进行实例化
    //
    private static User instance = new User();
    //3.提供一个public权限的方法，用来返回一个当前类的对象
    public static User CurrentUser1()
    {
        return instance;
    }
    /*下面以属性方法，下上面功能相同
    public static User CurrentUser
    {
        get { return instance; }
    }
    */
    //方法二：
    
    //2.提供一个私有的静态的当前对象，什么时候需要，什么时候实例化
    
    private static User instance1;
    public static User CurrentUser()
    {
        if (instance == null) instance = new User();
        return instance;
    }
   
    //方法三：
    public readonly static User instance2 = new User();

}
```