# 7. 导入文件

将文件导入 Python 对于学习如何管理数据集非常重要。在本章中，我们将研究基础知识。我们可以将多种类型的数据导入 Python：从最规范的格式（.csv）或 Excel 数据格式，到用于文本挖掘的文本格式，再到二进制文件，如图像、视频和音频。首先，让我们看看一些导入文件的基本方法。有时这样做的方法可能看起来有点棘手。我们在第 8 章中研究的 pandas 包使导入数据集进行分析变得更加容易。

导入文件的基本结构如下：

```
file1 = open("file.name", mode)
```

“mode” 代表我们打开文件的方式。最重要的方式有：

*   只读（“r”）
*   写入（“w”）
*   在文档末尾追加一些文本（“a”）
*   读写（“r+”）

我们使用 `open()` 函数来打开文件。如果文件不存在，它会在工作目录中被创建。

```
# 我们打开（或创建）文件
>>> file1 = open("file1.txt","w")
# 此时，文件已在我们的工作目录中创建，但它是空的
# 我们处于写入模式；然后我们这样添加文本
>>> file1.write("Add line n.1 to the file1")
# 我们关闭文件
>>> file1.flush()
>>> file1.close()
# 我们以读取模式打开文件
>>> file1 = open("file1.txt", "r")
# 我们创建一个包含我们文件的对象，并使用 print() 函数显示它
>>> text1 = file1.read()
>>> print(text1)
Add line 1 to file1
# 我们关闭文件
>>> file1.close()
# 我们以写入模式重新打开文件
>>> file1 = open("file1.txt","w")
# 如果我们现在写入新文本，之前写入的行将被替换
>>> file1.write("Let's replace the first line with this new one")
# 我们测试
>>> file1.flush()
>>> file1.close()
>>> file1 = open("file1.txt", "r")
>>> text2 = file1.read()
>>> print(text2)
Let's replace the first line with this new one
>>> file1.close()
# 要添加文本而不覆盖，我们以追加模式打开文件
>>> file1 = open("file1.txt", "a")
>>> file1.write("\n Add a second line to this new version of the file")
>>> file1.close()
>>> file1 = open("file1.txt", "r")
>>> text3 = file1.read()
>>> print(text3)
We replace the first line with this new one
Add a second line to this new version of the file
# 我们可以使用 len() 函数验证文本的长度
len(text3)

```

我们还可以创建一个读取文件每一行的小函数：

```
>>> file1 = open("file1.txt", "r")
>>> for line in file1:
...        print(line, end = "")
```

或者按如下方式进行：

```
>>> with open("file1.txt", "a") as file:
...        file1.write("this is the third line")
```

表 7-1 总结了用于打开文件的模式。

表 7-1

打开文件的模式

| 模式 | 描述 |
| :--- | :--- |
| ‘r’ | 只读，默认模式 |
| ‘rb’ | 以二进制格式只读 |
| ‘r+’ | 读写 |
| ‘rb+’ | 以二进制格式读写 |
| ‘w’ | 写入 |
| ‘wb’ | 仅以二进制格式写入。覆盖现有文件。如果文件不存在，则创建一个新文件。 |
| ‘w+’ | 读写。覆盖现有文件。如果文件不存在，则创建一个新文件。 |
| ‘wb+’ | 以二进制格式读写。覆盖现有文件。如果文件不存在，则创建一个新文件。 |
| ‘a’ | 追加到现有文件而不覆盖。如果文件不存在，则创建一个新文件。 |
| ‘ab’ | 追加到现有文件或创建新的二进制文件 |
| ‘a+’ | 读取、追加并覆盖新文件（或创建一个新文件） |
| ‘ab+’ | 以二进制格式读取和追加；覆盖新文件或创建一个新文件 |



## .csv 格式

.csv 或 .tsv 格式的文件是数据挖掘中最常用的文件类型。稍后，我们将介绍一些能更轻松导入和管理文件的包（例如 pandas）。不过，现在我们先学习一些无需单独安装的基本操作。

