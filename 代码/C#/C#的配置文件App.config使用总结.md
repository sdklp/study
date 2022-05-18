# C#的配置文件App.config使用总结



应用程序配置文件是标准的 XML 文件，XML 标记和属性是区分大小写的。它是可以按需要更改的，开发人员可以使用配置文件来更改设置，而不必重编译应用程序。
配置文件的根节点是configuration。我们经常访问的是appSettings，它是由.Net预定义配置节。我们经常使用的配置文件的架构是象下面的形式。
先大概有个印象，通过后面的实例会有一个比较清楚的认识。下面的“配置节”可以理解为进行配置一个XML的节点。 
\1.  向项目添加 app.config 文件：

右击项目名称，选择“添加”→“添加新建项”，在出现的“添加新项”对话框中，选择“添加应用程序配置文件”；如果项目以前没有配置文件，则默认的文件名称为“ app.config ”，单击“确定”。出现在设计器视图中的 app.config 文件为：

<? xml version = "1.0 "encoding = "utf-8 " ?>

< configuration >

</ configuration >

在项目进行编译后，在 bin/Debuge 文件下，将出现两个配置文件 ( 以本项目为例 ) ，一个名为“ JxcManagement.EXE.config ”，另一个名为“ JxcManagement.vshost.exe.config ”。第一个文件为项目实际使用的配置文件，在程序运行中所做的更改都将被保存于此；第二个文件为原代码“ app.config ”的同步文件，在程序运行中不会发生更改。

\2. connectionStrings 配置节：

请注意：如果您的 SQL 版本为 2005 Express 版，则默认安装时 SQL 服务器实例名为 localhost/SQLExpress ，须更改以下实例中“ Data Source=localhost; ”一句为“ Data Source=localhost/SQLExpress; ”，在等于号的两边不要加上空格。

<!-- 数据库连接串 -->

   < connectionStrings >

​     < clear />

​     < add name = "conJxcBook "

​        connectionString = "Data Source=localhost;Initial Catalog=jxcbook;User                   ID=sa;password=******** "

​        providerName = "System.Data.SqlClient " />

   </ connectionStrings >

\3. appSettings 配置节：

appSettings 配置节为整个程序的配置，如果是对当前用户的配置，请使用 userSettings 配置节，其格式与以下配置书写要求一样。

<!-- 进销存管理系统初始化需要的参数 -->

   < appSettings >

​     < clear />

​     < add key = "userName "value = "" />

​     < add key = "password "value = "" />

​     < add key = "Department "value = "" />

​     < add key = "returnValue "value = "" />

​     < add key = "pwdPattern "value = "" />

​     < add key = "userPattern "value = "" />

</ appSettings >

\4. 读取与更新 app.config

对于 app.config 文件的读写，参照了网络文章： http://www.codeproject.com/csharp/ SystemConfiguration.asp标题为“Read/Write App.Config File with .NET 2.0”一文。

请注意：要使用以下的代码访问app.config文件，除添加引用System.Configuration外，还必须在项目添加对System.Configuration.dll的引用。

4.1 读取connectionStrings配置节

/// <summary>

/// 依据连接串名字connectionName返回数据连接字符串

/// </summary>

/// <param name="connectionName"></param>

/// <returns></returns>

private static string GetConnectionStringsConfig(string connectionName)

{

string connectionString =

​    ConfigurationManager .ConnectionStrings[connectionName].ConnectionString.ToString();

  Console .WriteLine(connectionString);

  return connectionString;

}

4.2 更新connectionStrings配置节

/// <summary>

/// 更新连接字符串

/// </summary>

/// <param name="newName"> 连接字符串名称 </param>

/// <param name="newConString"> 连接字符串内容 </param>

/// <param name="newProviderName"> 数据提供程序名称 </param>

private static void UpdateConnectionStringsConfig(string newName,

  string newConString,

  string newProviderName)

