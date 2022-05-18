# 细说Joomla开发插件

by Terry / Feb 29, 2012

Joomla插件对于Joomla框架是一个灵活而强大的。它不仅可以用来处理插件触发的事件的核心应用和扩展，也可使Joomla的第三方扩展具备强大的可扩展性。

所以这里细说一下插件开发的基本知识作为你开发自己插件。大多数插件组成的只是一个单一的代码文件，但能在Joomla中正确安装插件的代码，它必须被打包成一个安装文件，可以被Joomla处理的安装程序。

**如何创建插件的安装程序**

在Joomla所有插件文件中，支持.zip和.tar.gz两个类型文件包的插件，并且文件包中必须包含一个XML文件，此XML文件就类似一个插件对象，里面包含这个插件所用到的一些列属性。这里举个搜索插件中的XML文件：

Categories searchbot
Joomla! Project
02/29/2012
(C) 2012 CCJK INC. All rights reserved.
GNU/GPL admin@ccjk.com www.joomla.org 1.1 Allows searching of Categories information

categories.searchbot.php

上面的插件的XML文件结构就类似Joomla的模板的XML文件一样，包括版本、作者、版权、文件等信息。请注意几个重要的属性，一个是group，一个是filename，这两个元素的值是用来告诉Joomla复制哪些文件，并把这个插件添加到哪个插件组中。

**创建插件**

Joomla的插件开发是属于一个面向对象方式的开发，这种面向对象的开发会涉及编写JPlugin的子类，这个JPlugin是一个基类，用于实现插件的基本属性。在开发的插件的方法中，以下的属性可用：

1. $this->params: 这个是由管理员设置的属性。
2. $this->_name: 插件的名字。
3. $this->_type: 插件所属的插件组。

下面是一个插件代码的例子， 代表插件所属的插件组的名称，代表插件的名称，请注意，在PHP中的类和函数名是区分大小写的。

// no direct access
defined( ‘_JEXEC’ ) or die( ‘Restricted access’ );

// Import library dependencies
jimport(‘joomla.plugin.plugin’);

class plg extends JPlugin
{
/**
\* Constructor
\* * For php4 compatibility we must not use the __constructor as a constructor for * plugins because func_get_args ( void ) returns a copy of all passed arguments * NOT references. This causes problems with cross-referencing necessary for the * observer design pattern. */ function plg( &$subject, $config ) { parent::__construct( $subject, $config );

}
/**
\* Plugin method with the same name as the event will be called automatically.
*/
function () { $app = &JFactory::getApplication();

// Plugin code goes here.
// You can access parameters via $this->params.
return true;
}
} ?>

使用插件

插件使用起来非常简单，重要的是更好的设计，因为某种程度上来说，你在提供其他人使用的接口。你可以使用下面的代码来激活插件。$result = $mainframe->triggerEvent( ‘onSomething’, $param );这一行的程序会调用所有注册为‘onSomething’事件处理函数的插件，并传递一个$param 参数。triggerEvent 返回一个 results 数组，参数和返回结果都是可选，可以根据实际情况使用。现在，你可以试着开发一个插件了，如果你已经开发了一个插件，那如何进行调用呢？如果你不想把插件注册到Joomla中一系列的内置事件中，下面的操作可以忽略。如果你想触发这个创建，可以这么做：
$dispatcher =& JDispatcher::getInstance();
$results = $dispatcher->trigger( ”, );

值得注意的是事件参数必须写在一个数组中，插件函数本身会逐个读取这些参数。返回值是一个由所有与此事件关联的插件的返回值组成的数组(因此可能是一个多维数组)。

如果你是创建一个非核心事件的插件，记得安装好后要激活插件。