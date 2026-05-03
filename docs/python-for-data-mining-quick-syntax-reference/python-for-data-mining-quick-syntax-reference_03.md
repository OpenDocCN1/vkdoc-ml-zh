# 3. 基本对象与结构

Python 最重要的特性之一是管理数据结构。让我们来看看它们。

## 数字

Python 中的数字可以是以下任何一种：

*   整数，即 int

*   浮点数，即 float

*   复数

*   布尔值——即 True 或 False

让我们看一些例子：

```
# 创建一个包含整数 (int) 的对象
>>> n1 = 19
>>> type(n1)

# 一个浮点数
>>> n2 = 7.5
>>> type(n2)

# 一个布尔值 (True/False)
>>> n3 = True
>>> type(n3)

# 一个复数
>>> n4 = 3j
>>> type(n4)

```

## 容器对象

Python 的核心是可以创建的各种类型的对象（表 3-1）。

表 3-1

Python 容器对象

| 容器 | 定界符 |
| --- | --- |
| 元组 | ( ) |
| 列表 | [ ] |
| 字典 | { } |
| 集合 | { } |
| 字符串 | """" |

让我们逐一检查它们。



## 元组

元组与字符串和列表一样，都属于*序列*类别。序列是可迭代的对象，表示任意长度的容器。元组是异构且不可变对象的序列，用圆括号标识。不可变性意味着一旦创建了元组，就无法更改它；我们不能将其中的某个元素替换为另一个元素。元组在内存消耗和运行时方面非常高效。

让我们创建一个元组：

```
>>> t1 = (1,2,3,4,5)
# 我们使用 type() 函数基于创建的对象类型进行查询
>>> type(t1)

# Python 告诉我们创建了一个元组，因此我们创建了正确的数据结构
```

序列的常见操作包括索引、切片、连接和重复。如前所述，元组创建后无法修改，但我们*可以*提取、连接或重复其元素。

```
# 我们创建另一个元组
>>> t2 = ("a", "b", "c", "d")
>>> type(t2)

# 我们提取元组 t2 的第一个元素
>>> t2[0]
'a'
# 要计算元组的元素个数，我们从零开始计数；要提取第一个元素 "a"，我们使用方括号进行切片，并在其中插入数字 0
# 我们也可以使用减号来提取元组的元素；这些元素从最后一个到第一个进行计数
>>> t2[-1]
'd'
# 我们可以使用冒号提取多个元素
>>> t2[1:3]
('b', 'c')
```

要确定某个元素是否存在于元组中，我们使用 `in` 运算符：

```
>>> 'a' in t2
True
>>> 'z' in t2
False
```

如前所述，元组是不可变的。如果我们尝试将元组的一个元素替换为另一个元素，会收到错误消息：

```
>>> t2['a'] = 15
Traceback (most recent call last):
File "", line 1, in 
TypeError: 'tuple' object does not support item assignment
```

要显示元组可用的函数，请键入

```
>>> dir(t2)
['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'count', 'index']
```

我们可以使用元组可用的函数向其中添加元素：

```
>>> t2 = t2.__add__(('xyz',))
# 让我们再次查看我们的元组
>>> t2
('a', 'b', 'c', 'd', 'xyz')
```

最后，我们可以创建包含多种类型对象的元组：

```
>>> t3 = (1,2,3,4,5, "test", 20.75, "string2")
>>> t3
(1, 2, 3, 4, 5, 'test', 20.75, 'string2')
```

## 列表

Python 列表包含各种类型的元素。它们与元组类似，区别在于它们是可变的；你可以向列表中添加或删除元素。

要创建列表，我们将元素放在方括号内，用逗号分隔：

```
>>> list1 = ["jan", "feb", "mar", "apr"]
type(list1)

```

我们也可以创建包含数值、逻辑值或字符串值的列表，或者混合多种数据类型：

```
>>> list2 = ["one", 25, True]
>>> type(list2)

```

我们可以使用 `print()` 函数显示列表：

```
>>> print(list1)
['jan', 'feb', 'mar', 'apr']
```

或者使用 `len()` 函数确定其长度：

```
>>> len(list1)

```

我们还可以根据位置打印单个列表元素：

```
>>> list1[0]
'jan'
>>> list1[-2]
'mar'
# 如果我们插入一个与列表中任何元素都不匹配的位置，会收到错误
>>> list1[7]
Traceback (most recent call last):
File "", line 1, in 
IndexError: list index out of range
```

我们可以从列表中选择一些元素：

```
>>> list1 = ["jan", "feb", "mar", "apr"]
>>> list1[1:]
['feb', 'mar', 'apr']
>>> list1[:3]
['jan', 'feb', 'mar']
```

我们可以对列表进行乘法运算：

```
>>> list1*2
['jan', 'feb', 'mar', 'apr', 'jan', 'feb', 'mar', 'apr']
```

或者通过组合两个列表来创建一个新列表：

```
>>> list3 = list1 + list2
>>> list3
['jan', 'feb', 'mar', 'apr', 'one', 25, True]
```

我们甚至可以提取一些元素并将其保存到另一个列表，这实际上就是切片操作。

```
>>> list4 = list3[2:6]
>>> list4
['mar', 'apr', 'one', 25]
```

我们也可以像这样从列表中删除一个元素：

```
>>> del list1[1]
>>> list1
['jan', 'mar', 'apr']
```

通过对列表使用 `dir()` 函数，我们可以查看可以在该列表上执行的所有操作：

```
>>> dir(list1)
['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

一些最重要的函数包括：

*   **append()** 向列表添加元素

*   **clear()** 移除列表中的所有元素

*   **copy()** 复制列表

*   **extend()** 合并两个列表

*   **insert()** 在列表的特定位置添加一个元素

*   **pop()** 从列表中移除一个元素

*   **remove()** 从列表的特定位置移除一个元素

让我们在 list3 上使用其中一些操作。

```
>>> list3
['jan', 'feb', 'mar', 'apr', 'one', 25, True]
# 我们可以使用 append() 方法添加一个元素
>>> list3.append(7)
>>> list3
['jan', 'feb', 'mar', 'apr', 'one', 25, True, 7]
# 使用 reverse() 反转列表元素的顺序
>>> list3.reverse()
>>> list3
[7, True, 25, 'one', 'apr', 'mar', 'feb', 'jan']
# 使用 pop() 删除最后一个元素
>>> list3.pop()
'jan'
>>> list3
[7, True, 25, 'one', 'apr', 'mar', 'feb']
# 使用 sort() 按升序对列表元素重新排序
>>> list5 = [100, 12, 45, 67, 89, 7, 19]
>>> list5.sort()
>>> list5
[7, 12, 19, 45, 67, 89, 100]
# 使用 extend() 用另一个列表扩展列表
>>> list5.extend([260, 35, 98, 124])
>>> list5
[7, 12, 19, 45, 67, 89, 100, 260, 35, 98, 124]
# 最后，我们可以使用 clear 函数删除列表中的元素
>>> list5.clear()
>>> list5
[]
```

我们也可以创建包含子列表的列表：

```
>>> list6 = [(5,7), (9,2), (2,3), (14,27)]
>>> list6
[(5, 7), (9, 2), (2, 3), (14, 27)]
# 在这种情况下，让我们选择 list6 对象的第三个元素：
>>> list6[2]
(2, 3)
# 让我们只选择 list6 第三个元素的第二个元素：
>>> list6[2][1]

```

我们可以使用 `range()` 函数创建一个包含一系列数字的列表。

```
# range() 函数创建一个从 1 到 19 的数字列表：
>>> list7 = range(20)
# 让我们检查对象的类型
>>> type(list7)

# 我们打印该对象
>>> print(list7)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19]
```



## 字典

Python 的另一种数据结构是字典。字典是存储键值对的容器，其特点在于使用花括号和冒号。字典是可变的，但无法排序。我们不能像处理列表和元组那样从字典中提取元素。

在第一个示例中，我们来看一个记录受试者姓名和身高的字典：

```
>>> dict1 = {'Laura': 163, 'Francis': 169, 'Kate': 165}
>>> type(dict1)

```

我们可以查询字典中某个键对应的值：

```
>>> print dict1['Francis']

```

我们也可以向字典中添加元素并重写它：

```
>>> dict1['Simon'] = '180'
>>> dict1
{'Laura': 163, 'Simon': '180', 'Francis': 169, 'Kate': 165}
```

要列出字典的键，我们使用 `.keys` 方法：

```
>>> dict1.keys()
['Laura', 'Simon', 'Francis', 'Kate']
```

要仅获取值，我们使用 `.values` 方法：

```
>>> dict1.values()
[163, 169, 165]
```

要判断某个键是否存在于字典中，我们使用 `in` 运算符：

```
>>> 'Laura' in dict1
True
>>> 'Stephanie' in dict1
False
```

我们可以使用 `del` 命令删除字典元素：

```
>>> del dict1['Simon']
>>> dict1
{'Laura': 163, 'Francis': 169, 'Kate': 165}
```

我们可以使用 `.clear` 方法清空所有字典元素：

```
>>> dict1.clear()
>>> dict1
{}
```

现在，让我们创建另一个字典：

```
>>> dict2 = {'Statistics':28, 'Machine Learning':30, 'Marketing':27,'Analysis':29}
>>> dict2
{'Marketing':27, 'Statistics':28, 'Analysis':29, 'Machine Learning':30}
```

我们可以使用 `len()` 验证字典中的元素数量：

```
>>> len(dict2)

```

字典 dict2 包含四个键值对。

即使不使用 `print()` 函数，我们也可以查询字典中某个元素的值：

```
>>> dict2['Marketing']

```

让我们使用 `list()` 函数检查键：

```
>>> list(dict2)
['Marketing', 'Statistics', 'Analysis', 'Machine Learning']
```

我们可以将键按字母顺序排列：

```
>>> sorted(list(dict2))
['Analysis', 'Machine Learning', 'Marketing', 'Statistics']
```

我们可以仅使用 `.values` 方法显示值：

```
>>> dict2.values()
[27, 28, 29, 30]
```

我们可以使用 `.items` 方法显示所有项：

```
>>> dict2.items()
[('Marketing', 27), ('Statistics', 28), ('Analysis', 29), ('Machine Learning', 30)]
```

我们可以通过创建一个函数来列出字典中的元素：

```
>>> for i in dict2: print(i)
...
Marketing
Statistics
Analysis
Machine Learning
```

我们也可以使用 `.pop` 方法删除其中一项：

```
>>> dict2.pop('Marketing')

>>> dict2
{'Statistics': 28, 'Analysis': 29, 'Machine Learning': 30}
```

另一方面，`.popitem` 方法会从字典中随机删除一个元素：

```
>>> dict2.popitem()
('Statistics', 28)
>>> dict2
{'Analysis': 29, 'Machine Learning': 30}
```

现在字典 dict2 中有两个元素。我们可以通过减法来更新其中一个值——例如，29：

```
>>> dict2
{'Analysis': 29, 'Machine Learning': 30}
>>> dict2['Analysis'] -2

```

在这种情况下，我们没有用新值覆盖旧值。让我们给 29 加上 1。为此，我们需要使用以下表示法：

```
>>> dict2['Analysis'] = dict2['Analysis'] + 1
>>> dict2
{'Analysis': 30, 'Machine Learning': 30}
```

我们也可以使用第 2 章中介绍的赋值运算符。在这种情况下，我们可以从 30 中减去 2：

```
>>> dict2['Analysis'] -= 2
>>> dict2
{'Analysis': 28, 'Machine Learning': 30}
```

我们还可以创建一个空字典并填充它：

```
>>> dict3 = {}
>>> dict3['key1'] = ['value1']
>>> dict3
{'key1': ['value1']}
>>> dict3['key2'] = ['value2']
>>> dict3
{'key2': ['value2'], 'key1': ['value1']}
```

字典的一个特性叫做*嵌套*。通过嵌套，我们可以将一个字典插入到另一个字典中：

```
>>> dict4 = {'key1': { 'nested1': { 'subnested1':'value1'}}}
```

此时，要获取 *value* 值，我们需要像这样进行子集索引：

```
>>> dict4['key1']['nested1']['subnested1']
'value1'
```

## 集合

集合是另一种 Python 结构。它们是包含无序、不重复元素的容器。集合也是不可变的，并支持典型的集合操作，例如并集、交集和差集。

```
# 我们创建一个集合
>>> set1 = {2, 5, 7, 9, 15}
# 检查其结构类型
>>> type(set1)

# 并检查其长度
>>> len(set1)

```

集合不支持索引：

```
>>> set1[2]
Traceback (most recent call last):
File "", line 1, in 
TypeError: 'set' object does not support indexing
```

但是，它们可以告诉我们某个元素是否在集合中：

```
>>> 9 in set1
True
>>> 17 in set1
False
```

我们也可以创建一个空集合：

```
>>> set2 = set()
>>> type(set2)

```

要填充它，我们使用 `.add` 方法：

```
>>> set2.add(17)
>>> set2
set([17])
>>> set2.add(24)
>>> set2
set([24, 17])
>>> set2.add(36)
>>> set2
set([24, 17, 36])
type(set2)

>>> len(set2)

```

让我们创建另一个集合：

```
>>> set3 = {1,1,1,1,1,2,2,2,2,2,3,3,3,3,3,4,4,4,4,5}
>>> set3
set([1, 2, 3, 4, 5])
# 如你所见，集合由唯一的元素组成
```



## 字符串

字符串是由单引号或双引号括起来的字符序列。它们是**不可变**对象，但可以重复和组合，也可以提取部分内容。我们这样编写字符串：

```
>>> string1 = "Hi!"
# 并打印它
>>> string1
'Hi!'
# 或者用单引号这样写
>>> string2 = 'Hello'
>>> string2
'Hello'
# 我们可以通过写变量名或使用 print() 函数来打印字符串
>>> print(string1)
Hi!
```

字符串可以由单个单词、句子片段或完整句子组成。使用单引号时要小心，因为它们可能会造成混淆，例如与撇号一起使用时：

```
>>> string3 = 'I'd like to code in Python'
File "", line 1
string3 = 'I'd like to code in Python'
^
SyntaxError: invalid syntax
>>> string4 = "I'd like to code in Python"
>>> string4
"I'd like to code in Python"
```

不过，我们可以用反斜杠来包含引号，如下所示：

```
>>> haml = "Hamlet said: \"to be or not to be ...\". Oratio answered "
>>> haml
'Hamlet said: "to be or not to be ...". Oratio answered '
```

还有一些控制字符也很有用。例如，“\n”表示换行：

```
>>> haml2 = "Hamlet said: to be or not to be \n Oratio answered ..."
>>> print(haml2)
Hamlet said: to be or not to be
Oratio answered ...
```

此外，“\t”表示制表符：

```
>>> haml3 = "Hamlet said: to be or not to be \t Oratio answered ..."
>>> print(haml3)
Hamlet said: to be or not to be   Oratio answered ...
```

引用字符串时可以使用的运算符，包括连接运算符“+”：

```
>>> string1 + string2
'Hi!Hello'
```

或重复运算符“*”：

```
>>> string1*10
'Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!'
```

我们可以输入三个引号来标记跨越多行的字符串的开始和结束：

```
>>> string5 = """I'd
... like
... to
... code
... in Python
... """
>>> print(string5)
I'd
like
to
code
in Python
```

我们使用 `type()` 函数验证字符串的类：

```
>>> type(string1)

```

并使用 `len()` 函数检查长度：

```
>>> len(string1)

```

要验证对象 ID，我们使用 `id()` 函数：

```
>>> id(string1)

```

我们还可以显示字符串的一部分：

```
>>> string1[0]
'H'
>>> string2[2]
'l'
>>> string4[-1]
'n'
>>> haml[1:10]
'amlet sai'
>>> haml[5:]
't said: "to be or not to be ...". Oratio answered '
>>> haml[:10]
'Hamlet sai'
>>> haml[:-2]
'Hamlet said: "to be or not to be ...". Oratio answere'
# 以下表示法用于反转字符串（或仅反转其一部分）
>>> haml[::-1]
' derewsna oitarO ."... eb ot ton ro eb ot" :dias telmaH'
```

与字符串相关的最重要的函数允许你执行例如将字符串首字母大写的操作。我们可以通过使用 `capitalize()` 方法来实现：

```
>>> string6 = "let's do a little test"
>>> string6.capitalize()
"Let's do a little test"
```

其他函数允许你将整个字符串转换为大写或小写字母：

```
>>> string6.upper()
"LET'S DO A LITTLE TEST"
>>> string7 = string6.upper()
>>> string7
"LET'S DO A LITTLE TEST"
>>> string7.lower()
"let's do a little test"
```

`.find` 方法、`.index` 方法和 `.count` 方法用于在字符串中查找一个或多个字符：

```
>>> string7.find("TT")

>>> string7.index('D')

>>> string7.count('L')

```

`strip()` 函数用于删除字符串开头和结尾的空白字符：

```
>>> string8 = "     test     "
>>> string8.strip()
'test'
```

`replace()` 函数允许我们用另一个元素替换字符串的一部分：

```
>>> string9 = "Let's do some tests"
>>> string9.replace("some", "a couple of")
"Let's do a couple of tests"
```

我们可以像这样验证字符串中是否存在子字符串：

```
>>> "do" in string9
True
>>> "ueioua" in string9
False
```

使用 `split()` 函数，我们可以将字符串拆分为包含多个元素的列表：

```
>>> string9.split()
["Let's", 'do', 'a', 'little', 'test']
```

`join()` 函数允许我们将一个列表组合成一个字符串：

```
>>> "-".join(["03", "01", "2017"])
'03-01-2017'
```

在前面的示例中，插入了连字符作为分隔符。以下示例不包含分隔符。因此，这些项目是连续列出的：

```
>>> "".join(["a", "b", "c", "d"])
'abcd'
# 在这个例子中，我们插入一个空格
>>> " ".join(["a", "b", "c", "d"])
'a b c d'
```

字符串具有不可变性——这意味着它们不能被修改。即使我们总是可以重复使用一个名称并用另一个对象覆盖它，但就其所有含义而言，它将是一个不同的对象。让我们看一个例子：

```
# 我们创建一个字符串
>>> string1 = "a b c d e f g"
# 我们尝试将第一个元素 "a" 替换为 "x"
>>> string1[0] = 'x'
Traceback (most recent call last):
File "", line 1, in 
TypeError: 'str' object does not support item assignment
# 如你所见，我们收到一个错误，因为我们无法以这种方式更改字符串
```

`%` 模运算符允许高级字符串格式化。`%` 运算符用于在字符串中搜索以 `%` 开头的元素，并用其后列表中包含的一个或多个值替换它们。`%` 符号后面必须跟一个字符，该字符指示我们正在输入字符串的数据类型。要打印两个字符串的内容，我们像这样使用 `%s`：

```
# 我们创建第一个字符串
>>> string1 = 'test'
# 如果我们想打印这段文本并合并我们的字符串，我们在结束引号前输入 %s，然后插入 % (string)
>>> print 'my string says: %s' %(string1)
my string says: test
```

我们可以使用循环来遍历字符串：

```
>>> for letter in string1: print(letter)
...
t
e
s
t
```

我们可以计算字符串中的字母数量：

```
>>> word = "string test"
>>> count = 0
>>> for letter in word :
...        count = count + 1
...        print(count)

```

### 注意

Python2 和 Python3 对字符串的管理略有不同。实际上，在 Python3 中，`print()` 是一个函数，需要括号。

```
# Python2 中的字符串管理
>>> print 'Hello world'
Hello world
# Python3 中的字符串管理
>>> print 'Hello world'
File "", line 1
print 'Hello world'
^
SyntaxError: Missing parentheses in call to 'print'
>>> print('Hello world')
Hello world
# 为了像在 Python3 中那样处理 Python2 中的字符串，我们可以导入 future 模块：
# Python2 中 future 模块的使用
>>> from __future__ import print_function
>>> print('Hello world')
Hello world
```

## 文件

除了我们检查过的功能之外，我们通常还会从计算机或互联网导入文件进行分析。文件通常被结构化为数据框，但我们也可以导入图像、音频、二进制、文本或其他专有格式，例如 SPSS、SAS、数据库等。我们将在第 6 章学习如何导入最简单的格式，例如 `.csv`。



## 不可变性

如前所述，不可变性是某些 Python 结构的一个特性：一旦创建，该结构便无法修改（见表 3-2）。我们可以重复使用一个名称，并用另一个对象覆盖该结构，但就所有意图和目的而言，它将是不同的。

表 3-2

数据结构与不可变性

| 结构 | 是否可变 |
| --- | --- |
| 列表 | ![../images/469457_1_En_3_Chapter/469457_1_En_3_Figa_HTML.jpg](img/469457_1_En_3_Figa_HTML.jpg) |
| 字典 | ![../images/469457_1_En_3_Chapter/469457_1_En_3_Figb_HTML.jpg](img/469457_1_En_3_Figb_HTML.jpg) |
| 元组 | ![../images/469457_1_En_3_Chapter/469457_1_En_3_Figc_HTML.jpg](img/469457_1_En_3_Figc_HTML.jpg) |
| 集合 | ![../images/469457_1_En_3_Chapter/469457_1_En_3_Figd_HTML.jpg](img/469457_1_En_3_Figd_HTML.jpg) |
| 字符串 | ![../images/469457_1_En_3_Chapter/469457_1_En_3_Fige_HTML.jpg](img/469457_1_En_3_Fige_HTML.jpg) |

让我们看更多示例。首先从列表开始，它是一个可变对象：

```
# 我们创建一个列表
>>> list1 = ["jan", "feb", "mar", "apr"]
# 检查对象的类型
>>> type(list1)

# 并检查所创建列表的 ID
>>> id(list1)

# 我们添加一个元素
>>> list1.append("oct")
# 重新打印列表
>>> list1
['jan', 'feb', 'mar', 'apr', 'oct']
# 再次检查 ID
>>> id(list1)

# 如你所见，ID 是相同的
```

现在，让我们创建一个元组，它是一个不可变对象：

```
>>> tuple1 = (1,2,3,4)
# 检查对象类
>>> type(tuple1)

# 并验证 ID
>>> id(tuple1)

# 然后尝试添加一个元素
>>> tuple1.append(5)
Traceback (most recent call last):
File "", line 1, in 
AttributeError: 'tuple' object has no attribute 'append'
# 我们重新创建包含最后一个对象的元组
>>> tuple1 = (1,2,3,4,5)
# 并打印其内容
>>> tuple1
(1, 2, 3, 4, 5)
# 并验证 ID
>>> id(tuple1)

# 如所见，我们并没有覆盖第一个对象；而是创建了第二个同名对象（第一个 tuple1 对象不再可用）
```

最后，让我们检查一些字符串示例，它们是不可变的：

```
# 我们创建一个字符串
>>> string1 = "a b c d e f g"
# 并尝试将第一个元素 "a" 替换为 "x" String1 [0] = 'x'
Traceback (most recent call last):
File "", line 1, in 
TypeError: 'str' object does not support item assignment
# 我们得到一个错误
```

## 格式转换

借助一些函数，我们可以相当轻松地将一种结构转换为另一种结构。

```
# 让我们创建一些对象
>>> tuple1 = (1,2,3,4,5)
>>> list1 = ["jan", "feb", "mar", "apr"]
>>> string1 = "2017"
>>> int1 = 67
# 检查对象的类型
>>> type(tuple1)

>>> type(list1)

>>> type(string1)

>>> type(int1)

```

要转换格式，我们使用以下函数：

```
# list() 可以将例如元组转换为列表
>>> convt1 = list(tuple1)
# 需要将结果保存到一个新对象中；让我们再做一次并重新检查类型
>>> type(convt1)

# 从列表转换为元组
>>> conv_to_list = tuple(list1)
>>> type(conv_to_list)

# 从字符串转换为整数
>>> conv_to_int = int(string1)
>>> type(conv_to_int)

```

## 总结

在本章中，我们学习了如何创建和操作 Python 中最重要的基本数据结构。像 Python 这样的面向对象编程语言基于两个主要特性：对象和操作。在本章中，我们更多地了解了对象；在第 4 章中，我们将通过创建函数来专注于操作。

