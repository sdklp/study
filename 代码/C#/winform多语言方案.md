# 【C#】winform多语言方案

### 1.CultureInfo的获取和设置

CultureInfo通常由两位小写的LanguageCode+两位大写的Country/RegionCode组成，如：zh-CN,zh-TW,jr-JP,en-US,zh-HK。部分地区由languageCode+sripttag+country/regioncode，如zh-Hans-HK（香港简体中文）。
几个有用的属性：

1. CultureInfo.CurrentUICulture: 只读属性，不可更改。获取当前系统的显示语言（Display Language），系统显示为英文就是英文语言，显示中文就是中文语言。
2. CultureInfo.CurrentCulture: 只读属性。获取当前系统设置的区域性信息。
   可通过以下路径“控制面板->时钟、语言和区域→格式→格式”进行修改，默认值是“与Windows显示语言匹配”。即通常来说CurrentCulture=CurrentUICulture。你可以进行更改，使二者不同。
   ![这里写图片描述](https://img-blog.csdn.net/20180604141654361?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NhdHNoaXRvbmU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
3. Thread.CurrentThread.CurrentUICulture：读取或设置当前线程的区域信息，只对当前线程有效。默认情况下此Culture等于CultureInfo.CurrentUICulture。
4. CultureInfo.DefaultThreadCurrentUICulture：同上，唯一不同的是对所有线程都有效。

### 2.两种常见的多语言方式

(1). 使用Visual Studio自动生成对应的语言的resource文件。参考[演练：本地化 Windows 窗体](https://docs.microsoft.com/zh-cn/previous-versions/visualstudio/visual-studio-2010/y99d1cd3(v=vs.100))
![这里写图片描述](https://img-blog.csdn.net/20180604141732725?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NhdHNoaXRvbmU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![这里写图片描述](https://img-blog.csdn.net/20180604141740819?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NhdHNoaXRvbmU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![这里写图片描述](https://img-blog.csdn.net/20180604141752108?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NhdHNoaXRvbmU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
如上图所示，一个窗体对应若干个多语言文件。多语言文件使用了同一个Name，但是Value是翻译过的。启动时系统自动将根据不同的用户设置显示对应的语言，如果没有对应语言的字符串则使用默认的字符串。

(2). 手动向项目中添加resource文件。
![这里写图片描述](https://img-blog.csdn.net/20180604141856943?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NhdHNoaXRvbmU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
如图：首先添加默认语言文件lang.resx，然后再添加不同的语言版本的resx。
使用时，通过ResourceManager来读取资源文件：

```C#
ResourceManager LocRM = new ResourceManager("Namespace.lang",typeof(Form1).Assembly);
MessageBox.Show(LocRM.GetString("STR_KEY"));
```

虽然构造LocRM时传入的是lang.resx，但系统会根据不同的语言读取不同的resx。
(3). 两种方式的比较

- VS自动生成：
  不用写代码，省力； 热切换方便。
  自由度不高； 操作复杂与设计器结合太紧 ；不容易分离和重用 ；扩展不方便。
- 手动添加：
  能够灵活控制 ；方便分离和重用 ；后期编辑修改方便。
  要写代码将翻译好的字符串绑定到UI上； 热切换要麻烦一点。

### 3.应用到winform客户端中

考虑到以后的扩展和开发时的效率，我认为选择“手动添加”的这种方式比较好。
（1）.对于程序部署到不同国家和地区中所共有的部分，如前端UI部分，前端HardCode部分，部分服务端数据返回。
前端UI：将对应的str_key绑定到control上。
前端HardCode：统一读取str_key对应的value。
部分服务端返回数据：如果属于文本类型，根据传入的language_id返回对应语言的版本；如果是dll插件类型，编码时应与UI编码方式一致，直接把dll加载到主程序时会自动本地化。
（2）.差异部分，如在使用过程中逐渐累积的数据。
这部分数据因为大部分都存在数据库中，且与多语言无关。

### 4.实现

（1）.添加多语言类库Globalization
这里存放所有翻译好的多语言文件。
（2）.对于需要本地化的项目引用Globalization即可。
在系统启动时加载多语言资源文件：
LocRM = new ResourceManager(“Globalization.lang”, typeof(Class1).Assembly);
（3）.构建多语言的Model并绑定到Controle

```C#
public class LangModel:INotifyPropertyChanged
    {
        public static LangModel Instance { get; } = new LangModel();
        public string IDS_CANCEL
        {
            get
            {
                return Program.LocRM.GetString("IDS_CANCEL");
            }
        }
        public string IDS_HELLO
        {
            get
            {
                return Program.LocRM.GetString("IDS_HELLO");
            }
        }
        public string IDS_OK
        {
            get
            {
                return Program.LocRM.GetString("IDS_OK");
            }
        }
        public string IDS_REPORT
        {
            get
            {
                return Program.LocRM.GetString("IDS_REPORT");
            }
        }

        public event PropertyChangedEventHandler PropertyChanged;
        void OnPropertyChanged(string propertyName)
        {
            if(PropertyChanged!=null)
            {
                PropertyChanged(this, new PropertyChangedEventArgs(propertyName));
            }
        }
        public void NotifyTarget()
        {
            //OnPropertyChanged("xxx");
            //OnPropertyChanged("xxx");
            //....
        }
    }
```

（4）.多语言热切换。
设置CultureInfo.DefaultThreadCurrentUICulture = new CultureInfo(“xx-XX”)，然后NotifyTarget。
可能存在的问题：如果多语言字符串非常多，NotifyTarget方法会通知所有的绑定，可能会有性能问题。需要和“只通知当前显示的UI上绑定的属性”这种方法进行比较。