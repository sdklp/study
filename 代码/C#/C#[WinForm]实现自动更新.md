```
winform程序相对web程序而言，功能更强大，编程更方便，但软件更新却相当麻烦，要到客户端一台一台地升级，面对这个实际问题，在最近的一个小项目中，本人设计了一个通过软件实现自动升级技术方案，弥补了这一缺陷，有较好的参考价值
实现原理：在WebServices中实现一个GetVer的WebMethod方法，其作用是获取当前的最新版本。 然后将现在版本与最新版本比较，如果有新版本，则进行升级。
步骤：
　　　　
1、准备一个XML文件 (Update.xml)。
<?xml version="1.0" encoding="utf-8" ?> 
<product>
<version>1.0.1818.42821</version>
<description>修正一些Bug</description>
<filelist count="4" sourcepath="./update/">
<item name="City.xml" size="">
<value />
</item>
<item name="CustomerApplication.exe" size="">
<value />
</item>
<item name="Interop.SHDocVw.dll" size="">
<value />
</item>
<item name="Citys.xml" size="">
<value />
</item>
</filelist>
</product>

作用是作为一个升级用的模板。
　　　　
2、WebServices的GetVer方法。

[WebMethod(Description="取得更新版本")]
public string GetVer()
{
XmlDocument doc = new XmlDocument();
doc.Load(Server.MapPath("update.xml"));
XmlElement root = doc.DocumentElement;
return root.SelectSingleNode("version").InnerText;
}
　　　　　
3、WebServices的GetUpdateData方法。

[WebMethod(Description="在线更新软件")]
[SoapHeader("sHeader")]
public System.Xml.XmlDocument GetUpdateData()
{
//验证用户是否登陆
if(sHeader==null)
return null;
if(!DataProvider.GetInstance.CheckLogin(sHeader.Username,sHeader.Password))
return null;
//取得更新的xml模板内容
XmlDocument doc = new XmlDocument();
doc.Load(Server.MapPath("update.xml"));
XmlElement root = doc.DocumentElement;
//看看有几个文件需要更新
XmlNode updateNode = root.SelectSingleNode("filelist");
string path = updateNode.Attributes["sourcepath"].Value;
int count = int.Parse(updateNode.Attributes["count"].Value);
//将xml中的value用实际内容替换
for(int i=0;i<count;i++)
{
XmlNode itemNode = updateNode.ChildNodes[i];
string fileName = path + itemNode.Attributes["name"].Value;
FileStream fs = File.OpenRead(Server.MapPath(fileName));
itemNode.Attributes["size"].Value = fs.Length.ToString();
BinaryReader br = new BinaryReader(fs);
//这里是文件的实际内容，使用了Base64String编码
itemNode.SelectSingleNode("value").InnerText = Convert.ToBase64String(br.ReadBytes((int)fs.Length),0,(int)fs.Length);
br.Close();
fs.Close();
}
return doc;
}
　　　
4、在客户端进行的工作。
首先引用此WebServices，例如命名为:WebSvs，

string nVer = Start.GetService.GetVer();　
if(Application.ProductVersion.CompareTo(nVer)<=0)
update();

