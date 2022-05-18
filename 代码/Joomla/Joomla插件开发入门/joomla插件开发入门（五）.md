# joomla插件开发入门（五）

**介绍**

本文重点介绍管理员的入口页面/文章。虽然MVC模式和前端用户一样，本文还是将快速地过一遍所有步骤，并创建管理单元的后端部分。本文将专注于创建针对*Hello*组件所有功能列表的基本框架，但不包括用户界面。而真正的用户界面，将在后续的文章中添加[MVC组件开发– 第六部分- 加入后端操作](http://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_6_-_Adding_Backend_Actions).



**教程中的命名**

在接下来文章中，我们将尽可能保持管理员部分说明中的名称与组件名称相仿。在一般的叙述中，我们将使用*Hellos*来标识数据库的列表。*Hellos*名称用来查看和处理来自数据库的多个Hello。当编辑或增加单个Hello时，我们将使用单数*Hello*作为控制器和视图的名称。本文的*Admin Hello*已经与*Site Hello*没有相关的功能了（当然，仅有的关联是数据库内容）。

MVC说明的第1、2、3章工作在com_hello组件的网站目录树中，而第5和第6章节则将重点放在管理目录树中。第5和第6章节没有增加或更改网站目录树中的文件。查看所有com_hello实现的附属例子中的XML文件。XML配置文件将帮助你确定在本文和后续章节中使用的不同文件的确切位置。



**创建基本的框架**

管理员面板的基本框架与网站部分的非常相似。hello.php是组件的管理员部分的主入口点。除了所加载的控制器名称变为HellosController外，这个文件的其它部分与网站部分所使用的hello.php文件相同。默认的控制器也叫做controller.php，而且除了控制器的名称由HelloController替换为HellosController外，这个文件的其它部分和网站部分的默认控制器也相同。这种差异让JController在默认情况下，将加载显示greeting列表的hellos视图。

下面是hello.php的清单：

**<?php**

*/***

** @package Joomla.Tutorials*

** @subpackageComponents*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_5*

** @license GNU/GPL*

**/*



*// No direct access*

defined('_JEXEC') or die('Restricted access');



*// Require the base controller*

require_once( JPATH_COMPONENT.DS.'controller.php');



*// Require specific controller if requested*

if($controller = JRequest::getWord('controller')){

$path = JPATH_COMPONENT.DS.'controllers'.DS.$controller.'.php';

if(file_exists($path)){

require_once$path;

}else{

$controller = '';

}

}



*// Create the controller*

$classname = 'HellosController'.$controller;

$controller = **new** $classname();



*// Perform the Request task*

$controller->execute(JRequest::getVar('task'));



*// Redirect if set by the controller*

$controller->redirect();

我们将从hellos视图和hellos模型开始。首先从模型开始。



**Hellos** **模型**

Hellos模型非常简单。我们目前仅需要一个能够从数据库中获取hello列表的操作。这个操作通过一个叫做getData()的方法来实现。

JModel类有一个内建的保护（protected）方法叫做_getList()。这个方法用于从数据库中获取记录列表的简单任务。我们将查询语句传给它，它将返回记录列表。

在后面的某个时间点，我们也许会将查询语句用在其它方法上。因此，我们将创建一个私有（private）方法_buildQuery()，该方法返回传给_getList()的查询语句。将查询语句固定在一个地方定义，更改起来将会变得更加方便。

因此，在我们的类中，需要两个方法：getData() 和_buildQuery()。

_buildQuery()简单地返回语句。看起来如下：

*/***

** Returns thequery*

** @return stringThe query to be used to retrieve the rows from the database*

**/*

**function** _buildQuery()

{

$query = ' SELECT* '

. ' FROM #__hello ';



return$query;

}

getData()将获取查询语句，并从数据库中返回记录集合。现在，可能会出现同一个页面加载中需要获取两次数据列表的情况。查询两次数据库是一种资源浪费。因此，我们让这个方法在一个保护（protected）属性中存储该数据，从而，让后续的请求能够简单地返回已经获取的数据。这个属性叫做_data（*<--* *命名约定冲突。私有或保护数据应该以**'_'* *为前缀，但是，若将这个数据引用返回到视图，就会被直接访问，这时这个数据就只能视为公开的*）。

下面是getData()方法：

*/***

** Retrieves thehello data*

** @return arrayArray of objects containing the data from the database*

**/*

**function** getData()

{

*// Lets load the data if it doesn't alreadyexist*

if(empty($this->_data ))

{

$query = $this->_buildQuery();

$this->_data = $this->_getList($query);

}

return$this->_data;

}

完整的模型如下：

**<?php**

*/***

** Hellos Model forHello World Component*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_5*

** @license GNU/GPL*

**/*



*// Check to ensure this file is included in Joomla!*

defined('_JEXEC') or die();



jimport('joomla.application.component.model');



*/***

** Hello Model*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

**/*

**class**HellosModelHellos **extends** JModel

{

*/***

** Hellos dataarray*

***

** @var array*

**/*

**var** $_data;



*/***

** Returns thequery*

** @returnstring The query to be used to retrieve the rows from the database*

**/*

**function**_buildQuery()

{

$query = ' SELECT* '

. ' FROM #__hello '

;

return$query;

}



*/***

** Retrievesthe hello data*

** @returnarray Array of objects containing the data from the database*

**/*

**function**getData()

{

*// Lets load the data if it doesn't alreadyexist*

if(empty($this->_data ))

{

$query = $this->_buildQuery();

$this->_data = $this->_getList($query);

}

return$this->_data;

}

}

这个文件保存为models/hellos.php。



**Hellos** **视图**

我们有了一个获取数据的模型，现在，需要显示这些数据。这个视图将与网站部分的视图非常相似。

管理员部分与前端部分一样，模型会被自动实例化。我们能使用JView类的get()方法来访问模型中的以"get"[ 如getData() ]开头的方法。因此，我们有三条线：一是从模型中获取数据，一个是将数据注入模板，一个是调用显示方法来显示输出。因此，源代码如下：

**<?php**

*/***

** Hellos View forHello World Component*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_5*

** @license GNU/GPL*

**/*

*// Check to ensure this file is included in Joomla!*

defined('_JEXEC') or die();

jimport('joomla.application.component.view');



*/***

** Hellos View*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

**/*

**class**HellosViewHellos **extends** JView

{

*/***

** Hellos viewdisplay method*

** @return void*

***/*

**function**display($tpl = **null**)

{

JToolBarHelper::title( JText::_('Hello Manager'), 'generic.png');

JToolBarHelper::deleteList();

JToolBarHelper::editListX();

JToolBarHelper::addNewX();



*// Get data from the model*

$items =& $this->get('Data');



$this->assignRef('items', $items);



parent::display($tpl);

}

}

这个文件保存为views/hellos/view.html.php。

仔细看一下，与*网站*例子相比几乎隐藏的差异。在模型中，封装了存储在变量中的数据。由于使用了非常方便地_getList()，数据量非常大，因此，返回一个引用而不是值是个显而易见的需求。

处理如下：

$items =& $this->get( 'Data');

再看一下这一行，你会注意到与*网站*的view.html.php相比有另一个不同点。该模型采用隐式方式进行函数调用。真正的模型函数的名称是getData()。而在*网站*例子中，你需要用两行来完成函数调用：

$model =& $this->getModel();

$greeting = $model->getData();

现在，这两行用$this->get( 'Data')的调用方式来替代。在get()的背后，'Data'是以’get’为前缀的，因此，在使用这个函数时，要确保模型函数要以’get’开头。这个函数也能用在*网站*部分。将数据保存在模型中、并通过引用的方式来访问及使用get()方法，是从模型中获取数据的首选方式。



**Hellos** **模板**

模板嵌入来自视图的数据，并产生输出。我们在简单的表格里面显示输出。虽然，前端的模板非常简单，但在管理端，我们将需要少量的额外逻辑来处理数据循环。

下面是我们的模板：

**<?php**defined('_JEXEC') or die('Restrictedaccess'); **?>**

<form action="index.php" method="post" name="adminForm">

<div id="editcell">

<table class="adminlist">

<thead>

<tr>

<thwidth="5">

**<?php**echo JText::_('ID'); **?>**

</th>

<th>

**<?php**echo JText::_('Greeting'); **?>**

</th>

</tr>

</thead>

**<?php**

$k = 0;

foreach($this->itemsas &$row)

{

**?>**

<tr **class**="<?php echo "row$k"; ?>">

<td>

**<?php**echo$row->id; **?>**

</td>

<td>

**<?php**echo$row->greeting; **?>**

</td>

</tr>

**<?php**

$k = 1- $k;

}

**?>**

</table>

</div>



<input type="hidden" name="option" value="com_hello" />

<input type="hidden" name="task" value="" />

<input type="hidden" name="boxchecked" value="0" />

<input type="hidden" name="controller" value="hello" />



</form>

这个文件保存为views/hellos/tmpl/default.php。

你会注意到输出是一个完整的表单。尽管现在这是没有必要的，不过很快就会这样要求了。

现在，我们已经完成了第一个视图的基本部分。我们增加了5个文件到组件的管理部分：

<!--[if !supportLists]-->§ <!--[endif]-->hello.php

<!--[if !supportLists]-->§ <!--[endif]-->controller.php

<!--[if !supportLists]-->§ <!--[endif]-->models/hellos.php

<!--[if !supportLists]-->§ <!--[endif]-->views/hellos/view.html.php

<!--[if !supportLists]-->§ <!--[endif]-->views/hellos/tmpl/default.php

现在，你将这些文件添加到XML安装文件中，测试一下吧！