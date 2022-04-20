jdoc 聲明會包含在每個 Joomla 的[佈景主題](https://docs.joomla.org/Special:MyLanguage/Template)中，並指定 Joomla 的部分或其它套件應該放置於整個網頁中的哪個位置。一個標準的 jdoc 聲明應該要像這樣：`<jdoc:include type="component" />`



## 目录



- 1 jdoc:include
  - 1.1 類型(type)屬性
    - 1.1.1 元件
    - 1.1.2 頁頭
    - 1.1.3 安裝
    - 1.1.4 訊息
    - 1.1.5 模組
    - 1.1.6 多個模組
  - 1.2 樣式屬性
- 2 另請參考



## jdoc:include

`<jdoc:include />` 聲明是 Joomla! 佈景主題用來指定目前瀏覽的頁面中要如何顯示內容的一種方法。在此有各種不同的 `<jdoc:include />` 聲明，每種都會在 Joomla! 頁面中導出不同的部分。至於替換本身則是在 `JDocumentHTML::_renderTemplate` 中完成。另見 `_parseTemplate`。

### 類型(type)屬性

`type` 屬性是用來指定 `<jdoc:include />` 元素中要使用的類型。例如，`<jdoc:include ***type="head"*** />` 聲明使用了 `head` 的 `type` 屬性（`type="head"`）。（*注意*：Jdoc 的寫法要求使用雙引號來包住屬性，如果錯誤地使用了單引號將無法正常作用。在最後面使用 /> 時，前面要先加一個半形空白才能完整關閉整句語法。）

#### 元件

```
<jdoc:include type="component" />
```

此元素只會在佈景主題的 <body> 中出現一次而已，它是用來呈現目前瀏覽頁面中的主要內容。

#### 頁頭

```
<jdoc:include type="head" />
```

此元素只會在佈景主題的 <head> 中出現一次而已，它是用來產生目前瀏覽頁面中所指定使用的 CSS 樣式、JavaScript腳本以及 meta 元素等。

#### 安裝

```
<jdoc:include type="installation" />
```

此元素只會使用於 Joomla [![Joomla 2.5](../../../我的文档/Typora/Pictures/Compat_icon_2_5.png)](https://docs.joomla.org/File:Compat_icon_2_5.png) 與之後版本的 Joomla! 安裝佈景主題，且在前台與後台佈景主題中沒有特定的用途。其實它有點等同於 '元件' 類型了，它會在安裝步驟中呈現主要內容。

#### 訊息

```
<jdoc:include type="message" />
```

此元素只會在佈景主題的 <body> 中出現一次而已，它是用來呈現請求發生的系統訊息和錯誤訊息。

至於系統訊息的 CSS 樣式可以至此路徑找到： templates\system\css\system.css

#### 模組

```
<jdoc:include type="module" name="breadcrumbs" title="Breadcrumbs" />
<jdoc:include type="module" name="mainmenu" title="Main Menu" />
```

此元素呈現一個由`name` 和 `title`屬性來指定的模組：`name` 要符合模組類型(例如 mod_breadcrumbs 和 mod_menu)； `title` 是模組名稱。 為了讓模組能被看到，模組必需要被發佈，並對目前的用戶是可使用的。如果有的話，額外的屬性可以控制模組的排版以及顯示效果。

#### 多個模組

多個模組 (modules)會使用類似以下的範例程式碼，呈現在頁面上。多個模組會基於`templatedetails.xml` 檔案中設定的模組位置，切割成不同的區域。使用 `jdoc:include`當中的 `name="*[模組位置名稱]*"` 屬性，可以在不同的位置呼叫出不同的模組，呈現並分別有不同的樣式設定。如果有的話，額外的屬性可以控制模組的排版以及顯示效果。

以下是Joomla! 佈景主題開發者常用的一些模組聲明的範例，裡頭有模組位置。

```
<jdoc:include type="modules" name="debug" />
<jdoc:include type="modules" name="icon" />
<jdoc:include type="modules" name="left" style="rounded" />
<jdoc:include type="modules" name="left" style="xhtml" />
<jdoc:include type="modules" name="right" style="xhtml" />
<jdoc:include type="modules" name="status"  />
<jdoc:include type="modules" name="syndicate" />
<jdoc:include type="modules" name="title" />
<jdoc:include type="modules" name="toolbar" />
<jdoc:include type="modules" name="top" />
<jdoc:include type="modules" name="top" style="xhtml" />
<jdoc:include type="modules" name="user1" style="xhtml" />
<jdoc:include type="modules" name="user2" style="xhtml" />
<jdoc:include type="modules" name="user3" />
<jdoc:include type="modules" name="user4" />
```

*註記：*`name="user3"`的模組位置通常（預設值）會被使用在上方選單。

### 樣式屬性

在模組(`module`) 和 多個模組(`modules`)類型的聲明中，還可以選擇性地使用 `style=""` 屬性。在模組輸出到頁面上時 ，將會使用這個屬性值當做外層區塊 [模組區塊](https://docs.joomla.org/Special:MyLanguage/Module_chrome) 的樣式。如果沒有提供這個值，預設會使用"`none`"。

佈景主題設計師可能會新增額外的「模組區塊 (module chrome)」名稱，請參考 [自訂模組區塊(module chrome)](https://docs.joomla.org/Special:MyLanguage/Applying_custom_module_chrome)