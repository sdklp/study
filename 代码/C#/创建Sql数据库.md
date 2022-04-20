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

namespace 设备管理软件
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            string connStr = @"server=PROTEC-PC\SQLEXPRESS;database=master;integrated security=SSPI";
            SqlConnection conn = null;
            try
            {
                conn = new SqlConnection(connStr);
                conn.Open();
                SqlCommand cmd = new SqlCommand("create database test1", conn);
                cmd.ExecuteNonQuery();
    
                MessageBox.Show("数据库已打开");
            }
            catch(Exception ex)
            {
                MessageBox.Show(ex.Message);
            }
            finally
            {
                conn.Close();
            }
         
        }
    }
}