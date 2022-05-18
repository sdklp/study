# [joomla插件开发入门（一）](https://www.cnblogs.com/ikodota/articles/2220475.html)

**介绍**

软件框架是一个可由开发人员使用的应用基础。Joomla 1.5框架为开发人员开放了强大的功能。Joomla 代码采用了可扩展的设计。本教程引导你使用框架来开发一个组件的全过程。

项目的范围是开发一个简单的Hello world 组件。在后续的教程中，将通过这个简单的组件来展示Joomla强大的功能和MVC设计模式的通用性。

**需求**

本教程需要Joomla 1.5或更高版本。

**介绍****MVC****（****Model-View-Controller****）**

虽然这个组件的设想比较简单，但是随着功能特性的添加或自定义界面，程序代码将很快会变得非常复杂。![img]()

Model-View-Controller(以下简写为MVC)是一种软件设计模式，它被用来组织业务逻辑和数据存储分离的程序代码。在将业务逻辑封装到一个模块的前提下，修改和定制用户界面和用户交互数据时，就不在需要重新编写业务逻辑。最初MVC被用来将传统的输入、处理和输出映射为GUI架构。

而这三个主要的角色就是Joomla MVC的基础。在这里将对它们进行简要的描述，更加详细的资料，请参与本教程结尾处提供的链接。

**模型（****Mode****）**

模型是组件中封装应用程序数据的部分。除了从模型中获取数据外，它通常还提供有效的方法来管理和操作数据。在我们的例子中，模型包含了用来增加、删除和更新数据库中greetings信息的方法。还包括从数据库中检索greetings列表的方法。一般来说，底层的数据访问技术应该被封装在模型中。这样，如果将应用程序从顺序文件存储系统迁移到数据库系统，则仅仅需要改变模型单元，而不是视图或控制器。

**视图（****View****）**

视图是组件中在用户交互模型中以恰当的方式向用户呈现数据的部分。对于基于WEB的应用程序，视图通常指的是返回数据的HTML页面。视图从模型获取数据（通过控制器传递给视图），将数据注入到合适模板，并呈现给用户。视图不会引起任何数据变更，它仅显示从模型中获取的数据。

**控制器（****Controller****）**

控制器负责响应用户的动作。在WEB应用程序的案例中，用户动作（通常）是一个页面请求。控制器将决定用户建立那些请求，通过激活合适的数据操作模型来对请求做出恰当的响应，并将模型传递给视图。控制器不直接在模型中显示数据，它仅仅触发修改数据的模型方法，然后将模型传递给显示数据的视图。

**MVC** **连接**

右边的简图描绘了Joomla中MVC的基本构成。除了模型、视图和控制器外，加入了一个用小圆圈表示的入口。增加了连接到视图的模板。借助这五个部分，你应该能够理解制作基本Joomla MVC组件的教程。![img]()

本教程的第一节仅关注于控制器和视图（也会使用模板），图中蓝色标记的部分。第二节和第三节增加、扩展了模型的抽象数据操作功能，图中标记绿色的部分。

请记住，这个简图仅适用于网站部分（即前端部分）。与之类似的简图适用于管理部分（即后端部分。本组件开发教程的4-6节中将关注管理部分。网站和管理部分都将通过基于XML的安装文件（通常叫做manifest文件）来维护和配置。

**Joomla! MVC** **实现**

