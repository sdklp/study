# C# File文件处理 删除文件



在C# 程序开发中，我们往往会遇到很多文件上传，文件写入等对于文件的操作业务需要开发，文件处理也是任何应用程序的重要组成部分。C# 有几种创建，读取，更新和删除文件的方法。本文主要介绍C# File文件处理 删除文件。



## 1、删除文件

要使用C# 删除文件，可以使用`File.Delete()`方法或调用`FileInfo`对象的`Delete()`方法，例如，

1）调用File.Delete()方法删除文件

```
string sourceDir = @"c:\cjavapy";
string backupDir = @"c:\cjavapy\2018";
try
{
    string[] picList = Directory.GetFiles(sourceDir, "*.jpg");
    string[] txtList = Directory.GetFiles(sourceDir, "*.txt");
    // 复制图片文件。
    foreach (string f in picList)
    {
        // 从文件名中删除路径。
        string fName = f.Substring(sourceDir.Length + 1);
        // 使用Path。组合方法可以安全地将文件名附加到路径。
        // 如果目标文件已经存在，将覆盖。
        File.Copy(Path.Combine(sourceDir, fName), Path.Combine(backupDir, fName), true);
    }
    // 复制文本文件。
    foreach (string f in txtList)
    {
        // 从文件名中删除路径。
        string fName = f.Substring(sourceDir.Length + 1);
        try
        {
            // 如果目标文件已经存在，则不覆盖。
            File.Copy(Path.Combine(sourceDir, fName), Path.Combine(backupDir, fName));
        }
        // 如果文件已被复制，则捕获异常。
        catch (IOException copyError)
        {
            Console.WriteLine(copyError.Message);
        }
    }
    // 删除已复制的源文件。
    foreach (string f in txtList)
    {
        File.Delete(f);
    }
    foreach (string f in picList)
    {
        File.Delete(f);
    }
}
catch (DirectoryNotFoundException dirNotFound)
{
    Console.WriteLine(dirNotFound.Message);
}
```

2）调用FileInfo对象的Delete()方法

```
using System;
using System.IO;
public class DeleteTest
{
    public static void Main()
    {
        // 创建对文件对象。
        FileInfo fi = new FileInfo("temp.txt");
        // 创建文件
        FileStream fs = fi.Create();
        // 根据需要修改文件，然后关闭文件。
        fs.Close();
        // 删除该文件。
        fi.Delete();
    }
}
```

## 2、删除文件夹

删除文件夹或删除文件下的所有文件及文件夹，都可以使用`Directory.Delete()`或`DirectoryInfo`对象的`Delete()`方法，例如，

1）调用Directory.Delete()删除文件夹

```
using System;
using System.IO;
namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            string subPath = @"D:\cjavapy";
            try
            {
                Directory.CreateDirectory(subPath);
              //仅删除文件夹，并且文件夹不能有内容，否则需要每二个参数传true
                Directory.Delete(subPath);
            }
            catch (Exception e)
            {
                Console.WriteLine("The process failed: {0}", e.Message);
            }
             //删除文件夹及所有子文件夹及文件
           try 
            {
                Directory.CreateDirectory(subPath);
                using (StreamWriter writer = File.CreateText(subPath + @"\example.txt"))
                {
                    writer.WriteLine("content added");
                }
                //需要传递两个参数
                Directory.Delete(topPath, true);
                bool directoryExists = Directory.Exists(topPath);
                Console.WriteLine("top-level directory exists: " + directoryExists);
            }
            catch (Exception e)
            {
                Console.WriteLine("The process failed: {0}", e.Message);
            }
        }
    }
}
```





2）调用DirectoryInfo对象的Delete()删除文件夹

```
using System;
using System.IO;
class Test
{
    public static void Main()
    {
        // 指定要操作的目录。
        DirectoryInfo di1 = new DirectoryInfo(@"c:\cajvapy");
        try
        {
            // 创建目录。
            di1.Create();
            di1.CreateSubdirectory("temp");
            //此操作将不被允许，因为有子目录。
            Console.WriteLine("delete {0}", di1.Name);
           //仅删除文件夹，并且文件夹不能有内容，否则参数需要传true
            di1.Delete();
            Console.WriteLine("successful");
         DirectoryInfo di = new DirectoryInfo("TempDir");
         // 只有当目录不存在时才创建该目录。
         if (di.Exists == false)
             di.Create();
        // 在刚刚创建的目录中创建一个子目录。
        DirectoryInfo dis = di.CreateSubdirectory("SubDir");
        // 根据需要处理该目录。
        // ...
        // 删除子目录。true表示如果子目录
        // 或者文件在这个目录中，它们也将被删除。
        dis.Delete(true);
        // Delete the directory.
        di.Delete(true);
        }
        catch (Exception)
        {
            Console.WriteLine("The Delete operation failed as expected.");
        }
        finally {}
    }
}
```

## 3、使用递归方式删除目录及该目录下的所有文件及文件夹

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;
namespace ConsoleApplication
{
    class Program
    {
        public static void DeleteFolder(string deleteDirectory)
        {
            if (Directory.Exists(deleteDirectory))
            {
                foreach (string deleteFile in Directory.GetFileSystemEntries(deleteDirectory))
                {
                    if (File.Exists(deleteFile))
                        File.Delete(deleteFile);
                    else
                        DeleteFolder(deleteFile);
                }
                Directory.Delete(deleteDirectory);
            }
        }
        static void Main(string[] args)
        {
            string dir = @"c:\cjavapy";
            DeleteFolder(dir);
        }
    }
}
```