# C#打开文件或文件夹

直接打开指定的文件

System.Diagnostics.Process.Start(fileFullPath);

直接打开目录

string v_OpenFolderPath = @"目录路径";

System.Diagnostics.Process.Start("explorer.exe", fullPath); 

System.Diagnostics.Process.Start(); 能做什么呢？它主要有以下几个功能：

1、打开某个链接网址（弹窗）。

2、定位打开某个文件目录。

3、打开系统特殊文件夹，如“控制面板”等。

那么它是怎么实现这几个功能的呢？在讲应用前，我们先来看看Process.Star()的构造方法。



![img](https://upload-images.jianshu.io/upload_images/17121226-b32ebad20b9fc544.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

System.Diagnostics.Process.Start("notepad.exe");     -- 打开记事本



System.Diagnostics.Process.Start("calc.exe ");        -- 打开计算器



System.Diagnostics.Process.Start("regedit.exe ");      -- 打开注册表



System.Diagnostics.Process.Start("mspaint.exe ");     -- 打开画图板



System.Diagnostics.Process.Start("write.exe ");        -- 打开写字板



System.Diagnostics.Process.Start("mplayer2.exe ");    --打开播放器



System.Diagnostics.Process.Start("taskmgr.exe ");      --打开任务管理器



System.Diagnostics.Process.Start("eventvwr.exe ");     --打开事件查看器



System.Diagnostics.Process.Start("winmsd.exe ");      --打开系统信息



System.Diagnostics.Process.Start("winver.exe ");       --打开Windows版本信息



System.Diagnostics.Process.Start("mailto: "+ address);  -- 发邮件





shutdown.exe：



参数：-s 关机  -r重启  -f强行  -t 时间  -a 取消关机  -l 注销   -i 显示用户界面



System.Diagnostics.Process.Start("shutdown.exe","-r");       -- 关闭并重启计算机



System.Diagnostics.Process.Start("shutdown.exe","-s -f");      -- 关闭计算机



System.Diagnostics.Process.Start("shutdown.exe","-s -f 30");   -- 30s后关闭计算机



System.Diagnostics.Process.Start("shutdown.exe","-l");        --注销计算机



System.Diagnostics.Process.Start("shutdown.exe","-a");        --撤销关闭计算机