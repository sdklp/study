# Joomla源代码解析 (续一)

**JDocument类**

在google搜索我的网站就会发现一些，因为没有注意meta和title 所有的开源项目相关的页面title和meta都一样，对用户很不友好，根本无法分清那个链接是说什么内容的，那么这里就需要JDocuement类来解决问题

用法很简单，首先取得document对象 $document =& JFactory::getDocument();

然后：
$document->setTitle(*****);

$document->setDescription(*****); //添加 meta

$document->addStyleSheet(*****) //添加CSS文件

$document->addScript(*****) //添加js脚本

$document->setLanguage(***) //设置语言文件

其他还有一些 setModifiedDate 等，其他基本就不常用了，记住这些就行啦！



**JFactory****类**

正如其名，这是一个工厂类，应该是简单工厂模式的标准实现。这个类几个重要的函数分别返回几个重要的实例。

1、getACL 调用_createACL 返回 joomla.user.authorization 的实例，数据来源

2、getApplication 调用 joomla.application.application 的实例获取函数JApplication::getInstance，也就是我们在index.php中提到的$mainframework

3、getConfig 调用 _createConfig 返回 joomla.registry.registry 实例，返回配置参数

4、getDBO 调用 _createDBO，返回数据连接JDatabase的实例，这个是以后扩展模块要经常用到的

5、getDocument 调用 _createDocument 根据请求的格式，返回JDocumentRaw或者JDocumentHtml实例

6、返回 Juser 实例

7、getLanguage 调用 _createLanguage 返回 joomla.i18n.language的实例，以后在多语言环境经常用到。

其他的比如getMailer，getCache 等就不在写了



**Jdatabase类**

**
**

这是数据库connector类，封装了与数据库操作的一系列操作。目前有两个子类，JDatabaseMysql,JDatabaseMysqli，这个类为以后扩展到其他数据库留出了接口。

关于一些具体函数没有什么特殊的，基本对应mysql的函数的一些特征，对于这个类应用基本都是首先 setquery ，然后load

或者直接执行 executequery ,主要还是不要混淆load开头的几个函数的具体功能：

1、loadObject 以对象的形式返回结果集的第一行数据

2、loadObjectList 对应上一个函数，返回一个对象的集合

3、loadResult 返回第一行的第一个字段或者NULL

4、loadResultArray 返回某一字段的数据到一个数组中

5、loadRow 返回第一行数据，应该是数组形式

6、loadRowList 对应上一个函数，返回行数据的集合

还有一个函数很重要就是 setUTF ，这个函数决定了数据库能显示中文。



**Juser类**

user 类处理所有程序中与用户交互的相关事情。

这个类的构造函数调用load函数，输入的参数是用户id,可以初始化用户的相关信息，这些信息包括 aid ,gid,usertype,username,name,id等等，这些信息在扩展中经常用到。

同时，在程序中，用刚刚说过的getUser，取得当前登录用户实例。具体用法如下：

$user  =& JFactory::getUser();
$userId  = (int) $user->get('id');

根据gid 可以判断用户的相关的组以及组权限。

了解了Juser类，还需要了解一下 JUserHelper类，这个类包括了与用户活动相关的几个函数，比如获得随机密码以及取得加密密码等。

同样 getUserId 根据用户名取得用户ID 也是经常用到的。



 **JPath JFile JFolder 类**

这几个类都是filesystem包中的重要类，具体的使用看我在模块快速生成器中的代码，函数意义都非常明确：

  if(JFolder::exists($targetpath)) JFolder::delete($targetpath);
  JFolder::create($targetpath);
  JFolder::create($targetpath.DS.'tmpl');

以上是目录使用的例子

  $sfile=$sourcepath.DS.'index.html';
  $tfile=$targetpath.DS.'index.html';
  JFile::copy($sfile,$tfile);
  $files[]=$tfile;
文件拷贝

  $sfile=$sourcepath.DS.'helper.php';
  $data=JFile::read($sfile);
  $tfile = $targetpath.'/helper.php';
  JFile::write($tfile,$data);
  $files[]=$tfile;
  unset($data);

文件读取和写入

**
**

**JHtml 类**



