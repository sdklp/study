# joomla插件开发入门（二）

**介绍**

在本系列教程的第一篇中，演示了使用Joomla 1.5 CMS框架创建简单的view-controller组件。

在第一篇教程中，我们在视图中嵌入了硬编码的greeting。这并不是完全遵循MVC模式的做法，原因是视图仅仅为了显示数据，而不是包含它（获取greeting数据）。

在本系列教程的第二篇中，我们将演示将greeting数据从视图移到模型中。在后续的教程中，我们将演示这种设计模式所提供的强大功能和灵活性。

**创建模型**

模型的概念来源于其名称，其用类来表示（或模型）实体。在本例中，第一个模型是一个’hello’，或者一个greeting。到目前为止，这都与我们的设计相吻合，因为在此之前我们已经建立了一个显示greeting的视图（’hello’）。

根据Joomla框架中模型命名的约定，模型类的名称将以组件名字为开头，再加上模型的名字（在本例中是以’hello’开头，加上’model’）。因此，我们模型类的名字叫做HelloModelHello。

现在，我们仅为hello模型建立一个行为，就是获取greeting值。这个方法叫做getGreeting()。它只简单地返回字符串’Hello, World!’。

site/models/hello.php的源代码：

**<?php**

*/***

** Hello Model forHello World Component*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_2*

** @license GNU/GPL*

**/*

*// No direct access*

defined('_JEXEC') or die('Restricted access');

jimport('joomla.application.component.model');



*/***

** Hello Model*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

**/*

**class**HelloModelHello **extends** JModel

{

*/***

** Gets thegreeting*

** @returnstring The greeting to be displayed to the user*

**/*

**function**getGreeting()

{

return'Hello,World!';

}

}

注意以jimport开始的这一行。jimport函数用来从Joomla框架中加载组件必需文件。这个语句加载了文件/libraries/joomla/application/component/model.php。’.’用作目录分隔符，最后一部分是所需加载的文件的文件名。库目录下所有文件都将被加载。这些文件包括了JModel类的定义，我们的模型继承了该类，因此该类是必须被加载的。

现在，我们已经创建了模型，还必须修改视图的源文件，才能使用该模型获取greeting。

**使用模型**

Joomla框架的运行方式是，控制器自动加载与视图名称相同的模型并注入到视图。既然视图叫做’Hello’，那么’Hello’模型将自动被加载并注入到视图中。因此，我们能够很方便地使用JView::getModel()方法获取模型的引用（如果模型没有遵循命名约定，那就需要将模型的名字传递给[JView::getModel()](http://api.joomla.org/Joomla-Framework/Application/JView.html#getModel)方法）。

之前的视图源代码中包含下面的行：

$greeting = "Hello World!";

为了使用我们的模型，将该行修改为：

$model =&$this->getModel();

$greeting = $model->getGreeting();

完整的视图源代码如下：

**<?php**

*/***

** @package Joomla.Tutorials*

** @subpackageComponents*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_2*

** @license GNU/GPL*

**/*

*// No direct access*

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

$model = &$this->getModel();

$greeting = $model->getGreeting();

$this->assignRef('greeting', $greeting);

parent::display($tpl);

}

}

**向包中加入文件**

下面要做的就是向XML文件中增加一个条目，这样新的模型文件才会被复制安装。Joomla框架会自动查找模型目录下的模型文件，模型文件条目看起来像这样（这行要加入到网站单元）：

<filename>models/hello.php</filename>

更新后的hello.xml文件是：

**<?xml**version="1.0"encoding="utf-8"**?>**

**<install**type="component"version="1.5.0"**>**

**<name>**Hello**</name>**

*<!-- The following elements are optionaland free of formatting conttraints -->*

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

**<filename>**models/hello.php**</filename>**

**<filename>**models/index.html**</filename>**

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

**结论**

我们现在有了一个简单的MVC组件。组件每个元素都非常简单，但是却提供了强大的灵活性和功能性。