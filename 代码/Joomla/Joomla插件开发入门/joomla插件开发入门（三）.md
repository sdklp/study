# joomla插件开发入门（三）

**介绍**

在本系列教程的第二篇中，向你展示了如何创建一个简单的model-view-controller组件。我们有了一个从模型（在第二篇教程中创建的）中获取数据的视图。在本篇教程中，我们将继续进行模型工作，为取代硬编码，模型将从数据库表中获取数据。

本篇教程将演示如何使用JDatabase类从数据库中获取数据。

**获取数据**

模型目前只有一个方法：getGreeting()。该方法非常简单—就是返回硬编码的greeting值。

为了让事情变得更加有趣，我们将从数据库中加载greeting值。下面我们将演示如何创建SQL文件，并将适当的代码添加到XML manifest文件中，以使得组件在安装时可以创建表和一些样例数据。现在，我们简单地用一些从数据库中获取greeting值并返回的源代码来替换原来的return语句。

第一步，获取数据库对象的引用。由于Joomla将数据库作为其常用的操作，数据库连接已经存在，因此，没有必要再自己创建数据库连接了。获取已存在的数据库的引用：

$db=& JFactory::getDBO();

JFactory是静态类，用来获取许多系统对象的引用。关于此类的更多信息，请参阅API文档[JFactoryAPI](http://docs.joomla.org/JFactory)。

方法getDBO用来获取数据库对象，这个方法简单，但要记住。

现在，已经得到数据库对象的引用，我们能够用它来获取数据。实现这一点需要两步：

<!--[if !supportLists]-->§ <!--[endif]-->在数据库对象中设置查询语句（setQuery）

<!--[if !supportLists]-->§ <!--[endif]-->加载查询结果（loadResult）

因此，getGreeting()方法将看起来像这样：

**function** getGreeting()

{

$db =& JFactory::getDBO();



$query = 'SELECTgreeting FROM #__hello';

$db->setQuery($query);

$greeting = $db->loadResult();

return$greeting;

}

hello是我们后面要创建的数据库表名，greeting是其字段名，用来存储greeting数据。如果不熟悉SQL语句，下载一个教程或课程对于你快速熟悉SQL语句是非常有帮助的。这样的教程在[w3schools](http://www.w3schools.com/sql/default.asp)能够找到。

方法$db->loadResult()执行存储在数据库对象中的查询语句，并返回查询结果第一行第一个字段的值。关于JDatabase类其它加载查询结果的方法，更多信息请参阅[JDatabaseAPI reference](http://api.joomla.org/Joomla-Framework/Database/JDatabase.html)。

**创建安装****SQL****文件**

Joomla的安装程序内建了在组件安装过程中执行查询语句的功能。查询语句全部都存储在一个标准的文本文件中。

在安装文件中，我们有三个查询语句：第一个是若表存在就删除表；第二个是用合适的字段创建表；第三个是插入数据。

下面是查询语句：

**DROP TABLE** IF **EXISTS** `*#__hello`;*



**CREATE TABLE** `*#__hello` (*

`id` **INT**(11)**UNSIGNED****NOT NULL****AUTO_INCREMENT**,

`greeting` **VARCHAR**(25)**NOT NULL**,

**PRIMARY KEY** (`id`)

)ENGINE=MyISAM **AUTO_INCREMENT**=0**DEFAULT****CHARSET**=utf8;



**INSERT****INTO** `*#__hello`(`greeting`) VALUES ('Hello, World!'), ('Bonjour, Monde!'), ('Ciao, Mondo!');*

你可能发现了表名的前缀很奇怪。Joomla将用当前安装时使用的前缀来替换它。对于大多数的安装，表名将变为jos_hello。这允许使用同一个数据库来安装多个Joomla，并防止与其它应用程序因使用相同表名而产生的冲突（即两个应用程序可能会共享一个数据库，但都需要一个’users’表。而此约定避免了该问题）。

在数据库中，我们建立了两个字段。第一个字段是id，是主键。主键是数据库表中用来唯一标识记录的字段。通常用来检索数据库表中的数据行。另一个字段是greeting。这个字段用来存储greeting值，该值在前面的查询代码中查询并返回。

我们将安装查询语句保存在admin/install.sql文件中。（注意：这个文件的原始版本使用了install.utf.sql，这是不对的。参见[discussion](http://docs.joomla.org/Talk:Developing_a_Model-View-Controller_Component_-_Part_3_-_Using_the_Database)）

**创建卸载****SQL****文件**

尽管希望人们不要’卸载’我们的组件，但是如果他们卸载，那么组件卸载后不留下任何东西就重要了。Joomla会自行删除在安装过程中所创建的文件和目录，但是要删除所有增加到数据库中的表，我们必须手工加入查询语句。我们只创建了一个表，因此删除也仅需要一个查询：

**DROP TABLE** IF **EXISTS** `*#__hello`;*

我们将卸载查询语句保存在admin/uninstall.sql文件中。（注意：注意：这个文件的原始版本使用了uninstall.utf.sql，这是不对的。参见[discussion](http://docs.joomla.org/Talk:Developing_a_Model-View-Controller_Component_-_Part_3_-_Using_the_Database)）

**更新****XML****文件**

我们需要对hello.xml文件做一些修改。第一，需要在安装文件清单中加入两个SQL文件。第二，SQL安装文件必须放在管理目录中。第三，在安装和卸载过程中，需要告诉安装程序执行我们的查询语句

新的文件看起来是这样：

**<?xml**version="1.0"encoding="utf-8"**?>**

**<install**type="component"version="1.5.0"**>**

**<name>**Hello**</name>**

*<!-- The following elements are optionaland free of formatting constraints -->*

**<creationDate>**2007-02-22**</creationDate>**

**<author>**JohnDoe**</author>**

**<authorEmail>**john.doe@example.org**</authorEmail>**

**<authorUrl>**http://www.example.org**</authorUrl>**

**<copyright>**CopyrightInfo**</copyright>**

**<license>**LicenseInfo**</license>**

*<!-- The version string is recorded in the components table -->*

**<version>**3.01**</version>**

*<!-- The description is optional anddefaults to the name -->*

**<description>**Descriptionof the component ...**</description>**



*<!-- Site Main File Copy Section -->*

*<!-- Note the folder attribute: Thisattribute describes the folder*

*to copy FROMin the package to install therefore files copied*

*in thissection are copied from /site/ in the package -->*

**<files**folder="site"**>**

**<filename>**controller.php**</filename>**

**<filename>**hello.php**</filename>**

**<filename>**index.html**</filename>**

**<filename>**models/hello.php**</filename>**

**<filename>**models/index.html**</filename>**

**<filename>**views/index.html**</filename>**

**<filename>**views/hello/index.html**</filename>**

**<filename>**views/hello/view.html.php**</filename>**

**<filename>**views/hello/tmpl/default.php**</filename>**

**<filename>**views/hello/tmpl/index.html**</filename>**

**</files>**



**<install>**

**<sql>**

**<file**charset="utf8"driver="mysql"**>**install.sql**</file>**

**</sql>**

**</install>**

**<uninstall>**

**<sql>**

**<file**charset="utf8"driver="mysql"**>**uninstall.sql**</file>**

**</sql>**

**</uninstall>**



**<administration>**

*<!-- Administration Menu Section -->*

**<menu>**HelloWorld!**</menu>**



*<!-- Administration Main File Copy Section-->*

**<files**folder="admin"**>**

**<filename>**hello.php**</filename>**

**<filename>**index.html**</filename>**

**<filename>**install.sql**</filename>**

**<filename>**uninstall.sql**</filename>**

**</files>**



**</administration>**

**</install>**

你会注意到在<install>和<uninstall>之间的<file>标记展示了两个属性：charset和driver。charset属性是使用的字符集类型。只有utf8是合法的字符集。如果要为非utf8字符集的数据库创建安装文件（如老版本的MySQL），则省略该属性。

driver属性指定了查询语句所针对的数据库种类。目前，这个属性只能设置为mysql，但Joomla未来的版本也许能够支持更多的数据库驱动。

**结论**

现在，我们有了一个同时使用Joomla MVC框架类和JDatabase类的组件。目前你能够编写MVC组件与数据库的交互，也能够使用Joomla的安装程序来创建和填充数据库表。