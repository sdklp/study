在平常工作中，生成word的方式主要是C#读取html的模板文件处理之后保存为.doc文件,这样的好处是方便，快捷，能满足大部分的需求。不过有些特殊的需求并不能满足，如要生成的Word为一个表格，只是一部分字符串需要变化，用上面的方法生成Word表格容易变形。如果我们能读取一个word模板，把模板里定义的固定字符串如{标记1}替换为想要的文字，然后生成新的word。这样生成的Word非常整洁。

   查找了网上许多方法，虽然都是调用office的接口，并没有一个好的方案。通过自己的实验，比较，使用Microsoft.Office.Interop.Word.dll调用相应的查找全部命令，将相应的标签替换为相应的字符串比较好用。下面是实现方法。
 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using Microsoft.Office.Interop.Word;

namespace JzyyTiJian.WinMain
{
  public  class WordFile
    {
       private object _outputname = "";
        private object _inputname = "";
        // 输出文件名，将word另存为的文件名，绝对地址
        public object OutPutName
        {
            get { return _outputname; }
            set { _outputname = value; }
        }

        //输入文件名,Word的模板文件（还有相应的标记{biaoji1}或者任意字符串).
        public object InPutName
        {
            get { return _inputname; }
            set { _inputname = value; }
        }

        public WordFile() { }

        public WordFile(object inputname,object outputname)
        {
            this._inputname = inputname;
            this._outputname = outputname;
        }
        private Dictionary _put = new Dictionary();
       //param key标记{biaoji1}或者要替换的任意字符串
       // param value替换后的字符串
     public void Put(string key, string value)
        {
            _put.Add(key, value);
        }
        #region 内部变量
        private object missValue = Missing.Value;
        private object trueValue = true;
        private object falseValue = false;
        private object oStory=WdUnits.wdStory;
        private object oMove=WdMovementType.wdMove;
        private ApplicationClass app = null;
        private Document doc=null;
        #endregion

        //打开文档。
        private void openDocument()
        {
            app = new ApplicationClass();
            app.Visible = false;
            app.DisplayAlerts = WdAlertLevel.wdAlertsNone;
            doc = app.Documents.Open(
                    ref _inputname,
                    ref missValue,
                    ref trueValue,
                    ref missValue,
                    ref missValue,
                    ref missValue,
                    ref missValue,
                    ref missValue,
                    ref missValue,
                    ref missValue,
                    ref missValue,
                    ref missValue,
                    ref missValue,
                    ref missValue,
                    ref missValue,
                    ref missValue
                    );
        }

        // 文档另存为新Word文件。
        private void saveAsDocument()
        {
            doc.SaveAs(
                   ref _outputname,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue,
                   ref missValue
                  );
        }

        // 将相应的标签替换然后保存
        public void Save()
        {
            if (string.IsNullOrEmpty(_inputname.ToString()))
            {
                throw new Exception("InPutName 不能为空，请输入模板文件的地址。InPutName为模板文件的绝对地址");
            }
            if (string.IsNullOrEmpty(_outputname.ToString()))
            {
                throw new Exception("OutPutName 不能为空,请输入保存模板文件的地址，OutPutName为模板文件的绝对地址");
            }
            try
            {
                //打开Word文档。
                this.openDocument();
                app.Selection.HomeKey(ref oStory,ref oMove);
                Find find = app.Selection.Find;
                find.ClearFormatting();
                find.Replacement.ClearFormatting();
                foreach (string key in _put.Keys)
                {
                    find.Text = key;
                    find.Replacement.Text = _put[key];
                    this.ExecuteReplace(find);
                }
                //将文档另存为新文档。
                this.saveAsDocument();
            }
            catch (Exception ex)
            {
                throw new Exception("错误：" + ex.Message);
            }
            finally
            {
                app.Quit(ref missValue, ref missValue, ref missValue);
            }
        }
        private bool ExecuteReplace(Find find)
        {
            return this.ExecuteReplace(find, WdReplace.wdReplaceAll);
        }
        private bool ExecuteReplace(Find find, object objReplaceOption)
        {
             return find.Execute(
                ref missValue,
                ref missValue,
                ref missValue,
                ref missValue,
                ref missValue,
                ref missValue,
                ref missValue,
                ref missValue,
                ref missValue,
                ref missValue,
                ref objReplaceOption,
                ref missValue,
                ref missValue,
                ref missValue,
                ref missValue
                );
        }
    }
}
string inputname = Server.MapPath("1.doc");
string outputname = Server.MapPath("3.doc");
Word word = new Word(inputname, outputname);
word.Put("{documentVersion}", "2.6");
word.Put("{ProjectName}", "Renfb.Word");
word.Put("{author}", "Renfb");
word.Put("{CreateRiqi}", "2011.07.04");
word.Put("{Lianxiren}", "任锋宾");
word.Put("{num}", "1");
word.Save();
```

调用前记得先引用程序集Renfb.Word.dll和Microsoft.Office.Interop.Word.dll,在使用的页面上记得添加using Renfb.Word;这篇文章只是起到了抛砖引玉的作用。希望能给大家一些启发。