在本代码中 Start.GetService是WebSvs的一个Static 实例。首先检查版本，将结果与当前版本进行比较，如果为新版本则执行UpDate方法。
void update()
{
this.statusBarPanel1.Text = "正在下载...";
System.Xml.XmlDocument doc = ((System.Xml.XmlDocument)Start.GetService.GetUpdateData());
doc.Save(Application.StartupPath + @"\update.xml");
System.Diagnostics.Process.Start(Application.StartupPath + @"\update.exe");
Close();
Application.Exit();
}
这里为了简单起见，没有使用异步方法，当然使用异步方法能更好的提高客户体验，这个需要读者们自己去添加。：） update的作用是将升级的XML文件下载下来，保存为执行文件目录下的一个Update.xml文件。任务完成，退出程序，等待Update.Exe 来进行升级。 　　　　
5、Update.Exe 的内容。
private void Form1_Load(object sender, System.EventArgs e)
{
System.Diagnostics.Process[] ps = System.Diagnostics.Process.GetProcesses();
foreach(System.Diagnostics.Process p in ps)
{
//MessageBox.Show(p.ProcessName);
if(p.ProcessName.ToLower()=="customerapplication")
{
p.Kill();
break;
}
}
XmlDocument doc = new XmlDocument();
doc.Load(Application.StartupPath + @"\update.xml");
XmlElement root = doc.DocumentElement;
XmlNode updateNode = root.SelectSingleNode("filelist");
string path = updateNode.Attributes["sourcepath"].Value;
int count = int.Parse(updateNode.Attributes["count"].Value);
for(int i=0;i<count;i++)
{
XmlNode itemNode = updateNode.ChildNodes[i];
string fileName = itemNode.Attributes["name"].Value;
FileInfo fi = new FileInfo(fileName);
fi.Delete();
//File.Delete(Application.StartupPath + @"\" + fileName);
this.label1.Text = "正在更新： " + fileName + " (" + itemNode.Attributes["size"].Value + ") ...";
FileStream fs = File.Open(fileName,FileMode.Create,FileAccess.Write);
fs.Write(System.Convert.FromBase64String(itemNode.SelectSingleNode("value").InnerText),0,int.Parse(itemNode.Attributes["size"].Value));
fs.Close();
}
label1.Text = "更新完成";
File.Delete(Application.StartupPath + @"\update.xml");
label1.Text = "正在重新启动应用程序...";
System.Diagnostics.Process.Start("CustomerApplication.exe");
Close();
Application.Exit();
}
这个代码也很容易懂，首先就是找到主进程，如果没有关闭，则用Process.Kill()来关闭主程序。然后则用一个XmlDocument来Load程序生成的update.xml文件。用xml文件里指定的路径和文件名来生成指定的文件，在这之前先前已经存在的文件删除。更新完毕后，则重新启动主应用程序。这样更新就完成了。
 
```

View Code

C#自动更新

![C#[WinForm]实现自动更新_新版本](../../../我的文档/Typora/Pictures/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=.jpeg)![C#[WinForm]实现自动更新_xml_02](../../../我的文档/Typora/Pictures/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=-16410172964121.jpeg)

```
在client根据server的配置文件判断是否有新版本，有的话下载更新信息：更新Ip、版本号、更新文件等，下载到本地。
再根据ip进行下载到本地临时文件中，下载完成后，提示用户是否更新。如果更新关闭当前系统，启动更新服务将临时文件中的文件替换到要替换的程序目录下，代码如下：
<br>/// <summary>
 /// 根据服务器上的数据进行同步数据
 /// </summary>
 /// <param name="sender"></param>
 /// <param name="e"></param>
 private void btnKeepUser_Click(object sender, EventArgs e)
 {
 #region web download
 bool isUpdate =false;
 string[] fileName =new string[] { "Accounting_Client.dll","Accounting_Component.dll","Accounting_Entity.dll","Accounting_UI.dll" }; //要下载的文件
 AutoUpdate update =new AutoUpdate();
 for (int i = 0; i < fileName.Length; i++)
 {
 isUpdate= update.start(fileName[i]);
 if (isUpdate ==false)
 {
 break;
 }
 }
 if (isUpdate ==false)
 {
 MessageBox.Show("Update failure");
 }
 else 
 {
 //自动下载完成后，将保存的路径和要替换的路径保存到ini配置文件中
 #region 写入INI文件
 //写入ini文件
 string s = Application.ExecutablePath;
 string s1;
 s1 = s.Replace("ini.exe","updateVersionConfig.ini");
 //写入版本信息
 string origin = System.Windows.Forms.Application.StartupPath +"";
 string destination = System.Windows.Forms.Application.StartupPath;
 string path ="C:\\updateVersionConfig.ini";
 
 WritePrivateProfileString("updateInfo","origin", origin, path); //写入源地址
 WritePrivateProfileString("updateInfo","destination" , destination, path); //写入更新地址
 #endregion
 #region 启动自动更新，关闭当前系统
 //System.Diagnostics.Process[] proc = System.Diagnostics.Process.GetProcessesByName("TIMS");
 ////关闭原有应用程序的所有进程
 //foreach (System.Diagnostics.Process pro in proc)
 //{
 // pro.Kill();
 //}
 //启动更新程序
 string strUpdaterPath = System.Windows.Forms.Application.StartupPath +"";
 this.Close();
 System.Diagnostics.Process.Start(strUpdaterPath);
 #endregion
 }
 
 #endregion
 
 
 //ProgressBar bar = new ProgressBar(); 
 //string curVersion = GetLastestVersionByFtp("");
 //#region 读取本地当前的版本
 ////此处获得软件的版本信息
 //string currentUser = "info";
 //StringBuilder temp = new StringBuilder(80000000);
 //string section = "versionInfo";
 //string iniPath = GlobalFile.GlobalConfigurationInfo.AddressOfIni; //"G:\\test\\userConfig.ini";
 //int i = GetPrivateProfileString(section, currentUser, "", temp, 80000000, iniPath);
 //string versionInfo = temp.ToString();
 //int versionPos = versionInfo.LastIndexOf("=");
 //versionInfo = versionInfo.Substring(versionPos + 1, versionInfo.Length - (versionPos + 1));
 //#endregion
 ////获得本地版本
 //string localVersion = versionInfo;
 ////获得服务器上的版本信息
 //UserLoginClient ftpInfo = new UserLoginClient();
 //string[] result = ftpInfo.getFtpInformation(localVersion);
 //if (result == null)
 //{
 // label2.Text = "已是最新版本！";
 // return;
 //}
 ////获得FTP返回的消息 0：最新版本号 1： FTP登陆名 2：FTP登陆密码 3： FTP登陆IP
 //string ftpVersion = result[0];
 //string ftpLoginName = result[1];
 //string ftpLoginPwd = result[2];
 //string ftpIp = result[3];
 ////如果已是最新版本
 //if (ftpVersion == localVersion)
 //{
 // label2.Text = "已是最新版本！";
 //}
 //else
 //{
 // //根据最新的版本号下载程序到本地
 
 // label2.Text = "已不是最新版本！";
 //}
 }