{

  bool isModified = false ;  // 记录该连接串是否已经存在

  // 如果要更改的连接串已经存在

  if (ConfigurationManager .ConnectionStrings[newName] != null )

  {

​    isModified = true ;

  }

  // 新建一个连接字符串实例

  ConnectionStringSettings mySettings =

​    new ConnectionStringSettings (newName, newConString, newProviderName);

  // 打开可执行的配置文件*.exe.config

  Configuration config =

​    ConfigurationManager .OpenExeConfiguration(ConfigurationUserLevel .None);

  // 如果连接串已存在，首先删除它

  if (isModified)

  {

​    config.ConnectionStrings.ConnectionStrings.Remove(newName);

  }

  // 将新的连接串添加到配置文件中.

  config.ConnectionStrings.ConnectionStrings.Add(mySettings);

  // 保存对配置文件所作的更改

  config.Save(ConfigurationSaveMode .Modified);

  // 强制重新载入配置文件的ConnectionStrings配置节

  ConfigurationManager .RefreshSection("ConnectionStrings" );

}

4.3 读取appStrings配置节

/// <summary>

/// 返回＊.exe.config文件中appSettings配置节的value项

/// </summary>

/// <param name="strKey"></param>

/// <returns></returns>

private static string GetAppConfig(string strKey)

{

  foreach (string key in ConfigurationManager .AppSettings)

  {

​    if (key == strKey)

​    {

​      return ConfigurationManager .AppSettings[strKey];

​    }

  }

  return null ;

}

4.4 更新connectionStrings配置节

/// <summary>

/// 在＊.exe.config文件中appSettings配置节增加一对键、值对

/// </summary>

/// <param name="newKey"></param>

/// <param name="newValue"></param>

private static void UpdateAppConfig(string newKey, string newValue)

{

  bool isModified = false ;  

  foreach (string key in ConfigurationManager .AppSettings)

  {

​    if (key==newKey)

​    {  

​      isModified = true ;

​    }

  }

 

  // Open App.Config of executable

  Configuration config =

​    ConfigurationManager .OpenExeConfiguration(ConfigurationUserLevel .None);

  // You need to remove the old settings object before you can replace it

  if (isModified)

  {

​    config.AppSettings.Settings.Remove(newKey);

  }  

  // Add an Application Setting.

  config.AppSettings.Settings.Add(newKey,newValue); 

  // Save the changes in App.config file.

  config.Save(ConfigurationSaveMode .Modified);

  // Force a reload of a changed section.

  ConfigurationManager .RefreshSection("appSettings" );

}

C#读写app.config中的数据



读语句：

```
      String str 
      =
       ConfigurationManager.AppSettings[
      "
      DemoKey
      "
      ];
     
```

 写语句：

```
      Configuration cfa 
      = 
     
     
      　　ConfigurationManager.OpenExeConfiguration(ConfigurationUserLevel.None);cfa.AppSettings.Settings[
      "
      DemoKey
      "
      ].Value 
      = 
      "
      DemoValue
      "
      ;cfa.Save();
     
```

 配置文件内容格式：（app.config）

```
      <?
      xml version="1.0" encoding="utf-8" 
      ?>
      
      <
      configuration
      >
      
      <
      appSettings
      >
          
      <
      add 
      key
      ="DemoKey"
       value
      ="*" 
      />
      
      </
      appSettings
      >
      
      </
      configuration
      >
     
```

 红笔标明的几个关键节是必须的

```
      System.Configuration.ConfigurationSettings.AppSettings[
      "
      Key
      "
      ];
     
```

 　但是现在FrameWork2.0已经明确表示此属性已经过时。并建议改为ConfigurationManager 

或WebConfigurationManager。并且AppSettings属性是只读的，并不支持修改属性值．

