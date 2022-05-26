## os.walk

- 返回指定路径下所有文件和子文件夹中所有文件列表
- 其中文件夹下路径如下：
  ![img](https://img2018.cnblogs.com/blog/949241/201811/949241-20181125194937743-1518526166.jpg)

```python
import os
def file_name_walk(file_dir):
    for root, dirs, files in os.walk(file_dir):
        print("root", root)  # 当前目录路径
        print("dirs", dirs)  # 当前路径下所有子目录
        print("files", files)  # 当前路径下所有非目录子文件

file_name_walk("./")
# root ./
# dirs ['test']
# files ['200-2000(1).txt', '200-2000(2).txt', '200-2000(3).txt', 'getFileName.py']
# root ./test
# dirs []
# files ['test.txt']
```

- 对于os.walk会遍历指定目录下的所有子文件夹和子文件夹中的所有文件，例如此处的root文件夹中有test文件夹和'200-2000(1).txt', '200-2000(2).txt', '200-2000(3).txt', 'getFileName.py'等文件
- `然后遍历子文件夹test,发现其中并没有子文件夹，所以dirs=[],但是子文件夹test中有文本文件test.txt.所以有['test.txt']的值`

------

## os.listdir()

- 返回指定路径下所有的文件和文件夹列表,但是子目录下文件不遍历。

```python
def file_name_listdir(file_dir):
    for files in os.listdir(file_dir):  # 不仅仅是文件，当前目录下的文件夹也会被认为遍历到
        print("files", files)

file_name_listdir("./")
# files 200-2000(1).txt
# files 200-2000(2).txt
# files 200-2000(3).txt
# files getFileName.py
# files test
```

- 注意：使用os.listdir函数不仅是文件，文件夹也会被遍历到,例如test即是一个文件夹。

------

## 全局变量保存

- 如果想要保存文件名可以使用全局变量或者局部变量进行保存。
- 如果使用全局变量，则每次调用函数的信息都存在全局列表中

```python
Files_Global = []

def file_name_listdir_global(file_dir):
    for files in os.listdir(file_dir):
        Files_Global.append(files)

file_name_listdir_global(".")
file_name_listdir_global("./test")
# 最终的结果都会被保存到全局列表变量中
print("Files_Global: ", Files_Global)
# Files_Global:  ['200-2000(1).txt', '200-2000(2).txt', '200-2000(3).txt', 'getFileName.py', 'test', 'test.txt']
```

- 此处使用os.listdir函数将遍历得到的结果都保存到全局变量Files_Global中，则其中包含了遍历`"."当前文件夹和"./test"当前文件夹中的test文件夹中的所有信息`

------

## 局部变量和函数返回

- 局部变量，只保存本次函数调用得到的结果，通过返回值保存
- 建议使用局部变量加返回值的方式

```python
def file_name_listdir_local(file_dir):
    files_local = []
    for files in os.listdir(file_dir):
        files_local.append(files)
    return files_local

file_local_1 = file_name_listdir_local(".")
file_local_2 = file_name_listdir_local("./test")
print("file_local_1：", file_local_1)  # 当前目录下文件
print("file_local_2", file_local_2)  # 子目录test下文件
# file_local_1： ['200-2000(1).txt', '200-2000(2).txt', '200-2000(3).txt', 'getFileName.py', 'test']
# file_local_2 ['test.txt']
```

## 通过os.path.splitext指定文件类型

- 选取特定文件类型
- 选取文件名中所有txt后缀名的文本文件

```python
def file_name(file_dir):
    File_Name=[]
    for files in os.listdir(file_dir):
        if os.path.splitext(files)[1] == '.txt':
            File_Name.append(files)
    return File_Name
txt_file_name=file_name(".")
print("txt_file_name",txt_file_name)

# txt_file_name ['200-2000(1).txt', '200-2000(2).txt', '200-2000(3).txt']
```