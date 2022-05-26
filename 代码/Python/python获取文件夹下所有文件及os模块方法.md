### python获取文件夹下所有文件

#### 方法一：使用os.listdir

```python
import os
for filename in os.listdir(r'c:\windows'):
    print filename
```

#### 方法二：使用glob模块，可以设置文件过滤

```python
import glob
for filename in glob.glob(r'c:\windows\*.exe'):
    print(filename)
```

#### 方法三：通过os.path.walk递归遍历，可以访问子文件夹

```php
import os
current_address = os.path.dirname(os.path.abspath(__file__))
for parent, dirnames, filenames in os.walk(current_address):
     # Case1: traversal the directories
     for dirname in dirnames:
        print("Parent folder:", parent)
        print("Dirname:", dirname)
     # Case2: traversal the files
     for filename in filenames:
         print("Parent folder:", parent)
         print("Filename:", filename)
```

#### 方法四：非递归

```swift
import os
current_address = os.path.dirname(os.path.abspath(__file__))
file_list = os.listdir(current_address)
for file_address in file_list:
    file_address = os.path.join(current_address, file_address)
    if os.path.isfile(file_address):
        print("这个是文件，文件名称：", file_address)
    elif os.path.isdir(file_address):
        print("这个是文件夹，文件夹名称：", file_address)
    else:
        print("这个情况没遇到")
```

### OS模块

##### 1.当前路径及路径下的文件

**os.getcwd()**：查看当前所在路径。
**os.listdir(path)**: 列举目录下的所有文件。返回的是列表类型。

##### 2.绝对路径

**os.path.abspath(path)**: 返回path的绝对路径。

##### 3.查看路径的文件夹部分和文件名部分

**os.path.split(path)** : 将路径分解为(文件夹,文件名)，返回的是元组类型。可以看出，若路径字符串最后一个字符是,则只有文件夹部分有值；若路径字符串中均无,则只有文件名部分有值。若路径字符串有\，且不在最后，则文件夹和文件名均有值。且返回的文件夹的结果不包含.

**os.path.join(path1,path2,...)** : 将path进行组合，若其中有绝对路径，则之前的path将被删除。

**os.path.dirname(path)** : 返回path中的文件夹部分，结果不包含''

**os.path.basename(path)** : 返回path中的文件名。

##### 4.查看文件时间

**os.path.getmtime(path)** : 文件或文件夹的最后修改时间，从新纪元到访问时的秒数。

**os.path.getatime(path)** : 文件或文件夹的最后访问时间，从新纪元到访问时的秒数。

**os.path.getctime(path)** : 文件或文件夹的创建时间，从新纪元到访问时的秒数。

##### 5.查看文件大小

**os.path.getsize(path)** : 文件或文件夹的大小，若是文件夹返回0。

##### 6.查看文件是否存在

**os.path.exists(path) ** : 文件或文件夹是否存在，返回True 或 False。

##### 7.一些表现形式参数

```ruby
>>> os.sep
'\\'
>>> os.extsep
'.'
>>> os.pathsep
';'
>>> os.linesep
'\r\n'
```

##### 8.实例说明

在自动化测试过程中，常常需要发送邮件，将最新的测试报告文档发送给相关人员查看，这是就需要查找最新文件的功能。
举例：查找文件夹下最新的文件。



![img](https://upload-images.jianshu.io/upload_images/11681023-666003c625a1f199.png?imageMogr2/auto-orient/strip|imageView2/2/w/694/format/webp)

image.png

```python
import os
def new_file(test_dir):
    #列举test_dir目录下的所有文件（名），结果以列表形式返回。
    lists=os.listdir(test_dir)
    #sort按key的关键字进行升序排序，lambda的入参fn为lists列表的元素，获取文件的最后修改时间，所以最终以文件时间从小到大排序
    #最后对lists元素，按文件修改时间大小从小到大排序。
    lists.sort(key=lambda fn:os.path.getmtime(test_dir+'\\'+fn))
    #获取最新文件的绝对路径，列表中最后一个值,文件夹+文件名
    file_path=os.path.join(test_dir,lists[-1])
    return file_path

#返回D:\pythontest\ostest下面最新的文件
print new_file('D:\\system files\\workspace\\selenium\\email126pro\\email126\\report')
```

运行结果：



![img](https://upload-images.jianshu.io/upload_images/11681023-715077567af56ee0.png?imageMogr2/auto-orient/strip|imageView2/2/w/965/format/webp)

image.png