```

View Code

inf文件介绍

![C#[WinForm]实现自动更新_新版本](../../../我的文档/Typora/Pictures/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=.jpeg)![C#[WinForm]实现自动更新_xml_02](../../../我的文档/Typora/Pictures/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=-16410172964121.jpeg)

```
INF文件全称Information File文件，是Winodws操作系统下用来描述设备或文件等数据信息的文件。INF文件是由标准的ASCII码组成，您可以用任何一款文字编辑器查看修改其中的内容。一般我们总是认为INF文件是系统设备的驱动程序，其实这是错误的认识，Windows之所以在安装某些硬件的驱动时提示需要INF文件是因为INF文件为该设备提供了一个全面描述硬件参数和相应驱动文件(DLL文件)的信息。就好比我们看着说明书安装电脑硬件一样，我们就是Windows系统，说明书就是INF文件。INF文件功能非常强大，几乎能完成日常操作的所有功能。您可以把它看成是Windows系统底下的超强批初理。要熟练掌握和理解甚至是编写INF文件需要对其内部结构有相当的认识。下面就让我们来深入到INF文件中的内部一窥其真面貌吧！
 
INF文件的组成有节(Sections)，键(Key)和值(value)三部分。
关键节有
[Version]版本描述信息，主要用于版本控制。
[Strings]字符串信息，用于常量定义。
[DestinationDirs]定义系统路径信息。
[SourceDisksNames]指明源盘信息。
[SourceDisksNames]指明源盘文件名。
[DefaultInstall]开始执行安装。
其它的节可以自定义，下面用一实例来具体讲解。
 
程序代码
[Version]
Signature=$Chicago$
Provider=%Author%
 
[Strings]
Product="添加文件关联演示"
Version="1.0"
Author="Xunchi"
Copyright="Copyright 2005"
CustomFile="inf" ;修改您需要的文件名后缀
Program="NOTEPAD.EXE" ;修改您需要关联的应用程序名
 
[Add.Reg]
HKCR,"."%CustomFile%,"",FLG_ADDREG_TYPE_SZ ,%CustomFile%File
HKCR,%CustomFile%File,"",FLG_ADDREG_TYPE_SZ,安装信息
HKCR,%CustomFile%"File\shell","",FLG_ADDREG_TYPE_SZ,open
HKCR,%CustomFile%"File\shell\open\command","",FLG_ADDREG_TYPE_SZ,%program% %1
 
[DefaultInstall]
AddReg=Add.Reg
 