JHtml 没有几个函数，但是在组成页面已经模板书写过程中经常用到，比如：



JHTML::_('date', $this->item->date, JText::_('DATE_FORMAT_LC5'))



以及在后台管理中常用到的



来看看这几个函数：



calendar 显示一个日历插件



date 显示格式化日期



iframe 插入一个iframe



image 插入一个图片



link 插入一个超链



以上是常用的函数，函数的以用方式就如例子。





**JToolBarHelper JToolBar 类**

这个两个类是后台管理过程经常用到的，比如：

  JToolBarHelper::title(  JText::_( '{{component}} Manager' ), 'generic.png' );

  JToolBarHelper::deleteList();

  JToolBarHelper::editListX();

  JToolBarHelper::addNewX();

这几句就添加了三个按钮，添加，删除，修改

其实还有几个常用的

preview //预览

publish //发布

cancel //取消

比较常用的就这几个了，主要是在后台管理toolbar上的按钮。相关按钮的动作对应后台管理的task，相应的对做要在controller中生命。



 **JText类**

Joomla 最常用的类之一，使用方式JTEXT::_('JJJJJ')

JJJJJ对应语言文件中的相应字符串。

为了实现多语言这个是常用的。当然如果你以utf-8字符集存储php文件，对于中文就不用考虑那么多了，不过不够规范喓。

要是只是自己用，也无所谓啦，开发要快点。

呵呵！推荐还是用吧！



**JRequest类**

这是另一个Joomla扩展中最常用的类，这个类封装了客户端提交的请求相关的信息，通过这个类你可以得到用户提交的相关信息和数据，有几个重要的函数：

首先是get($hash),我们看看部分源码就知道，get得到什么了

  switch ($hash)
  {
  case 'GET' :
   $input = $_GET;
   break;

  case 'POST' :
   $input = $_POST;
   break;

  case 'FILES' :
   $input = $_FILES;
   break;

  case 'COOKIE' :
   $input = $_COOKIE;
   break;

  case 'ENV'  :
   $input = &$_ENV;
   break;
  
  case 'SERVER'  :
   $input = &$_SERVER;
   break;

  default:
   $input = $_REQUEST;
   break;
  }

我们通过get('post') 等取得用户提交的数据数组。

还有getVar ，取得某一request变量

getURI ，返回请求的URI

setVar和set 则对应着getVar 和get

在程序中使用的方式是：JRequest::getVar('','');



**组件是如何被调用并渲染的**

Joomla代码中， 组件是如何被调用并渲染的呢？

在描述 /index.php的时候，我们看到根据option参数，$mainframework->dispatch()，就进入了组件的调用并渲染的过程，我们来看看JSite 的dispatch都做了什么工作。

dispatch 最关键的是这几句话：

  $document->setTitle( $params->get('page_title') );  //设置标题
  $document->setDescription( $params->get('page_description') ); //设置meta

  $contents = JComponentHelper::renderComponent($component);
  $document->setBuffer( $contents, 'component');

可以看到最为关键的是 JComponentHelper::renderComponent($component);

再看看这一行程序完成了那些工作

  $task = JRequest::getString( 'task' );

  // Build the component path
  $name = preg_replace('/[^A-Z0-9_\.-]/i', '', $name);
  $file = substr( $name, 4 );

  // Define component path
  define( 'JPATH_COMPONENT',   JPATH_BASE.DS.'components'.DS.$name);
  define( 'JPATH_COMPONENT_SITE',   JPATH_SITE.DS.'components'.DS.$name);
  define( 'JPATH_COMPONENT_ADMINISTRATOR', JPATH_ADMINISTRATOR.DS.'components'.DS.$name);

  // get component path
  if ( $mainframe->isAdmin() && file_exists(JPATH_COMPONENT.DS.'admin.'.$file.'.php') ) {
  $path = JPATH_COMPONENT.DS.'admin.'.$file.'.php';
  } else {
  $path = JPATH_COMPONENT.DS.$file.'.php';
  }

这部分实际上确定了那个compoent下的组件文件被引入，并取得了task,中间一部分兼容代码就不看了

我们来看关键代码：

  ob_start();
  require_once $path;
  $contents = ob_get_contents();
  ob_end_clean();

