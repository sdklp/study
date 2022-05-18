# 新建一个Windows项目

![image-20211003205029881](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20211003205029881.png)

点击一步，创建Windows窗体应用，并命名为DemoUserControl,然后点击创建；

![image-20211003205203627](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20211003205203627.png)

![image-20211003205318813](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20211003205318813.png)

然后在DemoUserControl上点击右键->添加->用户控件，并命名为：ucState.cs，点击添加

![image-20211003205504450](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20211003205504450.png)



![image-20211003205823383](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20211003205823383.png)

在打开的窗口中添加一个Label控件，文本设置为State:     添加一个combobox控件，名字改为：cboState，并调整到适当的大小

![image-20211003210349649](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20211003210349649.png)

右键查看代码，然后在DemoUserControl上点击右键->添加->类，命名为：States.cs

![image-20211003210648337](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20211003210648337.png)

States.cs类中的代码如下

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace DemoUserControl
{
    public class States
    {
        public int ID { get; set; }
        public string Name { get; set; }
    }
}
```

在ucState.cs里写以下代码,然后在DemoUserControl上点击右键，重新生成。

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

namespace DemoUserControl
{
    public partial class ucState : UserControl
    {
        public ucState()
        {
            InitializeComponent();
        }
        public States SelectedState
        {
            get
            {
                return (States)cboState.SelectedItem;
            }
        }
        private void ucState_Load(object sender, EventArgs e)
        {
            List<States> list = new List<States>();
            list.Add(new States() { ID = 1, Name = "Delhi" });
            list.Add(new States() { ID = 2, Name = "Bihar" });
            list.Add(new States() { ID = 3, Name = "Punjab" });
            list.Add(new States() { ID = 4, Name = "Up" });
            cboState.DataSource = list;
            cboState.ValueMember = "ID";
            cboState.DisplayMember = "Name";
        }
    }
}
```

在工具栏里就能看到添加的ucState控件了。

![image-20211003211924994](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20211003211924994.png)

在Form1里，添加ucState控件（ucState1）和一个button控件（名字设置为：btnGetState；文本设置为State）

![image-20211003212359643](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20211003212359643.png)

在Form1的编写按钮（btnGetState）的点击事件

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

namespace DemoUserControl
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void btnGetState_Click(object sender, EventArgs e)
        {
            MessageBox.Show(string.Format("State id={0},name={1}",ucState1.SelectedState.ID,ucState1.SelectedState.Name),"Message",MessageBoxButtons.OK,MessageBoxIcon.Information);
        }
    }

}
```

运行并点击按钮，运行情况如下：

![image-20211003212831915](C:\Users\konglingping\AppData\Roaming\Typora\typora-user-images\image-20211003212831915.png)