```
# 我们导入 csv
import csv
# 接下来，我生成一个随机的 csv 文件，将其保存在工作目录中，并命名为 'df'。第二个参数 'r' 表示我们以只读模式访问该文件。
csv1 = open('df', 'r')
# 如果我们想导入一个不在工作目录中的文件，可以包含完整的地址，例如：
csv2 = open('/Users/valentinaporcu/Desktop/df2', 'r')
# 我们继续读取第一个文件
read = csv.reader(csv1)
for row in read:
...     print row
...
[", '0', '1', '2', '3', '4']
['0', '15.982938813888007', '96.04182101708831', '74.68301929612825', '31.670249691004994', '50.37042800222742']
[...]
```

## 从网络读取

基本方法也允许我们从网络读取文件。例如：

```
# Python2
# 我们导入 csv 和 urllib2
import csv
import urllib2
# 我们创建一个包含地址的对象
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
# 我们创建一个连接
conn = urllib2.urlopen(url)
# 我们创建一个包含 .csv 文件的对象
file = csv.reader(conn)
# 我们打印文件
for row in file:
...     print row
...
['5.1', '3.5', '1.4', '0.2', 'Iris-setosa']
['4.9', '3.0', '1.4', '0.2', 'Iris-setosa']
['4.7', '3.2', '1.3', '0.2', 'Iris-setosa']
['4.6', '3.1', '1.5', '0.2', 'Iris-setosa']
['5.0', '3.6', '1.4', '0.2', 'Iris-setosa']
['5.4', '3.9', '1.7', '0.4', 'Iris-setosa']
[...]
```

## JSON 格式

让我们看看如何导入一个 JSON 格式的测试文件。一些 JSON 测试文件可以从 [`https://www.jsonar.com/resources/`](https://www.jsonar.com/resources/) 下载。

```
import json
jfile = open('zips.json').read()
print(jfile)
{ "city" : "AGAWAM", "loc" : [ -72.622739, 42.070206 ], "pop" : 15338, "state" : "MA", "_id" : "01001" }
{ "city" : "CUSHMAN", "loc" : [ -72.51564999999999, 42.377017 ], "pop" : 36963, "state" : "MA", "_id" : "01002" }
{ "city" : "BARRE", "loc" : [ -72.10835400000001, 42.409698 ], "pop" : 4546, "state" : "MA", "_id" : "01005" }
{ "city" : "BELCHERTOWN", "loc" : [ -72.41095300000001, 42.275103 ], "pop" : 10579, "state" : "MA", "_id" : "01007" }
{ "city" : "BLANDFORD", "loc" : [ -72.936114, 42.182949 ], "pop" : 1240, "state" : "MA", "_id" : "01008" }
{ "city" : "BRIMFIELD", "loc" : [ -72.188455, 42.116543 ], "pop" : 3706, "state" : "MA", "_id" : "01010" }
{ "city" : "CHESTER", "loc" : [ -72.988761, 42.279421 ], "pop" : 1688, "state" : "MA", "_id" : "01011" }
{ "city" : "CHESTERFIELD", "loc" : [ -72.833309, 42.38167 ], "pop" : 177, "state" : "MA", "_id" : "01012" }
{ "city" : "CHICOPEE", "loc" : [ -72.607962, 42.162046 ], "pop" : 23396, "state" : "MA", "_id" : "01013" }
{ "city" : "CHICOPEE", "loc" : [ -72.576142, 42.176443 ], "pop" : 31495, "state" : "MA", "_id" : "01020" }
{ "city" : "WESTOVER AFB", "loc" : [ -72.558657, 42.196672 ], "pop" : 1764, "state" : "MA", "_id" : "01022" }
{ "city" : "CUMMINGTON", "loc" : [ -72.905767, 42.435296 ], "pop" : 1484, "state" : "MA", "_id" : "01026" }
```

在第 8 章中，我们将学习如何使用 pandas 创建和导出 JSON 格式的数据框。

## 其他格式

现在我们已经了解了如何使用 Python 中一些最常见的格式导入文件和数据。其他格式包括：

*   lxml——特别是 **objectify** 模块——允许你将文件导入 XML。
*   SQLite3 允许你导入 SQL 数据库。
*   PyMongo 允许你管理 Mongo 数据库。
*   feedparser 允许你处理多种格式的订阅源，包括 RSS。
*   xlrd 允许你将文件导入 Excel（但请注意，pandas 使用起来要简单得多）。

## 总结

以多种格式导入文件和数据集是数据分析中最重要的内容之一。本章描述的过程很重要，因为我们不需要库来导入文件和数据。我们可以使用 Python 中的基本函数。

