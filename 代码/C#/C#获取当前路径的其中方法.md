C#获取当前路径的其中方法：

- 1.获取模块的完整路径。
- 2.获取和设置当前目录(该进程从中启动的目录)的完全限定目录 。
- 3.获取应用程序的当前工作目录 。
- 4.获取程序的基目录 。
- 5.获取和设置包括该应用程序的目录的名称 。
- 6.获取启动了应用程序的可执行文件的路径 。
- 7.获取启动了应用程序的可执行文件的路径及文件名 。

```c#
            //1.获取模块的完整路径。 
            string path1 = System.Diagnostics.Process.GetCurrentProcess().MainModule.FileName;

            //2.获取和设置当前目录(该进程从中启动的目录)的完全限定目录 
            string path2 = System.Environment.CurrentDirectory;

            //3.获取应用程序的当前工作目录 
            string path3 = System.IO.Directory.GetCurrentDirectory();

            //4.获取程序的基目录 
            string path4 = System.AppDomain.CurrentDomain.BaseDirectory;

            //5.获取和设置包括该应用程序的目录的名称 
            string path5 = System.AppDomain.CurrentDomain.SetupInformation.ApplicationBase;

            //6.获取启动了应用程序的可执行文件的路径 
            string path6 = System.Windows.Forms.Application.StartupPath;

            //7.获取启动了应用程序的可执行文件的路径及文件名 
            string path7 = System.Windows.Forms.Application.ExecutablePath;


            textBox1.AppendText("Button1_click handle!!!\n");
 
            textBox1.AppendText("获取模块的完整路径:"+path1 + "\n");
            textBox1.AppendText("获取和设置当前目录(该进程从中启动的目录)的完全限定目录:"+path2 + "\n");
            textBox1.AppendText("获取应用程序的当前工作目录:"+path3 + "\n");
            textBox1.AppendText("获取程序的基目录:"+path4 + "\n");
            textBox1.AppendText("获取和设置包括该应用程序的目录的名称:"+path5 + "\n");
            textBox1.AppendText("获取启动了应用程序的可执行文件的路径:"+path6 + "\n");
            textBox1.AppendText("获取启动了应用程序的可执行文件的路径及文件名:"+path7 + "\n");
```

输出结果：

```tex
获取模块的完整路径:E:\Catullus\CatullusNextVPU\AnalysisEndFile_CfgFile\WindowsFormsApp1\WindowsFormsApp1\bin\Debug\WindowsFormsApp1.exe
获取和设置当前目录(该进程从中启动的目录)的完全限定目录:E:\Catullus\CatullusNextVPU\AnalysisEndFile_CfgFile\WindowsFormsApp1\WindowsFormsApp1\bin\Debug
获取应用程序的当前工作目录:E:\Catullus\CatullusNextVPU\AnalysisEndFile_CfgFile\WindowsFormsApp1\WindowsFormsApp1\bin\Debug
获取程序的基目录:E:\Catullus\CatullusNextVPU\AnalysisEndFile_CfgFile\WindowsFormsApp1\WindowsFormsApp1\bin\Debug\
获取和设置包括该应用程序的目录的名称:E:\Catullus\CatullusNextVPU\AnalysisEndFile_CfgFile\WindowsFormsApp1\WindowsFormsApp1\bin\Debug\
获取启动了应用程序的可执行文件的路径:E:\Catullus\CatullusNextVPU\AnalysisEndFile_CfgFile\WindowsFormsApp1\WindowsFormsApp1\bin\Debug
获取启动了应用程序的可执行文件的路径及文件名:E:\Catullus\CatullusNextVPU\AnalysisEndFile_CfgFile\WindowsForms
```