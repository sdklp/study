

## C#获取网页中某个元素的位置，并模拟点击

技术标签： [c#](https://www.cxybb.com/searchArticle?qc=c#&page=1) [测试](https://www.cxybb.com/searchArticle?qc=测试&page=1) [javascript](https://www.cxybb.com/searchArticle?qc=javascript&page=1) 

我们在开发中，往往要得到网页中某个元素的位置，并且点击它。要模拟一次鼠标点击并不难，只要调用一个API就行了，关键就是怎么样得到这个元素的位置，还有判断是否要滚动滚动条，要滚动多少行能让元素显示出来。当然我们可以动态改变它的CSS，让它在特定的位置显示出来，但这个方法只对比较简单的网页有效。那我们怎么才能得到网页的位置呢，首先我们来看一张图片
 ![img](http://www.huxu.net.cn/upload/2011/1/1534423354-159722_3.jpg)从这里我们可以看到五个offset的属性，这里我们主要利用offsetparent, offsetleft 和offsettop，我们用offsetparent得到元素offset的父元素，再循环，直到BODY为止。首先我们引用windows\system32\mshtml.ltb这个文件，这样我们才可以得到一些特殊的功能，这个库的功能特别强大，如果自己要做HTML编辑器，可以利用这个库和webBrowser结合，做出来的编辑器功能很强大，就是代码有点不全WEB标准。然后我们要using mshtml;这样以下的代码才能正常运行。代码：

 HTMLDocument doc = webBrowser1.Document.DomDocument as HTMLDocument;
      //getElementsByName,getElementById 这里也可以用这两个方法
      IHTMLElementCollection els = doc.getElementsByTagName("a");
      Point p = new Point();
      foreach (IHTMLElement em in els)
      {
        if ((em.getAttribute("href").ToString() == "javascript:fGoto()") && (em.innerText == "添加附件"))
        {          
          IHTMLElement pem = em;
          //元素中间
          p.X = em.offsetWidth / 2;
          p.Y = em.offsetHeight / 2;
          do
          {
            pem = pem.offsetParent;
            p.X += pem.offsetLeft;
            p.Y += pem.offsetTop;
          } while (pem.tagName.ToLower() != "body");
           em.scrollIntoView();//显示元素
          break;
        }
      }

这样我们已经得到了无素的位置，并已经显示在浏览器的可见区域了，似乎我们用API就可以模拟点击了，然而你测试的时候，发现情况并不是这样的。为什么，接着往下看

这个坐标是屏幕坐标，从屏幕的左上角开始，有时你的浏览器并不是最大化的，即使是最大化也不一定窗体中只有webBrowser这个控件，就算只有这个控件，窗体的边框等一系列的，也可能是你的鼠标不移动正确位置上。而且，如果页面有滚动，我们也要得到滚动去的那一部分。

如果要点击则必须要以下代码：

//被卷去的部分
      int sl, st;
      sl = int.Parse(doc.documentElement.getAttribute("ScrollLeft").ToString());
      st = int.Parse(doc.documentElement.getAttribute("ScrollTop").ToString()); 
      //加上窗体的位置及控件的位置及窗体边框，30和8是窗体边框      
      p.X += em.offsetLeft + this.Left + webBrowser1.Left - sl + 8;       
      p.Y += em.offsetTop + this.Top + webBrowser1.Top + 30 - st;
      //定位鼠标
      Cursor.Position = p;      
      //单击
      mouse_event(6, 0, 0, 0, this.Handle);

这样我们就可以点击到我们需要点击的元素了。关于mouse_event这个API请去看MSDN我网上的教程。Cursor.Position这个鼠标定位也可以用API函数SetCursorPos，但C#有这个东西就不必去调用了。