　　在[Version]节中"Signature"项定义了该INF文件需要运行在何种操作系统版本中。有$Windows NT$, $Chicago$, or $Windows 95$三个值供选择，一般选择$Chicago$即可。项Provider中定义了该文件的创作来源，%Author%指引用Author项的值。您也可自定其它项来描述该INF文件的版本信息。该INF文件的作用是关联文件，所以主要是对注册表的操作，我们来看[Add.Reg]节，共四条语句，格式都是一样。HKCR表示根HKEY_CLASSES_ROOT，第二个参数是子键的路径名，第三个参数是表明值的类型，最后是值(具体见附表)。以上都是对操作的定义与过程，在节[DefaultInstall]中是开始执行要安装的流程，AddReg表明是对注册表进行操作，操作对象是Add.Reg节中的定义。如果您把AddReg换成DelReg则是删除注册表中的键值。当鼠标单击该INF文件在弹出的菜单中选择“安装”就开始执行您所定义的操作。该示例在系统的INF文件右键菜单中增加了查看编辑功能并设置了默认动作，因为在安装了不了解的INF文件有可能对系统产生不良的影响，这样双击文件就可打开编辑该文件了。
 
　　再看看INF文件在文件操作方面的能力吧。请看下面的一个例子。
 
程序代码
[Version]
Signature=$Chicago$
Provider=%Author%
[Strings]
Product="文件复制和安装演示"
Version="1.0"
Author="Xunchi"
Copyright="Copyright 2005"
 
[FileList]
ProcessList.exe ;此文件已在当前目录下，下同。
 
[FileList1]
Wordpad.exe
[DestinationDirs]
FileList=11 ;安装到Windows的系统目录
FileList1=10 ;安装到Windows目录
[DefaultInstall]
Copyfiles=FileList,FileList1
 
　　相同的节的作用与上一例类似，请注意新出现的节[FileList]，这是我自定义的节名，它表示了一个文件组，[FileList1]也类似。在节[DestinationDirs]中需定义每个文件组复制到的目录。Copyfiles指明了需要进行复制的文件组。
　　INF文件的操作还包括服务(NT系统)程序的安装和卸载，INI文件的转换等。由于这些操作都比较的复杂和繁琐，且有一定的危险性故下次有机会再向大家进行深入探讨。
　　最后我们来看一下INF文件的执行机制，这时你也许要问不就是简单的执行一下“安装”吗？知其然不知其所以然知识水平是不会提高的。在“文件夹选项”中的“文件类型”找到INF文件的“安装”命令看到一串命令。“rundll32.exe setupapi,InstallHinfSection DefaultInst_all 132 %1”它表示了运行Dll文件setupapi.dll中的命令InstallHinfSection并传递给它起始节的名字 DefaultInstall。可见起始节是可以自定义的。INF文件的执行也可用在各种支持API调用的编程工具中。至此INF文件的结构和运行机制我们已基本了解，现在就让你的思维开动起来，让它更好的为我们工作吧。
本文中，我们需要通过inf文件来完成的任务是：将Microsoft.mshtml.dll文件移动到C:\Windows\Microsoft.NET\Framework\v2.0.50727文件夹中（前提是本机已安装Framework.Net 2.0），然后安装QmsSetup.msi文件。
inf文件编写
[version]
signature="$CHICAGO$"
AdvancedINF=2.0
[FileList]
Microsoft.mshtml.dll
[DestinationDirs]
FileList=0,"C:\Windows\Microsoft.NET\Framework\v2.0.50727\"
[Add.Code]
QmsSetup.msi=QmsSetup.msi
[QmsSetup.msi]
file-win32-x86=thiscab
clsid={CF50DCDE-D952-431C-A4B1-6C62FD5500EE}
FileVersion=1,0,0,0
[Setup Hooks]
RunSetup=RunSetup
[RunSetup]
run="""msiexec.exe""" /i """%EXTRACT_DIR%\QmsSetup.msi""" /qn
[DefaultInstall]
CopyFiles=FileList
编写完inf文件后，还需要借助CABARC.EXE这个cab包打包工具。将inf文件，QmsSetup.msi，Microsoft.mshtml.dll和cabarc.exe放入同一个文件夹中。桌面左下角“开始”，“运行”，输入cmd，打开命令提示符工具，进入准备好的文件的目录，执行命令：cabarc n QmsSetup.cab QmsSetup.inf QmsSetup.msi Microsoft.mshtml.dll。显示“Completed successfully” ，打开所在目录，就可以看到生成的cab包了。
 
cab包生成完成后，需要嵌入到网页中。代码如下：
<object name="QmsSetup" style="display:none" id="QmsSetup" classid="CLSID:CF50DCDE-D952-431C-A4B1-6C62FD5500EE" codebase="../Cab/QmsSetup.cab"></object>
 
