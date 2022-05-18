# joomla插件开发入门（六）

**介绍**

本文重点关注为管理员向当前的*静态*页面/文章添加功能。对于管理员，当前默认的视图是没有什么用的。它并没有做什么实质工作– 只是显示了存储在数据库中的条目。

为了让它更具实用性，我们需要添加一些按钮和链接。本文为组件扩展内容管理的任务。将要增加的典型任务是添加、变更和删除。



**增加互动**

将在两个层面添加互动。在管理员框架层面增加工具条及在文章自身层面增加链接引用和表单提交。

了解基础知识，查看[Joomla MVC组件开发– 第4部分– 创建管理员界面](http://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_4_-_Creating_an_Administrator_Interface)。



**工具条**

你可能已经注意到在其它Joomla组件的管理员面板上方出现的工具条。我们的组件同样也需要一个。Joomla做到这个非常简单。我们将添加按钮删除记录、编辑记录和创建新记录。我们也将添加一个显示在工具条上的标题。

通过向视图中增加代码来实现这些。我们使用Joomla JToolBarHelper类的静态方法来增加按钮。代码如下：

JToolBarHelper::title( JText::_('Hello Manager'), 'generic.png');

JToolBarHelper::deleteList();

JToolBarHelper::editListX();

JToolBarHelper::addNewX();

这三个方法将创建想要的按钮。deleteList()方法最多三个可选参数– 第一个参数是一个显示给用户的字符串，用来确认他们要删除记录。第二个参数是发送的查询任务（默认是'remove'）；第三个参数是显示在按钮上的文本。

editListX() 和addNewX()每个方法最多两个可选参数。第一个参数是任务（默认值分别是edit和add），第二个参数是显示在按钮上的文本。

<!--[if !supportLists]-->§ <!--[endif]-->你可能已经注意到在之前的模板中和这里都使用了JText::_ 方法。这是个方便的功能，它让组件的翻译更加容易。JText::_ 方法将查找组件语言文件中的字符串，并返回转换后的字符串。如果没有找到转换文本，则返回你传递给它的字符串。如果你要将组件翻译成其它语言，所要做的就是创建语言文件，并将引号内的字符串映射为翻译版本的字符串。



**选择框和链接**

现在，我们有了按钮。其中两个按钮用来处理已存在的记录。但是，我们怎么知道该处理那条记录呢？这需要让用户告诉我们。要做到这些，需要为表格添加选择框，从而用户能选择明确的记录。这在模板中实现。

需要向表格增加额外的列来增加选择框。我们在已存在的两列之间增加该列。

在列头，我们增加一个选择框，用来切换其下所有选择框的选中或未选中状态：

<th width="20">

<input type="checkbox" name="toggle" value="" onclick="checkAll(<?phpecho count( $this->items ); ?>);" />

</th>

Javascript函数checkAll内置在Joomla的基本Javascript包中，它提供了我们想要的功能。

现在，我们向独立的行增加选择框。Joomla的JHTML类有一个方法JHTML::_()，它将为我们生成选择框。我们向循环中增加下面的代码行：

$checked = JHTML::_('grid.id', $i, $row->id);

这行之后：

$row=& $this->items[$i];

然后，我们在已存在的两列之间中增加一个单元格：

<td>

**<?php**echo$checked; **?>**

</td>

必须先选中想要编辑的选择框，然后再向上移动点击编辑按钮，这样做不够方便。因此，我们增加一个链接，这样就会直接链接到greeting的编辑表单。我们在调用JHTML::_()方法之后，增加下面的代码行来生成HTML链接：

$link =JRoute::_('index.php?option=com_hello&controller=hello&task=edit&cid[]='. $row->id);

然后，为显示greeting文本的单元格加上链接：

<td>

<a href="<?php echo $link; ?>"><?php echo$row->greeting; ?></a>

</td>

你应该注意到了这个链接指向hello控制器。这个控制器将处理greeting的数据操作。

如果你还记得前面的代码，在表单的底部有四个隐藏的字段。第一个输入字段是'option’。这个字段是必须的，填写我们的组件名。第二个输入字段是’task’。当点击工具栏的按钮时，设置这个表单属性。如果省略了这个输入字段，会产生Javascript错误并且按钮也无法运行。第三个输入字段是’boxchecked’。这个字段一直跟随当前选中的选择框编号。编辑和删除按钮将对该值进行检查来确保它大于0，而且如果该值没有设置将不允许提交表单。第四个输入字段是’ controller’。用来指定由hello控制器来处理该表单触发的任务。

下面是完整的default.php文件源代码：

**<?php**defined('_JEXEC') or die('Restrictedaccess'); **?>**

<form action="index.php" method="post" name="adminForm">

<div id="editcell">

<table class="adminlist">

<thead>

<tr>

<thwidth="5">

**<?php**echo JText::_('ID'); **?>**

</th>

<thwidth="20">

<input type="checkbox" name="toggle" value="" onclick="checkAll(<?php echo count( $this->items );?>);" />

</th>

<th>

**<?php**echo JText::_('Greeting'); **?>**

</th>

</tr>

</thead>

**<?php**

$k = 0;

for($i=0, $n=count($this->items); $i < $n; $i++)

{

$row =& $this->items[$i];

$checked = JHTML::_('grid.id', $i, $row->id);

$link = JRoute::_('index.php?option=com_hello&controller=hello&task=edit&cid[]='. $row->id);



**?>**

<tr **class**="<?php echo "row$k"; ?>">

<td>

**<?php**echo$row->id; **?>**

</td>

<td>

**<?php**echo$checked; **?>**

</td>

<td>

<a href="<?php echo$link; ?>"><?php echo$row->greeting;?></a>

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

现在hellos视图完成了。



Getting Down and Dirty: Doing the Real Work

现在，Hellos视图已经完成，是转向Hello控制器和模型的时候了。这里，将完成真正的工作。



**Hello** **控制器**

Hello控制器工作时，我们的默认控制器不会妨碍它– 它要做的一切的就是显示视图。

我们需要能够处理从Hellos视图发起的任务：添加、编辑和删除。创建Hello控制器（位于控制器子目录）用来实际执行添加、编辑和删除单个条目。

本质上讲，添加和编辑是同样的任务：它们都是向用户显示一个编辑greeting的表单。唯一的不同是，添加显示一个空白表单，而编辑显示一个带有数据的表单。既然它们很相似，我们将添加任务映射到编辑任务的处理中。这在我们的构造函数中指定：

*/***

** constructor(registers additional tasks to methods)*

** @return void*

**/*

**function** __construct()

{

parent::__construct();

*// Register Extra tasks*

$this->registerTask('add', 'edit');

}

JController::registerTask方法的第一个参数是被映射的任务，第二个参数是映射的目标任务。

我们将开始处理编辑任务。对于编辑任务来讲，控制器的工作是相当简单。所有要做的就是指定加载的视图和布局（hello视图和表单布局）。我们也将告诉Joomla，当编辑greeting时禁用mainmenu。这能防止用户离开未保存的记录。

编辑任务的处理看起来如下：

*/***

** display the editform*

** @return void*

**/*

**function** edit()

{

JRequest::setVar('view', 'hello');

JRequest::setVar('layout', 'form' );

JRequest::setVar('hidemainmenu', 1);



parent::display();

}

注意：关于命名约定：入口调用程序(hello.php)来假定控制器的文件名和类名。针对这个列子，汇总如下：

Defaultcontroller path: …/controller.php

Defaultcontroller class name: HellosController

Requestedcontroller path …/controllers/<requestName>.php

Requestedcontroller class name: HellosController<requestName>

admin/controllers/hello.php中的控制器源代码：

**<?php**

*/***

** Hello Controllerfor Hello World Component*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_4*

** @license GNU/GPL*

**/*



*// No direct access*

defined('_JEXEC') or die('Restricted access');



*/***

** Hello HelloController*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

**/*

**class**HellosControllerHello **extends** HellosController

{

*/***

**constructor (registers additional tasks to methods)*

** @returnvoid*

**/*

**function**__construct()

{

parent::__construct();



*// Register Extra tasks*

$this->registerTask('add' , 'edit');

}



*/***

** displaythe edit form*

** @returnvoid*

**/*

**function**edit()

{

JRequest::setVar('view', 'hello');

JRequest::setVar('layout', 'form' );

JRequest::setVar('hidemainmenu', 1);



parent::display();

}

}



**Hello** **视图**

Hello视图将显示让用户编辑greeting的表单。hello视图的显示方法要做一些简单的任务：

<!--[if !supportLists]-->§ <!--[endif]-->从模型中获取数据

<!--[if !supportLists]-->§ <!--[endif]-->建立工具条

<!--[if !supportLists]-->§ <!--[endif]-->将数据注入模板

<!--[if !supportLists]-->§ <!--[endif]-->调用display()方法来渲染模板

因为要在一个视图中同时处理编辑和添加任务，这变得有点复杂。在工具条中，我们要让用户知道他们是在添加，还是编辑。因此，我们必须确定那个任务被触发了。

既然，已经从模型中取得了想要显示的数据记录，我们就能使用这个数据来决定那个任务被触发了。如果是编辑任务，那么记录的id字段会被设置。如果是添加任务，那么id字段不会设置。这可以用来确定，我们是有一个新记录，还是现有记录。

我们添加两个按钮到工具条：保存和取消。虽然功能是一样的，但是我们还是要根据是新增还是现有记录，来显示不同的按钮。如果是新增记录，我们将显示取消。如果是现有记录，我们将显示关闭。

因而，我们的显示方法如下：

*/***

** display methodof Hello view*

** @return void*

***/*

**function** display($tpl = **null**)

{

*//get the hello*

$hello =& $this->get('Data');

$isNew = ($hello->id < 1);



$text = $isNew ? JText::_('New') : JText::_('Edit');

JToolBarHelper::title( JText::_('Hello').': < small><small>[ ' . $text.']</small></small>');

JToolBarHelper::save();

if($isNew) {

JToolBarHelper::cancel();

}else{

*// for existing items the button is renamed`close`*

JToolBarHelper::cancel('cancel', 'Close');

}



$this->assignRef('hello', $hello);

parent::display($tpl);

}

admin/views/hello/view.html.php中的视图源代码：

**<?php**

*/***

** Hello View forHello World Component*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_4*

** @license GNU/GPL*

**/*



*// No direct access*

defined('_JEXEC') or die('Restricted access');

jimport('joomla.application.component.view');



*/***

** Hello View*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

**/*

**class**HellosViewHello **extends** JView

{

*/***

** displaymethod of Hello view*

** @returnvoid*

***/*

**function**display($tpl = **null**)

{

*//get the hello*

$hello =& $this->get('Data');

$isNew = ($hello->id < 1);



$text = $isNew ? JText::_('New') : JText::_('Edit');

JToolBarHelper::title( JText::_('Hello').': < small><small>[ ' . $text.']</small></small>');

JToolBarHelper::save();

if($isNew) {

JToolBarHelper::cancel();

}else{

*// for existing itemsthe button is renamed `close`*

JToolBarHelper::cancel('cancel', 'Close');

}

$this->assignRef('hello', $hello);



parent::display($tpl);

}

}



**Hello** **模型**

我们的视图需要数据。因而，我们需要创建一个模型来模拟hello。

模型将有两个属性：_id和_data。_id将保存greeting的id，而数据保存在_data中。

我们从构造函数开始，它将尝试从查询中获取id：

*/***

** Constructor thatretrieves the ID from the request*

***

** @access public*

** @return void*

**/*

**function** __construct()

{

parent::__construct();

$array = JRequest::getVar('cid', 0, '', 'array');

$this->setId((int)$array[0]);

}

JRequest::getVar()方法用来从request中获取数据。第一个参数是表单的变量名称。第二个参数是变量在未找到值时的默认赋值。第三个参数是获取数据方式的名称（如：get, post），最后一个是变量强制转换的数据类型。

构造函数将cid数组的第一个数据赋值给id。

setId()方法用来设置id值。更改模型指向的id意味着id将指向错误的数据。因此，当设置id时，我们将清除数据属性：

*/***

** Method to setthe hello identifier*

***

** @access public*

** @param int Hello identifier*

** @return void*

**/*

**function** setId($id)

{

*// Set id and wipe data*

$this->_id = $id;

$this->_data = **null**;

}

最后，我们需要一个方法来获取数据：getData()

getData将检查_data属性是否已经设置，如果已经设置了，就简单的返回该数据。否则，将从数据库中加载数据。

*/***

** Method to get ahello*

** @return objectwith data*

**/*



**function** & getData()

{

*// Load the data*

if(empty($this->_data )){

$query = ' SELECT* FROM #__hello '.

' WHEREid = '.$this->_id;

$this->_db->setQuery($query);

$this->_data = $this->_db->loadObject();

}

if(!$this->_data){

$this->_data = **new** stdClass();

$this->_data->id = 0;

$this->_data->greeting = **null**;

}

return$this->_data;

}



**表单**

现在，剩下的工作是建立用来填充数据的表单。既然，我们为表单指定了布局，那么该表单将执行位于hello视图模板目录下的form.php文件。

**<?php**defined('_JEXEC') or die('Restrictedaccess'); **?>**



<form action="index.php" method="post" name="adminForm" id="adminForm">

<div class="col100">

<fieldset **class**="adminform">

<legend><?php echo JText::_('Details');?></legend>

<table class="admintable">

<tr>

<tdwidth="100" align="right"**class**="key">

<label for="greeting">

**<?php**echo JText::_('Greeting'); **?>**:

</label>

</td>

<td>

<input **class**="text_area" type="text" name="greeting" id="greeting" size="32" maxlength="250" value="<?php echo $this->hello->greeting;?>" />

</td>

</tr>

</table>

</fieldset>

</div>



<div class="clr"></div>



<input type="hidden" name="option" value="com_hello" />

<input type="hidden" name="id" value="<?php echo$this->hello->id; ?>" />

<input type="hidden" name="task" value="" />

<input type="hidden" name="controller" value="hello" />

</form>

注意，除了输入字段，还有隐藏的id字段。用户不需要编辑该id（并且是不能编辑），所以，我们在表单中默默传递它。



**已实现的功能**

到目前为止，我们的控制器仅能处理两个任务：编辑和添加。但是，我们也有按钮来保存、删除和取消记录。我们需要编写代码来处理和执行这些任务。



**保存记录**

按照常理，下一步是实现保存记录的功能。通常，这需要一些开关和逻辑来处理不同的情况，比如创建一个新记录（INSERT查询）和更新现有记录（UPDATE查询）之间的不同。而且，从表单获取数据并放入查询语句也有些复杂。

Joomla 框架为你想到了这些。JTable类让你在不用关心SQL语句的情况下，方便地处理数据库记录。同时，HTML表单的数据转移到数据库也将变得容易。



**创建表格类**

JTable类是一个抽象类，你可以继承子类来处理指定的表。要使用它，只需要简单地创建一个JTable类的的子类，添加数据库字段作为属性，并且重写构造函数来指定表名和主键。

这是我们的JTable类：

**<?php**

*/***

** Hello Worldtable class*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

** @linkhttp://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_4*

** @license GNU/GPL*

**/*



*// No direct access*

defined('_JEXEC') or die('Restrictedaccess');



*/***

** Hello Tableclass*

***

** @package Joomla.Tutorials*

** @subpackageComponents*

**/*

**class**TableHello **extends** JTable

{

*/***

** Primary Key*

***

** @var int*

**/*

**var** $id = **null**;



*/***

** @var string*

**/*

**var** $greeting = **null**;



*/***

** Constructor*

***

** @paramobject Database connector object*

**/*

**function**__construct( &$db){

parent::__construct('#__hello', 'id', $db);

}

}

你会看到，我们定义了两个字段：id字段和greeting字段。然后，我们定义了一个构造函数，其调用了父类的构造函数，并将表名(hello)、主键的字段名(id)和数据库连接对象传递给它。

这个文件应该叫做hello.php，放在组件管理单元的tables目录下。



**在模型中实现功能**

现在，我们准备向模型里面增加一个方法来保存记录。这个方法叫做store。store()方法做三个事情：绑定表单数据到TableHello对象，检查以确保记录被正确组装，保存记录到数据库。

这个方法如下：

*/***

** Method to storea record*

***

** @access public*

** @return boolean True on success*

**/*

**function** store()

{

$row =& $this->getTable();



$data = JRequest::get('post');

*// Bind the form fields to the hello table*

if(!$row->bind($data)){

$this->setError($this->_db->getErrorMsg());

return**false**;

}



*// Make sure the hello record is valid*

if(!$row->check()){

$this->setError($this->_db->getErrorMsg());

return**false**;

}



*// Store the web link table to the database*

if(!$row->store()){

$this->setError($this->_db->getErrorMsg());

return**false**;

}



return**true**;

}

这个方法被添加到hello模型。

这个方法接受一个参数：我们要存储到数据库的数据组成的数组。稍后将会看到，从request中获取数据很容易。

你会看到，第一行返回一个JTable对象的引用。如果表格命名正确，我们不需要指定它的名字– JModel类知道到哪里去找到它。你可能还记得，我们将表格类叫做TableHello，并把该类放到了tables目录下的hello.php文件中。如果遵循该约定，JModel类能自动创建你的对象。

第二行从表单中获取数据。用JRequest类实现非常容易。在本例中，我们获取用'POST'方法提交的所有变量。这些变量以关系数组形式返回。

剩下的简单– 绑定、检查和存储。[bind()](http://docs.joomla.org/API15:JTable/bind)方法将数组的数据复制到对应表格对象的属性。在本例中，将获取的id和greeting的值，复制到TableHello对象。

[check()](http://docs.joomla.org/API15:JTable/check)方法将执行数据校验。在JTable()类中，这个方法简单地返回true。虽然，现在没有为我们提供任何价值，但是在将来使用TableHello类时，我们就可以通过调用这个方法进行数据检查。在TableHello类中，我们可以重写该方法，来执行适合的检查。

[store()](http://docs.joomla.org/API15:JTable/store)方法将对象中的数据存储到数据库。如果id是0，它将创建一个新记录(INSERT)，否则它将更新现有记录(UPDATE)。



**向控制器添加任务**

现在，我们准备向控制器中添加任务了。既然，我们触发的任务叫做'save'，那么我们的方法必须被命名为'save'。这很简单：

*/***

** save a record(and redirect to main page)*

** @return void*

**/*

**function** save()

{

$model = $this->getModel('hello');



if($model->store()){

$msg = JText::_('Greeting Saved!');

}else{

$msg = JText::_('Error Saving Greeting');

}



*// Check the table in so it can be edited....we are done with it anyway*

$link = 'index.php?option=com_hello';

$this->setRedirect($link, $msg);

}

我们所要做的就是获取模型，并调用store()方法。然后，我们使用setRedirect()方法，重定向返回到greeting列表。我们也顺便传递一个信息，以显示在页面的顶部。



**删除记录（在模型中实现该功能）**

在模型中，我们检索要删除的ID列表，并调用JTable类来删除它们。

具体如下：

*/***

** Method to deleterecord(s)*

***

** @access public*

** @return boolean True on success*

**/*

**function** delete()

{

$cids = JRequest::getVar('cid', array(0), 'post', 'array');

$row =& $this->getTable();



foreach($cidsas$cid){

if(!$row->delete($cid)){

$this->setError($row->getErrorMsg());

return**false**;

}

}

return**true**;

}

我们调用JRequest::getVar()方法从request中获取数据，然后调用$row->delete()方法删除每一行。我们可以在模型中保存错误消息，如果需要，可以在后面再获取它们。



**在控制器中处理删除任务**

这和处理保存任务的save()方法类似：

*/***

** remove record(s)*

** @return void*

**/*

**function** remove()

{

$model = $this->getModel('hello');

if(!$model->delete()){

$msg = JText::_('Error: One or MoreGreetings Could not be Deleted');

}else{

$msg = JText::_('Greeting(s) Deleted');

}

$this->setRedirect('index.php?option=com_hello', $msg);

}



**取消编辑操作**

我们为了取消编辑操作，所要做的就是重定向返回到主视图：

*/***

** cancel editing arecord*

** @return void*

**/*

**function** cancel()

{

$msg = JText::_('Operation Cancelled');

$this->setRedirect('index.php?option=com_hello', $msg);

}



**结论**

现在，我们已经实现了一个基本的组件后端。我们能够编辑前端显示的条目。我们也展示了模型、视图和控制器之间的互动。我们还展示了如何扩展JTable类，来方便地访问数据库中的表。还可以看到如何使用JToolBarHelper来创建组件的按钮栏，让组件变得标准化。