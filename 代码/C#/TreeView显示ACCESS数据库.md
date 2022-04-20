![2021-09-23_093617](C:/Users/PROTEC/Desktop/New folder (3)/2021-09-23_093617.png)

![2021-09-23_093600](C:/Users/PROTEC/Desktop/New folder (3)/2021-09-23_093600.png)

using System;
using System.Collections.Generic;
using System.Drawing;
using System.Windows.Forms;
using System.Data;
using System.Data.OleDb;

namespace test
{
    /// <summary>
    /// Description of MainForm.
    /// </summary>
    public partial class MainForm : Form
    {
        public MainForm()
        {
            //
            // The InitializeComponent() call is required for Windows Forms designer support.
            //
            InitializeComponent();
            
            //
            // TODO: Add constructor code after the InitializeComponent() call.
            //
            
        }
        
        public DataTable bind_data(string strsql)
        {
            DataTable dt=new DataTable();
            string constr=@"provider=microsoft.ace.oledb.12.0;data source=C:\Users\konglingping\Desktop\equip123.accdb";
            OleDbConnection con=new OleDbConnection(constr);
            con.Open();
            OleDbCommand cmd=new OleDbCommand(strsql,con);               
            OleDbDataAdapter sda=new OleDbDataAdapter(cmd);
//            cmd.CommandText=CommandType.Text;
//            cmd.Connection=con;
//            sda.SelectCommand=cmd;
            sda.Fill(dt);
            return dt;               
               
        }
        public void ptreeview(DataTable dtparent,int parentid,TreeNode tree1)
        {
            foreach(DataRow row in dtparent.Rows)
            {
                TreeNode child=new TreeNode();
                child.Text=row["name1"].ToString();
                child.Tag=row["id"];
                if(parentid==0)
                {
                     treeView1.Nodes.Add(child);
                     DataTable dtchild=bind_data("select id,name1 from name1 where id_sport="+ child.Tag);
                    ptreeview(dtchild,Convert.ToInt32(child.Tag),child);
                }
                
                else
                {
                    tree1.Nodes.Add(child);
                }
                    
            }
        }
        void MainFormLoad(object sender, EventArgs e)
        {    
            DataTable dt =new DataTable();
            dt=bind_data("select id,name1 from sports1");
            this.ptreeview(dt,0,null);
        }
        
    }
}