　　但是要想调用ConfigurationManager必须要先在工程里添加system.[configuration](https://so.csdn.net/so/search?q=configuration&spm=1001.2101.3001.7020).dll程序集的引用。

（在解决方案管理器中右键点击工程名称，在右键菜单中选择添加引用，.net TablePage下即可找到）
添加引用后可以用 String str = ConfigurationManager.AppSettings["Key"]来获取对应的值了。

　　更新配置文件：

```
      Configuration cfa 
      =
       ConfigurationManager
      .
     
     
      　　　　OpenExeConfiguration(ConfigurationUserLevel.None);
     
     
      cfa.AppSettings.Settings.Add(
      "
      key
      "
      , 
      "
      Name
      "
      ) 
      ||
      　
     
     
      　　cfa.AppSettings.Settings[
      "
      BrowseDir
      "
      ].Value 
      = 
      "
      name
      "
      ;
     
```

　　等等...
　　最后调用
　　cfa.Save(); 
　　当前的配置文件更新成功。

*****************************************************************************************************************

读写配置文件app.config 
　　在.Net中提供了配置文件，让我们可以很方面的处理配置信息，这个配置是XML格式的。而且.Net中已经提供了一些访问这个文件的功能。

１、读取配置信息
　　下面是一个配置文件的具体内容：



```
      <?
      xml version="1.0" encoding="utf-8"
      ?>
      
      <
      configuration
      >
      
      <
      appSettings
      >
         
      <
      add 
      key
      ="ConnenctionString"
       value
      ="*" 
      />
         
      <
      add 
      key
      ="TmpPath"
       value
      ="C:\Temp" 
      />
         
      </
      appSettings
      >
      
      </
      configuration
      >
     
```

 .net提供了可以直接访问<appsettings>（注意大小写）元素的方法，在这元素中有很多的子元素，这些子元素名称都是 “add”，有两个属性分别是“key”和“value”。一般情况下我们可以将自己的配置信息写在这个区域中，通过下面的方式进行访问：





```
      string
       ConString
      =
      System.Configuration
     
     
      　　　　.ConfigurationSettings.AppSettings[
      "
      ConnenctionString
      "
      ];
     
```

在appsettings后面的是子元素的key属性的值，例如appsettings["connenctionstring"]，我们就是访 问<add key="ConnenctionString" value="*" />这个子元素，它的返回值就是“*”，即value属性的值。

２、设置配置信息

　　如果配置信息是静态的，我们可以手工配置，要注意格式。如果配置信息是动态的，就需要我们写程序来实现。在.Net中没有写配置文件的功能，我们可以使用操作XML文件的方式来操作配置文件。下面就是一个写配置文件的例子。



```
      private 
      void
       SaveConfig(
      string
       ConnenctionString){　　XmlDocument doc
      =
      new
       XmlDocument();　　
      //
      获得配置文件的全路径
      
      　　
      string
       strFileName
      =
      AppDomain.CurrentDomain.BaseDirectory.ToString()
     
     
       
      　　　　　　　　　　　　　　　　　　+
      "
      Code.exe.config
      "
      ;　　doc.LOAd(strFileName);　　
      //
      找出名称为“add”的所有元素
      
      　　XmlNodeList nodes
      =
      doc.GetElementsByTagName(
      "
      add
      "
      );　　
      for
      (
      int
       i
      =
      0
      ;i
      <
      nodes.Count;i
      ++
      )　　{　　　　
      //
      获得将当前元素的key属性
      
      　　　　XmlAttribute att
      =
      nodes[i].Attributes[
      "
      key
      "
      ];　　　　
      //
      根据元素的第一个属性来判断当前的元素是不是目标元素
      
      　　　　
      if
       (att.Value
      ==
      "
      ConnectionString
      "
      ) 　　　　{　　　　　　
      //
      对目标元素中的第二个属性赋值
      
      　　　　　　att
      =
      nodes[i].Attributes[
      "
      value
      "
      ];　　　　　　att.Value
      =
      ConnenctionString;　　　　　　
      break
      ;　　　　}　　}　　
      //
      保存上面的修改
      
      　　doc.Save(strFileName);}
     
```

读取并修改App.config文件



\1. 向项目添加 app.config 文件：

右击项目名称，选择“添加”→“添加新建项”，在出现的“添加新项”对话框中，选择“添加应用程序配置文件”；如果项目以前没有配置文件，则默认的文件名称为“ app.config ”，单击“确定”。出现在设计器视图中的 app.config 文件为：

<? xml version = "1.0"encoding="utf-8" ?>

< configuration >

</ configuration >

在项目进行编译后，在 bin\Debuge 文件下，将出现两个配置文件 ( 以本项目为例 ) ，一个名为“ JxcManagement.EXE.config ”，另一个名为“ JxcManagement.vshost.exe.config ”。第一个文件为项目实际使用的配置文件，在程序运行中所做的更改都将被保存于此；第二个文件为原代码“ app.config ”的同步文件，在程序运行中不会发生更改。

\2. connectionStrings 配置节：

请注意：如果您的 SQL 版本为 2005 Express 版，则默认安装时 SQL 服务器实例名为 localhost\SQLExpress ，须更改以下实例中“ Data Source=localhost; ”一句为“ Data Source=localhost\SQLExpress; ”，在等于号的两边不要加上空格。

<!-- 数据库连接串 -->

   < connectionStrings >

​     < clear />

​     < add name = "conJxcBook"

​        connectionString = "Data Source=localhost;Initial Catalog=jxcbook;User                   ID=sa;password=********"

​        providerName = "System.Data.SqlClient" />

   </ connectionStrings >

\3. appSettings 配置节：

appSettings 配置节为整个程序的配置，如果是对当前用户的配置，请使用 userSettings 配置节，其格式与以下配置书写要求一样。

<!-- 进销存管理系统初始化需要的参数 -->

   < appSettings >

​     < clear />

​     < add key = "userName"value="" />

​     < add key = "password"value="" />

​     < add key = "Department"value="" />

​     < add key = "returnValue"value="" />

​     < add key = "pwdPattern"value="" />

​     < add key = "userPattern"value="" />

</ appSettings >

\4. 读取与更新 app.config

对于 app.config文件的读写，参照了网络文章： http://www.codeproject.com/csharp/　SystemConfiguration.asp标题为“Read/Write App.Config File with .NET 2.0 ”一文。

请注意：要使用以下的代码访问app.config文件，除添加引用System.Configuration外，还必须在项目添加对System.Configuration.dll的引用。

4.1 读取connectionStrings配置节

/// <summary>

/// 依据连接串名字connectionName返回数据连接字符串

/// </summary>

/// <param name="connectionName"></param>

/// <returns></returns>

private static string GetConnectionStringsConfig(string connectionName)

{

string connectionString =

​    ConfigurationManager.ConnectionStrings[connectionName].ConnectionString.ToString();

  Console.WriteLine(connectionString);

  return connectionString;

}

4.2 更新connectionStrings配置节

/// <summary>

/// 更新连接字符串

/// </summary>

/// <param name="newName"> 连接字符串名称 </param>

/// <param name="newConString"> 连接字符串内容 </param>

/// <param name="newProviderName"> 数据提供程序名称 </param>

private static void UpdateConnectionStringsConfig(string newName,

  string newConString,

  string newProviderName)

{

  bool isModified = false;  // 记录该连接串是否已经存在

  // 如果要更改的连接串已经存在

  if (ConfigurationManager.ConnectionStrings[newName] != null)

  {

​    isModified = true;

  }

  // 新建一个连接字符串实例

  ConnectionStringSettings mySettings =

​    new ConnectionStringSettings(newName, newConString, newProviderName);

  // 打开可执行的配置文件*.exe.config

  Configuration config =

​    ConfigurationManager.OpenExeConfiguration(ConfigurationUserLevel.None);

  // 如果连接串已存在，首先删除它

  if (isModified)

  {

​    config.ConnectionStrings.ConnectionStrings.Remove(newName);

  }

  // 将新的连接串添加到配置文件中.

  config.ConnectionStrings.ConnectionStrings.Add(mySettings);

  // 保存对配置文件所作的更改

  config.Save(ConfigurationSaveMode.Modified);

  // 强制重新载入配置文件的ConnectionStrings配置节

  ConfigurationManager.RefreshSection("ConnectionStrings");

}

4.3 读取appStrings配置节

/// <summary>

/// 返回＊.exe.config文件中appSettings配置节的value项

/// </summary>

/// <param name="strKey"></param>

/// <returns></returns>

private static string GetAppConfig(string strKey)

{

  foreach (string key in ConfigurationManager.AppSettings)

  {

​    if (key == strKey)

​    {

​      return ConfigurationManager.AppSettings[strKey];

​    }

  }

  return null;

}

4.4 更新connectionStrings配置节

/// <summary>

/// 在＊.exe.config文件中appSettings配置节增加一对键、值对

/// </summary>

/// <param name="newKey"></param>

/// <param name="newValue"></param>

private static void UpdateAppConfig(string newKey, string newValue)

{

  bool isModified = false;  

  foreach (string key in ConfigurationManager.AppSettings)

  {

​    if(key==newKey)

​    {  

​      isModified = true;

​    }

  }

 

  // Open App.Config of executable

  Configuration config =

​    ConfigurationManager.OpenExeConfiguration(ConfigurationUserLevel.None);

  // You need to remove the old settings object before you can replace it

  if (isModified)

  {

​    config.AppSettings.Settings.Remove(newKey);

  }  

  // Add an Application Setting.

  config.AppSettings.Settings.Add(newKey,newValue); 

  // Save the changes in App.config file.

  config.Save(ConfigurationSaveMode.Modified);

  // Force a reload of a changed section.

  ConfigurationManager.RefreshSection("appSettings");

}