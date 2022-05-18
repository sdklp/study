using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Data.SqlClient;
using System.Data.OleDb;
namespace WindowsFormsApp2
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
        SqlDataAdapter sda;
        
        SqlCommandBuilder cb;
        DataTable dt = new DataTable();
        private void button1_Click(object sender, EventArgs e)
        {


            //this.Column2.CellType = DataGridViewComboBoxCell;
            dataGridView1.Columns.Remove("Column2");
            DataGridViewComboBoxColumn combo = new DataGridViewComboBoxColumn();
    
            combo.DataSource = DBHelper.ExecuteDataTable1("select sbname from tblsbname");
            combo.DisplayIndex = 1;
            combo.Name = "Column2";
            combo.HeaderText = "sbname";
            combo.DataPropertyName = "sbname";//資料行名稱
            combo.DisplayMember = "sbname";//顯示清單選項內容
            //combo.ValueMember = "sbxh"; //清單選項對應的值


            dataGridView1.Columns.Insert(1, combo);
            
        }




        private void Form1_Load(object sender, EventArgs e)
        {
            
            sda = DBHelper.ExecuteDataAdapter("select id,sbname from sb");
            sda.Fill(dt);
            dataGridView1.DataSource = dt;
        }
    
        private void button2_Click(object sender, EventArgs e)
        {
            cb = new SqlCommandBuilder(sda);
            sda.Update(dt);
        }


​      

        private void button3_Click(object sender, EventArgs e)
        {
            Form1_Load(null,null);
        }
    }
}