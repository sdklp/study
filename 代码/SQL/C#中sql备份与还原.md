## C#中sql备份与还原-多极客编程

[2011-11-13](https://www.moregeek.xyz/i/113600262275) tag: [sql](https://www.moregeek.xyz/tag/sql) tag: [string](https://www.moregeek.xyz/tag/string) tag: [c#](https://www.moregeek.xyz/tag/c%23) tag: [database](https://www.moregeek.xyz/tag/database) tag: [textbox](https://www.moregeek.xyz/tag/textbox)

备份：

try
      {
        string strg = Application.StartupPath.ToString();
        strg = strg.Substring(0, strg.LastIndexOf("\\"));
        strg += @"\Data";
        string sqltxt = @"BACKUP DATABASE db_MrCy TO Disk='" + strg + "\\" + txtpath.Text +".bak"+ "'";
        SqlConnection conn = BaseClass.DBConn.CyCon();
        conn.Open();
        SqlCommand cmd = new SqlCommand(sqltxt, conn);
        cmd.ExecuteNonQuery();
        conn.Close();
        if (MessageBox.Show("备份成功", "提示", MessageBoxButtons.OK, MessageBoxIcon.Exclamation) == DialogResult.OK)
        {
          this.Close();
        }
      }
      catch (Exception ex)
      {
        MessageBox.Show(ex.Message.ToString());
      }

 

还原：

try
      {
        string strg = Application.StartupPath.ToString();
        strg = strg.Substring(0, strg.LastIndexOf("\\"));
        strg += @"\Data";
        textBox1.Text = strg + "\\" + "mrcy.bak";
        string str = "use master restore database db_MrCy from Disk='"+textBox1.Text.Trim()+"'";
        SqlConnection conn = BaseClass.DBConn.CyCon();
        conn.Open();
        SqlCommand cmd = new SqlCommand(str,conn);
        cmd.ExecuteNonQuery();
        if (MessageBox.Show("恢复成功", "提示", MessageBoxButtons.OK, MessageBoxIcon.Exclamation) == DialogResult.OK)
        {
          this.Close();
        }
      }
      catch (Exception ex)
      {
        MessageBox.Show(ex.Message.ToString());
      }