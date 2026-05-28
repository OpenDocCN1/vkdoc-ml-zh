## 15 其他主题

程序最重要的属性是它是否实现了用户的意图。

C.A.R. Hoare

本章内容

*   列表排序

*   读写文件

*   `datetime` 对象

*   正则表达式

本章涵盖了一些 Python 标准库组件，这些组件对于数据科学和通用 Python 使用都是强大的工具。它从各种排序数据的方法开始，然后转向使用上下文管理器读写文件。接下来，本章探讨如何使用 `datetime` 对象表示时间。最后，本章介绍如何使用强大的正则表达式库搜索文本。至少对这些主题有一个高层次的了解是很重要的，因为它们在生产编程中都被大量使用。本章应使你对这些主题有足够的熟悉度，以便在需要时能够理解它们。

### 排序

一些 Python 数据结构，例如列表、NumPy 数组和 Pandas DataFrames，具有内置的排序功能。你可以直接使用这些数据结构，也可以使用自己的排序函数对其进行自定义。

#### 列表

对于 Python 列表，你可以使用内置的 `sort()` 方法，该方法会原地对列表进行排序。例如，假设你定义了一个表示鲸鱼名称的字符串列表：

```python
whales = [ 'Blue', 'Killer', 'Sperm', 'Humpback', 'Beluga', 'Bowhead' ]
```

如果你现在按如下方式使用该列表的 `sort()` 方法：

```python
whales.sort()
```

你会看到该列表现在已按字母顺序排序：

```python
whales

['Beluga', 'Blue', 'Bowhead', 'Humpback', 'Killer', 'Sperm']
```

此方法不会返回列表的副本。如果你捕获返回值，会发现它是 `None`：

```python
return_value = whales.sort()

print(return_value)

None
```

如果你想创建列表的排序副本，可以使用 Python 的内置 `sorted()` 函数，该函数会返回一个排序后的列表：

```python
sorted(whales)

['Beluga', 'Blue', 'Bowhead', 'Humpback', 'Killer', 'Sperm']
```

你可以在任何可迭代对象上使用 `sorted()`，包括列表、字符串、集合、元组和字典。无论可迭代对象的类型如何，此函数都会返回一个排序后的列表。如果你在字符串上调用它，它会返回一个由字符串字符组成的排序列表：

```python
sorted("Moby Dick")

[' ', 'D', 'M', 'b', 'c', 'i', 'k', 'o', 'y']
```

`list.sort()` 方法和 `sorted()` 函数都接受一个可选的 `reverse` 参数，该参数默认为 `False`：

```python
sorted(whales, reverse=True)

['Blue', 'Sperm', 'Beluga', 'Killer', 'Bowhead', 'Humpback']
```

`list.sort()` 和 `sorted()` 也都接受一个可选的 `key` 参数，用于定义排序方式。例如，要根据字符串长度对鲸鱼名称进行排序，你可以定义一个返回字符串长度的 lambda 函数，并将其作为 key 参数传递：

```python
sorted(whales, key=lambda x: len(x))

['Blue', 'Sperm', 'Beluga', 'Killer', 'Bowhead', 'Humpback']
```

你也可以定义更复杂的 `key` 函数。以下示例展示了如何定义一个函数，该函数返回字符串的长度，除非该字符串是 `'Beluga'`，在这种情况下它返回 `1`。这意味着只要其他字符串的长度大于 `1`，`key` 函数就会按字符串长度对列表进行排序，但 `'Beluga'` 除外，它会被放在第一位：

```python
def beluga_first(item):

if item == 'Beluga':

return 1

return len(item)

sorted(whales, key=beluga_first)

['Beluga', 'Blue', 'Sperm', 'Killer', 'Bowhead', 'Humpback']
```

你也可以在你定义的类上使用 `sorted()`。清单 15.1 定义了 `Food` 类并实例化了它的四个实例。然后，它使用属性 `rating` 作为排序键对这些实例进行排序。

清单 15.1 `使用 Lambda 对对象进行排序`

```python
class Food():

def __init__(self, rating, name):

self.rating = rating

self.name = name

def __repr__(self):

return f'Food({self.rating}, {self.name})'

foods = [Food(3, 'Bannana'),

Food(9, 'Orange'),

Food(2, 'Tomato'),

Food(1, 'Olive')]

foods

[Food(3, Bannana), Food(9, Orange), Food(2, Tomato), Food(1, Olive)]

sorted(foods, key=lambda x: x.rating)

[Food(1, Olive), Food(2, Tomato), Food(3, Bannana), Food(9, Orange)]
```

