# 如何开发一个Joomla插件

作者：Andrew Eddile
翻译：warran
原文地址：http://developer.joomla.org/tutorials/184-how-to-create-a-joomla-plugin.html

**介绍**
Joomla插件可用作各种用途。插件为网站的输出页面增加了更多的表达方式，并且具有安装功能。

**插件类型**
尽管Joomla的插件类型几乎是无限多的。但他们有8个核心的类型。这些核心类型，分类存放在/plugins/目录，他们是：

- authentication
- content
- editors
- editors-xtd
- search
- system
- user
- xmlrpc

![img](http://developer.joomla.org/images/tutorials/creating_a_plugin/plugin_files.png)



**Authentication** 插件允许你对不同的来源进行验证。当你登陆的时候可以通过Joomla的用户数据库进行验证。然而有很多其它的方式，也是可以的，例如：Google的OpenID（开放式用户中心身份标识），LDAP（轻量目录访问协议）和很多其它的方式。无论哪种来源，有其开放API，你都可以写一个验证插件，以确认登陆的身份。例如你可以写一个Twitter账户的验证插件，因为他们提供了开放API。

**Content** 插件用于在显示文章内容时改变或增加一些内容。例如content插件可以隐藏文章种的email地址，或者用自己的方式格式化URL地址。content插件也可以在文章种搜索特定的标记，然后将他们替换为其它的文本或者HTML。例如在名为Load Module插件中，将会启动所有在banner1位置的模块，并且把{loadmodule banner1}标记，替换成他们的输出内容。

**Editor** 插件允许你增加新的内容编辑器(常用的有WYSIYWG)

**Editor-XTD**(扩展)插件允许你editor上增加某些功能按钮。例如现有的默认editor下的几个按钮：Image（增加图片），Pagebreak（插入分页符）和Read more（阅读全文）按钮。

**Search** 插件允许你在不同的组件，不同的文章中进行搜索。比如文章系统的search插件：Contacts 、Weblinks 

**System** 插件允许你在Joomla站点的各个地方使用PHP代码执行各种动作。

**User** 插件允许你在不同的时候执行针对于用户的动作。包括登录时、登出时，还有存储用户数据的时候。用户插件中最典型的在不同web应用之间进行桥连接(bridge)。例如建立一个Joomla与Phpbb之间的桥连接。

**XML-RPC** 插件允许你为网站提供一个XML-RPC服务。当你的网站为其它应用程序（或许是个桌面应用程序）提供网络服务（web services）的时候，它为你提供了远程交互的能力。网络服务真的是一个高深的话题，这里没办法讲的太详细。

**基础的文件**
当然一个插件会有一些文件，其中有两个文件必须使用特定的文件名。当我们研究关于文件的内容时，首先我们必须确定插件的类型，他们必须是某种内置的文件类型（authentication,content,editors,editors-xtd,search,system,user,xml-rpc）。或者你可以在/plugins/目录下建立个自己的目录，创建自己的某种插件类型。关于authentication的插件文件，需要放在/plugins/authentication/目录下，system插件的文件放在/plugins/system/目录下，其它也一样。

让我们看下如何建立一个System类型的名为Test的插件的文件架构。插件文件的命名没有特别的规则（唯一要强调的是不要使用数字开头），但是一旦你决定 了文件名称后，插件的其它部分，需要遵守这个命名规则。

在这个插件中，首先建立一个PHP文件，test.php，用于Joomla进行载入。还需要建立一个XML文件，text.xml，在文件中，可以建立一些标识和插件的安装信息（同时也做为插件的参数）。

**test.php**文件源代码如下：

```
<?php
// no direct access
//禁止非Joomla程序直接调用
defined( '_JEXEC' ) or die( 'Restricted access' );

jimport( 'joomla.plugin.plugin' );

/**
 * Example system plugin
 * system插件例子
 */
class plgSystemTest extends JPlugin
{
 /**
  * Constructor
         * 构造函数
  *
  * For php4 compatibility we must not use the __constructor as a constructor for plugins
  * because func_get_args ( void ) returns a copy of all passed arguments NOT references.
  * This causes problems with cross-referencing necessary for the observer design pattern.
  *
         *为了兼容php4，不能直接使用__constructor做为插件的构造函数。
         *返回的是所有参数的拷贝，而不是引用。
         *交叉引用是Observe设计模式必须的。
  * @access protected
         *权限：受保护的
  * @param object $subject The object to observe
  * @param  array   $config  An array that holds the plugin configuration
         *一个数组，用于存储插件配置
  * @since 1.0
  */
 function plgSystemTest( &$subject, $config )
 {
  parent::__construct( $subject, $config );

  // Do some extra initialisation in this constructor if required
                //如果需要，可以在这里做一些额外的初始化
 }

 /**
  * Do something onAfterInitialise 
         * 初始化后，做些什么事
  */
 function onAfterInitialise()
 {
  // Perform some action
                // 执行一些动作
 }
}
```

让我们仔细的看一下源代码。请注意，很多php文件中常见的内容呗省略了。

文件通过检查常量_JEXEC开始，用于确保文件不会被直接通过url引用。这是个很重要的安全点，并且这行代码必须放在文件的最前端。做这项安全检查是非常非常重要的。

下面我们使用了jimport函数载入了库文件中定义的JPlugin类。
你会注意到一个插件仅仅是一个继承自JPlugin的类。（这个不同于以前版本的Joomla）。类的命名规则是很重要的。
命名公式如下：
plg+插件目录+无扩展名的插件文件名。要将单词的首字母大写，类似于“Camel Case”。虽然PHP的类对大小写是不敏感的，但这样会增加代码可读性。
在我们的例子中，通过公式，我们得到类名为：
plg+System+Test=plgSystemTest

让我们再看一下类里的方法。

第一个方法，叫做构造函数，是不强制使用的。你可以把在Joomla载入插件后想做的事情写到里面。
在helper方法里，使用`JPluginHelper::importPlugin(<plugin_type>)的时候`，它就会被执行。这意味着，即使插件从来没出发过，你仍然可以执行构造函数里的代码。

在PHP4中，构造函数的名称必须与类名相同，如果你的程序仅仅想支持PHP5的化，你可以把方法名替换为`__constructor。剩下的方法将会体现出Joomla代码中触发事件的名字。在这个例子中，我们知道，它的名字是 onAfterInitialise，当某项应用(application)准备好后，它是第一个被调用的方法。关于什么时候事件会被触发的更详细介绍请看文档中API执行顺序这篇文章。这个命名规则很简单：方法名字必须与你要触发的事件名字相同。Joomla框架会为你自动注册类中的发法。这是基本的PHP文件。它的位置，名字和方法依赖于你建立的插件的用途。有一点需要注意的是system插件不是仅仅处理系统事件。因为系统插件会在Joomla运行的每一个PHP文件中载入。你可以在一个系统插件中包含任意触发事件。Joomla的触发事件包括：`

Authentication

- onAuthenticate

Content

- onPrepareContent
- onAfterDisplayTitle
- onBeforeDisplayContent
- onBeforeContentSave (new in 1.5.4)
- onAfterContentSave (new in 1.5.4)

Editors

- onInit
- onGetContent
- onSetContent
- onSave
- onDisplay
- onGetInsertMethod

Editors XTD (Extended)

- onDisplay

Seach

- onSearch
- onSearchAreas

System

- onAfterInitialise
- onAfterRoute
- onAfterDispatch
- onAfterRender

User

- onLoginUser
- onLoginFailure
- onLogoutUser
- onLogoutFailure
- onBeforeStoreUser
- onAfterStoreUser
- onBeforeDeleteUser
- onAfterDeleteUser

XML-RPC

- onGetWebServices

关于如何创建特殊插件的介绍，请查看文档中的[插件种类](http://docs.joomla.org/Category:Plugins)文章。

**text.xml**
test.xml的代码如下：

```
<?xml version="1.0" encoding="utf-8"?>
<install version="1.5.2" type="plugin" group="system" method="upgrade">
 <name>System - Test</name>
 <author>Author</author>
 <creationDate>Month 2008</creationDate>
 <copyright>Copyright (C) 2008 Holder. All rights reserved.</copyright>
 <license>GNU General Public License</license>
 <authorEmail>email</authorEmail>
 <authorUrl>url</authorUrl>
 <version>1.0.1</version>
 <description>A test system plugin</description>
 <files>
  <filename plugin="example">example.php</filename>
 </files>
 <params>
    <param name="example"
    type="text"
    default=""
    label="Example"
    description="An example text parameter" />
 </params>
</install>
```

这是一个非常典型的XML标识文件的格式。让我们了解一下其中比较重要的标识(tags)：
**INSTALL**
这个install标识，有几个关键属性。type的属性必须是"plugin"，并且要指定group属性。group属性需要设定为你的文件所在目录的名字（例如system,content,etc）。我们使用method="upgrade"，是因为要允许安装插件，而没有卸载功能。换句话说，如果你与他人分享这个插件，你可以仅仅通过安装新的版本，去覆盖旧版本。

**NAME**
我们通常使用插件的属性开头，我们的例子中，插件是system属性的，并且带有一定的测试目的，所以我们将插件命名为："System - Test"。你可以将NAME以任意方式去起。但通常是以这个格式去起的。

**FILES**
files标识包含了插件中将要安装的所有文件名字。插件同样支持安装子目录，所以还要指定所有文件夹的名称，<folder>test</folder>这是只有一个子目录时的例子，很像设置PHP文件的标识一样（当然没有扩展名）。

**PARAMS**
插件可以指定任意多数量的参数。请注意，就如同模块和组件中的参数一样，没有高级群组。（大概是指每个参数只能单独设置）

打包插件
打包插件非常简单。如果你只有两个文件（PHP文件和XML文件），只要将他们压缩为一个zip文件即可。如果有子目录，也一同压缩进去。