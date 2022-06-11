## Chrome插件开发知识要点

> 本文先介绍插件的几个核心知识点，再从项目实战的角度深刻记忆它。

#### 简介

谷歌浏览器右上角的扩展插件(`Chrome Extension`)实际上是是更底层的浏览器功能扩展。`Chrome`插件是一个用Web技术开发、用来增强浏览器功能的软件，它其实就是一个由HTML、CSS、JS、图片等资源组成的一个[.crx](https://link.segmentfault.com/?enc=nyjX%2Bd70wsgEsxruaugorg%3D%3D.vjcj9U%2BSTXSkGqjAPjNa8i4B8n86rlA0IdK1p8v4XMhW6GixGqnd2yymR44InLmG)后缀的压缩包。

#### 核心介绍

##### manifest.json

这是一个Chrome插件最重要也是必不可少的文件，用来配置所有和插件相关的配置，必须放在根目录。其中，`manifest_version`、`name`、`version`3个是必不可少的，`description`和`icons`是推荐的。

下面给出的是一些常见的配置项，均有中文注释，完整的配置文档请戳[这里](https://link.segmentfault.com/?enc=yXIssH0ojR7G7P6nPobQIA%3D%3D.ADQMKBtSDmcTISpYy00ugXGz%2F%2BaawsgdqCLYxbt9%2FZHlV267JS5MheLafn3RLPj4)。

```json
{
    // 清单文件的版本，这个必须写，而且必须是2
    "manifest_version": 2,
    // 插件的名称
    "name": "demo",
    // 插件的版本
    "version": "1.0.0",
    // 插件描述
    "description": "简单的Chrome扩展demo",
    // 图标，一般偷懒全部用一个尺寸的也没问题
    "icons":
    {
        "16": "img/icon.png",
        "48": "img/icon.png",
        "128": "img/icon.png"
    },
    // 会一直常驻的后台JS或后台页面
    "background":
    {
        // 2种指定方式，如果指定JS，那么会自动生成一个背景页
        "page": "background.html"
        //"scripts": ["js/background.js"]
    },
    // 浏览器右上角图标设置，browser_action、page_action、app必须三选一
    "browser_action": 
    {
        "default_icon": "img/icon.png",
        // 图标悬停时的标题，可选
        "default_title": "这是一个示例Chrome插件",
        "default_popup": "popup.html"
    },
    // 当某些特定页面打开才显示的图标
    /*"page_action":
    {
        "default_icon": "img/icon.png",
        "default_title": "我是pageAction",
        "default_popup": "popup.html"
    },*/
    // 需要直接注入页面的JS
    "content_scripts": 
    [
        {
            //"matches": ["http://*/*", "https://*/*"],
            // "<all_urls>" 表示匹配所有地址
            "matches": ["<all_urls>"],
            // 多个JS按顺序注入
            "js": ["js/jquery-1.8.3.js", "js/content-script.js"],
            // JS的注入可以随便一点，但是CSS的注意就要千万小心了，因为一不小心就可能影响全局样式
            "css": ["css/custom.css"],
            // 代码注入的时间，可选值： "document_start", "document_end", or "document_idle"，最后一个表示页面空闲时，默认document_idle
            "run_at": "document_start"
        },
        // 这里仅仅是为了演示content-script可以配置多个规则
        {
            "matches": ["*://*/*.png", "*://*/*.jpg", "*://*/*.gif", "*://*/*.bmp"],
            "js": ["js/show-image-content-size.js"]
        }
    ],
    // 权限申请
    "permissions":
    [
        "contextMenus", // 右键菜单
        "tabs", // 标签
        "notifications", // 通知
        "webRequest", // web请求
        "webRequestBlocking",
        "storage", // 插件本地存储
        "http://*/*", // 可以通过executeScript或者insertCSS访问的网站
        "https://*/*" // 可以通过executeScript或者insertCSS访问的网站
    ],
    // 普通页面能够直接访问的插件资源列表，如果不设置是无法直接访问的
    "web_accessible_resources": ["js/inject.js"],
    // 插件主页，这个很重要，不要浪费了这个免费广告位
    "homepage_url": "https://www.baidu.com",
    // 覆盖浏览器默认页面
    "chrome_url_overrides":
    {
        // 覆盖浏览器默认的新标签页
        "newtab": "newtab.html"
    },
    // Chrome40以前的插件配置页写法
    "options_page": "options.html",
    // Chrome40以后的插件配置页写法，如果2个都写，新版Chrome只认后面这一个
    "options_ui":
    {
        "page": "options.html",
        // 添加一些默认的样式，推荐使用
        "chrome_style": true
    },
    // 向地址栏注册一个关键字以提供搜索建议，只能设置一个关键字
    "omnibox": { "keyword" : "go" },
    // 默认语言
    "default_locale": "zh_CN",
    // devtools页面入口，注意只能指向一个HTML文件，不能是JS文件
    "devtools_page": "devtools.html"
}
```

##### content-scripts

所谓[content-scripts](https://link.segmentfault.com/?enc=lbw0WOQLtMqLFH1V63gmkg%3D%3D.RRSz6yb2JEhl7mHNXx0uLzKOJe6h%2BrLUNFtS7B3XSe4brATl7XV15AV8B63hvTdzHfzxM5eNyoFoAfSmUrCFkw%3D%3D)，其实就是Chrome插件中向页面注入脚本的一种形式（虽然名为script，其实还可以包括css的），借助`content-scripts`我们可以实现通过配置的方式轻松向指定页面注入JS和CSS（如果需要动态注入，可以参考下文），最常见的比如：广告屏蔽、页面CSS定制，等等。

示例配置：

```json
{
    // 需要直接注入页面的JS
    "content_scripts": 
    [
        {
            //"matches": ["http://*/*", "https://*/*"],
            // "<all_urls>" 表示匹配所有地址
            "matches": ["<all_urls>"],
            // 多个JS按顺序注入
            "js": ["js/jquery-1.8.3.js", "js/content-script.js"],
            // JS的注入可以随便一点，但是CSS的注意就要千万小心了，因为一不小心就可能影响全局样式
            "css": ["css/custom.css"],
            // 代码注入的时间，可选值： "document_start", "document_end", or "document_idle"，最后一个表示页面空闲时，默认document_idle
            "run_at": "document_start"
        }
    ],
}
```

特别注意，如果没有主动指定`run_at`为`document_start`（默认为`document_idle`），下面这种代码是不会生效的：

```javascript
document.addEventListener('DOMContentLoaded', function()
{
    console.log('我被执行了！');
});
```

`content-scripts`和原始页面共享DOM，但是不共享JS，如要访问页面JS（例如某个JS变量），只能通过`injected js`来实现。`content-scripts`不能访问绝大部分`chrome.xxx.api`，除了下面这4种：

- chrome.extension(getURL , inIncognitoContext , lastError , onRequest , sendRequest)
- chrome.i18n
- chrome.runtime(connect , getManifest , getURL , id , onConnect , onMessage , sendMessage)
- chrome.storage

其实看到这里不要悲观，这些API绝大部分时候都够用了，非要调用其它API的话，你还可以通过通信来实现让background来帮你调用（关于通信，后文有详细介绍）。

##### background

后台（姑且这么翻译吧），是一个常驻的页面，它的生命周期是插件中所有类型页面中最长的，它随着浏览器的打开而打开，随着浏览器的关闭而关闭，所以通常把需要一直运行的、启动就运行的、全局的代码放在background里面。

background的权限非常高，几乎可以调用所有的Chrome扩展API（除了devtools），而且它可以无限制跨域，也就是可以跨域访问任何网站而无需要求对方设置`CORS`。

> 经过测试，其实不止是background，所有的直接通过`chrome-extension://id/xx.html`这种方式打开的网页都可以无限制跨域。

配置中，`background`可以通过`page`指定一张网页，也可以通过`scripts`直接指定一个JS，Chrome会自动为这个JS生成一个默认的网页：

```json
{
    // 会一直常驻的后台JS或后台页面
    "background":
    {
        // 2种指定方式，如果指定JS，那么会自动生成一个背景页
        "page": "background.html"
        //"scripts": ["js/background.js"]
    },
}
```

需要特别说明的是，虽然你可以通过`chrome-extension://xxx/background.html`直接打开后台页，但是你打开的后台页和真正一直在后台运行的那个页面不是同一个，换句话说，你可以打开无数个`background.html`，但是真正在后台常驻的只有一个，而且这个你永远看不到它的界面，只能调试它的代码。

##### event-pages

这里顺带介绍一下[event-pages](https://link.segmentfault.com/?enc=fN2SYaK95rulxF9%2FD3ptKw%3D%3D.mB7MJgg8RsJAFQN5rbzA1lFJJ3sWFYizeXz%2B8M93PuOLQCNu3f9OXne5wqhcx4aoEUVOP8dYYKlRvziayESvkw%3D%3D)，它是一个什么东西呢？鉴于background生命周期太长，长时间挂载后台可能会影响性能，所以Google又弄一个`event-pages`，在配置文件上，它与background的唯一区别就是多了一个`persistent`参数：

```json
{
    "background":
    {
        "scripts": ["event-page.js"],
        "persistent": false
    },
}
```

它的生命周期是：在被需要时加载，在空闲时被关闭，什么叫被需要时呢？比如第一次安装、插件更新、有content-script向它发送消息，等等。

除了配置文件的变化，代码上也有一些细微变化，个人这个简单了解一下就行了，一般情况下background也不会很消耗性能的。

##### popup

`popup`是点击`browser_action`或者`page_action`图标时打开的一个小窗口网页，焦点离开网页就立即关闭，一般用来做一些临时性的交互。

![博客园网摘插件popup效果](https://segmentfault.com/img/remote/1460000040715228)

`popup`可以包含任意你想要的HTML内容，并且会自适应大小。可以通过`default_popup`字段来指定popup页面，也可以调用`setPopup()`方法。

配置方式：

```javascript
{
    "browser_action":
    {
        "default_icon": "img/icon.png",
        // 图标悬停时的标题，可选
        "default_title": "这是一个示例Chrome插件",
        "default_popup": "popup.html"
    }
}
```

![img](https://segmentfault.com/img/remote/1460000040715229)

需要特别注意的是，由于单击图标打开popup，焦点离开又立即关闭，所以popup页面的生命周期一般很短，需要长时间运行的代码千万不要写在popup里面。

在权限上，它和background非常类似，它们之间最大的不同是生命周期的不同，popup中可以直接通过`chrome.extension.getBackgroundPage()`获取background的window对象。

##### injected-script

这里的`injected-script`是我给它取的，指的是通过DOM操作的方式向页面注入的一种JS。为什么要把这种JS单独拿出来讨论呢？又或者说为什么需要通过这种方式注入JS呢？

这是因为`content-script`有一个很大的“缺陷”，也就是无法访问页面中的JS，虽然它可以操作DOM，但是DOM却不能调用它，也就是无法在DOM中通过绑定事件的方式调用`content-script`中的代码（包括直接写`onclick`和`addEventListener`2种方式都不行），但是，“在页面上添加一个按钮并调用插件的扩展API”是一个很常见的需求，那该怎么办呢？其实这就是本小节要讲的。

在`content-script`中通过DOM方式向页面注入`inject-script`代码示例：

```javascript
// 向页面注入JS
function injectCustomJs(jsPath)
{
    jsPath = jsPath || 'js/inject.js';
    var temp = document.createElement('script');
    temp.setAttribute('type', 'text/javascript');
    // 获得的地址类似：chrome-extension://ihcokhadfjfchaeagdoclpnjdiokfakg/js/inject.js
    temp.src = chrome.extension.getURL(jsPath);
    temp.onload = function()
    {
        // 放在页面不好看，执行完后移除掉
        this.parentNode.removeChild(this);
    };
    document.head.appendChild(temp);
}
```

你以为这样就行了？执行一下你会看到如下报错：

```shell
Denying load of chrome-extension://efbllncjkjiijkppagepehoekjojdclc/js/inject.js. Resources must be listed in the web_accessible_resources manifest key in order to be loaded by pages outside the extension.
```

意思就是你想要在web中直接访问插件中的资源的话必须显示声明才行，配置文件中增加如下：

```javascript
{
    // 普通页面能够直接访问的插件资源列表，如果不设置是无法直接访问的
    "web_accessible_resources": ["js/inject.js"],
}
```

至于`inject-script`如何调用`content-script`中的代码，后面我会在专门的一个消息通信章节详细介绍。

##### homepage_url

![img](https://segmentfault.com/img/remote/1460000040715230)

![img](https://segmentfault.com/img/remote/1460000040715231)

#### Chrome插件的8种展示形式

##### browserAction(浏览器右上角)

通过配置`browser_action`可以在浏览器的右上角增加一个图标，一个`browser_action`可以拥有一个图标，一个`tooltip`，一个`badge`和一个`popup`。

示例配置如下：

```json
"browser_action":
{
    "default_icon": "img/icon.png",
    "default_title": "这是一个示例Chrome插件",
    "default_popup": "popup.html"
}
```

##### pageAction(地址栏右侧)

所谓`pageAction`，指的是只有当某些特定页面打开才显示的图标，它和`browserAction`最大的区别是一个始终都显示，一个只在特定情况才显示。

需要特别说明的是早些版本的Chrome是将pageAction放在地址栏的最右边，左键单击弹出popup，右键单击则弹出相关默认的选项菜单：

![img](https://segmentfault.com/img/remote/1460000040715231)

而新版的Chrome更改了这一策略，pageAction和普通的browserAction一样也是放在浏览器右上角，只不过没有点亮时是灰色的，点亮了才是彩色的，灰色时无论左键还是右键单击都是弹出选项：

![img](https://segmentfault.com/img/remote/1460000016920122)

> 具体是从哪一版本开始改的没去仔细考究，反正知道v50.0的时候还是前者，v58.0的时候已改为后者。

调整之后的`pageAction`我们可以简单地把它看成是可以置灰的`browserAction`。

- chrome.pageAction.show(tabId) 显示图标；
- chrome.pageAction.hide(tabId) 隐藏图标；

示例(只有打开百度才显示图标)：

```javascript
// manifest.json
{
    "page_action":
    {
        "default_icon": "img/icon.png",
        "default_title": "我是pageAction",
        "default_popup": "popup.html"
    },
    "permissions": ["declarativeContent"]
}

// background.js
chrome.runtime.onInstalled.addListener(function(){
    chrome.declarativeContent.onPageChanged.removeRules(undefined, function(){
        chrome.declarativeContent.onPageChanged.addRules([
            {
                conditions: [
                    // 只有打开百度才显示pageAction
                    new chrome.declarativeContent.PageStateMatcher({pageUrl: {urlContains: 'baidu.com'}})
                ],
                actions: [new chrome.declarativeContent.ShowPageAction()]
            }
        ]);
    });
});
```

效果图：

![img](https://segmentfault.com/img/remote/1460000016920123)

##### 右键菜单

通过开发Chrome插件可以自定义浏览器的右键菜单，主要是通过`chrome.contextMenus`API实现，右键菜单可以出现在不同的上下文，比如普通页面、选中的文字、图片、链接，等等，如果有同一个插件里面定义了多个菜单，Chrome会自动组合放到以插件名字命名的二级菜单里，如下：

![img](https://segmentfault.com/img/remote/1460000016920124)

最简单的右键菜单示例:

```javascript
// manifest.json
{"permissions": ["contextMenus"]}

// background.js
chrome.contextMenus.create({
    title: "测试右键菜单",
    onclick: function(){alert('您点击了右键菜单！');}
});
```

![img](https://segmentfault.com/img/remote/1460000016920125)

##### override(覆盖特定页面)

使用`override`页可以将Chrome默认的一些特定页面替换掉，改为使用扩展提供的页面。

扩展可以替代如下页面：

- 历史记录：从工具菜单上点击历史记录时访问的页面，或者从地址栏直接输入 chrome://history
- 新标签页：当创建新标签的时候访问的页面，或者从地址栏直接输入 chrome://newtab
- 书签：浏览器的书签，或者直接输入 chrome://bookmarks

注意：

- 一个扩展只能替代一个页面；
- 不能替代隐身窗口的新标签页；
- 网页必须设置title，否则用户可能会看到网页的URL，造成困扰；

代码（注意，一个插件只能替代一个默认页，以下仅为演示）：

```javascript
"chrome_url_overrides":
{
    "newtab": "newtab.html",
    "history": "history.html",
    "bookmarks": "bookmarks.html"
}
```

##### devtools(开发者工具)

使用过vue的应该见过这种类型的插件：

![img](https://segmentfault.com/img/remote/1460000016920128)

是的，Chrome允许插件在开发者工具(devtools)上动手脚，主要表现在：

- 自定义一个和多个和`Elements`、`Console`、`Sources`等同级别的面板；
- 自定义侧边栏(sidebar)，目前只能自定义`Elements`面板的侧边栏；

每打开一个开发者工具窗口，都会创建devtools页面的实例，F12窗口关闭，页面也随着关闭，所以devtools页面的生命周期和devtools窗口是一致的。devtools页面可以访问一组特有的`DevTools API`以及有限的扩展API，这组特有的`DevTools API`只有devtools页面才可以访问，background都无权访问，这些API包括：

- `chrome.devtools.panels`：面板相关；
- `chrome.devtools.inspectedWindow`：获取被审查窗口的有关信息；
- `chrome.devtools.network`：获取有关网络请求的信息；

大部分扩展API都无法直接被`DevTools`页面调用，但它可以像`content-script`一样直接调用`chrome.extension`和`chrome.runtime`API，同时它也可以像`content-script`一样使用Message交互的方式与background页面进行通信。

##### option(选项页)

所谓`options`页，就是插件的设置页面，有2个入口，一个是右键图标有一个“选项”菜单，还有一个在插件管理页面：

![img](https://segmentfault.com/img/remote/1460000040715232)

![img](https://segmentfault.com/img/remote/1460000040715233)

在Chrome40以前，options页面和其它普通页面没什么区别，Chrome40以后则有了一些变化。

我们先看老版的[options](https://link.segmentfault.com/?enc=sWqF36rg6Fsv8hVlJuD8sA%3D%3D.i5s%2BdtGmoa8QylC0VPek1kZ5nuuL5mNo%2F0qwaObbYz%2BEhoBHzsfSv0PxLIjVzaZ%2F)：

```javascript
{
    // Chrome40以前的插件配置页写法
    "options_page": "options.html",
}
```

这个页面里面的内容就随你自己发挥了，配置之后在插件管理页就会看到一个`选项`按钮入口，点进去就是打开一个网页，没啥好讲的。

效果:

![img](https://segmentfault.com/img/remote/1460000040715234)

再来看新版的[optionsV2](https://link.segmentfault.com/?enc=2j01j14vaP7ON78RJKuJlQ%3D%3D.YH6vdXKTsgUT8lvQMjmJYA2LTKFEbN1DZkbQITGwgLnS1esmWuLB2M7HSVjdVdxtakMwXXPsGO%2FZL7%2B8XiatTw%3D%3D)：

```javascript
{
    "options_ui":
    {
        "page": "options.html",
        // 添加一些默认的样式，推荐使用
        "chrome_style": true
    },
}
```

![img](https://segmentfault.com/img/remote/1460000040715235)

看起来是不是高大上了？

几点注意：

- 为了兼容，建议2种都写，如果都写了，Chrome40以后会默认读取新版的方式；
- 新版options中不能使用alert；
- 数据存储建议用chrome.storage，因为会随用户自动同步；

##### omnibox

`omnibox`是向用户提供搜索建议的一种方式。先来看个`gif`图以便了解一下这东西到底是个什么鬼：

![img](https://segmentfault.com/img/remote/1460000040715236)

注册某个关键字以触发插件自己的搜索建议界面，然后可以任意发挥了。

首先，配置文件如下：

```javascript
{
    // 向地址栏注册一个关键字以提供搜索建议，只能设置一个关键字
    "omnibox": { "keyword" : "go" },
}
```

然后`background.js`中注册监听事件：

```javascript
// omnibox 演示
chrome.omnibox.onInputChanged.addListener((text, suggest) => {
    console.log('inputChanged: ' + text);
    if(!text) return;
    if(text == '美女') {
        suggest([
            {content: '中国' + text, description: '你要找“中国美女”吗？'},
            {content: '日本' + text, description: '你要找“日本美女”吗？'},
            {content: '泰国' + text, description: '你要找“泰国美女或人妖”吗？'},
            {content: '韩国' + text, description: '你要找“韩国美女”吗？'}
        ]);
    }
    else if(text == '微博') {
        suggest([
            {content: '新浪' + text, description: '新浪' + text},
            {content: '腾讯' + text, description: '腾讯' + text},
            {content: '搜狐' + text, description: '搜索' + text},
        ]);
    }
    else {
        suggest([
            {content: '百度搜索 ' + text, description: '百度搜索 ' + text},
            {content: '谷歌搜索 ' + text, description: '谷歌搜索 ' + text},
        ]);
    }
});

// 当用户接收关键字建议时触发
chrome.omnibox.onInputEntered.addListener((text) => {
    console.log('inputEntered: ' + text);
    if(!text) return;
    var href = '';
    if(text.endsWith('美女')) href = 'http://image.baidu.com/search/index?tn=baiduimage&ie=utf-8&word=' + text;
    else if(text.startsWith('百度搜索')) href = 'https://www.baidu.com/s?ie=UTF-8&wd=' + text.replace('百度搜索 ', '');
    else if(text.startsWith('谷歌搜索')) href = 'https://www.google.com.tw/search?q=' + text.replace('谷歌搜索 ', '');
    else href = 'https://www.baidu.com/s?ie=UTF-8&wd=' + text;
    openUrlCurrentTab(href);
});
// 获取当前选项卡ID
function getCurrentTabId(callback)
{
    chrome.tabs.query({active: true, currentWindow: true}, function(tabs)
    {
        if(callback) callback(tabs.length ? tabs[0].id: null);
    });
}

// 当前标签打开某个链接
function openUrlCurrentTab(url)
{
    getCurrentTabId(tabId => {
        chrome.tabs.update(tabId, {url: url});
    })
}
```

##### 桌面通知

Chrome提供了一个`chrome.notifications`API以便插件推送桌面通知，暂未找到`chrome.notifications`和HTML5自带的`Notification`的显著区别及优势。

在后台JS中，无论是使用`chrome.notifications`还是`Notification`都不需要申请权限（HTML5方式需要申请权限），直接使用即可。

最简单的通知：

![img](https://segmentfault.com/img/remote/1460000040715237)

代码：

```javascript
chrome.notifications.create(null, {
    type: 'basic',
    iconUrl: 'img/icon.png',
    title: '这是标题',
    message: '您刚才点击了自定义右键菜单！'
});
```

#### 5种类型的JS对比

Chrome插件的JS主要可以分为这5类：`injected script`、`content-script`、`popup js`、`background js`和`devtools js`

##### 权限对比

| JS种类          | 可访问的API                                    | DOM访问情况  | JS访问情况 | 直接跨域 |
| --------------- | ---------------------------------------------- | ------------ | ---------- | -------- |
| injected script | 和普通JS无任何差别，不能访问任何扩展API        | 可以访问     | 可以访问   | 不可以   |
| content script  | 只能访问 extension、runtime等部分API           | 可以访问     | 不可以     | 不可以   |
| popup js        | 可访问绝大部分API，除了devtools系列            | 不可直接访问 | 不可以     | 可以     |
| background js   | 可访问绝大部分API，除了devtools系列            | 不可直接访问 | 不可以     | 可以     |
| devtools js     | 只能访问 devtools、extension、runtime等部分API | 可以         | 可以       | 不可以   |

##### 调试方式对比

| JS类型          | 调试方式                 | 图片说明                                                     |
| --------------- | ------------------------ | ------------------------------------------------------------ |
| injected script | 直接普通的F12即可        | 懒得截图                                                     |
| content-script  | 打开Console,如图切换     | ![img](https://segmentfault.com/img/remote/1460000040715238) |
| popup-js        | popup页面右键审查元素    | ![img](https://segmentfault.com/img/remote/1460000040715239) |
| background      | 插件管理页点击背景页即可 | ![img](https://segmentfault.com/img/remote/1460000040715240) |
| devtools-js     | 暂未找到有效方法         | -                                                            |

#### 消息通信

通信主页：[https://developer.chrome.com/...](https://link.segmentfault.com/?enc=OTlC37CLjIjudDg%2BxReXPw%3D%3D.ZC3zqzaFXKKAMM6ggirLteZOw%2FjF%2FWu8BdSVoCZV8WfdPnU9KEJTAU1vMJJdV6usotZsYq2sJwSG03TDWtw0Gg%3D%3D)

前面我们介绍了Chrome插件中存在的5种JS，那么它们之间如何互相通信呢？下面先来系统概况一下，然后再分类细说。需要知道的是，popup和background其实几乎可以视为一种东西，因为它们可访问的API都一样、通信机制一样、都可以跨域。

##### 互相通信概览

注：`-`表示不存在或者无意义，或者待验证。

|                 | injected-script                       | content-script                              | popup-js                                          | background-js                                     |
| --------------- | ------------------------------------- | ------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- |
| injected-script | -                                     | window.postMessage                          | -                                                 | -                                                 |
| content-script  | window.postMessage                    | -                                           | chrome.runtime.sendMessage chrome.runtime.connect | chrome.runtime.sendMessage chrome.runtime.connect |
| popup-js        | -                                     | chrome.tabs.sendMessage chrome.tabs.connect | -                                                 | chrome.extension. getBackgroundPage()             |
| background-js   | -                                     | chrome.tabs.sendMessage chrome.tabs.connect | chrome.extension.getViews                         | -                                                 |
| devtools-js     | chrome.devtools. inspectedWindow.eval | -                                           | chrome.runtime.sendMessage                        | chrome.runtime.sendMessage                        |

##### 通信详细介绍

###### 1. popup和background

popup可以直接调用background中的JS方法，也可以直接访问background的DOM：

```javascript
// background.js
function test()
{
    alert('我是background！');
}

// popup.js
var bg = chrome.extension.getBackgroundPage();
bg.test(); // 访问bg的函数
alert(bg.document.body.innerHTML); // 访问bg的DOM
```

> 小插曲，今天碰到一个情况，发现popup无法获取background的任何方法，找了半天才发现是因为background的js报错了，而你如果不主动查看background的js的话，是看不到错误信息的，特此提醒。

至于`background`访问`popup`如下（前提是`popup`已经打开）：

```javascript
var views = chrome.extension.getViews({type:'popup'});
if(views.length > 0) {
    console.log(views[0].location.href);
}
```

###### 2.popup或者bg向content主动发送消息

background.js或者popup.js：

```javascript
function sendMessageToContentScript(message, callback)
{
    chrome.tabs.query({active: true, currentWindow: true}, function(tabs)
    {
        chrome.tabs.sendMessage(tabs[0].id, message, function(response)
        {
            if(callback) callback(response);
        });
    });
}
sendMessageToContentScript({cmd:'test', value:'你好，我是popup！'}, function(response)
{
    console.log('来自content的回复：'+response);
});
```

`content-script.js`接收：

```javascript
chrome.runtime.onMessage.addListener(function(request, sender, sendResponse)
{
    // console.log(sender.tab ?"from a content script:" + sender.tab.url :"from the extension");
    if(request.cmd == 'test') alert(request.value);
    sendResponse('我收到了你的消息！');
});
```

双方通信直接发送的都是JSON对象，不是JSON字符串，所以无需解析，很方便（当然也可以直接发送字符串）。

> 网上有些老代码中用的是`chrome.extension.onMessage`，没有完全查清二者的区别(貌似是别名)，但是建议统一使用`chrome.runtime.onMessage`。

###### 3. content-script主动发消息给后台

content-script.js：

```javascript
chrome.runtime.sendMessage({greeting: '你好，我是content-script呀，我主动发消息给后台！'}, function(response) {
    console.log('收到来自后台的回复：' + response);
});
```

background.js 或者 popup.js：

```javascript
// 监听来自content-script的消息
chrome.runtime.onMessage.addListener(function(request, sender, sendResponse)
{
    console.log('收到来自content-script的消息：');
    console.log(request, sender, sendResponse);
    sendResponse('我是后台，我已收到你的消息：' + JSON.stringify(request));
});
```

注意事项：

- content_scripts向`popup`主动发消息的前提是popup必须打开！否则需要利用background作中转；
- 如果background和popup同时监听，那么它们都可以同时收到消息，但是只有一个可以sendResponse，一个先发送了，那么另外一个再发送就无效；

###### 4. injected script和content-script

`content-script`和页面内的脚本（`injected-script`自然也属于页面内的脚本）之间唯一共享的东西就是页面的DOM元素，有2种方法可以实现二者通讯：

1. 可以通过`window.postMessage`和`window.addEventListener`来实现二者消息通讯；
2. 通过自定义DOM事件来实现；

第一种方法（推荐）：

`injected-script`中：

```javascript
window.postMessage({"test": '你好！'}, '*');
```

content script中：

```javascript
window.addEventListener("message", function(e)
{
    console.log(e.data);
}, false);
```

第二种方法：

`injected-script`中：

```javascript
var customEvent = document.createEvent('Event');
customEvent.initEvent('myCustomEvent', true, true);
function fireCustomEvent(data) {
    hiddenDiv = document.getElementById('myCustomEventDiv');
    hiddenDiv.innerText = data
    hiddenDiv.dispatchEvent(customEvent);
}
fireCustomEvent('你好，我是普通JS！');
```

`content-script.js`中：

```javascript
var hiddenDiv = document.getElementById('myCustomEventDiv');
if(!hiddenDiv) {
    hiddenDiv = document.createElement('div');
    hiddenDiv.style.display = 'none';
    document.body.appendChild(hiddenDiv);
}
hiddenDiv.addEventListener('myCustomEvent', function() {
    var eventData = document.getElementById('myCustomEventDiv').innerText;
    console.log('收到自定义事件消息：' + eventData);
});
```

##### 长连接和短连接

其实上面已经涉及到了，这里再单独说明一下。Chrome插件中有2种通信方式，一个是短连接（`chrome.tabs.sendMessage`和`chrome.runtime.sendMessage`），一个是长连接（`chrome.tabs.connect`和`chrome.runtime.connect`）。

短连接的话就是挤牙膏一样，我发送一下，你收到了再回复一下，如果对方不回复，你只能重新发，而长连接类似`WebSocket`会一直建立连接，双方可以随时互发消息。

短连接上面已经有代码示例了，这里只讲一下长连接。

popup.js：

```javascript
getCurrentTabId((tabId) => {
    var port = chrome.tabs.connect(tabId, {name: 'test-connect'});
    port.postMessage({question: '你是谁啊？'});
    port.onMessage.addListener(function(msg) {
        alert('收到消息：'+msg.answer);
        if(msg.answer && msg.answer.startsWith('我是'))
        {
            port.postMessage({question: '哦，原来是你啊！'});
        }
    });
});
```

content-script.js：

```javascript
// 监听长连接
chrome.runtime.onConnect.addListener(function(port) {
    console.log(port);
    if(port.name == 'test-connect') {
        port.onMessage.addListener(function(msg) {
            console.log('收到长连接消息：', msg);
            if(msg.question == '你是谁啊？') port.postMessage({answer: '我是你爸！'});
        });
    }
});
```

#### 其它补充

##### 动态注入或执行JS

虽然在`background`和`popup`中无法直接访问页面DOM，但是可以通过`chrome.tabs.executeScript`来执行脚本，从而实现访问web页面的DOM（注意，这种方式也不能直接访问页面JS）。

示例`manifest.json`配置：

```javascript
{
    "name": "动态JS注入演示",
    ...
    "permissions": [
        "tabs", "http://*/*", "https://*/*"
    ],
    ...
}
```

JS：

```javascript
// 动态执行JS代码
chrome.tabs.executeScript(tabId, {code: 'document.body.style.backgroundColor="red"'});
// 动态执行JS文件
chrome.tabs.executeScript(tabId, {file: 'some-script.js'});
```

##### 动态注入CSS

示例`manifest.json`配置：

```javascript
{
    "name": "动态CSS注入演示",
    ...
    "permissions": [
        "tabs", "http://*/*", "https://*/*"
    ],
    ...
}
```

JS代码：

```javascript
// 动态执行CSS代码，TODO，这里有待验证
chrome.tabs.insertCSS(tabId, {code: 'xxx'});
// 动态执行CSS文件
chrome.tabs.insertCSS(tabId, {file: 'some-style.css'});
```

##### 获取当前窗口ID

```javascript
chrome.windows.getCurrent(function(currentWindow)
{
    console.log('当前窗口ID：' + currentWindow.id);
});
```

##### 获取当前标签页ID

一般有2种方法：

```javascript
// 获取当前选项卡ID
function getCurrentTabId(callback)
{
    chrome.tabs.query({active: true, currentWindow: true}, function(tabs)
    {
        if(callback) callback(tabs.length ? tabs[0].id: null);
    });
}
```

获取当前选项卡id的另一种方法，大部分时候都类似，只有少部分时候会不一样（例如当窗口最小化时）

```javascript
// 获取当前选项卡ID
function getCurrentTabId2()
{
    chrome.windows.getCurrent(function(currentWindow)
    {
        chrome.tabs.query({active: true, windowId: currentWindow.id}, function(tabs)
        {
            if(callback) callback(tabs.length ? tabs[0].id: null);
        });
    });
}
```

##### 本地存储

本地存储建议用`chrome.storage`而不是普通的`localStorage`，区别有好几点，个人认为最重要的2点区别是：

- `chrome.storage`是针对插件全局的，即使你在`background`中保存的数据，在`content-script`也能获取到；
- `chrome.storage.sync`可以跟随当前登录用户自动同步，这台电脑修改的设置会自动同步到其它电脑，很方便，如果没有登录或者未联网则先保存到本地，等登录了再同步至网络；

需要声明`storage`权限，有`chrome.storage.sync`和`chrome.storage.local`2种方式可供选择，使用示例如下：

```javascript
// 读取数据，第一个参数是指定要读取的key以及设置默认值
chrome.storage.sync.get({color: 'red', age: 18}, function(items) {
    console.log(items.color, items.age);
});
// 保存数据
chrome.storage.sync.set({color: 'blue'}, function() {
    console.log('保存成功！');
});
```

##### webRequest

通过webRequest系列API可以对HTTP请求进行任性地修改、定制，这里通过`beforeRequest`来简单演示一下它的冰山一角：

```javascript
//manifest.json
{
    // 权限申请
    "permissions":
    [
        "webRequest", // web请求
        "webRequestBlocking", // 阻塞式web请求
        "storage", // 插件本地存储
        "http://*/*", // 可以通过executeScript或者insertCSS访问的网站
        "https://*/*" // 可以通过executeScript或者insertCSS访问的网站
    ],
}


// background.js
// 是否显示图片
var showImage;
chrome.storage.sync.get({showImage: true}, function(items) {
    showImage = items.showImage;
});
// web请求监听，最后一个参数表示阻塞式，需单独声明权限：webRequestBlocking
chrome.webRequest.onBeforeRequest.addListener(details => {
    // cancel 表示取消本次请求
    if(!showImage && details.type == 'image') return {cancel: true};
    // 简单的音视频检测
    // 大部分网站视频的type并不是media，且视频做了防下载处理，所以这里仅仅是为了演示效果，无实际意义
    if(details.type == 'media') {
        chrome.notifications.create(null, {
            type: 'basic',
            iconUrl: 'img/icon.png',
            title: '检测到音视频',
            message: '音视频地址：' + details.url,
        });
    }
}, {urls: ["<all_urls>"]}, ["blocking"]);
```

##### API总结

比较常用用的一些API系列：

- chrome.tabs
- chrome.runtime
- chrome.webRequest
- chrome.window
- chrome.storage
- chrome.contextMenus
- chrome.devtools
- chrome.extension

## 项目实战

使用 vue cli 4 开发 chrome 插件

#### 创建 vue cli 4 项目

```bash
vue create google-extension-demo
```

勾选 `Babel`、`TypeScript`、`CSS Pre-processors`、`Linter / Formatter`

这里，CSS 预处理器随意，我选了` Sass/SCSS (with dart-sass)`。

另外，`linter / formatter` 中的` TSLint `已经不再维护，建议使用` ESLint `+ `Prettier`。
不要使用 `class-style component syntax`，见[vue-cli-plugin-chrome-ext](https://link.segmentfault.com/?enc=gd3DlZHI2PWLT1QInf%2FA2Q%3D%3D.MFe9MW0j1XQM5l5n8CuoXrJfsNplCtJ7XWfb6jRCDoi5p5CJBfQ2JsASTvDFmt9cX1Kr2eaki9MHuHpbtWvsyQ%3D%3D)。

#### 添加 chrome-ext 插件

```bash
vue add chrome-ext
```

记得这里勾选 ts:

```bash
? Name of the Chrome Extension? todolist
? Description for the Chrome Extension? chrome extension
? Version for the Chrome Extension? 0.0.1
? javascript or typescript? 
  js 
❯ ts 
```

出现了一个错误:

```bash
⠋  Running completion hooks...error: Require statement not part of import statement (@typescript-eslint/no-var-requires) at vue.config.js:1:27:
> 1 | const CopyWebpackPlugin = require("copy-webpack-plugin");
    |                           ^
  2 | const path = require("path");
  3 | 
  4 | // Generate pages object
```

这是因为 `eslint` 不允许 `commonjs` 的语法，考虑到 `vue.config.js` 是一个比较特殊的文件，我们应该把它放到 `.eslintignore` 文件中，让 `eslint` 忽略 它。

在 `.eslintignore` 中写入:

```bash
vue.config.js
```

`vue-cli-plugin-chrome-ext`` 创建不是 SPA 应用，chrome 插件有很多界面，所以是每一个界面分别编译。

比如，src/popup是一个界面，src/options也是一个界面。

这样，原先很多文件都没用了，我们可以把它们删掉，分别是 `src/main.ts`、`src/App.vue`。

#### 热重载开发

使用` npm run build-watch` 可以使用热重载的方式进行开发。

运行后会生成一个 dist 文件夹，每次你更改代码都会重新编译和加载一次。

将 dist 导入到 chrome 插件中
进入 `chrome://extensions/`，选择和加载 dist 文件夹进来。

![image-20210921181313389](https://segmentfault.com/img/remote/1460000040715242)

加载成功之后，记得去右上角管理界面开启 popup 界面 ( 新版本 chrome )。

![image-20210921181416179](https://segmentfault.com/img/remote/1460000040715243)

现在，代码打完之后就能立马在 chrome 的 popup 视图里看到渲染结果。

#### 使用 UI 组件

虽然 vue 是跑起来了，但是如果不能导入组件，那也没用。

以 `ant design vue` 为例，我门来测试一下。

```bash
npm i --save ant-design-vue
```

在 popup 界面导入

在 `src/popup/index.ts` 中添加以下内容:

```javascript
import Antd from 'ant-design-vue';
import 'ant-design-vue/dist/antd.css';
Vue.use(Antd);

Vue.config.productionTip = false;
```

前面我们说过，src文件夹下面的每一个文件夹代表了一个页面，每个页面都有对应的 index.ts(相当于main.js)、index.html、App.vue。

也就是说，我们如果要使用UI库，不是在一个全局的 main.js 里面配置一遍就好了，我们需要在每一个页面的文件夹下的 index.ts 中都导入。

这里我们仅在 popup 界面下导入了，如果你需要在 options 界面中也使用，则需要去 src/options/index.ts 下面一样导入一遍。

#### 编写一个简单的 popup 界面

使用 `ant design` 的 `card` 组件来测试一下，是否可以使用。

界面的主要代码，应该写在 `App/App.vue` 目录中。



```xml
<template>
  <div class="main_app">
    <a-card title="ghost wang">
      coding
    </a-card>
  </div>
</template>

<script lang="ts">
export default {
  name: "app"
};
</script>

<style>
.main_app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 500px;
  width: 300px;
  padding: 10px;
}
</style>
```

效果大概这样子:

![image-20210921181907717](https://segmentfault.com/img/remote/1460000040715243)