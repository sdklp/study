# Python 正则表达式(RegEx)

在本教程中，您将学习正则表达式（RegEx），并使用Python的re模块与RegEx一起使用（在示例的帮助下）。

正则表达式（RegEx）是定义搜索模式的字符序列。 例如，

```
^a...s$
```

上面的代码定义了RegEx模式。模式是：**以a开头并以s****结尾的****任何五个字母字符串**。

使用RegEx定义的模式可用于与字符串匹配。

| 表达式    | 字符串   | 匹配？   |
| :-------- | :------- | :------- |
| ^a...s$   | abs      | 没有匹配 |
| alias     | 匹配     |          |
| abyss     | 匹配     |          |
| Alias     | 没有匹配 |          |
| An abacus | 没有匹配 |          |

Python有一个名为reRegEx 的模块。这是一个示例：

```
import re

pattern = '^a...s$'
test_string = 'abyss'
result = re.match(pattern, test_string)

if result:
  print("查找成功.")
else:
  print("查找不成功.")
```

这里，我们使用re.match()函数来搜索测试字符串中的模式。如果搜索成功，该方法将返回一个匹配对象。如果没有，则返回None。

re模块中定义了其他一些函数，可与RegEx一起使用。在探讨之前，让我们学习正则表达式本身。

如果您已经了解RegEx的基础知识，请跳至[Python RegEx](https://www.cainiaojc.com/python/python-regex.html#python-regex)。

## 使用正则表达式指定模式

为了指定正则表达式，使用了元字符。在上面的示例中，^和$是元字符。

### 元字符

元字符是RegEx引擎以特殊方式解释的字符。以下是元字符列表：

**[]** **.** **^** **$** ***** **+** **?** **{}** **()** **\** **|**

**[] - 方括号**

方括号指定您要匹配的一组字符。

| 表达式    | 字符串   | 匹配？  |
| :-------- | :------- | :------ |
| [abc]     | a        | 1个匹配 |
| ac        | 2个匹配  |         |
| Hey Jude  | 没有匹配 |         |
| abc de ca | 5个匹配  |         |

在这里，[abc]将匹配，如果你想匹配字符串中包含任何的a，b或c。

您也可以使用-方括号内的字符范围。

- [a-e]与相同[abcde]。
- [1-4]与相同[1234]。
- [0-39]与相同[01239]。

您可以通过^在方括号的开头使用插入符号来补充（反转）字符集。

- [^abc]表示除a或b或c之外的任何字符。
- [^0-9] 表示任何非数字字符。

.- **句点**

句点匹配任何单个字符（换行符除外'\n'）。

| 表达式 | 字符串                   | 匹配？   |
| :----- | :----------------------- | :------- |
| ..     | a                        | 没有匹配 |
| ac     | 1个匹配                  |          |
| acd    | 1个匹配                  |          |
| acde   | 2个匹配项（包含4个字符） |          |

^- **插入符号**

插入符号^用于检查字符串是否以某个字符开头。

| 表达式 | 字符串                             | 匹配？  |
| :----- | :--------------------------------- | :------ |
| ^a     | a                                  | 1个匹配 |
| abc    | 1个匹配                            |         |
| bac    | 没有匹配                           |         |
| ^ab    | abc                                | 1个匹配 |
| acb    | 没有匹配项（以开头，a但之后没有b） |         |

$- **美元**

美元符号$用于检查字符串是否**以**某个特定字符**结尾**。

| 表达式  | 字符串   | 匹配？  |
| :------ | :------- | :------ |
| a$      | a        | 1个匹配 |
| formula | 1个匹配  |         |
| cab     | 没有匹配 |         |

*- **星号**

星号符号*匹配**零个或多个**剩余的模式。

| 表达式 | 字符串                   | 匹配？  |
| :----- | :----------------------- | :------ |
| ma*n   | mn                       | 1个匹配 |
| man    | 1个匹配                  |         |
| maaan  | 1个匹配                  |         |
| main   | 没有匹配项（a后面没有n） |         |
| woman  | 1个匹配                  |         |

+- **加号**

加号会+匹配**一个或多个**剩余的模式。

| 表达式 | 字符串               | 匹配？                  |
| :----- | :------------------- | :---------------------- |
| ma+n   | mn                   | 没有匹配项（没有a字符） |
| man    | 1个匹配              |                         |
| maaan  | 1个匹配              |                         |
| main   | 没有匹配项（a后跟n） |                         |
| woman  | 1个匹配              |                         |

?- **问号**

问号符号会?匹配**零或一出现**的剩余模式。

| 表达式 | 字符串                      | 匹配？  |
| :----- | :-------------------------- | :------ |
| ma?n   | mn                          | 1个匹配 |
| man    | 1个匹配                     |         |
| maaan  | 没有匹配项（超过一个a字符） |         |
| main   | 没有匹配项（a后跟n）        |         |
| woman  | 1个匹配                     |         |

{}- **大括号**

考虑以下代码：{n,m}。这意味着至少要保留n个样式，并且最多重复m个样式。

| 表达式      | 字符串                        | 匹配？   |
| :---------- | :---------------------------- | :------- |
| a{2,3}      | abc dat                       | 没有匹配 |
| abc daat    | 1个匹配（在）daat             |          |
| aabc daaat  | 2个匹配项（位于aabc和）daaat  |          |
| aabc daaaat | 2个匹配项（位于aabc和）daaaat |          |

让我们再尝试一个示例。RegEx [0-9]{2, 4}匹配至少2位但不超过4位

| 表达式        | 字符串                         | 匹配？                       |
| :------------ | :----------------------------- | :--------------------------- |
| [0-9]{2,4}    | ab123csde                      | 1个匹配（在处匹配）ab123csde |
| 12 and 345673 | 2个匹配项（位于）12 and 345673 |                              |
| 1 and 2       | 没有匹配                       |                              |

|- **竖线**

竖线|用于交替显示（or运算符）。

| 表达式 | 字符串                  | 匹配？   |
| :----- | :---------------------- | :------- |
| a\|b   | cde                     | 没有匹配 |
| ade    | 1个匹配（在处匹配ade）  |          |
| acdbea | 3个匹配项（位于）acdbea |          |

在这里，a|b匹配任何包含a或b的字符串

()- **括号**

括号()用于对子模式进行分组。例如，(a|b|c)xz匹配任何与a或b或c匹配且后跟xz的字符串

| 表达式      | 字符串                       | 匹配？   |
| :---------- | :--------------------------- | :------- |
| (a\|b\|c)xz | ab xz                        | 没有匹配 |
| abxz        | 1个匹配（在处匹配）abxz      |          |
| axz cabxz   | 2个匹配项（位于）axzbc cabxz |          |

\- **反斜杠**

反斜杠\用于转义包括所有元字符在内的各种字符。例如，

\$a如果字符串包含$后跟则匹配a。在此，$RegEx引擎不会以特殊方式对其进行解释。

如果不确定某个字符是否具有特殊含义，可以将其\放在前面。这样可以确保不对字符进行特殊处理。

**特殊序列**

特殊序列使常用模式更易于编写。以下是特殊序列的列表：

\A -如果指定字符在字符串的开头，则匹配。

| 表达式     | 字符串   | 匹配？ |
| :--------- | :------- | :----- |
| \Athe      | the sun  | 匹配   |
| In the sun | 没有匹配 |        |

\b -如果指定的字符在单词的开头或结尾，则匹配。

| 表达式        | 字符串   | 匹配？ |
| :------------ | :------- | :----- |
| \bfoo         | football | 匹配   |
| a football    | 匹配     |        |
| afootball     | 没有匹配 |        |
| foo\b         | the foo  | 匹配   |
| the afoo test | 匹配     |        |
| the afootest  | 没有匹配 |        |

\B-与\b。如果指定的字符**不在**单词的开头或结尾，则匹配。

| 表达式        | 字符串   | 匹配？   |
| :------------ | :------- | :------- |
| \Bfoo         | football | 没有匹配 |
| a football    | 没有匹配 |          |
| afootball     | 匹配     |          |
| foo\B         | the foo  | 没有匹配 |
| the afoo test | 没有匹配 |          |
| the afootest  | 匹配     |          |

\d-匹配任何十进制数字。相当于[0-9]

| 表达式 | 字符串   | 匹配？                  |
| :----- | :------- | :---------------------- |
| \d     | 12abc3   | 3个匹配项（位于）12abc3 |
| Python | 没有匹配 |                         |

\D-匹配任何非十进制数字。相当于[^0-9]

| 表达式 | 字符串   | 匹配？                    |
| :----- | :------- | :------------------------ |
| \D     | 1ab34"50 | 3个匹配项（位于）1ab34"50 |
| 1345   | 没有匹配 |                           |

\s-匹配字符串包含任何空格字符的地方。等同于[ \t\n\r\f\v]。

| 表达式      | 字符串       | 匹配？  |
| :---------- | :----------- | :------ |
| \s          | Python RegEx | 1个匹配 |
| PythonRegEx | 没有匹配     |         |

\S-匹配字符串包含任何非空白字符的地方。等同于[^ \t\n\r\f\v]。

| 表达式 | 字符串   | 匹配？                |
| :----- | :------- | :-------------------- |
| \S     | a b      | 2个匹配项（位于） a b |
|        | 没有匹配 |                       |

\w-匹配任何字母数字字符（数字和字母）。等同于[a-zA-Z0-9_]。顺便说一下，下划线_也被认为是字母数字字符。

| 表达式 | 字符串   | 匹配？                    |
| :----- | :------- | :------------------------ |
| \w     | 12&": ;c | 3个匹配项（位于）12&": ;c |
| %"> !  | 没有匹配 |                           |

\W-匹配任何非字母数字字符。相当于[^a-zA-Z0-9_]

| 表达式 | 字符串   | 匹配？             |
| :----- | :------- | :----------------- |
| \W     | 1a2%c    | 1个匹配（在）1a2%c |
| Python | 没有匹配 |                    |

\Z -如果指定的字符在字符串的末尾，则匹配。

| 表达式         | 字符串        | 匹配？  |
| :------------- | :------------ | :------ |
| \ZPython       | I like Python | 1个匹配 |
| I like Python  | 没有匹配      |         |
| Python is fun. | 没有匹配      |         |

**提示：**要构建和测试正则表达式，可以使用RegEx测试器工具，例如[regex](http://www.pcjson.com/regex/)。该工具不仅可以帮助您创建正则表达式，还可以帮助您学习它。

现在，您了解了RegEx的基础知识，让我们讨论如何在Python代码中使用RegEx。

## Python正则表达式

Python有一个名为re正则表达式的模块。要使用它，我们需要导入模块。

```
import re
```

该模块定义了一些可与RegEx一起使用的函数和常量。

## re.findall()

re.findall()方法返回包含所有匹配项的字符串列表。

### 示例1：re.findall()

```
# 从字符串中提取数字的程序

import re

string = 'hello 12 hi 89. Howdy 34'
pattern = '\d+'

result = re.findall(pattern, string) 
print(result)

# 输出: ['12', '89', '34']
```

如果找不到该模式，则re.findall()返回一个空列表。

## re.split()

split方法对匹配的字符串进行拆分，并返回发生拆分的字符串列表。

### 示例2：re.split()

```
import re

string = 'Twelve:12 Eighty nine:89.'
pattern = '\d+'

result = re.split(pattern, string) 
print(result)

# 输出: ['Twelve:', ' Eighty nine:', '.']
```

如果找不到该模式，则re.split()返回一个包含空字符串的列表。

您可以将maxsplit参数传递给re.split()方法。这是将要发生的最大拆分次数。

```
import re

string = 'Twelve:12 Eighty nine:89 Nine:9.'
pattern = '\d+'

# maxsplit = 1
# split only at the first occurrence
result = re.split(pattern, string, 1) 
print(result)

# 输出: ['Twelve:', ' Eighty nine:89 Nine:9.']
```

顺便说一下，maxsplit默认值为0；默认值为0。意味着拆分所有匹配的结果。

## re.sub()

re.sub()的语法：

```
re.sub(pattern, replace, string)
```

该方法返回一个字符串，其中匹配的匹配项被替换为replace变量的内容。

### 示例3：re.sub()

```
# 删除所有空格的程序
import re

# 多行字符串
string = 'abc 12\
de 23 \n f45 6'

# 匹配所有空白字符
pattern = '\s+'

# 空字符串
replace = ''

new_string = re.sub(pattern, replace, string) 
print(new_string)

# 输出: abc12de23f456
```

如果找不到该模式，则re.sub()返回原始字符串。

您可以将count作为第四个参数传递给该re.sub()方法。如果省略，则结果为0。这将替换所有出现的匹配项。

```
import re

# 多行字符串
string = 'abc 12\
de 23 \n f45 6'

# 匹配所有空白字符
pattern = '\s+'
replace = ''

new_string = re.sub(r'\s+', replace, string, 1) 
print(new_string)

# 输出:
# abc12de 23
# f45 6
```

## re.subn()

re.subn()与re.sub()类似，期望它返回一个包含2个项目的元组，其中包含新字符串和进行替换的次数。

### 示例4：re.subn()

```
# 删除所有空格的程序
import re

# 多行字符串
string = 'abc 12\
de 23 \n f45 6'

# 匹配所有空白字符
pattern = '\s+'

# 空字符串
replace = ''

new_string = re.subn(pattern, replace, string) 
print(new_string)

# 输出: ('abc12de23f456', 4)
```

## re.search()

re.search()方法采用两个参数：模式和字符串。 该方法寻找RegEx模式与字符串匹配的第一个位置。

如果搜索成功，则re.search()返回一个匹配对象。如果不是，则返回None。

```
match = re.search(pattern, str)
```

### 示例5：re.search()

```
import re

string = "Python is fun"

# 检查“Python”是否在开头
match = re.search('\APython', string)

if match:
  print("pattern found inside the string")
else:
  print("pattern not found")  

# 输出: pattern found inside the string
```

在这里，match包含一个match对象。

## 匹配对象

您可以使用[dir()](https://www.cainiaojc.com/python/python-methods-built-in-dir.html)函数获取匹配对象的方法和属性。

匹配对象的一些常用方法和属性是：

### match.group()

group()方法返回字符串中匹配的部分。

### 示例6：匹配对象

```
import re

string = '39801 356, 2102 1111'

# 三位数字，后跟空格，后两位数字
pattern = '(\d{3}) (\d{2})'

# match变量包含一个Match对象。
match = re.search(pattern, string) 

if match:
  print(match.group())
else:
  print("pattern not found")

# 输出: 801 35
```

在这里，match变量包含一个match对象。

我们的模式(\d{3}) (\d{2})有两个子组(\d{3})和(\d{2})。您可以获取这些带括号的子组的字符串的一部分。就是这样：

```
>>> match.group(1)
'801'

>>> match.group(2)
'35'
>>> match.group(1, 2)
('801', '35')

>>> match.groups()
('801', '35')
```

### match.start()，match.end()和match.span()

start()函数返回匹配的子字符串的开头的索引。同样，end()返回匹配的子字符串的结束索引。

```
>>> match.start()
2
>>> match.end()
8
```

span()函数返回一个包含匹配部分的开始和结束索引的元组。

```
>>> match.span()
(2, 8)
```

### match.re和match.string

匹配对象的re属性返回一个正则表达式对象。 同样，string属性返回传递的字符串。

```
>>> match.re
re.compile('(\\d{3}) (\\d{2})')

>>> match.string
'39801 356, 2102 1111'
```

我们已经介绍了re模块中定义的所有常用方法。如果您想了解更多信息，请访问[Python 3 re模块](https://docs.python.org/3/library/re.html)。

### 在RegEx之前使用r前缀

如果在正则表达式之前使用r或R前缀，则表示原始字符串。例如，'\n'是一个新行，而r'\n'表示两个字符：反斜杠\后跟n。

反斜杠\用于转义包括所有元字符在内的各种字符。但是，使用r前缀\会将其视为普通字符。

### 示例7：使用r前缀的原始字符串

```
import re

string = '\n and \r are escape sequences.'

result = re.findall(r'[\n\r]', string) 
print(result)

# 输出: ['\n', '\r']
```