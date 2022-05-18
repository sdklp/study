# 第二章 Joomla 扩展开发：入门

在你进行编码之前，有一些文件和文件夹需要创建和一些查询语句需要运行。你不但可以创建组件而且不用额外的配置就可以尝试不同的特性。你也可以看到Joomla!组织和访问组件方法的概况。最后，你会像其它组件一样加入工具栏。

Joomla!组件的结构

Joomla!的所有组件都遵守指定的命名约定。每个系统组件都有唯一的名字，名字不要包括空格。代码分成两个文件夹，文件夹以com_开头，紧接着就是组件的名字。因此，你要创建两个相同名字的com_reviews文件夹，一个放到前端components下，另一个放到后端administrator/components 下。当组件被前端加载的时候，Joomla!将会查找以组件唯一命名并以 .php 扩展名结束的文件。在components/com_reviews 下建立review.php文件。相似地，在后端建立的文件需要在前面加上 admin. ，在administrator/components/com_reviews 下建立 admin.reviews.php。

执行组件

Joomla!前端所有的请求都经过根目录的 index.php 文件，加载不同的组件是通过设置 URL GET 的option 变量。假设你本地的joomla!站点地址是 http://localhost/joomla/index.php，那么你加载的组件的地址应该是 http://localhost/joomla/index.php?option=reviews，打开reviews.php 文件并加入以下代码：

<?php

defined( '_JEXEC' ) or die( 'Restricted access' );

echo '<div class="componentheading">Restaurant Reviews</div>';

?>

你会看到类似的页面：

**暂时不提供图片显示，请参考《Joomla! extension development》**

你可能想知道一开始调用 defined() 函数的目的是什么，这是为了确保避免代码被直接通过 components/com_reviews/reviews.php 来访问。

在后端的administrator/components/com_reviews/admin.reviews.php 文件加上以下代码：

<?php

defined( '_JEXEC' ) or die( 'Restricted access' );

echo 'Restaurant Reviews';

?>

浏览地址 http://localhost/joomla/administrator/index.php?option=com_reviews ，比较页面的输出：

**暂时不提供图片显示，请参考《Joomla! extension development》**

Joomla!前后端的分离

Joomla! 的所有组件，它们的代码使得后端部分与前端部分的代码很好地分离，在某些情况下，例如数据库表类，后端会使用前端的某些文件，但它们是独立的。当你不让后端的函数混入前端的代码那么安全性就加强了。这是后端和前端的结构相似的同时的一个很重要的特性。以下显示了Joomla! 的根目录和administrator 文件夹展开的图表：



**暂时不提供图片显示，请参考《Joomla! extension development》**

要注意的是 administrator 文件夹与根目录有相似的结构。区分它们俩是很重要的，否则你可能会将你的代码放错位置了而执行失败，除非是将它们放回正确的位置。



在数据库注册组件

你现在知道怎么样访问前端和后端的组件，尽管每次你都能够通过手工输入URL来执行你的代码，但你的用户你无法接受的。如果你在数据库注册了组件，即在components数据表中加入一条记录，那么你就可以使用导航了。使用以下的SQL语句来注册组件：

**INSERT INTO jos_components (name, link, admin_menu_link,**

**admin_menu_alt, `option`, admin_menu_img, params)**

**VALUES ('Restaurant Reviews', 'option=com_reviews',**

**'option=com_reviews', 'Manage Reviews', 'com_reviews',**

**'js/ThemeOffice/component.png', '');**



这里声明了组件的名称，可以包括空格和标点，可以指定前端和后端的链接，可以指定后端组件菜单的图标。当你建立了基本的目录并加入了文件，有的组件已经准备好被执行了，而不需要写任何的SQL语句。这样你就在后端加入了组件的链接，也可以在前端适当的位置加入链接而不需要硬编码URL。刷新你后端的页面，下拉组件菜单，你会看到你的组件的子菜单项：

**暂时不提供图片显示，请参考《Joomla! extension development》**



既然组件已经注册了，你就可以在前端创建链接，去到 “菜单” | “主菜单”，然后单击“新建”按钮，从该页面中选择“Restaurant Reviews”，输入链接名称后，如下图：

**暂时不提供图片显示，请参考《Joomla! extension development》**



点击“保存”，然后去到前端，你应该看到“Reviews”链接：

**暂时不提供图片显示，请参考《Joomla! extension development》**