odebase属性是cab所在的文件路径。
此外，将该网页添加到浏览器的可信任站点中，并在自定义级别中设置ActiveX为启用，设置完成就可以打开网页测试了。
最后，补充一篇老外的博文，写的很详细。
http://www.olavaukan.com/2010/08/creating-an-activex-control-in-net-using-c/
 
```

View Code

自动更新

![C#[WinForm]实现自动更新_新版本](../../../我的文档/Typora/Pictures/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=.jpeg)![C#[WinForm]实现自动更新_xml_02](../../../我的文档/Typora/Pictures/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=-16410172964121.jpeg)

```
现在但凡是一个程序都有相应的升级程序，如果你的程序没有相应的升级程序，那么你就需要留意了。你的用户很可能丢失！！！网上关于自动升级的例子也有很多，前几天一个朋友很苦恼的跟我说它的客户在逐渐减少（据他所说，他都客户因为他的程序升级很麻烦，所以很多人放弃了使用它的软件），问我说怎么办？其实他也知道该怎么办？所以...朋友嘛！就给他做了一个自动升级程序。恰好今天CSDN上的一位老友也询问这件事情，所以就把代码共享大家了。
先个几个图：

 

 
主要原理（相当简单）：
升级程序一定要是一个单独的exe,最好不要和你的程序绑到一起（否则升级程序无法启动）。主程序退出----升级程序启动----升级程序访问你的网站的升级配置文件-----读取配置文件里面信息-----下载-----升级程序关闭----主程序启动
主要代码：
1.读取配置文件：
        private void GetUpdateInfo()
        {
            //获取服务器信息
            WebClient client = new WebClient();
            doc = new XmlDocument();
            try
            {
                doc.Load(client.OpenRead("http://192.168.3.43/update/update.xml"));
                //doc.Load(client.OpenRead(Config.IniReadValue("Update","UpdateURL",Application.StartupPath+"//config.ini")+"//update.xml"));
                //doc.Load(Application.StartupPath + "//update.xml");
                client = null;
            }
            catch
            {
                this.labHtml.Text = "无法取得更新文件！程序升级失败！";
                return;
            }
            if (doc == null)
                return;
            //分析文件
            XmlNode node;
            //获取文件列表
            string RootPath = doc.SelectSingleNode("Product/FileRootPath").InnerText.Trim();
            node = doc.SelectSingleNode("Product/FileList");
            if (node != null)
            {
                foreach (XmlNode xn in node.ChildNodes)
                {
                    this.listView1.Items.Add(new ListViewItem(new string[]
                        {
                             xn.Attributes["Name"].Value.ToString(),                            
                             new WebFileInfo(RootPath+xn.Attributes["Name"].Value.ToString()).GetFileSize().ToString(),
                            "---"
                        }));
                }
            }
        }
2.文件下载： 
/// <summary>
/// 下载文件
/// </summary>
public void Download()
{
FileStream fs = new FileStream( this.strFile,FileMode.Create,FileAccess.Write,FileShare.ReadWrite );
try
{
this.objWebRequest = (HttpWebRequest)WebRequest.Create( this.strUrl );
this.objWebRequest.AllowAutoRedirect = true;
// int nOffset = 0;
long nCount = 0;
byte[] buffer = new byte[ 4096 ]; //4KB
int nRecv = 0; //接收到的字节数
this.objWebResponse = (HttpWebResponse)this.objWebRequest.GetResponse();
Stream recvStream = this.objWebResponse.GetResponseStream();
long nMaxLength = (int)this.objWebResponse.ContentLength;
if( this.bCheckFileSize && nMaxLength != this.nFileSize )
{
throw new Exception( string.Format( "文件/"{0}/"被损坏,无法下载!",Path.GetFileName( this.strFile ) ) );
}
if( this.DownloadFileStart != null )
this.DownloadFileStart( new DownloadFileStartEventArgs( (int)nMaxLength ) );
while( true )
{
nRecv = recvStream.Read( buffer,0,buffer.Length );
if( nRecv == 0 )
break;
fs.Write( buffer,0,nRecv );
nCount += nRecv;
//引发下载块完成事件
if( this.DownloadFileBlock != null )
this.DownloadFileBlock( new DownloadFileEventArgs( (int)nMaxLength,(int)nCount ) );
}
recvStream.Close();
//引发下载完成事件
if( this.DownloadFileComplete != null )
this.DownloadFileComplete( this,EventArgs.Empty );
}
finally
{
fs.Close();
}
}
 
```

