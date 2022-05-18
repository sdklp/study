# Joomla源代码解析 (续二)

**MVC组件的执行**



以前的文章中，我们曾经说过 $mainframework->dispatch 是如何最终调用组件的，通过这个dispatch，最终 include 相应组件目录下的 组件名称.php 文件，现在我们来看看，这个文件是怎么按部就班的联系了MVC模式相关的各个文件。

require_once (JPATH_COMPONENT.DS.'controller.php');

// Require specific controller if requested
if($controller = JRequest::getVar('controller')) {
require_once (JPATH_COMPONENT.DS.'controllers'.DS.$controller.'.php');
}

// Create the controller
$classname = 'HelloController'.$controller;
$controller = new $classname( );

// Perform the Request task
$controller->execute( JRequest::getVar('task'));

// Redirect if set by the controller
$controller->redirect();

其实就是根据request提交的controller参数，创建相应的JController对象，然后由controoler对象执行相应的任务。

这样我们就完全理解了，一个组件是如何被调用，MVC组件是如何执行，并最后返回html代码的。



**joomla的接口介绍**



 ***\*1. 连接数据库\****

  取得数据连接对象，采用JFactory::getDBO()方法，这样可以取得已经建立好的Joomla数据库的连接。代码如下：

>   $db = & JFactory::getDBO();

  补充：JFactory是一个静态类，getDBO为其中取得数据源的方法，$db 是一个JDatabase的实例，可以执行数据库操作。

  **2. 执行SQL语句**

  在以上获得数据库实例后，调用实例中的Excute方法直接执行数据库SQL操作。在Excute执行SQL语句一般是非查询的SQL操作，如update,delete,insert等。代码如下：

>   $db = & JFactory::getDBO();
>
>   $query = ‘DELETE FROM #__tags’;
>
>   $db->Execute($query);

  补充：使用Joomla数据库调用接口，涉及数据表的操作时，不直接用数据表实际的名字，而使用“#__表名”的格式。比如数据表在数据库中的实际名字是“jos_tags”，则在调用中名字为“#__tags”。

  \3. 查询数据

  在Joomla系统中，完成SQL查询的操作过程是，先用setQuery执行查询语句，然后调用loadObjectList取得查询结果。注意，用loadObjectList方法取得的数据集是对象数组形式的，最后对结果集进行循环处理，取得每一条记录。代码如下：

>   $db = & JFactory::getDBO();
>
>   $query = ‘ SELECT * FROM #__tags WHERE tag<>”NO_TAGS” ‘;
>
>   $db->setQuery($query);
>
>   $items = $db->loadObjectList(); // 数据结果是由对象组成的数组，每一条记录为一个对象
>
>   foreach($items as $item){
>
> ​    echo $item->tag; // 打印记录tag字段值
>
>   }

  **4. 调用session**

  Joomla框架跟PHP不一样的Session存取机制。如果在joomla下进行Session数据存取，需要用到Joomla本身的程序接口，以下代码分别演示取得session值和存储session值。代码如下：

  1）取得Session值代码如下：

>   $session = & JFactory::getSession(); //取得Joomla的Session对象
>
>   $name = “session_name”; // session的name
>
>   $s_value = $session->get($name); // 取得session_name的值

  2） 存储Session值代码如下：

>   $session = JApplication::_createSession(); // 取得Joomla的Session对象
>
>   $name = “shopMoney”; // session的name
>
>   $value = “session_value”; // session的值
>
>   $session->set($name,$value); // 存储session的值

  **5. Joomla数据库常用数据表介绍**

  1）文章分类相关数据表

​    a. jos_categories // 子类

​    b. jos_sections // 类

  补充：两个数据实体之间是一对多关系，一个session可以有多个category。

  2）Joomla的组件、模块、插件的数据保存表如下：

​    a. jos_components

​    b. jos_modules

​    c. jos_plugins

  补充：了解这几个表很重要，比如可以在安装了某个插件并启用后，突然所有的页面都不能访问了，这时候就可以进入数据库，更新刚安装插件的publish字段为“0”，通常就可以恢复了。

  3）文章存储数据表

​    jos_content

  4）用户及登录相关数据表

​    a. jos_groups // 用户组表

​    b. jos_session // 登录会话表

​    c. jos_users // 会员表

  5）菜单表

  a. jos_menu