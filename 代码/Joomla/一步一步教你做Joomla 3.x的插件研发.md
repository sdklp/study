# 一步一步教你做Joomla 3.x的插件研发

[字号+](javascript:;) 编辑: 呆头鹅甲 修订: 呆头鹅甲 来源: [原创](https://www.wkwkk.com/articles/e5c99cd7f6dc1e28.html) 2018-03-04 16:53 [ 我要说两句](https://www.wkwkk.com/articles/e5c99cd7f6dc1e28.html#comment-district)([0](https://www.wkwkk.com/articles/e5c99cd7f6dc1e28.html#comment-district))

网上大多数关于joomla插件开发的文章都是1.5的, 连pdf文档都是1.5的, 早已经过时, 这里记录一下joomla 3.x版本的插件开发流程。

# 如何添加组件到joomla?

首先需要初始化一个基本的joomla组件，我们先把这个组件命名为test, 如何初始化？有很多途径，但只需要学一种，其他的就无所谓了。

现在我们学的是通过把代码打包成zip，并上传到joomla后台extension管理里面自动展开的办法。

# 初始化组件

先随便找个地方建个目录，名称叫com_test, 然后添加以下文件和目录,

其中 ./ 代表com_test目录自身, 里面包含的目录(例如site)需要我们自己手动新建:

- ./test.xml
- ./site/test.php
- ./site/index.html
- ./admin/index.html
- ./admin/test.php
- ./admin/sql/index.html
- ./admin/sql/updates/index.html
- ./admin/sql/updates/mysql/index.html
- ./admin/sql/updates/mysql/0.0.1.sql



以上文件里面有很多的index.html, 实际就是防人员输入目录名直接遍历到目录结构, 现在的一键安装包环境一般都把目录结构给禁了, 但为了兼容咱们笔记里的xml文档, 最好还是建立一下, 免得报错。



紧接着我们说说文件内容, 按照文件路径一个一个用sublime之类的编辑器(本站就有下载)把代码内容填进去:

./test.xml

```
<?xml version="1.0" encoding="utf-8"?>
<extension type="component" version="3.0" method="upgrade">
 
    <name>test</name>
    <!-- The following elements are optional and free of formatting constraints -->
    <creationDate>March 2018</creationDate>    John Doe</author>    john.doe@wkwkk.com</authorEmail>  http://www.wkwkk.com</authorUrl>
    <copyright>Copyright Info</copyright>  License Info</license>
    <!--  The version string is recorded in the components table -->
    <version>0.0.1</version>
    <!-- The description is optional and defaults to the name -->
    <description>Description of the test component ...</description>    <!-- Runs on update; New since J2.5 -->
        <schemas>
            <schemapath type="mysql">sql/updates/mysql</schemapath>
        </schemas>
    </update>
 
    <!-- Site Main File Copy Section -->
    <!-- Note the folder attribute: This attribute describes the folder
        to copy FROM in the package to install therefore files copied
        in this section are copied from /site/ in the package -->
    <files folder="site">
        <filename>index.html</filename>
        <filename>test.php</filename>
    </files>  
        <!-- Administration Menu Section -->
        <menu link=index.php?option=com_test>test</menu>
        <!-- Administration Main File Copy Section -->
        <!-- Note the folder attribute: This attribute describes the folder
            to copy FROM in the package to install therefore files copied
            in this section are copied from /admin/ in the package -->
        <files folder="admin">
            <!-- Admin Main File Copy Section -->
            <filename>index.html</filename>
            <filename>test.php</filename>
            <!-- SQL files section -->
            <folder>sql</folder>
        </files>
    </administration>
 
</extension>
```

./site/test.php

```
这里是test前台主文件
```

./admin/test.php

```
这里是test后台主文件
```

所有com_test下面的index.html全部留空白。

## 插件的打包和安装

接下来把这些代码打包成com_test.zip, 在压缩时把压缩方案选择成直接存储, 也就是不压缩, 然后上传到后台当中菜单的Extensions->manage->install界面, 上传安装即可。

# 追加一个前台视图和基本控制器给组件

joomla的组件也遵循了mvc的套路,不懂mvc的兄弟姐妹没关系, m模型c控制器v视图, mvc这种概念性的东西, 先了解到这里就行, 这里说一下v也就是View视图层的追究方法。

首先我们修改./site/test.php的内容, 代码替换成:

```
<?php
// No direct access to this file
defined(_JEXEC) or die(无效访问);
 
// Get an instance of the controller prefixed by Test
$controller = JControllerLegacy::getInstance(Test);
 
// Perform the Request task
$input = JFactory::getApplication()->input;
$controller->execute($input->getCmd(task));
 
// Redirect if set by the controller
$controller->redirect();
```

观察这个文件头部，其中这段代码

```
defined(_JEXEC) ``or` `die``(无效访问);
```

出现在好多文件的头部, 目的是为了防止黑客从外部浏览器敲链接直接访问, 尽管这是一种比较落后的防止被黑客玩弄的方式, 但joomla一直热衷使用, 对付着用吧。



接下来创建./site/controller.php, 也就是控制器文件, 代码内容为:

```
<?php
// No direct access to this file
defined(_JEXEC) or die(无效访问);
/**
 * Test Component Controller
 *
 * @since  0.0.1
 */
class TestController extends JControllerLegacy
{
}
```

接下来创建./site/views/test/view.html.php, 它是一个视图处理文件, 代码如下:

```
<?php
// No direct access to this file
defined(_JEXEC) or die(无效访问);
 
/**
 * HTML View class for the HelloWorld Component
 *
 * @since  0.0.1
 */
class TestViewTest extends JViewLegacy
{
    /**
     * Display the Test view
     *
     * @param   string  $tpl  The name of the template file to parse; automatically searches through the template paths.
     *
     * @return  void
     */
    function display($tpl = null)
    {
        // Assign data to the view
        $this->msg = 这里是Test视图里插入的msg变量内容;
 
        // Display the view
        parent::display($tpl);
    }
}
```

创建./site/views/test/tmpl/default.php, 这就是视图模板, 代码如下:

```
<?php
// No direct access to this file
defined(_JEXEC) or die(无效访问);
?>
<h1><?php echo $this->msg; ?></h1>
```

当然了, test.xml也发生了三行变化, 以下是代码:

```
<?xml version="1.0" encoding="utf-8"?><extension type="component" version="3.0" method="upgrade">
 
    <name>Hello World!</name>
    <!-- The following elements are optional and free of formatting constraints -->
    <creationDate>January 2018</creationDate>  John Doe</author>    john.doe@example.org</authorEmail>    http://www.example.org</authorUrl>
    <copyright>Copyright Info</copyright>  License Info</license>
    <!--  The version string is recorded in the components table -->   <version>0.0.2</version>    <!-- The description is optional and defaults to the name -->
    <description>Description of the Hello World component ...</description>    <!-- Runs on update; New since J2.5 -->
        <schemas>
            <schemapath type="mysql">sql/updates/mysql</schemapath>
        </schemas>
    </update>
 
    <!-- Site Main File Copy Section -->
    <!-- Note the folder attribute: This attribute describes the folder     to copy FROM in the package to install therefore files copied     in this section are copied from /site/ in the package -->
    <files folder="site">
        <filename>index.html</filename>
        <filename>helloworld.php</filename>     <filename>controller.php</filename>     <folder>views</folder>  </files>  
        <!-- Administration Menu Section -->
        <menu link=index.php?option=com_helloworld>Hello World!</menu>
        <!-- Administration Main File Copy Section -->
        <!-- Note the folder attribute: This attribute describes the folder         to copy FROM in the package to install therefore files copied         in this section are copied from /admin/ in the package -->
        <files folder="admin">
            <!-- Admin Main File Copy Section -->
            <filename>index.html</filename>
            <filename>helloworld.php</filename>
            <!-- SQL files section -->
            <folder>sql</folder>
        </files>
    </administration>
</extension>
```



# 建立joomla后台管理用的插件基本控制器、视图和模型

以我们当前的插件test举例，首先要改动**插件在后台的入口文件**，那么./admin/test.php的代码需要修改成以下的样子：

```
<?php
// No direct access to this file
defined(_JEXEC) or die(无效访问);
 
// Get an instance of the controller prefixed by Test
$controller = JControllerLegacy::getInstance(Test);
 
// Perform the Request task
$controller->execute(JFactory::getApplication()->input->get(task));
 
// Redirect if set by the controller
$controller->redirect();
```

回看上文的代码，觉得很前台的代码似乎一模一样对吧。

### 初始化后台控制器

基本控制器的路径是./admin/controller.php, 代码修改如下：

```
<?php
// No direct access to this file
defined(_JEXEC) or die(无效访问);
 
/**
 * General Controller of Test component
 *
 * @package     Joomla.Administrator
 * @subpackage  com_helloworld
 * @since       0.0.7
 */
class TestController extends JControllerLegacy
{
    /**
     * The default view for the display method.
     *
     * @var string
     * @since 12.2
     */
    protected $default_view = Tests;
}
```

注意这个Tests，是默认显示的视图名称，关联到后面的控制器名称尾巴和视图文件夹名称。

### 初始化后台视图

笔者认为joomla的视图实现的比较麻烦，现在新式的开发框架，display()函数已经封装好并嵌入到框架内核当中，开发者提交控制器变量给模板变量，display(模板名称)这种写法，用一行语句来发起就是了，不会像joomla这么费劲。

首先要写视图渲染方法文件，路径是./admin/views/tests/view.html.php，代码修改如下：

```
<?php
// No direct access to this file
defined(_JEXEC) or die(无效访问);
 
/**
 * Test View
 *
 * @since  0.0.1
 */
class TestViewTests extends JViewLegacy
{
    /**
     * Display the Test view
     *
     * @param   string  $tpl  The name of the template file to parse; automatically searches through the template paths.
     *
     * @return  void
     */
    function display($tpl = null)
    {
        // Get data from the model
        $this->items     = $this->get(Items);
        $this->pagination    = $this->get(Pagination);
 
        // Check for errors.
        if (count($errors = $this->get(Errors)))
        {
            JError::raiseError(500, implode(, $errors));
 
            return false;
        }
 
        // Display the template
        parent::display($tpl);
    }
}
```

其次就是写视图模板文件，路径是./admin/views/tests/tmpl/default.php, 代码如下：

```
<?php
// No direct access to this file
defined(_JEXEC) or die(无效访问);
?>
<form action="index.php?option=com_test&view=tests" method="post" id="adminForm" name="adminForm">    
        <thead>       
            <th width="1%"><?php echo JText::_(COM_TEST_NUM); ?></th>
            <th width="2%">
                <?php echo JHtml::_(grid.checkall); ?>
            </th>
            <th width="90%">
                <?php echo JText::_(COM_TEST_TESTS_NAME) ;?>
            </th>
            <th width="5%">
                <?php echo JText::_(COM_TEST_PUBLISHED); ?>
            </th>
            <th width="2%">
                <?php echo JText::_(COM_TEST_ID); ?>
            </th>     
        </thead>
        <tfoot>           
                    <?php echo $this->pagination->getListFooter(); ?>                
        </tfoot>      
            <?php if (!empty($this->items)) : ?>
                <?php foreach ($this->items as $i => $row) : ?>                 
                        <?php echo $this->pagination->getRowOffset($i); ?>                       
                    <?php echo JHtml::_(grid.id, $i, $row->id); ?>                        
                    <?php echo $row->greeting; ?>                       
                    <?php echo JHtml::_(jgrid.published, $row->published, $i, tests., true, cb); ?>                        
                    <?php echo $row->id; ?>                     
                <?php endforeach; ?>
            <?php endif; ?>     
  </form>
```

上文代码当中有一些奇怪的常量，例如COM_TEST_TESTS_NAME，是语言输出常量。因为本文求的是实用简练，没写这套东西，实际开发当中可以换成我们想要的，或者直接把一些文本名称写死，不用搞这么复杂。Joomla官方的学习路线比较诡异，生生要让学习者先学会i18n也就是国际化，实际是没有什么好处的，我们先把最基本的功能实现了，再实现周边的才是正确的学习方法。

笔者在这里提醒读者，这个模板只是一个样本，实际开发当中，joomla的方法不必一个一个都学会，按自己需要的来。