在Joomla中，MVC模式使用三个类来实现：[JModel](http://api.joomla.org/Joomla-Framework/Application/JModel.html), [JView](http://api.joomla.org/Joomla-Framework/Application/JView.html) and [JController](http://api.joomla.org/Joomla-Framework/Application/JController.html)。如需这些类更详细的信息，请参阅API参考文档（WIP）。

出于学习目的和调试，尤其是在开发（教程）组件的过程中，将运行时调试器加入到您的Joomla网站应该是一个好的做法。一个很好的例子是社区项目[J!Dump](http://extensions.joomla.org/extensions/miscellaneous/development/1509/details)，它具有在不影响原输出的情况下弹出调试窗口的优点。J!Dump系统不仅允许您查看开发属性，还能够查看方法的属性。

**建立组件**

对于基本的组件，我们仅需要五个文件：

<!--[if !supportLists]-->§ <!--[endif]-->site/hello.php – 这个文件是我们组件的入口

<!--[if !supportLists]-->§ <!--[endif]-->site/controller.php – 这个文件是组件的主控制器

<!--[if !supportLists]-->§ <!--[endif]-->site/views/hello/view.html.php – 这个文件获取必要的数据并注入到模板

<!--[if !supportLists]-->§ <!--[endif]-->site/views/hello/tmpl/default.php – 这个是输出模板

<!--[if !supportLists]-->§ <!--[endif]-->hello.xml – 这是用于Joomla安装的XML(manifest)配置文件

请记住，入口的文件名和组件的名字必须相同。例如，如果在安装的时候调用组件” Very Intricate Name Component”，则Joomla会建立目录com_veryintricatenamecomponent，并且入口php文件必须被命名为veryintricatenamecomponent.php，否则该组件将无法正常运行。请注意，一些特殊的字符，特别是下划线”_”，对Joomla来说有特殊的含义，应该尽量避免在组件和文件名字中使用。

这里的网站目录是指所安装组件的前端部分。

**命名约定**

主要文章：[Naming conventions](http://docs.joomla.org/Naming_conventions)

预留一些词用于组件或类的命名这点是很重要的，而违反这些命名将产生严重的调试错误。其中之一就是用于view类（JView的子类）和controller类（JController的子类）的” view”（不区分大小写），因为view类的名称需要与controller类名、component名称有相同的第一部分（尽管最后一个只不过是常用的约定，违反它不会产生错误）。

所有的模型、视图和控制器的文件名和目录名必须小写，以能够在Unix/Linux中方便地使用。

**创建入口点**

Joomla一直通过简单的入口来访问：网站应用使用index.php或者管理员应用使用administrator/index.php。之后，应用将根据URL或POST数据中的’option’的值，来加载请求的组件。对于调用我们的组件，URL应该是：

index.php?option=com_hello&view=hello

这将加载主文件，该文件可以被看作我们组件的一个简单的入口点：components/com_hello/hello.php。

该文件的源代码在组件中相当典型。

入口点site/hello.php的源代码：

**<?php**

*/***

** @package Joomla.Tutorials*

** @subpackage Components*

** components/com_hello/hello.php*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_1*

** @license GNU/GPL*

**/*

*// No direct access*

defined('_JEXEC') or die('Restricted access');

*// Require the basecontroller*

require_once( JPATH_COMPONENT.DS.'controller.php');



*// Require specificcontroller if requested*

if($controller = JRequest::getWord('controller')){

$path = JPATH_COMPONENT.DS.'controllers'.DS.$controller.'.php';

if(file_exists($path)){

require_once$path;

}else{

$controller = '';

}

}



*// Create thecontroller*

$classname = 'HelloController'.$controller;

$controller = **new** $classname();



*// Perform the Requesttask*

$controller->execute( JRequest::getWord('task'));



*// Redirect if set bythe controller*

$controller->redirect();

第一行语句是安全检查。

JPATH_COMPONENT是当前组件的绝对路径，在本例中是components/com_hello。如果需要特别指定是网站组件还是管理员组件，你可以使用JPATH_COMPONENT_SITE或JPATH_COMPONENT_ADMINISTRATOR。

DS是系统的目录分隔符：’/’ 或’\’。它由框架自动设置，因此开发者不需要关心为不同的操作系统开发不同版本的问题。当使用本地文件时，也应该一直使用’DS’常数。

在加载主控制器后，检查是否需要特定的控制器。在本例中，因只有主控制器，我们略过这个条件，在将来使用时再检查。

JRequest:getWord()用来获取URL或POST数据中的字符变量。如果URL是index.php?option=com_hello&controller=controller_name，则我们能够获取组件所使用控制器的名字：echo JRequest::getWord('controller');

现在，我们有了com_hello/controller.php中的主控制器'HelloController'，并且若需要可以添加控制器，如com_hello/controllers/controller1.php中'HelloControllerController1'。使用标准的命名方式将使后面的工作变得简单：

'{Componentname}{Controller}{Controllername}'

控制器建立之后，调用控制器执行任务，在URL中定义为：index.php?option=com_hello&task=sometask。若没有设置具体的任务，则执行默认任务'display'。当执行display任务时，’view’ 变量将判断显示什么内容。其它一些常见任务是保存（save）、编辑（edit）、新建（new）…

完成任务（如：'save'）后，通常控制器会决定重定向的页面。最后一行语句来确定实际的重定向页面。

本质上讲，主入口点（hello.php）将控制权交给了控制器，控制器来执行在请求中指定的任务。

注意：我们没有在该文件中使用php关闭标记：?>。这样做的原因是，在输出代码中我们不会有任何不必要的空格。这是自Joomla 1.5以来用于所有PHP文件的默认做法。

**创建控制器**

我们的组件仅有一个任务- greet the world。因此，控制器非常简单。没有数据操作请求。需要做全部工作就是加载合适的视图。控制器也只有一个方法：display()。JController内置了大多数必须的功能，因此我们只要调用JController::display()方法即可。

主控制site/controller.php的源代码：

**<?php**

*/***

** @package Joomla.Tutorials*

** @subpackageComponents*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_1*

** @license GNU/GPL*

**/*

*// No direct access*

defined('_JEXEC') or die('Restricted access');

jimport('joomla.application.component.controller');



*/***

** Hello WorldComponent Controller*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

**/*

**class**HelloController **extends** JController

{

*/***

** Method todisplay the view*

***

** @access public*

**/*

**function**display()

{

parent::display();

}

}

除非特别指定（使用registerDefaultTask()方法），JController构造函数总是注册display()任务，并将其设置为默认任务。

重写方法display()并不是完全必要的，因为它所要做的就是调用父类的构造方法。不过，这是个好的做法，这能够提示控制器中所发生的事情。

JController::display()方法根据请求来决定视图的名称和布局，并加载视图和设置布局。当为组件建立菜单项时，菜单管理器将允许管理员选择我们想要的视图，就是菜单链接所显示和指定的布局。视图通常会关联一个特定的数据集合（如汽车列表、事件列表、一个车、一个时间等）。布局是视图组织的方式。

在我们的组件中，我们有个简单的视图叫做hello，和一个简单的布局（默认的）。

**创建视图**

视图的任务非常简单。获取用来显示的数据并且将其注入到模板。使用JView::assignRef方法将数据注入到模板。（注意：传递给assignRef方法的键值（第一个参数）不能以下划线开头，即$this->assignRef('_greeting',$greeting)。这样做assignRef方法会返回false，并无法将变量值注入到模板中）

视图site/views/hello/view.html.php 的源代码：

**<?php**

*/***

** @package Joomla.Tutorials*

** @subpackageComponents*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_1*

** @license GNU/GPL*

**/*

*// no direct access*

defined('_JEXEC') or die('Restricted access');

jimport('joomla.application.component.view');



*/***

** HTML View classfor the HelloWorld Component*

***

** @package HelloWorld*

**/*

**class**HelloViewHello **extends** JView

{

**function**display($tpl = **null**)

{

$greeting = "HelloWorld!";

$this->assignRef('greeting', $greeting);

parent::display($tpl);

}

}



**创建模板**

Joomla模板/布局是常规的PHP文件，其用特殊的方式来布局视图中数据。使用JView::assignRef方法绑定的变量，在模板中能够通过$this->{propertyname}的方式来访问（见下面模板代码例子）。

模板非常简单：仅仅显示从视图传递来的greeting变量值。文件是site/views/hello/tmpl/default.php：

**<?php**

*// No direct access*

defined('_JEXEC') or die('Restrictedaccess'); **?>**

<h1><?php echo$this->greeting; ?></h1>



**组合起来****–** **创建****hello.xml** **文件**

通过FTP复制文件和修改数据库表，手工安装组件是可以的。不过，通过创建包文件，让Joomla为你安装组件更为高效。包文件包括一些信息：

<!--[if !supportLists]-->§ <!--[endif]-->组件的基本描述信息（即名字等）、及选项、说明、版权和许可信息。

<!--[if !supportLists]-->§ <!--[endif]-->需要复制文件的清单。

<!--[if !supportLists]-->§ <!--[endif]-->可选，执行额外安装和卸载操作的PHP文件。

<!--[if !supportLists]-->§ <!--[endif]-->可选，在安装/卸载过程执行数据库查询的SQL文件。

hello.xml文件的XML格式如下：

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

**<version>**1.01**</version>**

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

**<filename>**views/index.html**</filename>**

**<filename>**views/hello/index.html**</filename>**

**<filename>**views/hello/view.html.php**</filename>**

**<filename>**views/hello/tmpl/default.php**</filename>**

**<filename>**views/hello/tmpl/index.html**</filename>**

**</files>**



**<administration>**

*<!-- Administration Menu Section -->*

**<menu>**HelloWorld!**</menu>**

*<!-- Administration Main File Copy Section-->*

**<files**folder="admin"**>**

**<filename>**hello.php**</filename>**

**<filename>**index.html**</filename>**

**</files>**

**</administration>**

**</install>**

| ![img]() | 这个xml文件也叫做manifest，将该文件放置到包的根目录（因为安装程序会以此作为其它文件的根目录）不包括xml文件自身。 |
| -------- | ------------------------------------------------------------ |
|          |                                                              |

你可能已经注意到，manifest文件中提到了我们还没有讨论过的文件。这就是index.html文件和admin文件。为防止窥探用户获取目录列表，需要将index.html文件放置在每一个目录。如果没有index.html文件，一些WEB服务器将列出目录内容。这是不安全的。这些文件只有简单的一行：

<html><body bgcolor="#FFFFFF"></body></html>

仅简单地显示空白页面。

在admin目录下的hello.php文件是组件管理单元的入口点。目前组件还没有管理员需求，所以该文件的内容和index.html文件的内容相同。

如果你完成了上述步骤，可以访问index.php?option=com_hello来查看成果。