这部分代码就是包含了组件的开始文件，而这个文件，我们在组件开发的时候用到的。这个文件引入了controller 文件，并根据task决定进入那个分支。

再深入下去就是组件的整个生成过程，以后再看了。



**JTable是什么**

JTable是什么？肯定不是对应html中的table ，在做com_helloworld的时候，没有仔细理解，后来一位同事问我Jmodel,JTable,JDatabase有什么区别？一时语塞

JTable是数据库中数据表在程序中的表达，不知道这句话怎么说，其实JTable更对应着表中的一行，以及相应的操作。Joomla中的JTable**对应中数据库中 **表，我们在使用的时候要针对我们自己所使用的表扩展自己的JTable.我们需要关注的是JTable的函数checkin,checkout ,着两个函数对更新的数据进行合法性检查，我个人觉得对于数据完整性的检查应该放在Jtable的check中。

Jtable 比较常用的函数，看名字就明白了，记住几个吧：

delete,store,bind,load,setError等，具体还是需要用的时候看看源代码吧。



 JModel是什么

我们经常提到MVC模式，JModel在Joomla的MVC组件中是重要的一个环节，JModel是MVC中的数据视图层，我们需要明白的是JModel不同于JTable，数据视图是由一个或者几个table构成，或者多条数据记录构成的数据集合，以及数据集合的相关操作，对于JModel我们不必了解太多的具体函数，在组件开发过程中，通常都要继承JModel，在子类中完成数据集合的生成以及相关的操作，保存，删除。

我个人倾向对于几个表之间的数据完整性，要在JModel中验证，而对于单一表的数据完整性要通过JTable check函数完成。

同事对于那些有逻辑操作的验证则最好在MVC的 controller层完成。



接下来，我们要看看MVC中的 View 和 Control



 **Jview**

MVC模式中，重要的一环，JView 和 tmpl目录中的模板，共同决定了，页面html的代码，Jview是在Jmodel和template之间的桥梁。我们扩展做自己的组件，都需要扩展Jview的子类。这个类其实需要看看它的变量和函数也就理解：

跟数据相关的部分：

_defaultModel 默认的model ，可以通过 setModel 进行设置。同时function &get 可以从指定的model调用函数返回相应的数据

_models 存贮model的数组，getModel，可以从中返回指定的Model

assign assignref，数据赋值函数，这两个函数的任务是赋值变量给模板。

跟模板相关部分：

loadTemplate，setLayout，setLayoutExt 看名字就知道了

还有一个函数：display ,大部分的view子类都要继承这个。



 **JController**

**
**

同样 JController 是MVC中重要的起点，正式这个类决定的动作的下一步流向，我们来看看表格提交数据的典型的controller的代码：

function edit()
{
  JRequest::setVar( 'view', 'hello' );
  JRequest::setVar( 'layout', 'form' );
  JRequest::setVar('hidemainmenu', 1);

  parent::display();
}

/**
\* save a record (and redirect to main page)
\* @return void
*/
function save()
{
  $model = $this->getModel('hello');

  if ($model->store($post)) {
  $msg = JText::_( 'Greeting Saved!' );
  } else {
  $msg = JText::_( 'Error Saving Greeting' );
  }

  // Check the table in so it can be edited.... we are done with it anyway
  $link = 'index.php?option=com_hello';
  $this->setRedirect($link, $msg);
}

/**
\* remove record(s)
\* @return void
*/
function remove()
{
  $model = $this->getModel('hello');
  if(!$model->delete()) {
  $msg = JText::_( 'Error: One or More Greetings Could not be Deleted' );
  } else {
  $msg = JText::_( 'Greeting(s) Deleted' );
  }

  $this->setRedirect( 'index.php?option=com_hello', $msg );
}

/**
\* cancel editing a record
\* @return void
*/
function cancel()
{
  $msg = JText::_( 'Operation Cancelled' );
  $this->setRedirect( 'index.php?option=com_hello', $msg );
}

实际上 controller 跟提交的task参数，调用controller中的不同的函数，当然默认会调用display ，我觉得还需要记住的就是

getModel ，和setRedirect ，其余函数用到再看就可以了。