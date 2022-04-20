### 1、定义一个接口IchildForm

并设置为public属性（public interface IchildForm）

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Test
{
    public interface IchildForm
    {
        void setText(string txt);
    }
}
```

### 2、新建窗口Form2,

并让其继承接口IchildForm（public partial class Form2 : Form,IchildForm），并重写IchildForm中的setText()方法：[public void setText(string str){this.textBox1.Text = str; }]

![image-20211008110112160](C:/Users/PROTEC/AppData/Roaming/Typora/typora-user-images/image-20211008110112160.png)

```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace Test
{
    public partial class Form2 : Form,IchildForm
    {
        public Form2()
        {
            InitializeComponent();
        }
        public void setText(string str)
        {
            this.textBox1.Text = str; 
        }
    }
}
```

###　3、新建Form1窗口

并定义一个泛性（public List<IchildForm> iChildFormList { get; set; }，然后用setText(this.textBox1.Text);向其它窗体发送消息。

![image-20211008110034573](C:/Users/PROTEC/AppData/Roaming/Typora/typora-user-images/image-20211008110034573.png)

```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace Test
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
        public List<IchildForm> iChildFormList { get; set; }
        private void button1_Click(object sender, EventArgs e)
        {
            if (iChildFormList == null)
            {
                return;
            }
            foreach (var item in iChildFormList)
            {
                item.setText(this.textBox1.Text);
            }
        }

    }

}
```

### 4、在主窗体中注册(MainForm)，并打开窗口

![image-20211008110215018](C:/Users/PROTEC/AppData/Roaming/Typora/typora-user-images/image-20211008110215018.png)

```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace Test
{
    public partial class MainForm : Form
    {
        public MainForm()
        {
            InitializeComponent();
        }

        private void MainForm_Load(object sender, EventArgs e)
        {
            Form1 form1 = new Form1();
            Form2 form2 = new Form2();
            form1.iChildFormList = new List<IchildForm>();
            form1.iChildFormList.Add(form2);
            form1.Show();
            form2.Show();
        }
    }

}
```

### 5、运行结果如下

![image-20211008111021539](C:/Users/PROTEC/AppData/Roaming/Typora/typora-user-images/image-20211008111021539.png)
