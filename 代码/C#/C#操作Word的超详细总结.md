# C#操作Word的超详细总结

本文中用C#来操作Word，包括：

 

创建Word；

插入文字，选择文字，编辑文字的字号、粗细、颜色、下划线等；

设置段落的首行缩进、行距；

设置页面页边距和纸张大小；

设置页眉、页码；

插入图片，设置图片宽高以及给图片添加标题；

插入表格，格式化表格，往表格中插入数据；

保存Word，打印Word；

重新打开Word等。

 

Visual studio版本：Visual Studio 2012（2010应该也可以）

 

准备工作：

/*
\1. 添加引用COM里面的 Microsoft Word 12.0 Object. Library 引用（12.0表示Word 2007版本）

\2. 导命名空间

using MSWord =Microsoft.Office.Interop.Word;
using System.IO;
using System.Reflection;

\3. 把引用中的Microsoft.Office.Interop.Word的“属性”中的嵌入互操作设为False
*/

 

以下是全部代码：（代码有点长，但请不要有压力，直接复制进去就能直接成功运行）

[![复制代码](../../../我的文档/Typora/Pictures/copycode.gif)](javascript:void(0);)

```
  1 using System;
  2 using System.Collections.Generic;
  3 using System.Linq;
  4 using System.Runtime.InteropServices;
  5 using System.Text;
  6 using MSWord = Microsoft.Office.Interop.Word;
  7 using System.IO;
  8 using System.Reflection;
  9 
 10 namespace Console_WordSkill_All
 11 {
 12     class Program
 13     {
 14         static void Main(string[] args)
 15         {
 16             object path;                              //文件路径变量
 17             string strContent;                        //文本内容变量
 18             MSWord.Application wordApp;                   //Word应用程序变量 
 19             MSWord.Document wordDoc;                  //Word文档变量
 20             
 21             path = Environment.CurrentDirectory + "\\MyWord_Print.doc";
 22             wordApp = new MSWord.ApplicationClass(); //初始化
 23 
 24             wordApp.Visible = true;//使文档可见
 25 
 26             //如果已存在，则删除
 27             if (File.Exists((string)path))
 28             {
 29                 File.Delete((string)path);
 30             }
 31 
 32             //由于使用的是COM库，因此有许多变量需要用Missing.Value代替
 33             Object Nothing = Missing.Value;
 34             wordDoc = wordApp.Documents.Add(ref Nothing, ref Nothing, ref Nothing, ref Nothing);
 35 
 36             #region 页面设置、页眉图片和文字设置，最后跳出页眉设置
 37 
 38             //页面设置
 39             wordDoc.PageSetup.PaperSize = MSWord.WdPaperSize.wdPaperA4;//设置纸张样式为A4纸
 40             wordDoc.PageSetup.Orientation = MSWord.WdOrientation.wdOrientPortrait;//排列方式为垂直方向
 41             wordDoc.PageSetup.TopMargin = 57.0f;
 42             wordDoc.PageSetup.BottomMargin = 57.0f;
 43             wordDoc.PageSetup.LeftMargin = 57.0f;
 44             wordDoc.PageSetup.RightMargin = 57.0f;
 45             wordDoc.PageSetup.HeaderDistance = 30.0f;//页眉位置
 46 
 47             //设置页眉
 48             wordApp.ActiveWindow.View.Type = MSWord.WdViewType.wdNormalView;//普通视图（即页面视图）样式
 49             wordApp.ActiveWindow.View.SeekView = MSWord.WdSeekView.wdSeekPrimaryHeader;//进入页眉设置，其中页眉边距在页面设置中已完成
 50             wordApp.Selection.ParagraphFormat.Alignment = MSWord.WdParagraphAlignment.wdAlignParagraphRight;//页眉中的文字右对齐
 51 
 52 
 53             //插入页眉图片(测试结果图片未插入成功)
 54             wordApp.Selection.ParagraphFormat.Alignment = MSWord.WdParagraphAlignment.wdAlignParagraphCenter;
 55             string headerfile = @"C:\Users\xiahui\Desktop\OficeProgram\3.jpg";
 56             MSWord.InlineShape shape1 = wordApp.ActiveWindow.ActivePane.Selection.InlineShapes.AddPicture(headerfile, ref Nothing, ref Nothing, ref Nothing);
 57             shape1.Height = 5;//强行设置貌似无效，图片没有按设置的缩放——图片的比例并没有改变。
 58             shape1.Width = 20;
 59             wordApp.ActiveWindow.ActivePane.Selection.InsertAfter("  文档页眉");//在页眉的图片后面追加几个字
 60             
 61             //去掉页眉的横线
 62             wordApp.ActiveWindow.ActivePane.Selection.ParagraphFormat.Borders[MSWord.WdBorderType.wdBorderBottom].LineStyle = MSWord.WdLineStyle.wdLineStyleNone;
 63             wordApp.ActiveWindow.ActivePane.Selection.Borders[MSWord.WdBorderType.wdBorderBottom].Visible = false;
 64             wordApp.ActiveWindow.ActivePane.View.SeekView = MSWord.WdSeekView.wdSeekMainDocument;//退出页眉设置
 65             #endregion
 66 
 67             #region 页码设置并添加页码
 68 
 69             //为当前页添加页码
 70             MSWord.PageNumbers pns = wordApp.Selection.Sections[1].Headers[MSWord.WdHeaderFooterIndex.wdHeaderFooterEvenPages].PageNumbers;//获取当前页的号码
 71             pns.NumberStyle = MSWord.WdPageNumberStyle.wdPageNumberStyleNumberInDash;//设置页码的风格，是Dash形还是圆形的
 72             pns.HeadingLevelForChapter = 0;
 73             pns.IncludeChapterNumber = false;
 74             pns.RestartNumberingAtSection = false;
 75             pns.StartingNumber = 0; //开始页页码？
 76             object pagenmbetal = MSWord.WdPageNumberAlignment.wdAlignPageNumberCenter;//将号码设置在中间
 77             object first = true;
 78             wordApp.Selection.Sections[1].Footers[MSWord.WdHeaderFooterIndex.wdHeaderFooterEvenPages].PageNumbers.Add(ref pagenmbetal, ref first);
 79 
 80             #endregion
 81 
 82             #region 行间距与缩进、文本字体、字号、加粗、斜体、颜色、下划线、下划线颜色设置
 83 
 84             wordApp.Selection.ParagraphFormat.LineSpacing = 16f;//设置文档的行间距
 85             wordApp.Selection.ParagraphFormat.FirstLineIndent = 30;//首行缩进的长度
 86             //写入普通文本
 87             strContent = "我是普通文本\n";
 88             wordDoc.Paragraphs.Last.Range.Text = strContent;
 89 
 90             wordDoc.Paragraphs.Last.Range.Text = "我再加一行试试，这里不加'\\n'";
 91             //直接添加段，不是覆盖( += )
 92             wordDoc.Paragraphs.Last.Range.Text += "不会覆盖的,";
 93 
 94             //添加在此段的文字后面，不是新段落
 95             wordDoc.Paragraphs.Last.Range.InsertAfter("这是后面的内容\n");
 96 
 97             //将文档的前4个字替换成"哥是替换文字"，并将其颜色设为红色
 98             object start = 0;
 99             object end = 4;
100             MSWord.Range rang = wordDoc.Range(ref start, ref end);
101             rang.Font.Color = MSWord.WdColor.wdColorRed;
102             rang.Text = "哥是替换文字";
103             wordDoc.Range(ref start, ref end);
104 
105             //写入黑体文本
106             object unite = MSWord.WdUnits.wdStory;
107             wordApp.Selection.EndKey(ref unite, ref Nothing);//将光标移到文本末尾
108             wordApp.Selection.ParagraphFormat.FirstLineIndent = 0;//取消首行缩进的长度
109             strContent = "这是黑体文本\n";
110             wordDoc.Paragraphs.Last.Range.Font.Name = "黑体";
111             wordDoc.Paragraphs.Last.Range.Text = strContent;
112 
113             //写入加粗文本
114             strContent = "这是粗体文本\n"; //
115             wordApp.Selection.EndKey(ref unite, ref Nothing);//这一句不加，有时候好像也不出问题，不过还是加了安全
116             wordDoc.Paragraphs.Last.Range.Font.Bold = 1;
117             wordDoc.Paragraphs.Last.Range.Text = strContent;
118 
119             //写入15号字体文本
120             strContent = "我这个文本的字号是15号，而且是宋体\n";
121             wordApp.Selection.EndKey(ref unite, ref Nothing);
122             wordDoc.Paragraphs.Last.Range.Font.Size = 15;
123             wordDoc.Paragraphs.Last.Range.Font.Name = "宋体";
124             wordDoc.Paragraphs.Last.Range.Text = strContent;
125 
126             //写入斜体文本
127             strContent = "我是斜体字文本\n";
128             wordApp.Selection.EndKey(ref unite, ref Nothing);
129             wordDoc.Paragraphs.Last.Range.Font.Italic = 1;
130             wordDoc.Paragraphs.Last.Range.Text = strContent;
131 
132             //写入蓝色文本
133             strContent = "我是蓝色的文本\n";
134             wordApp.Selection.EndKey(ref unite, ref Nothing);
135             wordDoc.Paragraphs.Last.Range.Font.Color = MSWord.WdColor.wdColorBlue;
136             wordDoc.Paragraphs.Last.Range.Text = strContent;
137 
138             //写入下划线文本
139             strContent = "我是下划线文本\n";
140             wordApp.Selection.EndKey(ref unite, ref Nothing);
141             wordDoc.Paragraphs.Last.Range.Font.Underline = MSWord.WdUnderline.wdUnderlineThick;
142             wordDoc.Paragraphs.Last.Range.Text = strContent;
143 
144             //写入红色下画线文本
145             strContent = "我是点线下划线，并且下划线是红色的\n";
146             wordApp.Selection.EndKey(ref unite, ref Nothing);
147             wordDoc.Paragraphs.Last.Range.Font.Underline = MSWord.WdUnderline.wdUnderlineDottedHeavy;
148             wordDoc.Paragraphs.Last.Range.Font.UnderlineColor = MSWord.WdColor.wdColorRed;
149             wordDoc.Paragraphs.Last.Range.Text = strContent;
150 
151             //取消下划线，并且将字号调整为12号
152             strContent = "我他妈不要下划线了，并且设置字号为12号，黑色不要斜体\n";
153             wordApp.Selection.EndKey(ref unite, ref Nothing);
154             wordDoc.Paragraphs.Last.Range.Font.Size = 12;
155             wordDoc.Paragraphs.Last.Range.Font.Underline = MSWord.WdUnderline.wdUnderlineNone;
156             wordDoc.Paragraphs.Last.Range.Font.Color = MSWord.WdColor.wdColorBlack;
157             wordDoc.Paragraphs.Last.Range.Font.Italic = 0;
158             wordDoc.Paragraphs.Last.Range.Text = strContent;
159             
160 
161             #endregion
162 
163 
164             #region 插入图片、居中显示，设置图片的绝对尺寸和缩放尺寸，并给图片添加标题
165 
166             wordApp.Selection.EndKey(ref unite, ref Nothing); //将光标移动到文档末尾
167             //图片文件的路径
168             string filename = Environment.CurrentDirectory + "\\6.jpg";
169             //要向Word文档中插入图片的位置
170             Object range = wordDoc.Paragraphs.Last.Range;
171             //定义该插入的图片是否为外部链接
172             Object linkToFile = false;               //默认,这里貌似设置为bool类型更清晰一些
173             //定义要插入的图片是否随Word文档一起保存
174             Object saveWithDocument = true;              //默认
175             //使用InlineShapes.AddPicture方法(【即“嵌入型”】)插入图片
176             wordDoc.InlineShapes.AddPicture(filename, ref linkToFile, ref saveWithDocument, ref range);
177             wordApp.Selection.ParagraphFormat.Alignment = MSWord.WdParagraphAlignment.wdAlignParagraphCenter;//居中显示图片
178 
179             //设置图片宽高的绝对大小
180 
181             //wordDoc.InlineShapes[1].Width = 200;
182             //wordDoc.InlineShapes[1].Height = 150;
183             //按比例缩放大小
184 
185             wordDoc.InlineShapes[1].ScaleWidth = 20;//缩小到20% ？
186             wordDoc.InlineShapes[1].ScaleHeight = 20;
187 
188             //在图下方居中添加图片标题
189 
190             wordDoc.Content.InsertAfter("\n");//这一句与下一句的顺序不能颠倒，原因还没搞透
191             wordApp.Selection.EndKey(ref unite, ref Nothing);          
192             wordApp.Selection.ParagraphFormat.Alignment = MSWord.WdParagraphAlignment.wdAlignParagraphCenter;
193             wordApp.Selection.Font.Size = 10;//字体大小
194             wordApp.Selection.TypeText("图1 测试图片\n");
195 
196             #endregion
197 
198             #region 添加表格、填充数据、设置表格行列宽高、合并单元格、添加表头斜线、给单元格添加图片
199             wordDoc.Content.InsertAfter("\n");//这一句与下一句的顺序不能颠倒，原因还没搞透
200             wordApp.Selection.EndKey(ref unite, ref Nothing); //将光标移动到文档末尾
201             wordApp.Selection.ParagraphFormat.Alignment = MSWord.WdParagraphAlignment.wdAlignParagraphLeft;
202             //object WdLine2 = MSWord.WdUnits.wdLine;//换一行;  
203             //wordApp.Selection.MoveDown(ref WdLine2, 6, ref Nothing);//向下跨15行输入表格，这样表格就在文字下方了，不过这是非主流的方法
204 
205             //设置表格的行数和列数
206             int tableRow = 6;
207             int tableColumn = 6;
208 
209             //定义一个Word中的表格对象
210             MSWord.Table table = wordDoc.Tables.Add(wordApp.Selection.Range,
211             tableRow, tableColumn, ref Nothing, ref Nothing);
212 
213             //默认创建的表格没有边框，这里修改其属性，使得创建的表格带有边框 
214             table.Borders.Enable = 1;//这个值可以设置得很大，例如5、13等等
215 
216             //表格的索引是从1开始的。
217             wordDoc.Tables[1].Cell(1, 1).Range.Text = "列\n行";
218             for (int i = 1; i < tableRow; i++)
219             {
220                 for (int j = 1; j < tableColumn; j++)
221                 {
222                     if (i == 1)
223                     {
224                         table.Cell(i, j + 1).Range.Text = "Column " + j;//填充每列的标题
225                     }
226                     if (j == 1)
227                     {
228                         table.Cell(i + 1, j).Range.Text = "Row " + i; //填充每行的标题
229                     }
230                     table.Cell(i + 1, j + 1).Range.Text = i + "行 " + j + "列";  //填充表格的各个小格子
231                 }
232             }
233 
234 
235             //添加行
236             table.Rows.Add(ref Nothing);
237             table.Rows[tableRow + 1].Height = 35;//设置新增加的这行表格的高度
238             //向新添加的行的单元格中添加图片
239             string FileName = Environment.CurrentDirectory + "\\6.jpg";//图片所在路径
240             object LinkToFile = false;
241             object SaveWithDocument = true;
242             object Anchor = table.Cell(tableRow + 1, tableColumn).Range;//选中要添加图片的单元格
243             wordDoc.Application.ActiveDocument.InlineShapes.AddPicture(FileName, ref LinkToFile, ref SaveWithDocument, ref Anchor);
244 
245             //由于是本文档的第2张图，所以这里是InlineShapes[2]
246             wordDoc.Application.ActiveDocument.InlineShapes[2].Width = 50;//图片宽度
247             wordDoc.Application.ActiveDocument.InlineShapes[2].Height = 35;//图片高度
248             
249             // 将图片设置为四周环绕型
250             MSWord.Shape s = wordDoc.Application.ActiveDocument.InlineShapes[2].ConvertToShape();
251             s.WrapFormat.Type = MSWord.WdWrapType.wdWrapSquare;
252 
253 
254             //设置table样式
255             table.Rows.HeightRule = MSWord.WdRowHeightRule.wdRowHeightAtLeast;//高度规则是：行高有最低值下限？
256             table.Rows.Height = wordApp.CentimetersToPoints(float.Parse("0.8"));// 
257 
258             table.Range.Font.Size = 10.5F;
259             table.Range.Font.Bold = 0;
260 
261             table.Range.ParagraphFormat.Alignment = MSWord.WdParagraphAlignment.wdAlignParagraphCenter;//表格文本居中
262             table.Range.Cells.VerticalAlignment = MSWord.WdCellVerticalAlignment.wdCellAlignVerticalBottom;//文本垂直贴到底部
263             //设置table边框样式
264             table.Borders.OutsideLineStyle = MSWord.WdLineStyle.wdLineStyleDouble;//表格外框是双线
265             table.Borders.InsideLineStyle = MSWord.WdLineStyle.wdLineStyleSingle;//表格内框是单线
266 
267             table.Rows[1].Range.Font.Bold = 1;//加粗
268             table.Rows[1].Range.Font.Size = 12F;
269             table.Cell(1, 1).Range.Font.Size = 10.5F;
270             wordApp.Selection.Cells.Height = 30;//所有单元格的高度
271 
272             //除第一行外，其他行的行高都设置为20
273             for (int i = 2; i <= tableRow; i++)
274             {
275                 table.Rows[i].Height = 20;
276             }
277 
278             //将表格左上角的单元格里的文字（“行” 和 “列”）居右
279             table.Cell(1, 1).Range.ParagraphFormat.Alignment = MSWord.WdParagraphAlignment.wdAlignParagraphRight;
280             //将表格左上角的单元格里面下面的“列”字移到左边，相比上一行就是将ParagraphFormat改成了Paragraphs[2].Format
281             table.Cell(1, 1).Range.Paragraphs[2].Format.Alignment = MSWord.WdParagraphAlignment.wdAlignParagraphLeft;
282 
283             table.Columns[1].Width = 50;//将第 1列宽度设置为50
284 
285             //将其他列的宽度都设置为75
286             for (int i = 2; i <= tableColumn; i++)
287             {
288                 table.Columns[i].Width = 75;
289             }
290 
291 
292             //添加表头斜线,并设置表头的样式
293             table.Cell(1, 1).Borders[MSWord.WdBorderType.wdBorderDiagonalDown].Visible = true;
294             table.Cell(1, 1).Borders[MSWord.WdBorderType.wdBorderDiagonalDown].Color = MSWord.WdColor.wdColorRed;
295             table.Cell(1, 1).Borders[MSWord.WdBorderType.wdBorderDiagonalDown].LineWidth = MSWord.WdLineWidth.wdLineWidth150pt;
296 
297             //合并单元格
298             table.Cell(4, 4).Merge(table.Cell(4, 5));//横向合并
299 
300             table.Cell(2, 3).Merge(table.Cell(4, 3));//纵向合并，合并(2,3),(3,3),(4,3)
301 
302             #endregion
303 
304             wordApp.Selection.EndKey(ref unite, ref Nothing); //将光标移动到文档末尾
305 
306             wordDoc.Content.InsertAfter("\n");
307             wordDoc.Content.InsertAfter("就写这么多，算了吧！2016.09.27");
308             
309 
310 
311             //WdSaveFormat为Word 2003文档的保存格式
312             object format = MSWord.WdSaveFormat.wdFormatDocument;// office 2007就是wdFormatDocumentDefault
313             //将wordDoc文档对象的内容保存为DOCX文档
314             wordDoc.SaveAs(ref path, ref format, ref Nothing, ref Nothing, ref Nothing, ref Nothing, ref Nothing, ref Nothing, ref Nothing, ref Nothing, ref Nothing, ref Nothing, ref Nothing, ref Nothing, ref Nothing, ref Nothing);
315             //关闭wordDoc文档对象
316 
317             //看是不是要打印
318             //wordDoc.PrintOut();
319 
320 
321 
322             wordDoc.Close(ref Nothing, ref Nothing, ref Nothing);
323             //关闭wordApp组件对象
324             wordApp.Quit(ref Nothing, ref Nothing, ref Nothing);
325             Console.WriteLine(path + " 创建完毕！");
326             Console.ReadKey();
327 
328 
329             //我还要打开这个文档玩玩
330             MSWord.Application app = new MSWord.Application();
331             MSWord.Document doc = null;
332             try
333             {
334                 
335                 object unknow = Type.Missing;
336                 app.Visible = true;
337                 string str = Environment.CurrentDirectory + "\\MyWord_Print.doc";
338                 object file = str;
339                 doc = app.Documents.Open(ref file,
340                     ref unknow, ref unknow, ref unknow, ref unknow,
341                     ref unknow, ref unknow, ref unknow, ref unknow,
342                     ref unknow, ref unknow, ref unknow, ref unknow,
343                     ref unknow, ref unknow, ref unknow);
344                 string temp = doc.Paragraphs[1].Range.Text.Trim();
345                 Console.WriteLine("你他妈输出temp干嘛？");
346             }
347             catch (Exception ex)
348             {
349                 Console.WriteLine(ex.Message);
350             }
351             wordDoc = doc;
352             wordDoc.Paragraphs.Last.Range.Text += "我真的不打算再写了,就写这么多吧";
353 
354             Console.ReadKey();
355         }
356 
357     }
358 }
```

[![复制代码](../../../我的文档/Typora/Pictures/copycode.gif)](javascript:void(0);)

 

生成编辑的Word内容如下图所示：

![img](../../../我的文档/Typora/Pictures/1002191-20160928112743750-1069733335.png)

 

