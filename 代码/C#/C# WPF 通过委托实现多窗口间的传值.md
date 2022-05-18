## C# WPF 通过委托实现多窗口间的传值

在使用WPF开发的时候就不免会遇到需要两个窗口间进行传值操作，当然多窗口间传值的方法有很多种，本文介绍的是使用委托实现多窗口间的传值。

在上代码之前呢，先简单介绍一下什么是C#中的委托（如果只想了解如何传值可以略过这部分）在网络上有很多对于委托的介绍和讲解，经过我的学习和总结加上了一点我自己的理解，我认为委托是一种类似于C语言的指针，但是它指向的是方法而不是变量。如果把委托看作一个变量，那么这个变量里存着的就是你目标方法的地址，**调用委托约等于调用你的目标方法**。（个人理解欢迎指正交流）

 

以下正文：

实现窗口间的相互传值，先创建两个窗口，先上代码主窗口代码：

MainWindow.xaml

```
<Grid>
 　<TextBox Name="MainWindowTextBox" HorizontalAlignment="Left" Height="23" Margin="10,61,0,0" TextWrapping="Wrap" Text="空" VerticalAlignment="Top" Width="297"/>
 　<Button Content="打开新窗口" HorizontalAlignment="Left" Margin="10,130,0,0" VerticalAlignment="Top" Width="297" Click="ButtonBase_OnClick"/>
</Grid>
```

 MainWindow.xaml.cs



```
 1 public void GetValue(string value1, TextBox value2)
 2 　　{
 3 　　　　MainWindowTextBox.Text = value1;
 4 　　}
 5 
 6 private void ButtonBase_OnClick(object sender, RoutedEventArgs e)
 7 　　{
 8 　　　　Window1 newWindow1 = new Window1();
 9 　　　　newWindow1.getTextHandler = GetValue;          //将方法赋给委托对象
10 　　　　newWindow1.ShowDialog();
11 
12　　 }
```



 

效果图如下：

![img](https://images2018.cnblogs.com/blog/1223717/201808/1223717-20180830133310923-69425951.png)

第二个窗口Window1代码：

Window1.xaml

```
<Grid>
    <TextBox Name="Window1TextBox" HorizontalAlignment="Left" Height="23" Margin="84,73,0,0" TextWrapping="Wrap" Text="" VerticalAlignment="Top" Width="120"/>
    <Button Content="传值" HorizontalAlignment="Left" Margin="84,125,0,0" VerticalAlignment="Top" Width="120" Click="ButtonBase_OnClick"/>
</Grid>
```

 Window1.xaml.cs



```
1 public delegate void GetTextHandler(string value1, TextBox value2);  //声明委托
2 public GetTextHandler getTextHandler;                                //委托对象
3 
4 private void ButtonBase_OnClick(object sender, RoutedEventArgs e)
5 　　{
6 　　　　getTextHandler(Window1TextBox.Text, Window1TextBox);
7 　　}
```



效果图：

![img](https://images2018.cnblogs.com/blog/1223717/201808/1223717-20180830134155893-1453387894.png)

实现效果当运行程序后，点击打开新窗口按钮后，会打开Window1窗口，在Window1窗口的Textbox中输入内容，点击传值，你所输入的内容就会传到主窗口，通过委托的事件将主窗口中的Textbox控件的内容更改为你传过去的值。效果如下：

![img](https://images2018.cnblogs.com/blog/1223717/201808/1223717-20180830134727381-777009062.png)

![img](https://images2018.cnblogs.com/blog/1223717/201808/1223717-20180830134740821-1767025735.png)

现在就已经实现了窗口间传值的操作了。接下来我会简单介绍一下以上代码的实现方法和一些自己的理解，如果不感兴趣或者已经会使用委托进行多窗口间的传值了，后面的部分可以略过。 

前台代码在此就先不介绍了哈，在MainWindow.xaml.cs文件中

```
public void GetValue(string value1, TextBox value2)
```

此方法即为委托的目标方法，此方法返回值为空，也可以设置其返回值，当使用委托时也会收到目标方法的返回值。再有就是此方法接收两个参数，一个是字符串一个是TextBox，第二个参数倒是没什么实际含义，只是为了说明这里传递的变量可以多个，也可以是其它object类型。

 

```
newWindow1.getTextHandler = GetValue;          //将方法赋给委托对象
```

将方法赋给委托对象，可以理解为把他们两个绑定在一起的getTextHandler这个委托对应的目标方法就是GetValue。

在Windo1.xaml.cs中：

```
public delegate void GetTextHandler(string value1, TextBox value2);  //声明委托
public GetTextHandler getTextHandler;                                //委托对象
```

delegate是声明委托的关键字，这里的返回值为空，若目标方法是有返回值的，在这里将返回值写成同种类型即可，接收的两个变量类型也要和目标方法一致。

接下来就是定义委托对象，大写的GetTextHandler是委托，而小写的getTextHandler是对象，在使用该委托时候使用的也是小写的getTextHandler使用方法：

```
getTextHandler(Window1TextBox.Text, Window1TextBox);
```