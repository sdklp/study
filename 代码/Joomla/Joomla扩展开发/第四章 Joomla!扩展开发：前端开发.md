# 第四章 Joomla!扩展开发：前端开发

在第二章里，我们访问 http://localhost/joomla/index.php?option=com_reviews，页面与下图相似：

**暂时不提供图片显示，请参考《Joomla! extension development》**

我们将会在页面显示带有超链接的点评列表，所以我们要预先在后端加上一些点评的数据。首先，在 /component/com_reviews/reviews.php 文件中插入以下的代码：

jimport('joomla.application.helper');

require_once( JApplicationHelper::getPath( 'html' ) );

JTable::addIncludePath(JPATH_ADMINISTRATOR.DS.

​         'components'.DS.$option.DS.'tables');

switch($task)

{

default:

  showPublishedReviews($option);

  break;

}

function showPublishedReviews($option)

{

$db =& JFactory::getDBO();

$query = "SELECT * FROM #__reviews WHERE

​     published = '1' ORDER BY review_date DESC";

$db->setQuery( $query );

$rows = $db->loadObjectList();

if ($db->getErrorNum())

{

  echo $db->stderr();

  return false;

}

HTML_reviews::showReviews($rows, $option);

}

与后端的方法相似，require_once( JApplicationHelper::getPath( 'html' ) ); 导进文件reviews.html.php，传递 JPATH_ADMINISTRATOR.DS.'components'.DS.$option.DS.'tables' 到 Jtable::addIncludePath(); 导进数据表类。最后 switch() 语句设置了默认的 case 来显示所有发布的点评。这里的 SQL 语句确保只有发布的点评被选择并且以点评日期的倒序来排序。在刷新页面之前，我们需要在 /component/com_reviews/reviews.html.php 中加入 HTML_reviews 类：

<?php

class HTML_reviews

{

function showReviews($rows, $option)

{

  ?><table><?php

 foreach($rows as $row)

  {

   $link = 'index.php?option=' .

​             $option . '&id=' . $row->id . '&task=view';

   echo

'<tr>

  <td>

      <a href="' . $link . '">' . $row->name . '</a>

  </td>

</tr>';

  }

  ?></table><?php

}

}

?>

showReview() 函数接收一个数据对象的行和一个组件名为参数，使用 <table> 来显示数据，在 <tr> 作循环显示数据结果并在每一行加入链接，保存文件后刷新页面，你会看到相似的页面：

**暂时不提供图片显示，请参考《Joomla! extension development》**


显示一个点评

如果你没有写任何的代码来处理 “view” “task”，当你单击一条链接后，你看到的是相同的页面，在views.php 中加入以下代码：

function viewReview($option)