如果你在字典上调用 `sorted()`，它会返回一个由字典键名组成的排序列表。从 Python 3.7 开始（参见 [docs.python.org/3/whatsnew/3.7.html](https://docs.python.org/3/whatsnew/3.7.html)），字典键会按照它们被插入字典的顺序出现。清单 15.2 根据来自 [www.whalefacts.org/how-big-are-whales/](https://www.whalefacts.org/how-big-are-whales/) 的数据创建了一个鲸鱼体重的字典。它打印字典键以演示它们保留了插入时的顺序。然后，你使用 `sorted()` 获取一个按字母数字排序的键名列表，并按顺序打印出鲸鱼名称和体重。

清单 15.2 `对字典键进行排序`

```python
weights = {'Blue': 300000,

'Killer': 12000,

'Sperm': 100000,

'Humpback': 78000,

'Beluga':  3500,

'Bowhead': 200000 }

for key in weights:

print(key)

Blue

Killer

Sperm

Humpback

Beluga

Bowhead

sorted(weights)

['Beluga', 'Blue', 'Bowhead', 'Humpback', 'Killer', 'Sperm']

for key in sorted(weights):

print(f'{key} {weights[key]}')

Beluga 3500

Blue 300000

Bowhead 200000

Humpback 78000

Killer 12000

Sperm 100000
```

Pandas DataFrames 有一个排序方法 `.sort_values()`，它接受一个可以排序的列名列表（参见清单 15.3）。

清单 15.3 `对 Pandas DataFrames 进行排序`

```python
import pandas as pd

data = {'first': ['Dan', 'Barb', Bob],

'last': ['Huerando', 'Pousin', 'Smith'],

'score': [0, 143, 99]}

df = pd.DataFrame(data)

df

first    last       score

0       Dan     Huerando       0

1       Bob     Pousin       143

2       Bob     Smith         99

df.sort_values(by=['last','first'])

first    last       score

0       Bob      Pousin      143

1       Bob      Smith        99

2       Dan      Huerando      0
```

### 读取和写入文件

你已经看到 Pandas 可以直接将各种文件读取到 DataFrame 中。有时，你会希望在不使用 Pandas 的情况下读取和写入文件数据。Python 有一个内置函数 `open()`，给定一个路径，它会返回一个打开的文件对象。以下示例展示了如何从我的主目录打开一个配置文件（尽管你可以以相同的方式使用任何文件路径）：

点击此处查看代码图片

```
read_me = open('/Users/kbehrman/.vimrc')

read_me

<_io.TextIOWrapper name='/Users/kbehrman/.vimrc' mode='r' encoding='UTF-8'>
```

你可以使用 `.readline()` 方法从文件对象中读取单行：

点击此处查看代码图片

```
read_me.readline()

'set nocompatible\n'
```

文件对象会跟踪你在文件中的位置。每次后续调用 `.readline()` 时，下一行会作为字符串返回：

点击此处查看代码图片

```
read_me.readline()

'filetype off\n'
```

在完成文件操作后，关闭与文件的连接非常重要，否则可能会影响再次打开该文件的能力。你可以使用 `close()` 函数来执行此操作：

```
read_me.close()
```

#### 上下文管理器

使用上下文管理器复合语句是一种自动关闭文件的方式。这种语句以关键字 `with` 开头，并在退出其局部状态时关闭文件。以下示例通过上下文管理器打开一个文件，并使用 `readlines()` 方法读取其内容：

点击此处查看代码图片

```
with open('/Users/kbehrman/.vimrc') as open_file:

    data = open_file.readlines()

data[0]

'set nocompatible\n'
```

文件内容被读取为字符串列表，并赋值给名为 `data` 的变量，然后退出上下文，文件对象被自动关闭。

打开文件时，文件对象默认准备以文本形式读取。你可以指定其他状态，例如读取二进制（`'rb'`）、写入（`'w'`）和写入二进制（`'wb'`）。以下示例使用 `'w'` 参数写入一个新文件：

点击此处查看代码图片

```
text = 'My intriguing story'

with open('/Users/kbehrman/my_new_file.txt', 'w') as open_file:

    open_file.write(text)
```

你可以通过以下方式检查文件是否确实已创建：

点击此处查看代码图片

```
!ls /Users/kbehrman

Applications    Downloads       Movies          Public

Desktop         Google Drive    Music           my_new_file.txt

Documents       Library         Pictures        sample.json
```

JSON 是一种用于传输和存储数据的常见格式。Python 标准库包含一个用于与 JSON 相互转换的模块。该模块可以在 JSON 字符串和 Python 类型之间进行转换。此示例展示了如何打开并读取一个 JSON 文件：

点击此处查看代码图片

```
import json

with open('/Users/kbehrman/sample.json') as open_file:

    data = json.load(open_file)
```

### `datetime` 对象

随时间变化的数据模型，称为时间序列数据，常用于解决数据科学问题。为了使用这类数据，你需要一种表示时间的方式。一种常见方式是使用字符串。如果你需要更多功能，例如能够轻松地进行加减运算，或轻松提取年、月、日的值，则需要更复杂的工具。`Datetime` 库提供了多种建模时间的方式，以及用于时间值操作的有用功能。`datetime.datetime()` 类表示精确到微秒的时间点。清单 15.4 演示了如何创建一个 `datetime` 对象并访问其部分值。

清单 15.4 `datetime` 属性

点击此处查看代码图片

```
from datetime import datetime

dt = datetime(2022, 10, 1, 13, 59, 33, 10000)

dt

datetime.datetime(2022, 10, 1, 13, 59, 33, 10000)

dt.year

dt.month

dt.day

dt.hour

dt.minute

dt.second

dt.microsecond

```

你可以使用 `datetime.now()` 函数获取当前时间的对象：

点击此处查看代码图片

```
datetime.now()

datetime.datetime(2021, 3, 7, 13, 25, 22, 984991)
```

你可以使用 `datetime.strptime()` 和 `datetime.strftime()` 函数将字符串转换为 `datetime` 对象，以及将 `datetime` 对象转换为字符串。这两个函数都依赖于定义字符串处理方式的格式代码。这些格式代码在 Python 文档中定义，网址为 [`docs.python.org/3/library/datetime.html#strftime-strptime-behavior`](https://docs.python.org/3/library/datetime.html#strftime-strptime-behavior)。

清单 15.5 使用格式代码 `%Y`（四位年份）、`%m`（两位月份）和 `%d`（两位日期）从字符串创建一个 `datetime`。然后你可以使用 `%y`（表示两位年份）创建一个新的字符串版本。

清单 15.5 `datetime` 对象与字符串的相互转换

点击此处查看代码图片

```
dt = datetime.strptime('1968-06-20', '%Y-%m-%d')

dt

datetime.datetime(1968, 6, 20, 0, 0)

dt.strftime('%m/%d/%y')

'06/20/68'
```

你可以使用 `datetime.timedelta` 类创建一个相对于现有 `datetime` 的新 `datetime`：

点击此处查看代码图片

```
from datetime import timedelta

delta = timedelta(days=3)

dt - delta

datetime.datetime(1968, 6, 17, 0, 0)
```

Python 3.9 引入了一个名为 `zoneinfo` 的新包，用于设置时区。使用这个包，可以轻松设置 `datetime` 的时区：

点击此处查看代码图片

```
from zoneinfo import ZoneInfo

dt = datetime(2032, 10, 14, 23, tzinfo=ZoneInfo("America/Jujuy"))

dt.tzname()

'-03'
```

**注意**

在撰写本书时，Colab 仍在运行 Python 3.7，因此你可能还无法使用 `zoneinfo`。

`datetime` 库还包含一个 `datetime.date` 类：

点击此处查看代码图片

```
from datetime import date

date.today()

datetime.date(2021, 3, 7)
```

这个类与 `datetime.datetime` 类似，区别在于它只跟踪日期，而不跟踪一天中的时间。

### 正则表达式

本章介绍的最后一个包是正则表达式库 `re`。正则表达式（regex）提供了一种用于在文本中搜索的复杂语言。你可以将搜索模式定义为一个字符串，然后使用它来搜索目标文本。在最简单的层面上，搜索模式可以是你想要匹配的确切文本。以下示例定义了包含船长及其电子邮件地址的文本。然后使用 `re.match()` 函数搜索该文本，该函数返回一个 `match` 对象：

点击此处查看代码图片

```
captains = '''Ahab: ahab@pequod.com

              Peleg: peleg@pequod.com

              Ishmael: ishmael@pequod.com

              Herman: herman@acushnet.io

              Pollard: pollard@essex.me'''

import re

re.match("Ahab:", captains )

<re.Match object; span=(0, 5), match='Ahab:'>
```

# 正则表达式基础功能

你可以将此匹配结果与 `if` 语句一起使用，其代码块仅在文本匹配时执行。

```
if re.match("Ahab:", captains ):
    print("We found Ahab")
```

输出：
```
We found Ahab
```

`re.match()` 函数从字符串开头开始匹配。如果你尝试匹配源字符串中较晚出现的子字符串，它将无法匹配：

```
if re.match("Peleg", captains):
    print("We found Peleg")
else:
    print("No Peleg found!")
```

输出：
```
No Peleg found!
```

如果你想匹配文本中包含的任何子字符串，可以使用 `re.search()` 函数：

```
re.search("Peleg", captains)
```

输出：
```
<re.Match object; span=(22, 27), match='Peleg'>
```

## 字符集

字符集提供了定义更通用匹配的语法。字符集的语法是用方括号括起来的一组字符。要搜索 `0` 或 `1` 的首次出现，可以使用以下字符集：

```
"[01]"
```

要搜索元音后跟标点符号的首次出现，可以使用以下字符集：

```
"[aeiou][!,?.;]"
```

你可以使用连字符来表示字符集中的字符范围。对于任何数字，可以使用语法 `[0-9]`；对于任何大写字母，使用 `[A-Z]`；对于任何小写字母，使用 `[a-z]`。你可以在字符集后加上 `+` 来匹配一个或多个实例。你可以在字符集后加上花括号中的数字，以匹配连续出现的精确次数。清单 15.5 演示了字符集的使用。

清单 15.6 字符集

```
re.search("[A-Z][a-z]", captains)
re.search("[A-Za-z]+", captains)
re.search("[A-Za-z]{7}", captains)
re.search("[a-z]+\@[a-z]+\.[a-z]+", captains)
```

输出：
```
<re.Match object; span=(0, 2), match='Ah'>
<re.Match object; span=(0, 4), match='Ahab'>
<re.Match object; span=(46, 53), match='Ishmael'>
<re.Match object; span=(6, 21), match='ahab@pequod.com'>
```

## 字符类

字符类是预定义的字符组，用于简化匹配。你可以在 `re` 文档中查看完整的字符类列表（参见 [`docs.python.org/3/library/re.html`](https://docs.python.org/3/library/re.html)）。一些常用的字符类包括：`\d` 用于数字字符，`\s` 用于空白字符，`\w` 用于单词字符。单词字符通常匹配单词中常用的任何字符，以及数字和下划线。

要搜索被单词字符包围的数字的首次出现，可以使用 `"\w\d\w"`：

```
re.search("\w\d\w", "His panic over Y2K was overwhelming.")
```

输出：
```
<re.Match object; span=(15, 18), match='Y2K'>
```

你可以像使用字符集一样，使用 `+` 或花括号来表示字符类的多个连续出现：

```
re.search("\w+\@\w+\.\w+", captains)
```

输出：
```
<re.Match object; span=(6, 21), match='ahab@pequod.com'>
```

## 分组

如果将正则表达式模式的一部分用括号括起来，它们就成为一个分组。你可以使用 `group()` 方法访问匹配对象上的分组。分组是编号的，其中分组 `0` 是整个匹配：

```
m = re.search("(\w+)\@(\w+)\.(\w+)", captains)
print(f'分组  0 是 {m.group(0)}')
print(f'分组  1 是 {m.group(1)}')
print(f'分组  2 是 {m.group(2)}')
print(f'分组  3 是 {m.group(3)}')
```

输出：
```
分组  0 是 ahab@pequod.com
分组  1 是 ahab
分组  2 是 pequod
分组  3 是 com
```

## 命名分组

通过名称而不是数字来引用分组通常很有用。定义命名分组的语法如下：

```
(?P<GROUP_NAME>PATTERN)
```

然后你可以使用分组名称而不是数字来获取分组：

```
m = re.search("(?P<name>\w+)\@(?P<SLD>\w+)\.(?P<TLD>\w+)", captains)
print(f'''
电子邮件地址: {m.group()}
名称:  {m.group("name")}
二级域名: {m.group("SLD")}
顶级域名: {m.group("TLD")}''')
```

输出：
```
电子邮件地址: ahab@pequod.com
名称:  ahab
二级域名: pequod
顶级域名: com
```

## 查找所有

到目前为止，你只能找到第一个匹配项。你可以使用 `re.findall()` 函数来匹配所有出现。此函数将每个匹配项作为字符串返回：

```
re.findall("\w+\@\w+\.\w+", captains)
```

输出：
```
['ahab@pequod.com',
 'peleg@pequod.com',
 'ishmael@pequod.com',
 'herman@acushnet.io',
 'pollard@essex.me']
```

如果你定义了分组，`re.findall()` 会将每个匹配项作为字符串元组返回，每个字符串对应一个分组的匹配开始：

```
re.findall("(?P<name>\w+)\@(?P<SLD>\w+)\.(?P<TLD>\w+)", captains)
```

输出：
```
[('ahab', 'pequod', 'com'),
 ('peleg', 'pequod', 'com'),
 ('ishmael', 'pequod', 'com'),
 ('herman', 'acushnet', 'io'),
 ('pollard', 'essex', 'me')]
```

## 查找迭代器

如果你正在大型文本中搜索所有匹配项，可以使用 `re.finditer()`。此函数返回一个迭代器，每次迭代返回后续的匹配项：

```
iterator = re.finditer("\w+\@\w+\.\w+", captains)
print(f"finditer 返回了一个 {type(iterator)} 对象" )
m = next(iterator)
print(f"第一个匹配项 {m.group()} 已被处理，而无需处理文本的其余部分")
```

输出：
```
finditer 返回了一个 <class 'callable_iterator'> 对象
第一个匹配项 ahab@pequod.com 已被处理，而无需处理文本的其余部分
```

## 替换

你可以使用正则表达式进行替换和匹配。`re.sub()` 函数接受一个匹配模式、一个替换字符串和一个源文本：

```
re.sub("\d", "#", "Your secret pin is 12345")
```

输出：
```
'Your secret pin is #####'
```

## 使用命名分组进行替换

你可以使用以下语法在替换字符串中引用命名分组：

```
\g<GROUP_NAME>
```

要反转 `captains` 文本中的电子邮件地址，可以按如下方式使用替换：

```
new_text = re.sub("(?P<name>\w+)\@(?P<SLD>\w+)\.(?P<TLD>\w+)",
                  "\g<TLD>.\g<SLD>.\g<name>", captains)
print(new_text)
```

输出：
```
Ahab: com.pequod.ahab
Peleg: com.pequod.peleg
Ishmael: com.pequod.ishmael
Herman: io.acushnet.herman
Pollard: me.essex.pollard
```

## 编译正则表达式

编译正则表达式模式需要一些开销。如果你多次使用同一个正则表达式，编译一次会更高效。你可以使用 `re.compile()` 函数来实现，该函数根据匹配模式返回一个编译后的正则表达式对象：

点击此处查看代码图片

```
regex = re.compile("\w+: (?P<name>\w+)\@(?P<SLD>\w+)\.(?P<TLD>\w+)")

regex

re.compile(r'\w+: (?P<name>\w+)\@(?P<SLD>\w+)\.(?P<TLD>\w+)', re.UNICODE)
```

该对象具有映射到许多`re`函数的方法，例如`match()`、`search()`、`findall()`、`finditer()`和`sub()`，如清单15.7所示。

清单 15.7 **编译后的正则表达式**

点击此处查看代码图片

```
regex.match(captains)

<re.Match object; span=(0, 21), match='Ahab: ahab@pequod.com'>

regex.search(captains)

<re.Match object; span=(0, 21), match='Ahab: ahab@pequod.com'>

regex.findall(captains)

[('ahab', 'pequod', 'com'),

 ('peleg', 'pequod', 'com'),

 ('ishmael', 'pequod', 'com'),

 ('herman', 'acushnet', 'io'),

 ('pollard', 'essex', 'me')]

new_text = regex.sub("Ahoy \g<name>!", captains)

print(new_text)

Ahoy ahab!

Ahoy peleg!

Ahoy ishmael!

Ahoy herman!

Ahoy pollard!
```

### 本章小结

本章介绍了数据排序、文件对象、`Datetime`库和`re`库。对于任何Python开发者来说，至少对这些主题有初步了解都是很重要的。你可以使用`sorted()`函数或对象的`sort()`方法（例如列表对象所附带的`sort()`方法）进行排序。你可以使用`open()`函数打开文件，并且在文件打开期间，你可以从中读取数据或向其写入数据。`Datetime`库用于模拟时间，在处理时间序列数据时尤其有用。最后，你可以使用`re`库来定义复杂的文本搜索。

### 练习题

1. 在以下示例中，`sorted_names`的最终值是什么？

点击此处查看代码图片

```
names = ['Rolly', 'Polly', 'Molly']

sorted_names = names.sort()
```

2. 如何按降序对列表`nums = [0, 4, 3, 2, 5]`进行排序？

3. 上下文管理器会处理哪些特定于文件对象的清理工作？

4. 如何根据以下变量创建一个`datetime`对象：

点击此处查看代码图片

```
year = 2022

month = 10

day = 14

hour = 12

minute = 59

second = 11

microsecond = 100
```

5. 在正则表达式模式中，`\d`代表什么？

## A

## 章末练习题答案

此处列出了每章末尾练习题的答案。

### 第 1 章

1. Jupyter notebooks。

2. 文本和代码。

3. 使用左侧导航栏“文件”部分中的“挂载驱动器”按钮。

4. Google Colab中的Python。

### 第 2 章

1. `int`

2. 它们会正常执行。

3. `raise LastParamError`

4. `print("Hello")`

5. `2**3`

### 第 3 章

1. `'a' in my_list`

2. `my_string.count('b')`

3. `my_list.append('a')`

4. 是

5. `range(3, 14)`

### 第 4 章

1. `dict(name='Smuah', height=62)`

或者

`{'name':'Smuah', 'height':62}`

或者

点击此处查看代码图片

```
dict([['name','Smuah'],['height',62]])
```

2. `student['gpa'] = 4.0`

3. `data.get('settings')`

4. 可变对象的数据可以被更改；不可变对象的数据在创建后不能被更改。

5. `set("lost and lost again")`

### 第 5 章

1. `Biya []`

2. Hiya Henry

3.

```
for x in range(9):
    if x not in (3, 5, 7):
        print(x)
```

### 第 6 章

1. `'after-nighttime'`

2. `'before-nighttime'`

3. 一个错误。

4. `@standard_logging`

5. `a`

`b`

`1`

### 第 7 章

1. NumPy数组只包含一种数据类型。
NumPy数组执行逐元素操作。
NumPy数组具有矩阵数学方法。

2.

```
array([[1, 3],
       [2, 9]])
```

3.

```
array([[0, 1, 0],
       [4, 2, 9]])
```

4. `5, 2, 3`

5. `poly1d((6,2,5,1,-10))`

### 第 8 章

1. `stats.norm(loc=15)`

2. `nrm.rvs(25)`

3. `scipy.special`

4. `std`()

### 第 9 章

1.

点击此处查看代码图片

```
df = pd.DataFrame({'Sample Size(mg)':[ 0.24, 2.34, 0.0234 ],
                   '%P':                      [40, 34, 12 ],
                   '%Q':                     [60, 66, 88 ]})
```

或者

点击此处查看代码图片

```
df = pd.DataFrame([[ 0.24, 40, 60 ], 
                   [ 2.34, 34, 66],
                   [0.0234, 12, 88 ]],
                        columns=[ 'Sample Size(mg) ', '%P', '%Q' ])
```

2.

点击此处查看代码图片

```
df['Total Q'] = df['%Q']/df['Sample Size(mg)']
```

或者

点击此处查看代码图片

```
df['Total Q'] = df.loc[:,'%Q']/df.loc[:,'Sample Size(mg)']
```

或者

点击此处查看代码图片

```
df['Total Q'] = df.iloc[:,2]/df.iloc[:,0]
```

3. `df.loc[:, ['%P', '%Q']] / 100`

### 第 10 章

1. `plt.plot(data['X'], data['Y']`

2. `plt.plot(data['X'], data['Y'])`

3.

点击此处查看代码图片

```
fig, (ax1, ax2) = plt.subplots(1, 2)
ax1.plot(data['X'], data['Y'])
ax2.plot(data['X'], data['Y1'])
fig.show()
```

或者

点击此处查看代码图片

```
fig, (ax1, ax2) = plt.subplots(1, 2)
ax1.plot('X','Y', data=data)
ax2.plot('X','Y1', data=data)
fig.show()
```

### 第 11 章

1. 转换数据。

2. 为了避免过拟合。

3. 测试模型的准确性。

### 第 12 章

1.

点击此处查看代码图片

```
gutenberg.words('austen-emma.txt')
gutenberg.sents('austen-emma.txt')
gutenberg.paras('austen-emma.txt')
```

2.

点击此处查看代码图片

```
alice = gutenberg.words('carroll-alice.txt')
alice['Alice']
```

3.

点击此处查看代码图片

# 第 13 章

1.  `4`

    `4`

2.  点击此处查看代码图片

    ```python
    list(map(lambda x: f'{x}'*2, 'omni'))
    ```

    或者

    点击此处查看代码图片

    ```python
    list(map(lambda x: f'{x}{x}', 'omni'))
    ```

3.  `sum([x for x in range(100, 2)])`

4.  `(x**2 for x in range(1000))`

5.  点击此处查看代码图片

    ```python
    def fib():
        f0 = 0
        f1 = 1
        while True:
            yield f0
            f0, f1 = f1, f0 + f1
    ```

# 第 14 章

1.  类的当前实例。

2.  当对象被实例化时。

3.  `0`

4.  `Hello from A`
    `Goodbye from B`

# 第 15 章

1.  `None`

2.  `nums.sort(reverse=True)`

3.  关闭文件对象。

4.  `datetime(year, month, day, hour, minute, second, microsecond)`

5.  一个数字。

# 代码示例

```python
alice = gutenberg.words('carroll-alice.txt')
alice_r = []
for word in alice_w:
    if word not in string.punctuation:
        if word.lower() not in english_stopwords:
            alice_r.append(word)
alice_dist = nltk.FreqDist(alice_r)
alice_dist.tabulate(10)
```

4. 点击此处查看代码图片

```python
alice = Text(gutenberg.words('carroll-alice.txt'))
alice.similar('rabbit')
```

5. 点击此处查看代码图片

```python
nltk.download('names')
names = nltk.corpus.names
all_names = names.words('male.txt')
all_names.extend( names.words('female.txt') )
hamlet_w = gutenberg.words('shakespeare-hamlet.txt')
hamlet_names = []
for word in hamlet_w:
    if word in all_names:
        hamlet_names.append(word)
hamlet_dist = nltk.FreqDist(hamlet_names)
hamlet_dist.most_common(5)
```

# 索引

## A

**匿名函数, 80**

**安斯库姆, F., 135**

**`apply()` 方法, 132–133**

**参数, 30**

**数组**

- 广播, 98–99
- 扩展维度, 99–100
- 修改值, 91
- 副本，修改值, 95
- 创建, 86–88
    - 一维, 87
    - 二维, 88
    - 使用 `reshape` 方法, 88–89
- 逐元素运算, 91–92
- 过滤值, 92–94
- 索引, 89–90
- 矩阵运算, 96–97
- 方法, 95–96
- 一维, 87
- 序列与, 91
- 自动设置类型, 97
- 显式设置类型, 97–98
- 切片, 89–90
- 二维, 88
- 索引与切片, 90
- 视图, 94
    - 修改值, 94

**断言语句, 16–17**

**赋值语句, 17**

**属性, 22**

**轴, 136, 143–144**

## B

**二项分布, 105–107**

**Bokeh, 149–150**

**布尔运算符, 14, 58–59, 125**

- `DataFrames` 与, 126–127

**方括号语法, 121–122**

**`break` 语句, 64**

**`break` 语句, 19**

**广播, 98–99**

- 扩展维度, 99–100

**内置类型, 14**

## C

**单元格, 4–5**

**字符类, 209**

**字符集, 208–209**

**类, 22, 188–189**

- `datetime.date`, 207
- 继承, 196–199
- 变量, 190–191

**分类器类, 166**

**代码块, 56, 63–64**

**`collocations()` 方法, 165**

**列**

- 创建, 128
- 更新, 129

**比较运算符, 57–58, 93–94**

**编译正则表达式, 211–212**

**复合语句, 55**

- `if`, 59–62
- 结构, 56

**`concordance()` 方法, 165**

**构造函数**

- `dict()`, 38
- `list()`, 29
- `tuple()`, 29

**上下文管理器, 205**

**`continue` 语句, 19**

**连续分布, 108**

- 指数分布, 110
- 正态分布, 108–110
- 均匀分布, 110–111

**控制语句, 56, 68**

**副本，修改值, 95**

**语料库读取器, 160**

- 加载文本, 160–161
- 分词器, 161

**语料库，下载, 166–167**

**创建**

- 数组, 86–88
    - 一维, 87
    - 二维, 88
    - 使用 `reshape` 方法, 88–89
- 列, 128
- `DataFrames`, 114
    - 从字典, 114–115
    - 从文件, 116
    - 从列表的列表, 115–116
- `datetime` 对象, 206
- 字典, 38
- 列表, 29–30
- 元组, 29–30

## D

**`DataFrames`（数据框），113**

- 访问数据，120–121
- `apply()` 方法，132–133
- 布尔运算符，126–127
- 方括号语法，121–122
- 列
    - 创建，128
    - 更新，129
- 创建，114
    - 从字典创建，114–115
    - 从文件创建，116
    - 从列表的列表创建，115–116
- 数据操作，129–131
- `describe` 方法，118
    - `exclude` 参数，120
    - `include` 关键字，119–120
    - `percentiles` 参数，118–119
- `head` 方法，117
- 交互操作，117
- 交互式显示，133
- 操作，127–128, 129
- 掩码与过滤，125–126
- 方法，128
- 优化访问
    - 按索引，124
    - 按标签，123–124
- `replace` 方法，131–132
- 排序，204
- `tail` 方法，118

**`datetime` 对象，207**

- 创建，206
- 设置时区，207
- 将字符串转换为 `datetime` 对象，207

**装饰器，76–77, 79**

- 语法，79–80

**`del()` 函数，40**

**删除语句，18**

**`describe` 方法，118**

- `exclude` 参数，120
- `include` 关键字，119–120
- `percentiles` 参数，118–119

**`dict()` 构造函数，38**

**`dict_key` 视图，41–42**

**字典，37–39**

- 检查键是否存在，43
- 创建，38
- 从字典创建 `DataFrames`，114–115
- `get` 方法，43–44
- `hash()` 方法，45
- 键/值对
    - 添加，39
    - 更新，39
    - 删除条目，39–40
- 有效的键类型，44–45

**字典推导式，181**

**字典视图，40–42**

- `dict_key`，41–42
- `key_item`，42

**`difference()` 方法，51**

**离散分布，105**

- 二项分布，105–107
- 泊松分布，107–108

**不相交集合，48**

**`dispersion_plot()` 方法，165–166**

**文档字符串，68–69**

**点号表示法，22**

**下载，语料库，166–167**

## E

**`elif` 语句，62**

**`else` 语句，61**

**相等运算符，56–57, 125**

**估计器，156**

**异常，18–19**

**指数分布，110**

**表达式，16**

- 生成器，182–183

**`extend` 方法，31**

## F

**图形**，136

**`fileids()` 方法**，160

**文件**

- 从文件创建 `DataFrame`，116
- Google Colab，9–10
- 打开，205
- 读取与写入，204–205

**`filter()` 函数**，179

- 用列表推导式替代，180

**过滤**，`DataFrame`，125–126

# 查找与迭代

**`find` 迭代器**，211

**`findall()` 方法**，165

**展平嵌套列表**，167

**`for` 循环**，63–64

## `FreqDist` 类

内置绘图方法，164

方法，164

## 频率分布，161–162

过滤停用词，163–164

移除标点符号，162–163

## 其他常用结构

**冻结集合**，53

**f-字符串**，34

### 函数式编程，173，174–175

修改可变数据，176–177

字典推导式，181

`filter()` 函数，179

生成器，182

表达式，182–183

函数，183–184

`lambda` 函数，179

列表推导式，179

条件语句与，181

多个变量，181

用列表推导式替代 `map()` 和 `filter()`，180

语法，179–180

`map()` 函数，177–178

`operator` 模块，179

`reduce()` 函数，178，179

作用域，173–174

继承，174

外部，175–176

状态，174

### 函数，15，67

匿名函数，80

控制语句，68

`datetime.now()`，206

装饰器，76–77，79

语法，79–80

`del()`，40

文档字符串，68–69

生成器，183–184

辅助函数，33–34

`lambda`，179

`len`，27

`max`，28

`min`，28

嵌套，77

嵌套包装，78–79

`open()`，204–205

作为参数，78

参数，69–70

默认值，71–72

关键字赋值，70–71

关键字通配符，74–75

可变默认值，72–73

位置通配符，74

仅限位置参数，73

`re.compile()`，211

`re.findall()`，210

`re.finditer()`，211

`re.match()`，207–208

`re.search()`，208

`return` 语句，75

`reversed`，41

作用域，75–76

`sorted()`，202–204

包装，77–78

## 未来语句，20

# G

## 生成器，182

表达式，182–183

函数，183–184

## `get` 方法，43–44

## `global` 语句，20

## Google Colab，5–6

代码单元格，9

代码片段，11

与现有集合，11

文件，9–10

标题，7–8

`LaTeX`，8–9

笔记本，管理，10

`Shell` 命令，11–12

系统别名，11–12

文本单元格，6–8

## 分组，209–210

命名分组，210

# H

## `hash()` 方法，45

## `head` 方法，117

## 辅助函数，33–34

## 高级编程语言，15

# I

## `if` 语句，59–62

## 不可变对象，44–45

## `import` 语句，19–20

## `index` 方法，28

## 索引，26

数组，89–90

`DataFrame` 与，124

## 不等运算符，56–57

## 继承，196–199

## 继承作用域，174

## 安装，`NumPy`，86

## 实例，188

## 与 `DataFrame` 数据交互，117

## 查询，27–28

## 交集，51

## 整数，14

`numerator` 属性，22

## `issuperset()` 方法，50

## `items()` 方法，40

# J-K

## JSON 文件，打开与读取，205

## Jupyter 笔记本，4–5

## Keras，153

## `key_item` 视图，42

## `keys()` 方法，40

## 键/值对，37

添加，39

更新，39

# L

## 标签，`DataFrame` 访问与，123–124

## `lambda` 函数，80，179

## `LaTeX`，8–9

## `len` 函数，27

## 库。*另请参阅* `NumPy`；`SciPy`

机器学习，153–154

`SciPy`，103

第三方，85

可视化，`matplotlib`，135–136

## 列表推导式，179

条件语句与，181

多个变量，181

用其替换 `map()` 和 `filter()`，180

语法，179–180

## `list()` 构造函数，29

## 列表，29

添加和删除项目，30–31

创建，29–30

从列表创建 `DataFrame`，115–116

展平，167

嵌套，31

排序，32，201–204

解包，31–32

## 循环

`break` 语句，64

`for` 循环，63–64

`while` 循环，62–63

## 低级编程语言，15

# M

## 机器学习，153。*另请参阅* `Scikit-learn`

过拟合，155

拆分测试数据和训练数据，155–156

监督学习与无监督学习，154

变换，154–155

## 魔法函数，12

## 操作 `DataFrame`，127–128，129

## `map()` 函数，177–178

用列表推导式替换，180

## Markdown，6

## 数学运算，21–22

## 数学运算符方法，195–196

## `matplotlib`，135–136

颜色，139

创建多个坐标轴，143–144

带标签的数据，140–141

线条样式，138

标记类型，137–138

面向对象风格，143

绘制多组数据，141–143

样式化图表，137，139–140

## 矩阵运算，96–97

## `max` 函数，28

## 方法，188–190

`to_bytes()`，187–188

`apply()`，132–133

数组与方法，95–96

`collocations()`，165

`concordance()`，165

`count`，28

`DataFrame` 方法，128

`describe`，118

`exclude` 参数，120

`include` 关键字，119–120

`percentiles` 参数，118–119

`difference()`，51

`disjoint()`，48

`dispersion_plot()`，165–166

`extend`，31

`fileids()`，160

`findall()`，165

`get`，43–44

`hash()`，45

`head`，117

`index`，28

继承，196–199

`intersection()`，51

`issuperset()`，50

`items()`，40

`keys()`，40

数学运算符，195–196

`min()`，125

`pop`，30

私有，190

公有，190

`replace`，131–132

表示，192

`reverse`，32

富比较，192–195

`similar()`，165

`sort`，32

`sort()`，201–202

特殊，191

`subset()`，49

`symmetric difference()`，51

`tail`，118

`union()`，50

`values()`，40

## `min` 函数，28

## `min()` 方法，125

## `MinMaxScaler` 变换器，154–155

## 多条语句，16

## 可变对象，44，176–177

# N

## 命名组，210

替换与，211

## 自然语言处理，159

*《Python 自然语言处理》*，169

## 嵌套函数，77

## 嵌套列表，31

## 嵌套包装函数，78–79

## `NLTK`（自然语言工具包），159

分类器类，166

定义特征，168

下载语料库，166–167

展平嵌套列表，167

标注数据，167

训练与测试，168–169

语料库读取器，160

加载文本，160–161

分词器，161

`fileids()` 方法，160

`FreqDist` 类

内置绘图方法，164

方法，164

频率分布，161–162

过滤停用词，163–164

移除标点符号，162–163

示例文本，159–160

`Text` 类，165

`collocations()` 方法，165

`concordance()` 方法，165

`dispersion_plot()` 方法，165–166

`findall()` 方法，165

`similar()` 方法，165

## `NoneType`，15

## `nonlocal` 语句，20

## 正态分布，108–110

## 笔记本，4–5

Google Colab，5–6

Jupyter，4–5

管理，10

## 数值类型，14

## `NumPy`。*另请参阅* 数组；`SciPy`

创建数组，86–87

安装与导入，86

多项式，100–101

# O

## 面向对象编程，187

类，188–189

### P

变量，190–191

继承，196–199

实例，188

方法，188–190

数学运算符，195–196

表示，192

富比较，192–195

特殊方法，191

对象，187–188

私有方法，190

**对象，22，187–188**

`datetime`，创建，206

求值，59

不可变，44–45

可变，44，176–177

`range`，34–35

**一维数组，87**

**`open()` 函数，204–205**

**`in` 运算符，26，40**

**`or` 运算符，59**

**运算符，21–22**

布尔运算符，58–59

布尔运算符，126–127

比较运算符，57–58，93–94

相等/不等运算符，56–57，125

`in`，40

数学运算符，28–29

`or`，59

海象运算符，60

**过拟合，155**

包，`zoneinfo`，207

**`Pandas` DataFrames。** *请参阅* `DataFrames`

**参数**

默认值，71–72

作为函数参数，78

关键字赋值，70–71

关键字通配符，74–75

可变默认值，72–73

位置通配符，74

仅限位置参数，73

**解析器，14**

**`pass` 语句，18**

**`Plotly`，148–149**

**泊松分布，107–108**

**多项式，100–101**

**`pop` 方法，30**

**`print` 语句，20–21**

**私有方法，190**

**过程式编程，174**

**编程语言，高级与低级，15**

**真子集，49**

**公有方法，190**

**`Punkt` 分词器，161**

**`Python`，类型，14–15**

**`PyTorch`，154**

### Q-R

**引号，字符串与，33**

**`raise` 语句，18–19**

**`range`，34–35**

**原始字符串，33**

**读取文件，204–205**

**`re.compile()` 函数，211**

**`reduce()` 函数，178，179**

**`re.findall()` 函数，210**

**`re.finditer()` 函数，211**

**正则表达式，207–208**

编译，211–212

组，209–210

命名组，210

替换，211

使用命名组，211

**`re.match()` 函数，207–208**

**移除，字典中的条目，39–40**

**`replace` 方法，131–132**

**表示方法，192**

**`re.search()` 函数，208**

**`return` 语句，18，75**

**`reverse` 方法，32**

**富比较方法，192–195**

**运行语句，4**

### S

**`Scikit-learn`，154**

估计器，156

`MinMaxScaler` 转换器，154–155

拆分测试集和训练集，155–156

训练模型，156

训练与测试，156

教程，157

**`SciPy`，103**

连续分布，108

指数分布，110

正态分布，108–110

均匀分布，110–111

离散分布，105

二项分布，105–107

泊松分布，107–108

**`scipy.misc` 子模块，104–105**

**`scipy.special` 子模块，105**

**`scipy.stats` 子模块，105**

**作用域，20，75–76，173–174**

继承，174

**`Seaborn`，144–145**

绘图类型，148

主题，145–147

**序列，14，25**

数组与序列，91

冻结集合与序列，53

索引，26

查询，27–28

交集，51

列表，29

添加和删除元素，30–31

嵌套，31

排序，32

解包，31–32

数学运算，28–29

切片，27

成员测试，26

元组，29

解包，31–32

**集合，46–48**

差集，51

不相交，48

真子集，49

子集与集合，49

超集与集合，50

对称差集，51

并集，50

更新，51–52

**共享操作，25**

**`similar()` 方法，165**

**切片，27**

数组，89–90

`DataFrame`，122

**`sort()` 方法，201–202**

**`sort` 方法，32**

**`sorted()` 函数，202–204**

**排序，列表，32，201–204**

**特殊字符，33**

**语句，15–16**

`assert`，16–17

赋值，17

`break`，19，64

代码块，56，63–64

`continue`，19，64–65

`delete`，18

`elif`，62

`else`，61

表达式，16

`future`，20

`global`，20

`if`，59–62

`import`，19–20

多条语句，16

`nonlocal`，20

`pass`，18

`print`，20–21

`raise`，18–19

`return`，18，75

运行，4

`yield`，18

**停用词，163–164**

**字符串，14，32–33**

f-字符串，34

辅助函数，33–34

引号与字符串，33

原始字符串，33

特殊字符，33

转换为 `datetime` 对象，207

**子模块**

`scipy.misc`，104–105

`scipy.special`，105

`scipy.stats`，105

**`subset()` 方法，49**

**替换，211**

**超集，50**

**`symmetric_difference()` 方法，51**

**语法**

方括号，121–122

装饰器，79–80

列表推导式，179–180

### T

**`tail` 方法，118**

**`TensorFlow`，153**

**文本单元格，6–8**

**`Text` 类，165**

`collocations()` 方法，165

`concordance()` 方法，165

`dispersion_plot()` 方法，165–166

`findall()` 方法，165

`similar()` 方法，165

**第三方库，85**

**时间序列数据，206**

**时区，为 `datetime` 对象设置，207**

**`to_bytes()` 方法，187–188**

**分词器，161**

**转换，154–155**

**`tuple()` 构造函数，29**

**元组，29**

创建，29–30

解包，31–32

**二维数组，88**

索引与切片，90

**类型，14–15。** *另请参阅* `序列`

### U

**均匀分布，110–111**

**`union()` 方法，50**

**更新**

列，-129

集合，51–52

### V

**`values()` 方法，40**

**变量，190–191**

**视图，94**

更改其中的值，94

**可视化库，151**

`Bokeh`，149–150

`matplotlib`，135–136

颜色，139

创建多个坐标轴，143–144

带标签的数据，140–141

线条样式，138

标记类型，137–138

面向对象风格，143

绘制多组数据，141–143

图表样式设置，137，139–140

`Plotly`，148–149

`Seaborn`，144–145

图表类型，148

主题，145–147

### W

**海象运算符，60**

**`while` 循环，62–63**

**包装函数，77–78**

**写入文件数据，204–205**

### X-Y-Z

**`yield` 语句，18**

**`zoneinfo` 包，207**

![一张图展示了一个标题为“数据科学书籍、电子书和视频”的页面。该页面列出了一些技能，例如机器学习、分析、Hadoop 和 Spark、R 编程等。左下角显示 Inform IT 和 Pearson 的标志。](img/bm01.jpg)![一则广告页面展示了在 inform it dot com 注册产品可享受 35% 折扣的优惠。提供了产品和 Inform IT 的详细信息。左下角显示社交媒体、Inform IT 和 Pearson 的标志。](img/bm02.jpg)

## 代码片段

许多标题包含编程代码或配置示例。为了优化这些元素的呈现，请在单列横向模式下查看电子书，并将字体大小调整为最小设置。除了在可重排文本格式中呈现代码和配置外，我们还包含了模仿印刷书籍呈现方式的代码图片；因此，在可重排格式可能影响代码列表呈现的地方，您会看到“点击此处查看代码图片”链接。点击该链接可查看与印刷版一致的代码图片。要返回之前查看的页面，请点击设备或应用程序上的“返回”按钮。

![Image](img/`#ch01.xhtml#p004pro01`)

![Image](img/`#ch01.xhtml#p012pro02`)

![Image](img/`#ch01.xhtml#p012pro03`)

# 第2章

![Image](img/`#ch02.xhtml#p015pro02`)

![Image](img/`#ch02.xhtml#p016pro01`)

![Image](img/`#ch02.xhtml#p016pro02`)

![Image](img/`#ch02.xhtml#p017pro01`)

![Image](img/`#ch02.xhtml#p017pro03`)

![Image](img/`#ch02.xhtml#p018pro01`)

![Image](img/`#ch02.xhtml#p019pro01`)

![Image](img/`#ch02.xhtml#p019pro02`)

![Image](img/`#ch02.xhtml#p020pro01`)

![Image](img/`#ch02.xhtml#p020pro02`)

![Image](img/`#ch02.xhtml#p021pro04`)

![Image](img/`#ch02.xhtml#p022pro05`)

# 第3章

![Image](img/`#ch03.xhtml#p026pro01`)

![Image](img/`#ch03.xhtml#p026pro03`)

![Image](img/`#ch03.xhtml#p027pro01`)

![Image](img/`#ch03.xhtml#p027pro04`)

![Image](img/`#ch03.xhtml#p028pro01`)

![Image](img/`#ch03.xhtml#p028pro02`)

![Image](img/`#ch03.xhtml#p028pro05`)

![Image](img/`#ch03.xhtml#p028pro06`)

![Image](img/`#ch03.xhtml#p029pro01`)

![Image](img/`#ch03.xhtml#p029pro02`)

![Image](img/`#ch03.xhtml#p029pro03`)

![Image](img/`#ch03.xhtml#p030pro01`)

![Image](img/`#ch03.xhtml#p030pro02`)

![Image](img/`#ch03.xhtml#p030pro03`)

![Image](img/`#ch03.xhtml#p031pro01`)

![Image](img/`#ch03.xhtml#p031pro02`)

![Image](img/`#ch03.xhtml#p031pro03`)

![Image](img/`#ch03.xhtml#p031pro04`)

![Image](img/`#ch03.xhtml#p032pro01`)

![Image](img/`#ch03.xhtml#p032pro02`)

![Image](img/`#ch03.xhtml#p032pro03`)

![Image](img/`#ch03.xhtml#p033pro01`)

![Image](img/`#ch03.xhtml#p033pro02`)

![Image](img/`#ch03.xhtml#p033pro03`)

![Image](img/`#ch03.xhtml#p033pro04`)

![Image](img/`#ch03.xhtml#p033pro05`)

![Image](img/`#ch03.xhtml#p034pro01`)

![Image](img/`#ch03.xhtml#p034pro02`)

![Image](img/`#ch03.xhtml#p034pro03`)

![Image](img/`#ch03.xhtml#p034pro04`)

# 第4章

![Image](img/`#ch04.xhtml#p037pro01`)

![Image](img/`#ch04.xhtml#p038pro01`)

![Image](img/`#ch04.xhtml#p038pro02`)

![Image](img/`#ch04.xhtml#p038pro03`)

![Image](img/`#ch04.xhtml#p038pro04`)

![Image](img/`#ch04.xhtml#p038pro05`)

![Image](img/`#ch04.xhtml#p038pro06`)

![Image](img/`#ch04.xhtml#p039pro01`)

![Image](img/`#ch04.xhtml#p039pro02`)

![Image](img/`#ch04.xhtml#p039pro03`)

![Image](img/`#ch04.xhtml#p039pro04`)

![Image](img/`#ch04.xhtml#p040pro01`)

![Image](img/`#ch04.xhtml#p040pro02`)

![Image](img/`#ch04.xhtml#p040pro03`)

![Image](img/`#ch04.xhtml#p040pro04`)

![Image](img/`#ch04.xhtml#p041pro03`)

![Image](img/`#ch04.xhtml#p041pro04`)

![Image](img/`#ch04.xhtml#p041pro05`)

![Image](img/`#ch04.xhtml#p041pro06`)

![Image](img/`#ch04.xhtml#p041pro07`)

![Image](img/`#ch04.xhtml#p042pro01`)

![Image](img/`#ch04.xhtml#p042pro02`)

![Image](img/`#ch04.xhtml#p042pro03`)

![Image](img/`#ch04.xhtml#p042pro04`)

![Image](img/`#ch04.xhtml#p042pro05`)

![Image](img/`#ch04.xhtml#p042pro06`)

![Image](img/`#ch04.xhtml#p043pro01`)

![Image](img/`#ch04.xhtml#p043pro03`)

![Image](img/`#ch04.xhtml#p043pro04`)

![Image](img/`#ch04.xhtml#p043pro05`)

![Image](img/`#ch04.xhtml#p043pro06`)

![Image](img/`#ch04.xhtml#p044pro01`)

![Image](img/`#ch04.xhtml#p044pro02`)

![Image](img/`#ch04.xhtml#p044pro03`)

![Image](img/`#ch04.xhtml#p044pro04`)

![Image](img/`#ch04.xhtml#p044pro05`)

![Image](img/`#ch04.xhtml#p045pro01`)

![Image](img/`#ch04.xhtml#p045pro02`)

![Image](img/`#ch04.xhtml#p045pro03`)

![Image](img/`#ch04.xhtml#p046pro02`)

![Image](img/`#ch04.xhtml#p046pro03`)

![Image](img/`#ch04.xhtml#p046pro04`)

![Image](img/`#ch04.xhtml#p046pro05`)

![Image](img/`#ch04.xhtml#p047pro01`)

![Image](img/`#ch04.xhtml#p047pro03`)

![Image](img/`#ch04.xhtml#p047pro04`)

![Image](img/`#ch04.xhtml#p047pro05`)

![Image](img/`#ch04.xhtml#p047pro06`)

![Image](img/`#ch04.xhtml#p047pro07`)

![Image](img/`#ch04.xhtml#p048pro01`)

![Image](img/`#ch04.xhtml#p048pro02`)

![Image](img/`#ch04.xhtml#p048pro03`)

![Image](img/`#ch04.xhtml#p049pro01`)

![Image](img/`#ch04.xhtml#p049pro03`)

![Image](img/`#ch04.xhtml#p049pro04`)

![Image](img/`#ch04.xhtml#p049pro05`)

![Image](img/`#ch04.xhtml#p050pro01`)

![Image](img/`#ch04.xhtml#p050pro02`)

![Image](img/`#ch04.xhtml#p050pro03`)

![Image](img/`#ch04.xhtml#p051pro01`)

![Image](img/`#ch04.xhtml#p051pro02`)

![Image](img/`#ch04.xhtml#p051pro03`)

![Image](img/`#ch04.xhtml#p051pro04`)

![Image](img/`#ch04.xhtml#p052pro01`)

![Image](img/`#ch04.xhtml#p052pro02`)

![Image](img/`#ch04.xhtml#p052pro03`)

![Image](img/`#ch04.xhtml#p052pro04`)

![Image](img/`#ch04.xhtml#p052pro05`)

![Image](img/`#ch04.xhtml#p052pro06`)

![Image](img/`#ch04.xhtml#p052pro07`)

![Image](img/`#ch04.xhtml#p053pro01`)

# 第5章

![Image](img/`#ch05.xhtml#p056pro01`)

![Image](img/`#ch05.xhtml#p056pro02`)

![Image](img/`#ch05.xhtml#p057pro01`)

![Image](img/`#ch05.xhtml#p057pro04`)

![Image](img/`#ch05.xhtml#p058pro01`)

![Image](img/`#ch05.xhtml#p059pro01`)

![Image](img/`#ch05.xhtml#p059pro02`)

![Image](img/`#ch05.xhtml#p059pro03`)

![Image](img/`#ch05.xhtml#p059pro04`)

![Image](img/`#ch05.xhtml#p060pro01`)

![Image](img/`#ch05.xhtml#p060pro02`)

![Image](img/`#ch05.xhtml#p060pro03`)

![Image](img/`#ch05.xhtml#p060pro04`)

![Image](img/`#ch05.xhtml#p061pro01`)

![Image](img/`#ch05.xhtml#p061pro02`)

![Image](img/`#ch05.xhtml#p061pro03`)

![Image](img/`#ch05.xhtml#p062pro01`)

![Image](img/`#ch05.xhtml#p062pro02`)

![Image](img/`#ch05.xhtml#p063pro01`)

![Image](img/`#ch05.xhtml#p063pro02`)

![Image](img/`#ch05.xhtml#p063pro03`)

![Image](img/`#ch05.xhtml#p064pro01`)

![Image](img/`#ch05.xhtml#p064pro02`)

![Image](img/`#ch05.xhtml#p065pro01`)

![Image](img/`#ch05.xhtml#p065pro02`)

# 第6章

![Image](img/`#ch06.xhtml#p068pro01`)

![Image](img/`#ch06.xhtml#p068pro03`)

![Image](img/`#ch06.xhtml#p068pro04`)

![Image](img/`#ch06.xhtml#p069pro01`)

![Image](img/`#ch06.xhtml#p069pro02`)

![Image](img/`#ch06.xhtml#p069pro03`)

![Image](img/`#ch06.xhtml#p070pro01`)

![Image](img/`#ch06.xhtml#p070pro02`)

![Image](img/`#ch06.xhtml#p071pro01`)

![Image](img/`#ch06.xhtml#p071pro02`)

![Image](img/`#ch06.xhtml#p071pro03`)

![Image](img/`#ch06.xhtml#p072pro01`)

![Image](img/`#ch06.xhtml#p072pro02`)

![Image](img/`#ch06.xhtml#p073pro01`)

![Image](img/`#ch06.xhtml#p073pro02`)

![Image](img/`#ch06.xhtml#p073pro03`)

![Image](img/`#ch06.xhtml#p074pro01`)

![Image](img/`#ch06.xhtml#p074pro02`)

![Image](img/`#ch06.xhtml#p074pro03`)

![Image](img/`#ch06.xhtml#p075pro01`)

![Image](img/`#ch06.xhtml#p075pro02`)

![Image](img/`#ch06.xhtml#p075pro03`)

![Image](img/`#ch06.xhtml#p076pro01`)

![Image](img/`#ch06.xhtml#p076pro02`)

![Image](img/`#ch06.xhtml#p077pro01`)

![Image](img/`#ch06.xhtml#p077pro02`)

![Image](img/`#ch06.xhtml#p078pro01`)

![Image](img/`#ch06.xhtml#p078pro02`)

![Image](img/`#ch06.xhtml#p079pro01`)

![Image](img/`#ch06.xhtml#p079pro02`)

![Image](img/`#ch06.xhtml#p080pro01`)

![Image](img/`#ch06.xhtml#p081pro01`)

![Image](img/`#ch06.xhtml#p081pro02`)

# 第7章

![Image](img/`#ch07.xhtml#p086pro01`)

![Image](img/`#ch07.xhtml#p087pro01`)

![Image](img/`#ch07.xhtml#p088pro01`)

![Image](img/`#ch07.xhtml#p088pro02`)

![Image](img/`#ch07.xhtml#p089pro01`)

![Image](img/`#ch07.xhtml#p089pro02`)

![Image](img/`#ch07.xhtml#p089pro03`)

![Image](img/`#ch07.xhtml#p090pro01`)

![Image](img/`#ch07.xhtml#p091pro01`)

![Image](img/`#ch07.xhtml#p091pro02`)

![Image](img/`#ch07.xhtml#p092pro01`)

![Image](img/`#ch07.xhtml#p092pro02`)

![Image](img/`#ch07.xhtml#p093pro01`)

![Image](img/`#ch07.xhtml#p093pro02`)

![Image](img/`#ch07.xhtml#p093pro03`)

![Image](img/`#ch07.xhtml#p094pro01`)

![Image](img/`#ch07.xhtml#p095pro01`)

![Image](img/`#ch07.xhtml#p095pro02`)

![Image](img/`#ch07.xhtml#p096pro01`)

![Image](img/`#ch07.xhtml#p097pro01`)

![Image](img/`#ch07.xhtml#p097pro02`)

![Image](img/`#ch07.xhtml#p098pro01`)

![Image](img/`#ch07.xhtml#p098pro03`)

![Image](img/`#ch07.xhtml#p099pro01`)

![Image](img/`#ch07.xhtml#p100pro01`)

![Image](img/`#ch07.xhtml#p101pro01`)

![Image](img/`#ch07.xhtml#p101pro03`)

![Image](img/`#ch07.xhtml#p102pro01`)

![Image](img/`#ch07.xhtml#p102pro02`)

# 第8章

![Image](img/`#ch08.xhtml#p104pro01`)

![Image](img/`#ch08.xhtml#p104pro02`)

![Image](img/`#ch08.xhtml#p105pro01`)

![Image](img/`#ch08.xhtml#p105pro02`)

![Image](img/`#ch08.xhtml#p105pro03`)

![Image](img/`#ch08.xhtml#p106pro01`)

![Image](img/`#ch08.xhtml#p106pro02`)

![Image](img/`#ch08.xhtml#p107pro01`)

![Image](img/`#ch08.xhtml#p107pro02`)

![Image](img/`#ch08.xhtml#p108pro01`)

![Image](img/`#ch08.xhtml#p109pro01`)

![Image](img/`#ch08.xhtml#p110pro01`)

![Image](img/`#ch08.xhtml#p110pro02`)

![Image](img/`#ch08.xhtml#p110pro03`)

# 第9章

![Image](img/`#ch09.xhtml#p114pro01`)

![Image](img/`#ch09.xhtml#p114pro02`)

![Image](img/`#ch09.xhtml#p115pro01`)

![Image](img/`#ch09.xhtml#p115pro02`)

![Image](img/`#ch09.xhtml#p115pro03`)

![Image](img/`#ch09.xhtml#p115pro04`)

![Image](img/`#ch09.xhtml#p116pro01`)

![Image](img/`#ch09.xhtml#p116pro02`)

![Image](img/`#ch09.xhtml#p118pro01`)

![Image](img/`#ch09.xhtml#p119pro01`)

![Image](img/`#ch09.xhtml#p119pro02`)

![Image](img/`#ch09.xhtml#p119pro03`)

![Image](img/`#ch09.xhtml#p120pro01`)

![Image](img/`#ch09.xhtml#p121pro01`)

![Image](img/`#ch09.xhtml#p121pro02`)

![Image](img/`#ch09.xhtml#p121pro03`)

![Image](img/`#ch09.xhtml#p122pro01`)

![Image](img/`#ch09.xhtml#p123pro01`)

![Image](img/`#ch09.xhtml#p123pro02`)

![Image](img/`#ch09.xhtml#p123pro03`)

![Image](img/`#ch09.xhtml#p124pro01`)

![Image](img/`#ch09.xhtml#p124pro02`)

![Image](img/`#ch09.xhtml#p124pro03`)

![Image](img/`#ch09.xhtml#p125pro01`)

![Image](img/`#ch09.xhtml#p125pro02`)

![Image](img/`#ch09.xhtml#p125pro03`)

![Image](img/`#ch09.xhtml#p126pro01`)

![Image](img/`#ch09.xhtml#p126pro02`)

![Image](img/`#ch09.xhtml#p126pro03`)

![Image](img/`#ch09.xhtml#p126pro04`)

![Image](img/`#ch09.xhtml#p126pro05`)

![Image](img/`#ch09.xhtml#p127pro01`)

![Image](img/`#ch09.xhtml#p127pro02`)

![Image](img/`#ch09.xhtml#p127pro03`)

![Image](img/`#ch09.xhtml#p127pro04`)

![Image](img/`#ch09.xhtml#p127pro05`)

![Image](img/`#ch09.xhtml#p127pro06`)

![Image](img/`#ch09.xhtml#p128pro01`)

![Image](img/`#ch09.xhtml#p128pro02`)

![Image](img/`#ch09.xhtml#p128pro03`)

![Image](img/`#ch09.xhtml#p129pro01`)

![Image](img/`#ch09.xhtml#p129pro02`)

![Image](img/`#ch09.xhtml#p130pro01`)

![Image](img/`#ch09.xhtml#p130pro02`)

![Image](img/`#ch09.xhtml#p130pro03`)

![Image](img/`#ch09.xhtml#p131pro01`)

![Image](img/`#ch09.xhtml#p131pro02`)

![Image](img/`#ch09.xhtml#p132pro01`)

![Image](img/`#ch09.xhtml#p132pro02`)

![Image](img/`#ch09.xhtml#p132pro03`)

![Image](img/`#ch09.xhtml#p132pro04`)

![Image](img/`#ch09.xhtml#p133pro01`)

# 第10章

![Image](img/`#ch10.xhtml#p0136pro03`)

![Image](img/`#ch10.xhtml#p137pro01`)

![Image](img/`#ch10.xhtml#p138pro01`)

![Image](img/`#ch10.xhtml#p139pro01`)

![Image](img/`#ch10.xhtml#p139pro02`)

![Image](img/`#ch10.xhtml#p140pro01`)

![Image](img/`#ch10.xhtml#p140pro02`)

![Image](img/`#ch10.xhtml#p141pro01`)

![Image](img/`#ch10.xhtml#p141pro02`)

![Image](img/`#ch10.xhtml#p141pro03`)

![Image](img/`#ch10.xhtml#p142pro01`)

![Image](img/`#ch10.xhtml#p143pro01`)

![Image](img/`#ch10.xhtml#p143pro02`)

![Image](img/`#ch10.xhtml#p145pro01`)

![Image](img/`#ch10.xhtml#p145pro02`)

![Image](img/`#ch10.xhtml#p146pro01`)

![Image](img/`#ch10.xhtml#p0146pro02`)

![Image](img/`#ch10.xhtml#p0147pro01`)

![Image](img/`#ch10.xhtml#p0148pro01`)

![Image](img/`#ch10.xhtml#p148pro02`)

![Image](img/`#ch10.xhtml#p149pro01`)

![Image](img/`#ch10.xhtml#p151pro01`)

# 第11章

![Image](img/`#ch11.xhtml#p154pro01`)

![Image](img/`#ch11.xhtml#p155pro01`)

![Image](img/`#ch11.xhtml#p156pro01`)

![Image](img/`#ch11.xhtml#p156pro02`)

# 第12章

![Image](img/`#ch12.xhtml#p159pro01`)

![Image](img/`#ch12.xhtml#p160pro01`)

![Image](img/`#ch12.xhtml#p160pro02`)

![Image](img/`#ch12.xhtml#p160pro03`)

![Image](img/`#ch12.xhtml#p161pro01`)

![Image](img/`#ch12.xhtml#p162pro01`)

![Image](img/`#ch12.xhtml#p162pro02`)

![Image](img/`#ch12.xhtml#p163pro01`)

![Image](img/`#ch12.xhtml#p164pro01`)

![Image](img/`#ch12.xhtml#p165pro01`)

![Image](img/`#ch12.xhtml#p165pro06`)

![Image](img/`#ch12.xhtml#p165pro02`)

![Image](img/`#ch12.xhtml#p165pro03`)

![Image](img/`#ch12.xhtml#p165pro04`)

![Image](img/`#ch12.xhtml#p165pro05`)

![Image](img/`#ch12.xhtml#p166pro01`)

![Image](img/`#ch12.xhtml#p166pro02`)

![Image](img/`#ch12.xhtml#p167pro01`)

![Image](img/`#ch12.xhtml#p167pro02`)

![Image](img/`#ch12.xhtml#p168pro01`)

![Image](img/`#ch12.xhtml#p168pro02`)

# 第13章

![Image](img/`#ch13.xhtml#p174pro01`)

![Image](img/`#ch13.xhtml#p175pro01`)

![Image](img/`#ch13.xhtml#p175pro02`)

![Image](img/`#ch13.xhtml#p175pro03`)

![Image](img/`#ch13.xhtml#p176pro01`)

![Image](img/`#ch13.xhtml#p176pro02`)

![Image](img/`#ch13.xhtml#p177pro01`)

![Image](img/`#ch13.xhtml#p177pro02`)

![Image](img/`#ch13.xhtml#p177pro03`)

![Image](img/`#ch13.xhtml#p178pro01`)

![Image](img/`#ch13.xhtml#p178pro02`)

![Image](img/`#ch13.xhtml#p178pro03`)

![Image](img/`#ch13.xhtml#p179pro01`)

![Image](img/`#ch13.xhtml#p179pro02`)

![Image](img/`#ch13.xhtml#p179pro03`)

![Image](img/`#ch13.xhtml#p179pro04`)

![Image](img/`#ch13.xhtml#p180pro01`)

![Image](img/`#ch13.xhtml#p180pro02`)

![Image](img/`#ch13.xhtml#p180pro03`)

![Image](img/`#ch13.xhtml#p180pro04`)

![Image](img/`#ch13.xhtml#p181pro01`)

![Image](img/`#ch13.xhtml#p181pro02`)

![Image](img/`#ch13.xhtml#p181pro03`)

![Image](img/`#ch13.xhtml#p181pro04`)

![Image](img/`#ch13.xhtml#p181pro05`)

![Image](img/`#ch13.xhtml#p181pro06`)

![Image](img/`#ch13.xhtml#p182pro01`)

![Image](img/`#ch13.xhtml#p182pro03`)

![Image](img/`#ch13.xhtml#p183pro01`)

![Image](img/`#ch13.xhtml#p183pro02`)

![Image](img/`#ch13.xhtml#p183pro03`)

![Image](img/`#ch13.xhtml#p184pro01`)

![Image](img/`#ch13.xhtml#p184pro02`)

![Image](img/`#ch13.xhtml#p185pro01`)

# 第14章

![Image](img/`#ch14.xhtml#p188pro01`)

![Image](img/`#ch14.xhtml#p188pro02`)

![Image](img/`#ch14.xhtml#p188pro04`)

![Image](img/`#ch14.xhtml#p188pro05`)

![Image](img/`#ch14.xhtml#p188pro06`)

![Image](img/`#ch14.xhtml#p189pro01`)

![Image](img/`#ch14.xhtml#p189pro02`)

![Image](img/`#ch14.xhtml#p189pro03`)

![Image](img/`#ch14.xhtml#p190pro01`)

![Image](img/`#ch14.xhtml#p190pro02`)

![Image](img/`#ch14.xhtml#p191pro01`)

![Image](img/`#ch14.xhtml#p192pro01`)

![Image](img/`#ch14.xhtml#p193pro01`)

![Image](img/`#ch14.xhtml#p193pro02`)

![Image](img/`#ch14.xhtml#p194pro01`)

![Image](img/`#ch14.xhtml#p195pro01`)

![Image](img/`#ch14.xhtml#p195pro02`)

![Image](img/`#ch14.xhtml#p196pro01`)

![Image](img/`#ch14.xhtml#p197pro03`)

![Image](img/`#ch14.xhtml#p197pro04`)

![Image](img/`#ch14.xhtml#p197pro05`)

![Image](img/`#ch14.xhtml#p198pro01`)

![Image](img/`#ch14.xhtml#p198pro02`)

![Image](img/`#ch14.xhtml#p198pro03`)

![Image](img/`#ch14.xhtml#p198pro04`)

![Image](img/`#ch14.xhtml#p199pro01`)

![Image](img/`#ch14.xhtml#p200pro02`)

# 第15章

![Image](img/`#ch15.xhtml#p201pro01`)

![Image](img/`#ch15.xhtml#p202pro01`)

![Image](img/`#ch15.xhtml#p202pro02`)

![Image](img/`#ch15.xhtml#p202pro03`)

![Image](img/`#ch15.xhtml#p202pro04`)

![Image](img/`#ch15.xhtml#p202pro05`)

![Image](img/`#ch15.xhtml#p202pro06`)

![Image](img/`#ch15.xhtml#p202pro07`)

![Image](img/`#ch15.xhtml#p203pro01`)

![Image](img/`#ch15.xhtml#p203pro02`)

![Image](img/`#ch15.xhtml#p204pro01`)

![Image](img/`#ch15.xhtml#p204pro02`)

![Image](img/`#ch15.xhtml#p205pro01`)

![Image](img/`#ch15.xhtml#p205pro02`)

![Image](img/`#ch15.xhtml#p205pro03`)

![Image](img/`#ch15.xhtml#p205pro04`)

![Image](img/`#ch15.xhtml#p205pro05`)

![Image](img/`#ch15.xhtml#p205pro06`)

![Image](img/`#ch15.xhtml#p206pro01`)

![Image](img/`#ch15.xhtml#p206pro02`)

![Image](img/`#ch15.xhtml#p207pro01`)

![Image](img/`#ch15.xhtml#p207pro02`)

![Image](img/`#ch15.xhtml#p207pro03`)

![Image](img/`#ch15.xhtml#p207pro04`)

![Image](img/`#ch15.xhtml#p208pro01`)

![Image](img/`#ch15.xhtml#p208pro02`)

![Image](img/`#ch15.xhtml#p208pro03`)

![Image](img/`#ch15.xhtml#p208pro04`)

![Image](img/`#ch15.xhtml#p209pro01`)

![Image](img/`#ch15.xhtml#p209pro02`)

![Image](img/`#ch15.xhtml#p209pro03`)

![Image](img/`#ch15.xhtml#p209pro04`)

![Image](img/`#ch15.xhtml#p210pro01`)

![Image](img/`#ch15.xhtml#p210pro02`)

![Image](img/`#ch15.xhtml#p210pro03`)

![Image](img/`#ch15.xhtml#p211pro01`)

![Image](img/`#ch15.xhtml#p211pro02`)

![Image](img/`#ch15.xhtml#p211pro03`)

![Image](img/`#ch15.xhtml#p211pro04`)

![Image](img/`#ch15.xhtml#p212pro01`)

![Image](img/`#ch15.xhtml#p212pro02`)

![Image](img/`#ch15.xhtml#p213pro01`)

# 附录

![Image](img/`#app.xhtml#p216pro01`)

![Image](img/`#app.xhtml#p217pro04`)

![Image](img/`#app.xhtml#p217pro05`)

![Image](img/`#app.xhtml#p218pro01`)

![Image](img/`#app.xhtml#p218pro02`)

![Image](img/`#app.xhtml#p218pro03`)

![Image](img/`#app.xhtml#p218pro04`)

![Image](img/`#app.xhtml#p218pro05`)

![Image](img/`#app.xhtml#p218pro06`)

![Image](img/`#app.xhtml#p219pro01`)

![Image](img/`#app.xhtml#p219pro02`)

![Image](img/`#app.xhtml#p219pro03`)

![Image](img/`#app.xhtml#p219pro04`)

![Image](img/`#app.xhtml#p219pro06`)

![Image](img/`#app.xhtml#p219pro07`)

![Image](img/`#app.xhtml#p219pro08`)