你可以准备你的PHP技巧开始编写组件了。还要确保所有的前端请求都要通过 http://localhost/joomla/index.php?option=com_views，后端的请求通过 http://localhost/joomla/administrator/index.php?option=com_reviews。

Joomla!是非常灵活的，可以让你做你喜欢做的事情。我们这个例子中，会教你从新建一个组件开始，然后设计工具栏、用户、数据库类和库等，一旦你理解了它们的工作原理，这些元素将会省下你大量的时间。



创建工具栏

在Joomla!的后端，所有的核心组件都实现相同的保存、删除、编辑和发布项目等按钮，你可以在你的组件中使用这些按钮以便管理员会有无缝的体验。首先，在administartor/components/com_reviews 文件夹下创建 toolbar.reviews.html.php 文件，并输入一下代码：

<?php

defined( '_JEXEC' ) or die( 'Restricted access' );

class TOOLBAR_reviews {

function _NEW() {

JToolBarHelper::save();

JToolBarHelper::apply();

JToolBarHelper::cancel();

}

function _DEFAULT() {

JToolBarHelper::title( JText::_( 'Restaurant Reviews' ),

'generic.png' );

JToolBarHelper::publishList();

JToolBarHelper::unpublishList();

JToolBarHelper::editList();

JToolBarHelper::deleteList();

JToolBarHelper::addNew();

}

}

?>



包括输出代码的文件通常会组织成类，像这里的 TOOLBAR_reviews。每个成员函数都会显示不同的工具栏。JtoolBarHelper 类包含了所有创建工具栏HTML元素的函数，你也可以加入自定义的HTML。你需要明白的是工具栏是有HTML表格构建的，你可能想在你的导航上加入 <td> 标签。

工具栏现在被定义，但你需要加入一些代码来决定显示哪些按钮。Joomla!会自动加载以组件名开始，以 .reviews.php 结束的文件。加入以下的代码到 administrator/components/com_reviews 下的 toolbar.reviews.php 文件：



<?php

defined( '_JEXEC' ) or die( 'Restricted access' );

require_once( JApplicationHelper::getPath( 'toolbar_html' ) );

switch($task)

{

case 'edit':

case 'add':

TOOLBAR_reviews::_NEW();

break;

default:

TOOLBAR_reviews::_DEFAULT();

break;

}

?>

 

这行包含 require_once()，使用 JapplicationHelper 类的成员函数 getPath() 来获取 toolbar.reviews.php 文件的路径，而不用包括组件的名称，即使你改变了组件的名称，你不需要修改代码还是可以正常加载文件。

 

说明：

你可能想知道为什么一开始就创建 toolbar.reviews.php 和 toolbar.reviews.html.php 这两个文件。组件开发人员首选的编码风格是让处理逻辑与输出完全分离，这样以后加入特性和与别人分享代码就非常容易了。

 

toolbar.reviews.php 用输出类加载文件后，你需要要决定显示哪个工具栏。请求的变量 $task 会自动注册成为全局变量并有来导向组件的逻辑流。现在刷新后端的页面，进入 Restaurant Reviews 组件，然后你应该能看到以下的工具栏：

**暂时不提供图片显示，请参考《Joomla! extension development》**

要看其它的工具栏，在URL后面加上 &task=add ，重新加载页面，你应该看到以下的工具栏：

**暂时不提供图片显示，请参考《Joomla! extension development》**



当你的用户要使用你的组件的时候，他们当然不想自己手动地在URL后添加 task 变量，那么他们怎么才能使用第二个工具栏呢？每个工具栏都对应着不同的 task ，当一个按钮被点击，相关的 task 就会加入到表单中并自动提交。

一旦适合的表单在适合的位置时，单击“新建”按钮会看到第二个工具栏，既然我们没有任何的表单在后端，这些工具栏按钮是不会工作的。下一章将会教你怎么让这些按钮生效。



有效的工具栏按钮

Joomla!允许你使用自己的 task 和 label 覆盖任何的按钮，分别传入第一个和第二个参数来覆盖。以下是Joomla!标准版本提供的有效的按钮：

**暂时不提供图片显示，请参考《Joomla! extension development》**



说明：

如果你想创建想核心按钮一样的自定义按钮，可以使用 JtoolBarHelper 的成员函数 custom() ，并传递 task、icon、mouse-over 图片和文本描述作为参数。