{

$id = JRequest::getVar('id', 0);

$row =& JTable::getInstance('review', 'Table');

$row->load($id);

if(!$row->published)

{

  JError::raiseError( 404, JText::_( 'Invalid

​                  ID provided' ) );

}

HTML_reviews::showReview($row, $option);

}

先，用 getVar() 获取请求的 id，它能检查变量的各种攻击。外部的数据处理要很小心，特别是处理公共访问的网站的数据，在我们的代码中统一使用 getVar() 将会提供一个合理的安全层。如果 id 的值丢失或者不合法，第二个参数 0 将会作为默认值提供给 id。

然后，我们从后端得到一个数据表类，加载相应 id 的记录后，我们检查选择的记录是否被发布，如果不是发布的，我们用 JError 的成员函数 raiseError() 来输出找不到信息的 404 页面：

**暂时不提供图片显示，请参考《Joomla! extension development》**

这个检查确保不让用户随便输入 id 来获取点评，而且如果记录不存在也会显示以上的页面。viewReview() 函数会做所有的事情来加载请求的点评，但是我们仍然需要加入代码来调用这个函数，加入下面的高亮代码到 switch() 中：

switch($task)

{

**case 'view':**

  **viewReview($option);**

  **break;**

default:

  showPublishedReviews($option);

  break;

我们也需要在我们的输出类创建一个显示函数，在 reviews.html.php 文件中加入 showReview() 函数到 HTML_reviews：

function showReview($row, $option)

{

 

?>

<p class="contentheading"><?php echo $row->name; ?></p>

<p class="createdate"><?php echo JHTML::Date

​               ($row->review_date); ?></p>

<p><?php echo $row->quicktake; ?></p>

<p><strong>Address:</strong> <?php echo $row->address; ?></p>

<p><strong>Cuisine:</strong> <?php echo $row->cuisine; ?></p>

<p><strong>Average dinner price:</strong> $<?php echo

​              $row->avg_dinner_price; ?></p>

<p><strong>Credit cards:</strong> <?php echo

​                $row->credit_cards; ?></p>

<p><strong>Reservations:</strong> <?php echo

​                $row->reservations; ?></p>

<p><strong>Smoking:</strong> <?php

  if($row->smoking == 0)

  {

   echo "No";

  }

  else

  {

   echo "Yes";

  }

?></p>

<p><?php echo $row->review; ?></p>

<p><em>Notes:</em> <?php echo $row->notes; ?></p>

<?php $link = 'index.php?option=' . $option ; ?>

<a href="<?php echo $link; ?>">&lt; return to the reviews</a>

<?php

}

showReview() 函数传进一个数据库行对象和组件的名字作为参数，这行记录的大部分字段都以HTML格式显示，也包含许多逻辑。Smoking 字段陪赋给合适的 Yes 或者 No 值。调用 JHTML::Date() 来格式化从数据库取出来的时间戳。最后，显示一个可以返回点评列表的链接，保存文件后，再次点击点评的链接，你会看到以下相似的页面：

**暂时不提供图片显示，请参考《Joomla! extension development》**

 

创建搜索引擎友好链接

现在浏览我们的点评的链接(http://localhost/joomla/index.

php?option=com_reviews&id=1&task=view&Itemid=1)出现了很长的 GET 字符串，用户肯定都会讨厌看到这么长的链接，更重要的是这样的链接对于搜索引擎收集我们的网站是没有帮助的，搜索引擎对这样的链接更友好：http://www.ourdomain.com/reviews/view/1，为了实现这样的链接，我们定义了一个路由来产生和解析 Search-Engine Friendly (SEF) 即搜索引擎友好的链接，去到后台的菜单“网站 | 配置”，将“搜索引擎友好URL”设置为 Yes，如果你使用的是 Apache 作为你的 Web 服务器并且启用了 mod_rewrite 模块，你也能够设置“使用 mod_rewrite”为 Yes，它会在你的URL中删除 index.php，你所做的设置应该像下图：

**暂时不提供图片显示，请参考《Joomla! extension development》**

如果你不能设置 mod_rewrite 模块，那么你的URL会像这样：http://www.yoursite.com/index.php/search/engine/friendly/link。

​    保存你刚才所做的设置，假设你是设置了 mod_rewrite 模块，然后重命名 .htaccess.txt 为 .htaccess，如果你看到提示说你的配置不能写，那么打开根目录下的 configuration.php 文件，设置 $sef 成员变量的值为1即可。


创建URL段

在 Joomla! 的组件和模块中创建内部链接的时候会调用 Jroute::_() 函数，这个函数将链接作为参数并且返回一个 SEF （搜索引擎友好）的链接。要创建 SEF 链接，JRoute::_() 首先将链接分析成数组，然后删除 option 元素并将它的值加到新的 URL 的第一段，这函数会在与 option 的值相同目录内寻找 router.php，如果找到，将文件包含进来并调用以组件名开头以 BuildRoute() 结束的函数，我们这个例子是调用 ReviewsBuildRoute()。要创建 ReviewsBuildRoute() ，打开/component/com_reviews 目录，创建 router.php 文件，然后加入以下的代码：

<?php

defined( '_JEXEC' ) or die( 'Restricted access' );

function ReviewsBuildRoute(&$query)

{

$segments = array();

if (isset($query['task']))

{

  $segments[] = $query['task'];

unset($query['task']);

}

if(isset($query['id']))

{

  $segments[] = $query['id'];

  unset($query['id']);

}

return $segments;

}

?>

Jroute::_() 函数决定了正在处理的链接是餐厅的点评，ReviewBuildRoute() 函数会被调用并传递一个分析URL后返回的数组参数。为了创建SEF链接，我们需要返回URL段的有序数组。首先，赋值一个空数组给变量 $segments；下一步检查数组 $query 是否存在 task 元素，如果存在，我们把 task 的值加入到 $segments 中的第一个元素，然后将它从 $query 中删除；下一步我们对 id 做相同的操作；最后，我们返回 $segments 以便 JRoute::_() 能创建 URL。

要得到正确的 SEF URL ，这个函数的编写有两个很重要的方法要注意的，首先，变量 $query 要以引用传递（在变量前加上 &）。因为我们创建段时，我们会从 $query 中删除已经处理的元素，任何在 $query 中剩下来的元素都会被处理回到 URL 中，也就是会以原来的 GET 元素出现在 URL。如果我们没有以引用来传递，使用 unset() 只会影响局部的拷贝，URL中所有的 elements 都会出现在 SEF $segments 后面。既然 SEF URL 没有方法可以识别元素的值，那么唯一的方法就是靠我们预先定义好的顺序来做映射。当返回 $segments，Jroute::_() 从它返回每个元素，然后以斜线分隔加到 URL。如果在 $query 中有剩下来的元素会以 GET 方式加到 URL后面。

我们已经有 router.php 来生成 SEF URL，但是我们的组件输出函数还没有使用。打开/components/com_reviews/reviews.html.php 文件并注意 HTML_reviews的成员函数 showReviews() 中的高亮代码：

foreach($rows as $row)

{

**$link = JRoute::_('index.php?option=' . $option . '&id=' .**

​           **$row->id . '&task=view');**

echo '<tr><td><a href="' . $link . '">' .

​             $row->name . '</a></td></tr>';

}

同样也注意 HTML_reviews::showReview() 中的高亮代码：

<p><em>Notes:</em> <?php echo $row->notes; ?></p>

**<?php $link = JRoute::_('index.php?option=' . $option); ?>**

<a href="<?php echo $link; ?>">&lt; return to the reviews</a>

现在，组件会根据我们在 ReviewsBuildRoute() 设定的模式来生成 SEF URL。



分析URL段

如果你现在想点击一条点评，那么你会看到类似这样的信息： "Fatal error: Call to undefned function reviewsParseRoute()"，我们需要一个能分析URL的函数。回到/components/com_reviews/router.php 并加入一下的函数：

function ReviewsParseRoute($segments)

{

$vars = array();

$vars['task'] = $segments[0];

$vars['id'] = $segments[1];

return $vars;

}

Joomla! 收到页面的请求后，它会调用 BuildParseRoute() 并传递一个相关的URL 段的数组参数。首先，初始化一个 $vars 数组来存贮将要返回的变量，然后设置vars的元素 task 和 id 的值，分别与 $segments的第一和第二段的值对应，最后返回这个数组。以这种方式，对剩下来的代码整个路由的过程都是透明的。

现在可以点击页面的点评链接，注意他们的URL会像这样：

http://www.oursite.com/reviews/view/1 或者

http://www.oursite.com/index.php/reviews/view/1 如果URL像这样

http://www.oursite.com/component/reviews/view/1 这只是一个 non_SEF URL

 

添加评论

我们说某间餐厅很好（或者不好），大多数访客都会相信我们的话。然而，可能有一些会不同意的。为什么不给他们一个机会评论一下他们在这间餐厅的经验呢？我们需要一个地方来存贮他们的评论，根据一下的SQL命令来操作：

CREATE TABLE 'jos_reviews_comments' (

'id' int(11) NOT NULL auto_increment,

'review_id' int(11) NOT NULL,

'user_id' int(11) NOT NULL,

'full_name' varchar(50) NOT NULL,

'comment_date' datetime NOT NULL,

'comment_text' text NOT NULL,

PRIMARY KEY ('id')

)

如果你想用 phpMyAdmin 也可以：

**暂时不提供图片显示，请参考《Joomla! extension development》**

**暂时不提供图片显示，请参考《Joomla! extension development》**

**暂时不提供图片显示，请参考《Joomla! extension development》**

我们将加入另一个数据库类来处理基本的功能。既然我们已经在 administrator/components/

com_reviews/tables 中有点评类了，那么在这里再增加第二个。创建 comment.php，加入以下的代码：

<?php

defined('_JEXEC') or die('Restricted access');

class TableComment extends JTable

{

var $id = null;

var $review_id = null;

var $user_id = null;

var $full_name = null;

var $comment_date = null;

var $comment_text = null;

function __construct(&$db)

{

  parent::__construct( '#__reviews_comments',

​                  'id', $db );

}

}

?>

我们已经建立地方来存贮评论了，现在需要增加一个表单来让访客来填写。打开 reviews.html.php 文件并在HTML_reviews中加入以下的函数代码：

function showCommentForm($option, $review_id, $name)

{

?>

<br /><br />

<form action="index.php" method="post">

<table>

  <tr>

   <td>

​    <strong>Name:</strong>

   </td>

   <td>

​    <input class="text_area" type="text" name="full_name"

​     id="full_name" value="<?php echo $name; ?>" />

   </td>

  </tr>

 <tr>

   <td>

​    <strong>Comment:</strong>

   </td>

   <td>

​    <textarea class="text_area" cols="20" rows="4"

​      name="comment_text" id="comment_text"

​      style="width:500px"></textarea>

   </td>

  </tr>

</table>

<input type="hidden" name="review_id"

   value="<?php echo $review_id; ?>" />

<input type="hidden" name="task"

   value="comment" />

<input type="hidden" name="option"

   value="<?php echo $option; ?>" />

<input type="submit" class="button" id="button"

   value="Submit" />

</form>

<?php

}

ShowCommentForm() 带有三个参数，分别是组件名、点评的id和名字。如果用户已经登录了，那么名字就自动填充到表单的名字栏，如果没有登录，那么访客就要手工填写。还有一个返回点评组件（及点评列表）的链接。为了确保评论对应到正确的点评，我们传递了当前的点评id review_id，我们想将评论显示在点评的下面，所以在 reviews.php 中加入以下的高亮的代码：

if(!$row->published)

{

JError::raiseError( 404, JText::_( 'Invalid ID provided' ) );

}

HTML_reviews::showReview($row, $option);

**$user =& JFactory::getUser();**

**if($user->name)**

**{**

**$name = $user->name;**

**}**

**else**

**{**

**$name = '';**

**}**

**HTML_reviews::showCommentForm($option, $id, $name);**

 

调用输出 html 函数之前，我们需要取得当前登录的用户名（如果有登录的话）。保存文件后刷新页面，如果你在前端登录过，你的屏幕应该显示类似的页面（如果没有登录那么名字一栏显示为空）：

**暂时不提供图片显示，请参考《Joomla! extension development》**

当我们填写和提交表单之前，我们需要加入处理输入数据和插入到数据库。在 reviews.php 文件中加入以下的高亮代码：

switch($task)

{

case 'view':

  viewReview($option);

  break;

**case 'comment':**

 **addComment($option);**

  **break;**

default:

  showPublishedReviews($option);

  break;

}

然后再同一文件中加入 addCommnet() 函数：

function addComment($option)

{

global $mainframe;

$row =& JTable::getInstance('comment', 'Table');

if (!$row->bind(JRequest::get('post')))

{

  echo "<script> alert('".$row->getError()."');

​          window.history.go(-1); </script>\n";

  exit();

}

$row->comment_date = date( 'Y-m-d H:i:s' );

$user =& JFactory::getUser();

if($user->id)

{

  $row->user_id = $user->id;

}

if (!$row->store())

{

  echo "<script> alert('".$row->getError()."');

​         window.history.go(-1); </script>\n";

  exit();

}

$mainframe->redirect('index.php?option=' .

​         $option . '&id=' . $row->review_id .

​         '&task=view', 'Comment Added.');

}

大部分的代码我们都应该看起来很熟悉。我们得到一个当前用户的引用，将用户的ID写进数据库。目前，我们允许注册用户和匿名的评论，但现在记录这些信息有助于我们以后跟踪注册用户。当访客没有登录，$user 为空，user_id 字段默认为0。保存数据之前，设置 comment_date 为当前日期和时间。

 

显示评论

保存你的代码后，你就可以提交表单和返回点评页，但是，页面不会显示任何已经发表的评论。在其它的网站，你经常会看到评论会直接跟在内容的下面，接着就是添加更多评论的表单。我们会沿用这种方式，在 reviews.php 中加入以下高亮的代码以从数据库中返回所有的评论并循环显示出来：

HTML_reviews::showReview($row, $option);

$db =& JFactory::getDBO();

**$db->setQuery("SELECT \* FROM #__reviews_comments**

​            **WHERE review_id = '$id'");**

**$rows = $db->loadObjectList();**

**foreach($rows as $row)**

**{**

**HTML_reviews::showComment($row);**

**}**

$user =& JFactory::getUser();

也要在 reviews.html.php 中加入相应输出单条的评论的函数：

function showComment($row)

{

?>

<br /><br />

<p><strong><?php echo $row->full_name;

?></strong> <em><?php

​     echo JHTML::Date($row->comment_date);

​     ?></em></p>

<p><?php echo $row->comment_text; ?></p>

<?php

}

一旦你加入了一条或者两条评论后，刷新点评的详细页面，你应该会看到类似以下的页面：