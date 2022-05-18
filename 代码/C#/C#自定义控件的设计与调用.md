# C#自定义控件的设计与调用



  在C#下建立自己的控件库，需用到自定义控件的设计与调用。

# 一、自定义控件的设计

  自定义控件，步骤如下：

- 1.点击文件－＞新建项目－＞选择Windows控件库
- 2.编辑控件
- 3.点击生成－＞生成　项目名称 ,完成这一步后会在bin或debug目录下看到"项目名称.dll"文件，这个便是你的控件库了。

  具体实操：

## 1、新建项目

  点击文件－＞新建项目－＞选择Windows控件库。



![img](https://upload-images.jianshu.io/upload_images/17748967-e13a194149d6c3d9.png?imageMogr2/auto-orient/strip|imageView2/2/w/972/format/webp)



项目名称Ky_ColorHatch

## 2、界面布局

  组件面板panel1至panel11，按钮 More。



![img](https://upload-images.jianshu.io/upload_images/17748967-c65842ca68d41a67.png?imageMogr2/auto-orient/strip|imageView2/2/w/374/format/webp)

## 3、定义外部属性

  给控件定义一个外部属性，使我们可以在属性设计视图中对其进行设置。

- 方法是首先声明一个private 变量 private Color hatchColor;
- 然后编写set与get方法,也就是对应的属性赋值与取值的方法



```csharp
        private Color hatchColor = Color.Black;//当前颜色

        #region  自定义属性
        [Description("设置当前颜色")]　//显示在属性设计视图中的描述
        [DefaultValue(typeof(Color), "Black")]
        public Color HatchColor
        {
            get { return hatchColor; }
            set
            {
                hatchColor = value;
                panel1.BackColor = value;
            }
        }
        #endregion
```

## 4、自定义事件

- 创建事件所需的委托
- 定义一个当颜色改变时触发的ColorChanged事件
- 编写事件触发方法
- EventArgs是用户传入的参数，我们这个ColorChangedEventArgs就是继承自这个EventArgs的一个类,目的是用来传递我们选中的颜色给调用方



```csharp
        #region 自定义事件
        //事件所需的委托
        public delegate void ColorChangedEventHandler(object sender, ColorChangedEventArgs e);
        //当颜色改变时触发事件
        public event ColorChangedEventHandler ColorChanged;//定义一个ColorChanged事件

        protected virtual void OnColorChanged(ColorChangedEventArgs e)
        {//事件触发方法
            if (ColorChanged != null)
            {//判断事件是否为空
                ColorChanged(this, e);//触发事件
            }
        }
        #endregion

        /// <summary>
        /// 颜色改变事件数据
        /// </summary>
        public class ColorChangedEventArgs : EventArgs
        {
            private Color color;

            /// <summary>
            /// 颜色改变事件数据
            /// </summary>
            /// <param name="c">改变后的颜色</param>
            public ColorChangedEventArgs(Color c)
            {
                color = c;
            }

            /// <summary>
            /// 获取颜色
            /// </summary>
            public Color GetColor
            {
                get { return color; }
            }

        }
    }
```

## 5、与控件进行关联

- 在panel2至panel11的Click事件中填panel_Click
- 在panel2至panel11的MouseEnter事件中填panel_MouseEnter
- 在panel2至panel11的MouseLeave事件中填panel_MouseLeave
- 双击按钮More的Click事件
- 编写事件触发程序



```csharp
        private void More_Click(object sender, EventArgs e)
        {
            ColorDialog cd = new ColorDialog();
            if (cd.ShowDialog() == DialogResult.OK)
            {
                hatchColor = cd.Color;
                panel1.BackColor = hatchColor;
                OnColorChanged(new ColorChangedEventArgs(hatchColor));//因为颜色改变所以触发事件
            }
        }

        private void panel_Click(object sender, EventArgs e)
        {
            Panel p = sender as Panel;
            if (p != null)
            {
                hatchColor = p.BackColor;
                panel1.BackColor = hatchColor;
                OnColorChanged(new ColorChangedEventArgs(hatchColor));//因为颜色改变所以触发事件
            }
        }

        private void panel_MouseEnter(object sender, EventArgs e)
        {
            Panel p = sender as Panel;
            if (p != null)
            {
                p.BorderStyle = BorderStyle.FixedSingle;
            }
        }

        private void panel_MouseLeave(object sender, EventArgs e)
        {
            Panel p = sender as Panel;
            if (p != null)
            {
                p.BorderStyle = BorderStyle.None;
            }
        }
```

## 6、编译，生成DLL文件

  在bin\debug\下生成了Ky_ColorHatch.dll

# 二、自定义控件的调用

- 新建一个windows窗体应用的项目，如Ky_ColorHatch_test.

- 点击工具－＞选择工具项－＞浏览－＞选择刚才的那个Ky_ColorHatch.dll文件，这样你便会在你的工具箱中找到你的那个控件
  **注意：**
  放置Ky_ColorHatch.dll的文件夹不要采用汉字文件夹，否则会出现“没有可以放置在工具箱上的组件”

- 此时工具栏出现Ky_ColorHatch控件，双击Ky_ColorHatch控件，会在form上出现Ky_ColorHatch控件，放到正确的位置。

  ![img](https://upload-images.jianshu.io/upload_images/17748967-4468e1a42138535a.png?imageMogr2/auto-orient/strip|imageView2/2/w/972/format/webp)

- 编写按钮事件，把控件的选的颜色传到按钮上



```csharp
        private void button1_Click(object sender, EventArgs e)
        {
            button1.BackColor = ky_ColorHatch1.HatchColor;
        }
```

运行程序：



![img](https://upload-images.jianshu.io/upload_images/17748967-e66bbf4c512ee168.gif?imageMogr2/auto-orient/strip|imageView2/2/w/403/format/webp)

Ky_ColorHatch_test.exe