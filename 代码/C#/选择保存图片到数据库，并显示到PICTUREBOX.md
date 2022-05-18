

```c#
        private void btnSelect_Click(object sender, EventArgs e)
        {
            picUrl = null;
            imgName = "";
            openFileDialog1.Filter = "Image Files(*.BMP; *.JPG; *.GIF;*.PNG;*.JPEG)| *.BMP; *.JPG; *.GIF;*.PNG;*.JPEG | All files(*.*) | *.*";
            openFileDialog1.ShowDialog();
       //MessageBox.Show(openFileDialog1.SafeFileName);
        if (openFileDialog1.FileName != "openFileDialog1")
        {
            picUrl = openFileDialog1.FileName;
            imgName = openFileDialog1.SafeFileName;
            ptbPic.Image = Image.FromFile(openFileDialog1.FileName);

        }

    }
    private void SavePic()
    {
        if (picUrl == null)
        {
            return;
        }
        FileInfo fi = new FileInfo(picUrl);
        FileStream fs = fi.OpenRead();
        byte[] bytes = new byte[fs.Length];
        fs.Read(bytes, 0, Convert.ToInt32(fs.Length));
        OleDbParameter spFile = new OleDbParameter("@file", OleDbType.Binary);
        spFile.Value = bytes;
        DBHelper.ExecuteNonQuery($"update tblpj set picName='{imgName}', pic=@file where id={cId}", CommandType.Text, spFile);
        fs.Close();

    }
//显示配件图片
            if (string.IsNullOrEmpty(dt.Rows[0]["pic"].ToString()) == false)
            {
                Byte[] bytes = new Byte[0];
                bytes = (Byte[])(dt.Rows[0]["pic"]);
                MemoryStream ms = new MemoryStream(bytes);
                ptbPic.Image = Image.FromStream(ms);
            }
```