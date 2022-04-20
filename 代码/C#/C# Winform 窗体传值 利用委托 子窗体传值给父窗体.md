# C# Winform 窗体传值 利用委托 子窗体传值给父窗体

常用的Winform窗体传值有两种方式。

**1.更改Form.designer.cs文件，将控件的设为Public，供子窗体访问。**

　　在designer.cs文件的最后，找到你的控件声明。

```c#
private System.Windows.Forms.TextBox textBox1;
```

　　更改Private为public，保存即可。

**2.利用委托进行窗体传值。**

　　父窗体：Form1

　　子窗体：Form2

　　点击Form1，弹出Form2，点击按钮返回值给Form1

　

　　**首先在Form2中定义委托和事件：**

```c#
     //声明委托 和 事件
    public delegate void TransfDelegate(String value);
    public partial class Form2 : Form
    {
        public Form2()
        {
            InitializeComponent();
        }

        public event TransfDelegate TransfEvent; 
        private void button1_Click(object sender, EventArgs e)
        {
            //触发事件
            TransfEvent(textBox1.Text);
            this.Close();
        }
    }
```

　　**然后在Form1中进行调用：**

```c#
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
            
        }

        private void button1_Click(object sender, EventArgs e)
        {
            Form2 frm = new Form2();
            //注册事件
            frm.TransfEvent += frm_TransfEvent;
            frm.ShowDialog();
        }

        //事件处理方法
        void frm_TransfEvent(string value)
        {
            textBox1.Text = value;
        }
    }
```