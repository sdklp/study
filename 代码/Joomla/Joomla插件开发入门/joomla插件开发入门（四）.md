# joomla插件开发入门（四）

 

**介绍**

在前面三个部分中，我们开发了一个从数据库表中获取数据的MVC组件。不过，除非利用其它的工具手工添加，我们现在还没有办法向数据库中添加数据。在本教程接下来的章节，我们将为组件开发管理员部分的功能，从而可以管理数据库中的记录。

**第****4****部分****–** **创建管理员界面**，本文没有为Hello组件新增源代码，但是将描述其基本细节并深入解析MVC模式。这个过渡章节并不是要完成Hello模型，因此，如果你觉得已经了解了这些基础知识，那么继续[Joomla MVC组件开发– 第五部分– 后端基本框架](http://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_5_-_Basic_Backend_Framework)。

在前端解决方案（网站部分，第1、2、3篇）中，我们开发了组件的第一部分。前端解决方案基于默认的控制器、视图和模板，并”手工修改代码”及”信任其它所有”默认的处理代码。但在Hello组件的后端或管理员部分，这些都将改变（第5、6部分）：我们将开发所有的源代码，来管理应用流程。



**网站****/** **管理**

Joomla是一个内容管理系统。其**前端**用来展示用户及内容，并允许用户登录来操作这些内容。**后端**负责管理网站的框架（结构/管理/控制/维护）。将网站内容（前端）和管理系统（后端）分开，是Joomla架构的基础。



**入口点**

在前面的示例XML文件中，管理部分显然已经存在了：

**<?xml**version="1.0"encoding="utf-8"**?>**

...

**<administration>**

*<!-- Administration Menu Section -->*

**<menu>**HelloWorld!**</menu>**

*<!-- Administration Main File Copy Section -->*

**<files**folder="admin"**>**

**<filename>**hello.php**</filename>**

**<filename>**index.html**</filename>**

**<filename>**install.sql**</filename>**

**<filename>**uninstall.sql**</filename>**

**</files>**

**</administration>**

...

在前端视图的安装中仅使用了sql文件，而其它两个文件没有什么实际内容，仅生成了空白页面。在安装com_hello组件前端后，访问网站托管提供商（或你自己的服务器）的文件系统，浏览整个目录。你会注意到Hello组件出现了两次：

<root>/components/com_hello

<root>/administrator/components/com_hello

这两个子目录分别链接到前面说明的网站内容和管理系统。管理界面显然是通过administrator子目录来访问，而来宾用户或注册用户则通过root下的默认入口进入：

<root>/index.php

管理用户必须登录，登录后访问网站通过：

<root>/administrator/index.php

采用不同的访问入口，就能很容易理解，管理员部分所遵循的命名规则与网站部分无关。MVC模式也适用于管理员部分，这就是说可以使用和网站部分相同的控制器、视图和模型命名（并且有时是必须的）。



**MVC****模式交互**

在[Joomla MVC组件开发（1）](http://docs.joomla.org/Developing_a_Model-View-Controller_Component_-_Part_1)中，右图用来解释Joomla MVC组件开发教程的前三部分。

现在，我们使用这幅图来解释如何决定将要显示哪些内容及如何处理这些内容。

![img](https://pic002.cnblogs.com/images/2011/275975/2011102117014148.png) 



**范例说明**

下面我们用一个简单的例子来解释。图书馆的主要功能是借书给注册用户。简单地罗列需要的三个表：

<!--[if !supportLists]-->§ <!--[endif]-->users

<!--[if !supportLists]-->§ <!--[endif]-->books

<!--[if !supportLists]-->§ <!--[endif]-->relation

我们使用非常简单的表结构。users表有Id和Name字段；books有Id和Title字段；relation表有IdU、IdB和Date（借书日期）字段。



![img](https://pic002.cnblogs.com/images/2011/275975/2011102117020596.png)



这个精心挑选的例子，将有助于我们解释Joomla架构的更多细节。Hello组件的管理端甚至还没有那么复杂，只有一个表要管理。说明这些之后，理解教程的后续部分就比较容易了。



**将范例映射成****JoomlaMVC****组件**

在本例中，我们将假设由管理员来执行管理任务（添加、编辑、删除）。对于前端用户，只会对relation表的视图感兴趣（”我是什么时候借书的？”）。本例将显示全部记录，并忽略所有注册用户仅能浏览自有借书的权限问题（在模板模块中创建用户的差异见：[JUser object](http://api.joomla.org/Joomla-Framework/User/JUser.html)）。

就像前面的Hello组件，library组件在前端仅使用了默认的视图。它显示relation表记录，并通过与其它两个表进行左连接，来获取用户易读的图书清单（译者注：这里指的是显示姓名和书名，而不是其Id）及相应的借阅日期。

Alice | One flew over ... | 12-aug-2009

Alice | Celeb talk | 12-aug-2009

Mark |Joomla! | 15-aug-2009

对于管理部分重要的是，要明白我们有一个默认的和三个专门的视图，每个专门的视图控制三个任务：

<!--[if !supportLists]-->§ <!--[endif]--><Component name default view>

<!--[if !supportLists]-->§ <!--[endif]-->User 管理

<!--[if !supportLists]-->§ <!--[endif]-->添加（Add）

<!--[if !supportLists]-->§ <!--[endif]-->变更（Change）

<!--[if !supportLists]-->§ <!--[endif]-->删除（Delete）

<!--[if !supportLists]-->§ <!--[endif]-->Book 管理

<!--[if !supportLists]-->§ <!--[endif]-->添加（Add）

<!--[if !supportLists]-->§ <!--[endif]-->变更（Change）

<!--[if !supportLists]-->§ <!--[endif]-->删除（Delete）

<!--[if !supportLists]-->§ <!--[endif]-->Relation 管理

<!--[if !supportLists]-->§ <!--[endif]-->添加（Add）

<!--[if !supportLists]-->§ <!--[endif]-->变更（Change）

<!--[if !supportLists]-->§ <!--[endif]-->删除（Delete）



**控制器**

管理部分的控制器最重要的是要区分添加（Adds）、变更（Changes）或删除（Deletes）的不同任务请求。这通过为每个视图创建子控制器，来处理其特定的任务。

<root>/administrator/controller.php

<root>/administrator/controllers/users.php

<root>/administrator/controllers/books.php

<root>/administrator/controllers/relation.php

控制器是MVC模式的重要组成部分。它不仅要响应请求的任务，还要初始化与之相同名字的模型实例，并负责设置视图及其需要的表单。控制器的功能就像其名字一样，是名副其实的。

在控制器内部，将**activating****任务**（例如，从菜单选择编辑）和**resulting****任务**（例如，编辑触发的结果是提交数据）区分开来是个好的习惯。

典型的控制器功能看起来如下：

**function** < activating task>() *// <-- edit, add, delete*

{

JRequest::setVar('view', '[<componentname>| users | books | relation ]');

JRequest::setVar('layout', 'default' ); *// <-- The default form is named here, but in*

*// some complex views,multiple layouts might*

*// be needed.*

parent::display();

}



**function** < resulting task>() *// <-- save, remove*

{

$model = $this->getModel('[<componentname> | users | books |relation ]');

if(!$model->action()){ *// <-- action could be delete() or store()*

$msg = JText::_('Error: Could notperform action');

}else{

$msg = JText::_('Action executed');

}



$this->setRedirect('index.php?option=<componentname>', $msg);

}

一个专门控制器负责响应其所有任务。在resulting任务完成后，模块将返回到该组件的主管理入口点，即使用默认视图的主控制器。activating任务在首次定义之后将显示一个带有表单的视图。

在视图中明确地定义表单可能会让一些人感到惊讶，不过我们的例子实在是太简单了。为了普遍的理解和一致性，字段大多数都是*默认的*。在复杂的视图中，多个表单也能定义在一个视图中。



**模型**

控制器与其同名的模型副本相互作用。在前端视图中，模型仅仅用来返回数据。后端拥有更多的控制器，因此也有更多的模型文件。后端的模型文件不仅负责将数据传输到视图，还要照顾到控制器所生成的任务。一个好的模型要包含管理数据库中单个表的所有功能。

<root>/administrator/models/<componentname>.php

<root>/administrator/models/users.php

<root>/administrator/models/books.php

<root>/administrator/models/relation.php



**视图**

当然，具有独立功能的视图也需要的。对于视图和随后的表单，并没有强制的命名规范要求（控制器只关心连接的视图）。下面的清单中，不同的视图都将管理任务作为其子集。这个选择完全是随机的，甚至没有逻辑性，不过这并不会妨碍我们的解释。仅为了举例的目的，我也添加了一个特别的视图：一个用于所有管理任务中所有删除操作的删除视图，该视图用来显示询问——”您确认删除吗？”。

<root>/administrator/views/<componentname>/view.html.php+ .../tmpl/default.php

<root>/administrator/views/users/view.html.php + .../tmpl/default.php

<root>/administrator/views/books/view.html.php + .../tmpl/default.php

<root>/administrator/views/relation/view.html.php + .../tmpl/default.php

<root>/administrator/views/delete/view.html.php + .../tmpl/default.php

*注意：*一般来说，view.html.php还需要传输内容，因而每个视图维护一个表单是种好的做法。你可以用一些简单的逻辑来开启、禁止某些内容，但是如果这些逻辑变得太复杂，就要开始考虑分拆这些视图。

*注意：*你能使用PHP的include / require，在不同视图之间共享模板的一部分（如统一的布局顶部和组件标题）。还有一个小问题——如何正确的引用共享模块？在我的模块中，有个针对通用模块的包。它用于解决视图和表单的共享，因此我将这个通用包放在了view目录。你需要在文件最前面几行的某个位置定义变量$pathToGeneralView，还需要确保路径的引用逻辑是正确的（即’..’）。在下面的例子中，用’*’标记的文件源代码如下：

./com_compname/views/general/navigate.header.php <-- sniplet code for the header

./com_compname/views/general/navigate.footer.php <-- sniplet code for the footer

./com_compname/views/mngtable1/view.html.php

./com_compname/views/mngtable1/tmpl/default.php *

./com_compname/views/mngtable2/view.html.php

./com_compname/views/mngtable2/tmpl/default.php *

$pathToGeneralView = strchr(dirname(**__FILE__**), dirname($_SERVER['SCRIPT_NAME']));

$pathToGeneralView = str_replace(dirname($_SERVER['SCRIPT_NAME']),'.',$pathToGeneralView);

$pathToGeneralView = $pathToGeneralView . "/http://www.cnblogs.com/general/"; <-- returning path from current position.

...

**<?php**require_once$pathToGeneralView . 'navigation.header.php'; **?>**

<P>Do something

**<?php**require_once$pathToGeneralView . 'navigation.footer.php'; **?>**

另外一种实现方法：

$pathToGeneralView = JPATH_COMPONENT_ADMINISTRATOR. "/views/general/"; <-- will return'path/Joomla/administrator/components/com_example/views/general/'

...

**<?php**require_once$pathToGeneralView . 'navigation.header.php'; **?>**

<P>Do something

**<?php**require_once$pathToGeneralView . 'navigation.footer.php'; **?>**

*注意：*当存在很多不同的视图时，当然要用现成的表单逻辑名来替代*默认*命名。此外，还有很多*默认*表单存在其内容难以确定的情况。但另一方面，却不能重命名view.html.php，因为一直有程序强制查找目录下的该视图名称。



**基本的交互参数**

所有东西都在合适的位置：

<!--[if !supportLists]-->§ <!--[endif]-->主要的和专用的控制器；

<!--[if !supportLists]-->§ <!--[endif]-->主要的和专用的模型；

<!--[if !supportLists]-->§ <!--[endif]-->不同视图和它们的表单。

只剩下一个大问题：**如何使用它们！**

要让Joomla正确运行，三个参数是必须的：

<!--[if !supportLists]-->§ <!--[endif]-->选项（option）

<!--[if !supportLists]-->§ <!--[endif]-->控制器（controller）

<!--[if !supportLists]-->§ <!--[endif]-->任务（task）

这三个参数几乎都是自解释的。在开发组件时，总是将组件的名字分配给*选项（**option**）*部分。对组件开发来说，认为这个参数是一个常数，当然Joomla引擎拥有更多的选项（option），而不仅仅是组件。

在组件中，*控制器（**controller**）和任务（**task**）*参数可以按照你自己的方式来使用。



**这一切如何一起工作**

看一下简化的MVC图片，Joomla逻辑交互流程按照下面的方式运行：

<!--[if !supportLists]-->1. <!--[endif]-->我的入口点是什么？

<!--[if !supportLists]-->§ <!--[endif]-->Joomla引擎发现有管理员登录，就将入口点设置为<root>/administrator/index.php，否则进入<root>/index.php入口。

<!--[if !supportLists]-->2. <!--[endif]-->请求了那个选项（option）？

<!--[if !supportLists]-->§ <!--[endif]-->Joomla引擎读取option变量，发现请求名字为<componentname>的组件。入口点变更为：<root>/administrator/components/com_<componentname>/<componentname>.php

<!--[if !supportLists]-->3. <!--[endif]-->是否需要访问专门的控制器？

<!--[if !supportLists]-->§ <!--[endif]-->Joomla引擎读取controller变量，发现请求了指定（dedicated）的控制器：<root>/administrator/components/com_<componentname>/controllers/<dedicatedcontroller>.php

<!--[if !supportLists]-->4. <!--[endif]-->是否有个任务需要解决？

<!--[if !supportLists]-->§ <!--[endif]-->指定（dedicated）的控制器获取task值作为参数。

<!--[if !supportLists]-->5. <!--[endif]-->控制器将模型传入并进行实例化。

<!--[if !supportLists]-->6. <!--[endif]-->在控制器中设置视图和表单，并进行初始化使之激活。



**如何增加交互**

在HTML中，有两种常用的方法与Joomla进行交互：

<!--[if !supportLists]-->1. <!--[endif]-->使用链接

<!--[if !supportLists]-->2. <!--[endif]-->表单提交post / get



**链接到****Joomla****引擎**

还记得前面提到的**activating****任务**和**resulting****任务**吗？大多数的activating任务都是由链接激活的。在图书馆案例中，能将网站部分的概要复制到管理部分。现在，扩展其默认视图，将每个单元格变为编辑相关数据库元素的链接。

使用图书馆案例，在不考虑循环控制的情况下，模板的PHP代码包含以下内容：

$link =JRoute::_('index.php?option=com_library&controller=users&task=edit&cid='. $row->idu);

echo"<td><a href=**\"**".$link."**\"**>$row->name</a></td>";

$link =JRoute::_('index.php?option=com_library&controller=books&task=edit&cid='. $row->idb);

echo"<td><a href=**\"**".$link."**\"**>$row->title</a></td>";

$link =JRoute::_('index.php?option=com_library&controller=relation&task=edit&cid='. $row->idr);

echo"<td><a href=**\"**".$link."**\"**>$row->date</a></td>";

在每个可点击的字段中，可以看到有三个用于控制Joomla的强制性参数。另外，还有一个用户参数（cid），表示在控制器的任务中处理内容的索引。这些参数用’&’来分隔。不要在您的变量中使用空格，因为在一些浏览器中，这会让参数处理变得一团糟。加上循环后，所有的文本项都能被点击，并触发相应的带有与关联（数据库）表一致行id的控制器。

[Alice] | [Oneflew over ...] | [12-aug-2009]

[Alice] | [Celeb talk] | [12-aug-2009]

[Mark] |[Joomla!] | [15-aug-2009]



**提交表单数据到****Joomla****引擎**

activating任务启动后，结果可能是一个输入表单视图。下面的代码片段，可能是点击默认组件视图第一个单元格后的结果，点击后进入编辑（[Alice]:cid=3）。

<form action="index.php" method="post">

<input type="text" name="username" id="username" size="32" maxlength="250" value="<?php echo $this->user->name;?>" />



<input type="submit" name="SubmitButton" value="Save" />



<input type="hidden" name="option" value="com_library" /> *// <-- mandatory*

<input type="hidden" name="controller" value="hello" /> *// <-- mandatory*

<input type="hidden" name="task" value="save" /> *// <-- mandatory*

<input type="hidden" name="id" value="<?php echo $this->user->id; ?>" /> *// <-- userparameter, index in database for [Alice]*

</form>

三个Joomla的强制参数被设置为表单的隐藏字段。隐藏参数不会以任何方式显示，但会被加入到提交数据中。

*提示：*调试表单时，将form标记中的method="post"临时替换为method="get"。所有post的参数都将显示在浏览器的URL中。在模块正式运行前，再撤销此更改。这样做的其中一个原因是，不显示这些参数，URL看起来会更加清晰，而且还可以避免激发用户自己操纵浏览器的URL。另一个原因更加技术性，简单来说就是post方法（即method="post"）能容纳更多（复杂）的数据。

*备注：*在一些已开发的模块中，你可能会注意到开发者也加入了view参数。这不是一个好的编程习惯，应该由控制器来决定*视图（**view**）*和*表单（**form**）*。



**结论**

本文阐述了三个强制性参数的使用和一些不同的访问入口。让我们利用这些知识，在本教程后续的章节中扩展Hello组件的管理部分。