# [C# winForm资源文件实现多语言切换](https://www.cnblogs.com/aijiao/p/10715262.html)

 

C# winForm资源文件实现多语言切换

这是我目前看到过最简单的多语言切换了

**操作步骤**

界面上的多语

Step1.将界面Form的属性的Localizable属性设为True

![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416095322591-160214550.png)

Step2.切换界面From的Language属性为欲使用的语系

![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416095435281-252203262.png)

设完后会在分页标签上看到目前设定的语系

![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416095537172-838006554.png)

Step3.设定界面上欲显示的字样并适当的调整版面

 ![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416095714473-2145087797.png)

![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416095821161-1016744303.png)

到此，用资源档做的多语系程序就完成了。

 

我们可以到档案总管看一下，可看到Visual Studio自己帮我们产生了对应的资源档。

![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416100000676-680686559.png)

因此，在实作界面上的多语时，我们并不需自己手动加入资源档。

![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416100125717-1191140164.png)

信息的多语

上面的示例带出了界面上支持多语的写法，但却不适用于信息上，若要在信息上也支持多语。请依下列步骤：

Step1.加入资源档 

资源档依照“资源文件名.文化特性.resx”格式命名，如“Resources.zh-tw.resx”。

 ![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416100311080-4907675.png)

Step2.设定资源档内的信息内容 

在资源档上连点两下

 ![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416100404967-14716052.png)

设定对应的语系字串

 ![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416100439720-1930334115.png)

Step3.使用资源档内的信息内容 

这边只要透过My.Resources去取出资源档内的值即可，内部OR Mapping转换都帮你做好好，可以用强型别的方式直接取用，能避免掉许多不必要的低级错误。 

MsgBox（My.Resources.Resource1.String1）

 其实个人习惯是命名为“Resources.文化特性.resx”，因为项目里已偷放了一个Resources.resx资源档，因此只需加入非预设语系的资源档即可

在使用上也会变得较为简短

 

MsgBox（My.Resources.String1）

切换语系

依上面步骤操作完后，其实已具多语支持能力。当程序开启时，会自动依照当前语系去显示界面画面。因此我们开启时应该是显示中文而不是本来的英文。若要自己切换语系可利用Threading.Thread.CurrentThread.CurrentUICulture。

 

Threading.Thread.CurrentThread.CurrentUICulture = New CultureInfo（“en”）

值得注意的是，这样的写法是不会影响已开启的视窗的。只有后来开启的视窗会被切换语系。

 

那是否已开启的视窗就无法切换了呢？那倒也不是。可以透过使用ResourceManager.GetString去取得对应的字串，把取出的字串再设到界面上即可。但是这也不是很好的方法，个人倾向使用ComponentResourceManager配合递回去把界面上的字串换成对应语系的字串。有兴趣的可以参考Form.Designer.vb档的代码。

 ![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416100801915-1257923590.png)

![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416100816985-838119107.png)

注意事项

在用多国语言资源档时，建议最后在弄其它语系的设定。因为当我们设定多语系时，Visual Studio会自动帮我们产生资源档，且内含预设的设定值。若太早让Visual Studi产生资源档，则预设的设定值将只有少少的几个，后面的设定值都需手动的设定，且每个语系的资源档都要设定。

![img](https://img2018.cnblogs.com/blog/562297/201904/562297-20190416101236724-1849393846.png)

 

|      |      |
| ---- | ---- |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |

调用

SetLang("语言标识", this, typeof(窗体));