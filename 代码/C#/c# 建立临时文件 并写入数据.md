第一种

```
 //建立临时文件
                    　　string tempFile = Path.GetTempFileName();
　　　　　　　　　　　　　　string ls_fileNeme = System.IO.Path.ChangeExtension(ls_file, ".rep");
                        File.Move(ls_file, ls_fileNeme);//修改文件扩展名
                        using (FileStream fs = new FileStream(ls_fileNeme, FileMode.Open))
                        {
                            StreamWriter sw = new StreamWriter(fs, Encoding.Unicode);
                            sw.WriteLine("what are you doing");
                            sw.Flush();
                            sw.Close();
                        }       
```

第二种



```
using System;
using System.IO;
class MainClass {
 static void Main() {
  string tempFile = Path.GetTempFileName();
  Console.WriteLine("Using " + tempFile);
  using(FileStream fs = new FileStream(tempFile,FileMode.Open)){
   // (Write some data.)
  }
  // Now delete the file.
  File.Delete(tempFile);
 }
}
```



# 创建临时文件

### 在临时文件只能够创建一个临时文件并返回该文件的完整路径

```
// 在临时文件只能够创建一个临时文件并返回该文件的完整路径：
// C:\Documents and Settings\YourName\Local Settings\Temp\tmp3E6.tmp
System.IO.Path.GetTempFileName();
```

###  根据文件名返回临时文件夹中唯一命名的文件的完整路径
```
/// <summary>
/// 根据文件名返回临时文件夹中唯一命名的文件的完整路径
///  形如：公司文档(1).doc，公司文档(2).doc
/// </summary>
public static string GetTempPathFileName(string fileName)
{
  // 系统临时文件夹
  string tempPath = Path.GetTempPath();
  // 文件的完成路径
  fileName = tempPath + Path.GetFileName(fileName);
  // 文件名
  string fileNameWithoutExt = 
      Path.GetFileNameWithoutExtension(fileName);
  // 扩展名
  string fileExt = Path.GetExtension(fileName);
  int i = 0;
  while (File.Exists(fileName))
  {
    // 生成类似这样的文件名：公司文档(1).doc，公司文档(2).doc
    fileName = tempPath + fileNameWithoutExt + 
          string.Format("({0})", ++i) + fileExt;
  }
  return fileName;
}
```

### 返回系统的临时文件夹的路径

```
// 返回系统的临时文件夹的路径：
//  C:\Documents and Settings\YourName\Local Settings\Temp\
System.IO.Path.GetTempPath();
```

### 返回一个随机的文件名

```
// 返回一个随机的文件名：41ceduv1.uwv
System.IO.Path.GetRandomFileName();
```

