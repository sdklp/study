

我们在程序中经常会用到MessageBox，MessageBox.Show()共有21中重载方法。现将其常见用法总结如下：



### 1.最简单的，只显示提示信息

```
MessageBox.Show("Hello~~~~");
```

![img](https://images0.cnblogs.com/blog/33509/201306/03233847-7c071a397912491097b746f8ef60ff13.png)



### 2. 可以给消息框加上标题。

```
MessageBox.Show("There are something wrong!","ERROR");
```

![img](https://images0.cnblogs.com/blog/33509/201306/03233856-0895087e44404b0fbd206ae4a830a2a7.png)



### 3. “确定”和“取消”

询问是否删除时会用到这个。

```
if(MessageBox.Show("Delete this user?","Confirm Message",MessageBoxButtons.OKCancel) ==DialogResult.OK)
{
    //delete
}
```

![img](https://images0.cnblogs.com/blog/33509/201306/03234011-f760f213f3524b44a3487744936de2ec.png)



### 4. 给MessageBox加上一个Icon，.net提供常见的Icon共选择。

```
if (MessageBox.Show("Delete this user?", "Confirm Message", MessageBoxButtons.OKCancel,MessageBoxIcon.Question) == DialogResult.OK)
{
    //delete
}
```

![img](https://images0.cnblogs.com/blog/33509/201306/03234019-ad2e3f3c2e2642ef88a907ea667a7806.png)



### 5. 可以改变MessageBox的默认焦点

```
if (MessageBox.Show("Delete this user?", "Confirm Message", MessageBoxButtons.OKCancel, MessageBoxIcon.Question,MessageBoxDefaultButton.Button2) == DialogResult.OK)
{
   //delete
}
```

![img](https://images0.cnblogs.com/blog/33509/201306/03234026-b006a0b1893a4e29a55eb4429889b90c.png)



### 6. 反向显示：

```
f (MessageBox.Show("Delete this user?", "Confirm Message", MessageBoxButtons.OKCancel, MessageBoxIcon.Question,MessageBoxDefaultButton.Button2,MessageBoxOptions.RtlReading) == DialogResult.OK)
{
   //delete
}
```

![img](https://images0.cnblogs.com/blog/33509/201306/03234034-028b29cd8b8e47aba726ce70fa61af41.png)



### 7. 添加Help按钮：

```
if (MessageBox.Show("Delete this user?", "Confirm Message", MessageBoxButtons.OKCancel, MessageBoxIcon.Question, MessageBoxDefaultButton.Button2, MessageBoxOptions.RightAlign,true) == DialogResult.OK)
{
    //delete
}
```

![img](https://images0.cnblogs.com/blog/33509/201306/03234041-fcf06cbe0d9a4f638abd4c141edaa3cd.png)



### 8. 指定帮助文件的路径，点击即可打开该路径下的帮助文件。

```
if (MessageBox.Show("Delete this user?", "Confirm Message", MessageBoxButtons.OKCancel, MessageBoxIcon.Question, MessageBoxDefaultButton.Button1, MessageBoxOptions.RtlReading, @"/folder/file.htm") == DialogResult.OK)
{
   //delete
}
```

![img](https://images0.cnblogs.com/blog/33509/201306/03234058-3d536088a6b742fea1162427da440a57.png)



### 9. HelpNavigator指定常数来指示要显示的帮助文件元素。Find 帮助文件将打开到搜索页。

```
if (MessageBox.Show("Delete this user?", "Confirm Message", MessageBoxButtons.OKCancel, MessageBoxIcon.Question, MessageBoxDefaultButton.Button1, MessageBoxOptions.RtlReading, @"/folder/file.htm", HelpNavigator.Find) == DialogResult.OK)
{
    //delete
}
```

![img](https://images0.cnblogs.com/blog/33509/201306/03234120-83f520f598ac49fc80e6567a58c18215.png)

还有一些用法，不是太实用这里就不一一介绍