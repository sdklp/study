**C# 在word中查找及替换文本**

在处理word文档时，很多人都会用到查找和替换功能。尤其是在处理庞大的word文档的时候，Microsoft word的查找替换功能就变得尤为重要，它不仅能让我们轻易地查找到整篇文章里的

某些文字、词语或者句子，还可以选择替换搜索到的这些文本。这些在Microsoft word中都很容易实现。但对于开发者来说，可能更需要通过编程的方式来实现它，这种方式较于直接在word

中的操作更为复杂。接下来就分享一下如何使用免费.NET API以C#编程的方式在word文档中实现查找和替换功能。在下面的示例中我使用的是Spire.Doc。

**免费版Spire.Doc简单介绍**

免费版Spire.Doc是一个独立的word API，可以使编程者在任意.NET平台上对word文档进行操作，如新建、读、写、保存、打印和转换word文档等。

在开始前，请先下载并安装[Spire.Doc](http://www.e-iceblue.com/Download/download-word-for-net-free.html)软件，然后将Spire.Doc.dll文件添加为项目的引用。如下图：

 ![img](../../../我的文档/Typora/Pictures/img_0a9908c64677032b2879f0cc57f80d6f.jpeg)            

这是原文档的截图：

![img](../../../我的文档/Typora/Pictures/img_8e1e48c4d9716f531c70b2a848c375a7.jpeg) 

以下是详细步骤和代码片段：

**步骤****1：**新建一个word文档对象，并加载示例word文档。

```
Document document = new Document();

document.LoadFromFile("法国旅游景点介绍.docx");
```

 

**步骤****2****：**调用Document.Replace方法将文档中的文本巴黎替换为新文本Paris。

```
document.Replace("巴黎", "Paris", false, false);
```


**步骤****3****：**保存文档并重新打开。

```
document.SaveToFile("Replace.docx", FileFormat.Docx);

System.Diagnostics.Process.Start("Replace.docx");
```

 

替换后的文档截图：

![img](../../../我的文档/Typora/Pictures/img_ca683d0f1a2eb04f1407680d1997e724.jpeg) 

 

**全部代码：**

```
using Spire.Doc;

namespace ReplaceString

{

    class Program

    {
        static void Main(string[] args)

        {

            Document document = new Document();

            document.LoadFromFile("法国旅游景点介绍.docx");

            document.Replace("巴黎", "Paris", false, false);

            document.SaveToFile("Replace.docx", FileFormat.Docx);

            System.Diagnostics.Process.Start("Replace.docx");

        }

    }

}
```