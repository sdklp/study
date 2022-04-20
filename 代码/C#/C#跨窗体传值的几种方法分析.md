# C#跨窗体传值的几种方法分析（很详细）

创建一个Winform窗体应用程序项目，然后添加一个Form2窗体。

![img](https://images2015.cnblogs.com/blog/1002191/201611/1002191-20161115211537076-760674385.png)

在Form1和Form2中各添加一个textBox和button：

![img](https://images2015.cnblogs.com/blog/1002191/201611/1002191-20161114215246513-1329396172.png)

![img](https://images2015.cnblogs.com/blog/1002191/201611/1002191-20161114215254701-1156162563.png)

 

单击Form1中的button1，弹出Form2，然后要做的就是在Form1中的textBox1和Form2中的textBox2中传值。

 

为了方便起见，将Form1称作**父窗体**，将Form2称作**子窗体**。

 

相对来说，将父窗体的值传到子窗体较为容易实现。下面分别进行说明。

**一、父窗体传值给子窗体。**

 

**方法1：通过Form类构造方法的重载传参。**

Form1类中代码：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form1 : Form
 7     {
 8         public Form1()
 9         {
10             InitializeComponent();
11         }
12 
13         private void button1_Click(object sender, EventArgs e)
14         {
15             Form2 f2 = new Form2(textBox1.Text);
16             f2.ShowDialog();
17         }  
18     }
19 }
```



Form2类中代码：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form2 : Form
 7     {
 8         public Form2()
 9         {
10             InitializeComponent();
11         }
12 
13         public Form2(string str)
14         {        
15             InitializeComponent();
16             textBox2.Text = str;//这句必须放在InitializeComponent();的后面，否则会引起“空引用异常”
17         }
18 
19     }
20 }
```



![img](https://images2015.cnblogs.com/blog/1002191/201611/1002191-20161114231251060-275445134.png)

 

**方法2：通过外部可访问的中间变量传参。**

先看一串无法实现目的的代码。

Form1中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form1 : Form
 7     {
 8         public Form1()
 9         {
10             InitializeComponent();
11         }
12 
13         public  string str;//public类型的实例字段
14 
15         private void button1_Click(object sender, EventArgs e)
16         {
17             Form2 f2 = new Form2();
18             str = textBox1.Text;//注意，这句不能放在f2.ShowDialog();的后面，否则会先执行textBox2.Text = f1.str;再执行str = textBox1.Text;
19             f2.ShowDialog();
20         }  
21     }
22 }
```



Form2中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form2 : Form
 7     {
 8         public Form2()
 9         {
10             InitializeComponent();
11         }
12 
13         private void Form2_Load(object sender, EventArgs e)
14         {
15             Form1 f1 = new Form1();
16             textBox2.Text = f1.str;
17         }
18 
19     }
20 }
```



结果值没有传过来：

![img](https://images2015.cnblogs.com/blog/1002191/201611/1002191-20161114231228310-1066070049.png)

这是因为：Form1 f1 = new Form1();相当于新建了一个对象，这个新对象的str初始值是null（只有该对象的button1被click的时候才会改变str的值）。textBox2.Text = f1.str;其实就是将null赋给了textBox2.Text。

调试如图：

![img](https://images2015.cnblogs.com/blog/1002191/201611/1002191-20161114232325967-1952553077.png)

 

static变量可以认为是全局变量，static变量被所有对象所共有，也可以被所有对象所改变，将相关代码修改如下即可：



```
public static string str;//public类型的实例字段


private void Form2_Load(object sender, EventArgs e)
        { 
            textBox2.Text = Form1.str;
        }
```



 

它有一种变式：

Form1中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form1 : Form
 7     {
 8         public Form1()
 9         {
10             InitializeComponent();
11         }
12 
13         private void button1_Click(object sender, EventArgs e)
14         {
15             Form2 f2 = new Form2();
16             f2.str = textBox1.Text;
17             f2.ShowDialog();
18 
19         }
20     }
21 }
```



Form2中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form2 : Form
 7     {
 8         public Form2()
 9         {
10             InitializeComponent();
11         }
12 
13         public string str;//这里str是在Form2类中定义的，并且不需要static关键字
14 
15         private void Form2_Load(object sender, EventArgs e)
16         {
17             textBox2.Text = str;
18         }
19 
20     }
21 }
```



分析： f2.str = textBox1.Text;将textBox1的值赋给f2.str，由于f2.ShowDialog();跳出来的刚好是这个对象，因此这个对象的str值不为空。最终通过textBox2.Text = str;将值传过去了。跟上一个例子的不同之处是：**str声明的位置不一样，一个是在Form1类中，一个是在Form2类中。**

 

**一、子窗体传值给父窗体。**

 

**方法1：通过Owner设置窗口归属来传值。**

**① 通过Controls**

Form1中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form1 : Form
 7     {
 8         public Form1()
 9         {
10             InitializeComponent();
11         }
12 
13         private void button1_Click(object sender, EventArgs e)
14         {
15             Form2 f2 = new Form2();
16             f2.ShowDialog(this);//这个this必不可少（将窗体显示为具有指定所有者：窗体f2的所有者是Form1类当前的对象）
17         }
18     }
19 }
```



Form2中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form2 : Form
 7     {
 8         public Form2()
 9         {
10             InitializeComponent();
11         }
12 
13         private void button2_Click(object sender, EventArgs e)
14         {
15             Form1 f1 = (Form1) this.Owner;//将本窗体的拥有者强制设为Form1类的实例f1
16             f1.Controls["textBox1"].Text = textBox2.Text;
17         }
18 
19     }
20 }
```



![img](https://images2015.cnblogs.com/blog/1002191/201611/1002191-20161115001512107-1466488096.png)

 

**② 通过在控件所在类中增加改变控件属性的方法**

或者通过在Form1中添加一个ChangeText方法也行：

```
public void ChangeText(string str)
        {
            textBox1.Text = str;
        }
```

Form2中代码相应改变为：

```
private void button2_Click(object sender, EventArgs e)
        {
            Form1 f1 = (Form1) this.Owner;//将本窗体的拥有者强制设为Form1类的实例f1
            f1.ChangeText(textBox2.Text);
        }
```

 

注意：如果不用Owner，仅仅想用下面的代码实现，是没可能的。



```
//Form1中：
using System;
using System.Windows.Forms;

namespace WindowsForms跨窗体传值大全
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            Form2 f2 = new Form2();
            f2.ShowDialog();
        }

        public void ChangeText(string str)
        {
            textBox1.Text = str;
        }
    }
}


//Form2中：
using System;
using System.Windows.Forms;

namespace WindowsForms跨窗体传值大全
{
    public partial class Form2 : Form
    {
        public Form2()
        {
            InitializeComponent();
        }

        private void button2_Click(object sender, EventArgs e)
        {
            Form1 f1 =new Form1();
            f1.ChangeText(textBox2.Text);
        }

    }
}

//这种代码无法实现目的
```



 

**③ 通过将外界不可访问的控件“属性”封装成可以访问的属性。**

Form1中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form1 : Form
 7     {
 8         public Form1()
 9         {
10             InitializeComponent();
11         }
12 
13         private void button1_Click(object sender, EventArgs e)
14         {
15             Form2 f2 = new Form2();
16             f2.ShowDialog(this);
17         }
18 
19         public string TextBox1Value
20         {
21             set { textBox1.Text = value; }
22             get { return textBox1.Text; }
23         }
24 
25     }
26 }
```



Form2中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form2 : Form
 7     {
 8         public Form2()
 9         {
10             InitializeComponent();
11         }
12 
13         private void button2_Click(object sender, EventArgs e)
14         {
15             Form1 f1 =(Form1)this.Owner;
16             f1.TextBox1Value = textBox2.Text;
17         }
18 
19     }
20 }
```



 

**方法2：通过提供外部可访问的属性和DialogResult的状态来传值（这个例子同时实现了父传子、子传父）。**

Form1中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form1 : Form
 7     {
 8         public Form1()
 9         {
10             InitializeComponent();
11         }
12 
13         private void button1_Click(object sender, EventArgs e)
14         {          
15             Form2 f2 = new Form2();
16             f2.TextBox2Value = textBox1.Text;//顺便把父窗体的值先传给子窗体
17 
18             //这一行不需要再写f2.ShowDialog();或者f2.Show();，否则f2会弹出来两次
19 
20             if (f2.ShowDialog() == DialogResult.OK)//这样的语句是合法的：DialogResult f = f2.ShowDialog();
21             {
22                 textBox1.Text = f2.TextBox2Value;
23                 //f2.Close();//这一句写或者不写，f2都会关闭
24             }  
25         }    
26     }
27 }
```



Form2中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form2 : Form
 7     {
 8         public Form2()
 9         {
10             InitializeComponent();
11         }
12 
13         public string TextBox2Value //将外部不可访问的textBox2.Text封装成属性TextBox1Value
14         {
15             set { textBox2.Text = value; }
16             get { return textBox2.Text; }
17         }
18 
19         private void button2_Click(object sender, EventArgs e)
20         {
21             this.DialogResult = DialogResult.OK;//这里的DialogResult是Form2类对象的属性
22         }
23 
24     }
25 }
```



 

**方法3：通过委托事件来传值。（这种方式可以实现不同控件数据实时联动的复杂功能）**

Form1中：



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public partial class Form1 : Form
 7     {
 8         public Form1()
 9         {
10             InitializeComponent();
11         }
12 
13         private void button1_Click(object sender, EventArgs e)
14         {
15             Form2 f2 = new Form2();
16             f2.ChangeText += new ChangeTextHandler(Change_Text);//将事件和处理方法绑在一起，这句话必须放在f2.ShowDialog();前面
17             f2.ShowDialog();            
18         }
19 
20        public void Change_Text(string str)
21         {
22             textBox1.Text = str;
23         }  
24     }
25 }
```



Form2中：

 



```
 1 using System;
 2 using System.Windows.Forms;
 3 
 4 namespace WindowsForms跨窗体传值大全
 5 {
 6     public delegate void ChangeTextHandler(string str);  //定义委托
 7 
 8     public partial class Form2 : Form
 9     {
10         public Form2()
11         {
12             InitializeComponent();
13         }
14 
15         public event ChangeTextHandler ChangeText;  //定义事件
16 
17         private void button2_Click(object sender, EventArgs e)
18         {
19             if (ChangeText != null)//判断事件是否为空
20             {
21                 ChangeText(textBox2.Text);//执行委托实例  
22                 this.Close();
23             }           
24         }    
25     }
26 }
```



 可以用.NET提供的Action<>泛型委托和lambda表达式简化上面这个例子：



```
 1 //Form1中：
 2 using System;
 3 using System.Windows.Forms;
 4 
 5 namespace WindowsForms跨窗体传值大全
 6 {
 7     public partial class Form1 : Form
 8     {
 9         public Form1()
10         {
11             InitializeComponent();
12         }
13 
14         private void button1_Click(object sender, EventArgs e)
15         {
16             Form2 f2 = new Form2();
17             f2.ChangeText = (str) => textBox1.Text = str;//用lambda表达式实现，这句话必须放在f2.ShowDialog();前面
18             f2.ShowDialog();            
19         }
20     }
21 }
22 
23 
24 //Form2中：
25 using System;
26 using System.Windows.Forms;
27 
28 namespace WindowsForms跨窗体传值大全
29 {  
30     public partial class Form2 : Form
31     {
32         public Form2()
33         {
34             InitializeComponent();
35         }
36 
37         public Action<string> ChangeText;//之前的定义委托和定义事件由这一句话代替
38 
39         private void button2_Click(object sender, EventArgs e)
40         {
41             if (ChangeText != null)//判断事件是否为空
42             {
43                 ChangeText(textBox2.Text);//执行委托实例  
44                 this.Close();
45             }           
46         }    
47     }
48 }
```

