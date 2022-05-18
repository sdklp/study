# Joomla源代码解析



**一、目录结构**

测试网站搭建完毕，首先来熟悉一下，1.5RC3版的目录结构：

1、componets 所有安装的组件的，前端用户界面相关程序都在这里，每个组件一个子目录，通常是com_***

2、plugins 所有的插件，涉及的程序文件和模板文件，资源等等

3、modules 所以安装的模块相关的程序和资源

4、includes 涉及重要的类，定义等等

5、languages，下面是所有的语言文件，新的规范是一种语言一个目录，比如英文就是en-GB，中文简体就是zh-CN，目录下是相应的语言定义文件，命名规则是 语种.组件名字（插件名字）.ini ，比如 zh-CN.com_showcase.ini zh-CN.plg-***.ini等等。

6、templates 没说的，一种模板一个目录

7、library 最重要的目录之一了，下面都是重要的类文件，子目录结构对应类名称，比如

jimport( 'joomla.environment.uri' );

  那么对应的是 joomla/environment/uri.hp 文件

其他的cache,logs目录就不说了，以后用的到。



**二、入口文件**



documentroot/index.php ，以及 template/***/index.php ，可以称为万源之源，因为可以说所有的页面都是这两个文件的成果。

/index.php 是所有页面程序的起点，让我们来看看这个文件到底做了什么？



define( '_JEXEC', 1 );  //标志这是一个根文件

define('JPATH_BASE', dirname(__FILE__) ); //取得Document root,就是 /index.php所在的绝对路径
define( 'DS', DIRECTORY_SEPARATOR ); // 定义目录分隔符

require_once ( JPATH_BASE .DS.'includes'.DS.'defines.php' ); //defines.php定义了一些目录变量，以后详细的写
require_once ( JPATH_BASE .DS.'includes'.DS.'framework.php' );

//framework.php是另一个非常重要的文件，在framework.php读入了config.php中定义的变量，同时
//framework中引入了一些的基础类，例如JFactory,JUser等等

//全局对象,工厂类JFactory,JFactory符合设计模式中的工厂模式，基本生成的对象大部分是单例模式，接下来我详细描述JFactory，JFactory 在/libraries/joomla/factory.php中定义，
$mainframe =& JFactory::getApplication('site');

//取得JApplication 对象，JApplication是一个工厂类，提供了一些指定对象的生成，并提供了一系列的api函数

//application初始化过程，设置语言，并缺的editor的设置，并生成Juser对象
$mainframe->initialise();

//引入system 组的插件
JPluginHelper::importPlugin('system');

// 触发初始化完毕后定义的pluging响应事件
$mainframe->triggerEvent('onAfterInitialise');

//route()函数，根据url生成进行解析，设置JRequest 
$mainframe->route();

// authorization
$Itemid = JRequest::getInt( 'Itemid');
$mainframe->authorize($Itemid);

//触发route后plugin
JDEBUG ? $_PROFILER->mark('afterRoute') : null;
$mainframe->triggerEvent('onAfterRoute');

//根据JRequest的的option参数，dispatch到那个组件，也就决定页面的内容部分是那个组件生成
$option = JRequest::getCmd('option');
$mainframe->dispatch($option);

//触发dispatch后的plugin
JDEBUG ? $_PROFILER->mark('afterDispatch') : null;
$mainframe->triggerEvent('onAfterDispatch');

//页面的渲染过程，生成整个页面html
$mainframe->render();

// trigger the onAfterDisplay events
JDEBUG ? $_PROFILER->mark('afterRender') : null;
$mainframe->triggerEvent('onAfterRender');

echo JResponse::toString($mainframe->getCfg('gzip'));

以上是 /index.php的内容，从这个index.php的引出了几个重要的文件需要我们去注意

/includes/defines.php
/includes/framework.php
/libraries/joomla/application.php
/libraries/joomla/factory.php

接下来我们主要看看这些文件。

**三、defines.php**

**
**



其实这个文件没什么好说的，主要就是定义一些路径，贴出来，主要是以后文件中提这些路径的时候，有一个印象

$parts = explode( DS, JPATH_BASE );

//Defines
define( 'JPATH_ROOT',  implode( DS, $parts ) );

define( 'JPATH_SITE',  JPATH_ROOT );
define( 'JPATH_CONFIGURATION', JPATH_ROOT );
define( 'JPATH_ADMINISTRATOR', JPATH_ROOT.DS.'administrator' );
define( 'JPATH_XMLRPC',   JPATH_ROOT.DS.'xmlrpc' );
define( 'JPATH_LIBRARIES', JPATH_ROOT.DS.'libraries' );
define( 'JPATH_PLUGINS',  JPATH_ROOT.DS.'plugins'  );
define( 'JPATH_INSTALLATION', JPATH_ROOT.DS.'installation' );
define( 'JPATH_THEMES'  , JPATH_BASE.DS.'templates' );
define( 'JPATH_CACHE',  JPATH_BASE.DS.'cache');

这些路径在以后的文件中经常用到。



**四、framework.php**



/include/framework.php 这个文件在index.php中是最早引入的文件之一，这个文件主要实现了一些基本类的引入，下面我们逐一看一下：

require_once( JPATH_LIBRARIES  . DS . 'loader.php' ); //loader.php 是一个载入类的基本工作，最重要的是Jimport
比如 jimport( 'joomla.environment.response'  ); 实际上就include_once /libaries/jooma/environment/response.php


require_once( JPATH_CONFIGURATION . DS . 'configuration.php' ); //引入了configuration.php

jimport( 'joomla.base.object' );
jimport( 'joomla.environment.request' );
JRequest::clean(); //清空Jrequest

// System configuration
$CONFIG = new JConfig(); //读取配置文件，生成Jconfig对象

if (JDEBUG) {
jimport( 'joomla.utilities.profiler' );
$_PROFILER =& JProfiler::getInstance( 'Application' );
}

以上这段主要是如果配置处于debug状态，那么就生成$_PROFILER,这个对象主要要用来，记录页面执行到某一节点的执行时间，内存状态调试信息。


jimport( 'joomla.environment.response'  );
jimport( 'joomla.application.menu' );   //needs to be loaded later
jimport( 'joomla.user.user');
jimport( 'joomla.environment.uri' );
jimport( 'joomla.factory' );
jimport( 'joomla.methods' );
jimport( 'joomla.html.html' );    //needs to be loaded later
jimport( 'joomla.utilities.array' );    //needs to be loaded later
jimport( 'joomla.utilities.error' );
jimport( 'joomla.utilities.utility' );
jimport( 'joomla.utilities.string' );  //needs to be loaded later
jimport( 'joomla.filter.output' );
jimport( 'joomla.version' );   //needs to be loaded later
jimport( 'joomla.event.*');

这些引入的类中，比较重要的是 factory user menu ，需要仔细研究一下，其他的用的时候，再仔细看文档就行啦,

当然，methods也包含了很多基本的函数和类，尤其是需要了解SEF的时候。

接下来，我们将仔细看看/index.php中 $mainframe =& JFactory::getApplication('site') 这句话到底完成了什么工作。