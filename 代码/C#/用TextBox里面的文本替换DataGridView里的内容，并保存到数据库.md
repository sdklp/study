 ## 用TextBox里面的文本替换DataGridView里的内容，并保存到数据库
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
using System.Data.OleDb;
using System.Configuration;

namespace test
{
    public partial class Form1 : Form
    {
        OleDbCommandBuilder cb;
        OleDbDataAdapter da;
        DataTable dt;        
        OleDbConnection conn;
        public Form1()
        {
            InitializeComponent();  
    }

    private void btnRefresh_Click(object sender, EventArgs e)
    {
        conn = new OleDbConnection(@"Provider=Microsoft.ACE.OLEDB.12.0;Data Source=D:\test\db.accdb") ;
        //用App.config里面的字符串不能更新到数据库，只能更新到DataTable或DataSet
        da = new OleDbDataAdapter("select * from tblTest",conn);
        dt = new DataTable();
        da.Fill(dt);
        dataGridView1.DataSource = dt;
    }

    private void btnSave_Click(object sender, EventArgs e)
    {
        cb = new OleDbCommandBuilder(da);
        da.Update(dt);
    }

    private void btnEdit_Click(object sender, EventArgs e)
    {
        
        dataGridView1.Rows[0].Cells[1].Value = textBox1.Text;
        this.BindingContext[dt].EndCurrentEdit//不加这行，不能更新到数据库，只能更新到DataTable或DataSet
       
     }
  }
}
```

​    


​      



