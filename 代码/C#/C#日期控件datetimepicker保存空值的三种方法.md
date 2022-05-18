# C#日期控件datetimepicker保存空值的三种方法

**方法一（推荐）：**

设置datetimepicker的属性ShowCheckBox为true

在窗口初始化时候，添加代码this.datetimepicker1.Checked = false;

保存日期值入库的时候，就可以根据if(this.datetimepicker1.Checked ==false)，保存空值。

**方法二：**

在窗口初始化函数中添加：

复制代码 代码如下:


this.dateTimePicker1.Format=DateTimePickerFormat.Custom;



this.dateTimePicker1.CustomFormat="  ";


在日期改变事件里写：

复制代码 代码如下:


private void dateTimePicker1_ValueChanged(object sender, System.EventArgs e)



{

this.dateTimePicker1.Format=DateTimePickerFormat.Long;

this.dateTimePicker1.CustomFormat=null;

}


这样就实现了，在程序初始化时dateTimePicker显示为空



但是，这种写法有个问题，保存入库的时候，还要加一个判断if(this.dateTimePicker1.Text.toString()==""),保存空值；else 保存this.dateTimePicker1.value。

这种写法遇到个bug，一直没有解决，就是日期控件默认是空的，在第一次选择一个日期后必须失去焦点才能选择新的日期，不知道什么原因？

**方法三：**

在日期控件上面覆盖一个文本框，然后初始化时候文本框是空值，每次日期选择之后将值附在文本框里面。



### 其它方法

 #region  日期控件初始为空值处理

        /// <summary>
        /// 初始化日期时间控件
        /// </summary>
        /// <param name="dtp"></param>
        public static void InitDateTimePicker(DateTimePicker dtp)
        {
            dtp.Format = DateTimePickerFormat.Custom;
            dtp.CustomFormat = " ";  //必须设置成" "
            dtp.ValueChanged -= DateTimePicker_ValueChanged;
            dtp.ValueChanged += DateTimePicker_ValueChanged;
            dtp.KeyPress -= DateTimePicker_KeyPress;
            dtp.KeyPress += DateTimePicker_KeyPress;
        }
    
        public static void DateTimePicker_ValueChanged(object sender, EventArgs e)
        {
            DateTimePicker dtp = (DateTimePicker)sender;
            dtp.Format = DateTimePickerFormat.Short;
            dtp.CustomFormat = null; //null;
            dtp.Checked = false;// 解决BUG ：防止日期控件不能选择相同日期的 --- 要放置在设置格式之后


        }
    
        public static void DateTimePicker_KeyPress(object sender, KeyPressEventArgs e)
        {
            if (e.KeyChar == (char)8)  // backspace左删除键
            {
                DateTimePicker dtp = (DateTimePicker)sender;
                dtp.Format = DateTimePickerFormat.Custom;
                dtp.CustomFormat = " ";
            }
        }
        #endregion