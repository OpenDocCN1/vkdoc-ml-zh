## 4 其他数据结构

统计思维终将像读写能力一样，成为高效公民所必需的素养。

——塞缪尔·S·威尔克斯

本章内容

*   创建字典

*   访问和更新字典内容

*   创建集合

*   集合操作

基于顺序的数据表示功能强大，但其他数据表示方式也是可行的。字典和集合是不依赖于数据顺序的数据结构。两者都是强大的模型，是 Python 工具箱中不可或缺的组成部分。

### 字典

想象一下，你正在进行一项研究，以确定学生身高与平均绩点（GPA）之间是否存在相关性。你需要一种数据结构来表示单个学生的数据，包括姓名、身高和 GPA。你可以将信息存储在列表或元组中。不过，你必须跟踪哪个索引代表哪条数据。更好的表示方式是为数据添加标签，这样你就不需要跟踪从索引到属性的转换。你可以使用字典将数据存储为键/值对。字典中的每个项或值都通过键来访问。这种查找非常高效，并且比搜索长序列快得多。

在键/值对中，键和值用冒号分隔。你可以呈现多个键/值对，用逗号分隔并括在花括号中。因此，学生记录的字典可能如下所示：

```
{ 'name': 'Betty', 'height': 62,'gpa': 3.6 }
```

该字典的键是字符串 `'name'`、`'height'` 和 `'gpa'`。每个键指向一条数据：`'name'` 指向字符串 `'Betty'`，`'height'` 指向整数 `62`，`'gpa'` 指向浮点数 `3.6`。值可以是任何类型，不过对键的类型有一些限制，本章后面会讨论。

#### 创建字典

你可以创建带有初始数据或不带初始数据的字典。通过使用 `dict()` 构造方法或直接使用花括号，可以创建一个空字典：

```
dictionary = dict()

dictionary

{}

dictionary = {}

dictionary

{}
```

第一个示例使用 `dict()` 构造方法创建了一个空字典，并将该字典赋值给名为 `dictionary` 的变量。第二个示例使用花括号创建了一个空字典，并赋值给同一个变量。这两个示例都生成了一个空字典，用空花括号表示。

你也可以创建带有初始数据的字典。一种方法是像下面这个示例一样，将键和值作为命名参数传入：

```
subject_1 = dict(name='Paula', height=64, gpa=3.8, ranking=1)
```

另一种方法是将键/值对作为列表或元组的列表/元组传入构造方法，其中每个子列表或子元组代表一个键/值对：

```
subject_2 = dict([['name','Paula'],['height',64],['gpa',3.8]],['ranking',1])
```

第三种方法是使用花括号创建字典，键和值之间用冒号配对，并用逗号分隔：

```
subject_3 = {'name':'Paula', 'height':64, 'gpa':3.8, 'ranking':1}
```

只要使用相同的键和值，这三种方法创建的字典在求值时结果相同：

```
subject_1 == subject_2 == subject_3

True
```

#### 使用键进行访问、添加和更新

字典的键提供了一种访问和更改数据的方法。通常，你可以像访问序列中的索引一样，通过方括号中的相关键来访问数据：

```
student_record = {'name':'Paula', 'height':64, 'gpa':3.8}

student_record['name']

'Paula'

student_record['height']

student_record['gpa']

3.8
```

如果你想向现有字典中添加新的键/值对，可以使用相同的语法将值赋给该键位：

```
student_record['applied'] = '2019-10-31'

student_record

{'name':'Paula',

 'height':64,

 'gpa':3.8,

 'applied': '2019-10-31'}
```

现在，这个新的键/值对已包含在原始字典中。

如果你想更新现有键的值，也可以使用方括号语法：

```
student_record['gpa'] = 3.0

student_record['gpa']

3.0
```

一种便捷的数值递增方法是使用 `+=` 运算符，它是通过加法更新值的快捷方式：

```
student_record['gpa'] += 1.0

student_record['gpa']

4.0
```

#### 从字典中删除条目

有时你需要删除数据，例如当字典中包含个人身份信息（PII）时。假设你的数据中包含学生的 ID，但这个 ID 与某项特定研究无关。为了保护学生的隐私，你可以将该 ID 的值更新为 `None`：

```
student_record = {'advisor': 'Pickerson',

                  'first': 'Julia',

                  'gpa': 4.0,

                  'last': 'Brown',

                  'major': 'Data Science',

                  'minor': 'Math'}

student_record['id'] = None

student_record

{'advisor': 'Pickerson',

 'first': 'Julia',

 'gpa': 4.0,

 'id': None,

 'last': 'Brown',

 'major': 'Data Science',

 'minor': 'Math'}
```

这样可以防止任何人使用该 ID。

另一种方法是使用 `del()` 函数彻底删除该键/值对。该函数将带有方括号中键的字典作为参数，并移除相应的键/值对：

```
del(student_record['id'])

student_record

{'advisor': 'Pickerson',

 'first': 'Julia',

 'gpa': 4.0,

 'last': 'Brown',

 'major': 'Data Science',

 'minor': 'Math'}
```

**注意**

当然，要真正保护对象的身份，你还需要删除该人的姓名以及任何其他个人身份信息。

#### 字典视图

*字典视图*是能够洞察字典内部的对象。共有三种视图：`dict_keys`、`dict_values` 和 `dict_items`。每种视图类型都让你从不同角度审视字典。

字典有一个 `keys()` 方法，它会返回一个 `dict_keys` 对象。该对象让你能够访问字典当前的键：

```
keys = subject_1.keys()

keys

dict_keys(['name', 'height', 'gpa', 'ranking'])
```

`values()` 方法返回一个 `dict_values` 对象，该对象让你能够访问字典中存储的值：

```
values = subject_1.values()

values

dict_values(['Paula', 64, 4.0, 1])
```

`items()` 方法返回一个 `dict_items` 对象，该对象表示字典中的键/值对：

```
items = subject_1.items()

items

dict_items([('name', 'Paula'), ('height', 64), ('gpa', 4.0), ('ranking', 1)])
```

你可以使用 `in` 运算符测试任何这些视图的成员资格。此示例展示了如何检查键 `'ranking'` 是否在此字典中使用：

```
'ranking' in keys

True
```

此示例展示了如何检查整数 `1` 是否是字典中的值之一：

```
1 in values

True
```

此示例展示了如何检查键/值对映射 `'ranking'` 是否为 `1`：

```
('ranking',1) in items

True
```

从 Python 3.8 开始，字典视图是动态的。这意味着如果你在获取视图后更改了字典，该视图会反映新的更改。例如，假设你想从上面访问其视图的字典中删除一个键/值对，如下所示：

```
del(subject_1['ranking'])

subject_1

{'name': 'Paula', 'height': 64, 'gpa': 4.0}
```

该键/值对也会从视图对象中删除：

```
'ranking' in keys

False

1 in values

False

('ranking',1) in items

False
```

每个字典视图类型都有一个长度，你可以使用与序列相同的 `len` 函数来访问它：

```
len(keys)

len(values)

len(items)
```

从 Python 3.8 开始，你可以在 `dict_key` 视图上使用 `reversed` 函数来获取逆序的视图：

```
keys

dict_keys(['name', 'height', 'gpa'])

list(reversed(keys))

['gpa', 'height', 'name']
```

`dict_key` 视图是类似集合的对象，这意味着许多集合操作都可以在它们上面进行。此示例展示了如何创建两个字典：

```
admission_record = {'first':'Julia',

'last':'Brown',

'id': 'ax012E4',

'admitted': '2020-03-14'}

student_record = {'first':'Julia',

'last':'Brown',

'id': 'ax012E4',

'gpa':3.8,

'major':'Data Science',

'minor': 'Math',

'advisor':'Pickerson'}
```

然后你可以测试键的相等性：

```
admission_record.keys() == student_record.keys()

False
```

你也可以查找对称差集：

```
admission_record.keys() ^ student_record.keys()

{'admitted', 'advisor', 'gpa', 'major', 'minor'}
```

以下是查找交集的方法：

```
admission_record.keys() & student_record.keys()

{'first', 'id', 'last'}
```

以下是查找差集的方法：

```
admission_record.keys() - student_record.keys()

{'admitted'}
```

以下是查找并集的方法：

```
admission_record.keys() | student_record.keys()

{'admitted', 'advisor', 'first', 'gpa', 'id', 'last', 'major', 'minor'}
```

**注意**

你将在下一节中了解更多关于集合和集合操作的内容。

`key_item` 视图最常见的用途是遍历字典并对每个键/值对执行操作。以下示例使用 `for` 循环（参见第 5 章“执行控制”）来打印每个键/值对：

```
for k,v in student_record.items():

print(f"{k} => {v}")

first => Julia

last => Brown

gpa => 4.0

major => Data Science

minor => Math

advisor => Pickerson
```

你可以根据需要，对 `dict_keys` 或 `dict_values` 执行类似的循环。

### 检查字典是否包含某个键

你可以使用 `dict_key` 和 `in` 运算符来检查某个键是否在字典中使用：

```
'last' in student_record.keys()

True
```

作为一种快捷方式，你也可以在不显式调用 `dict_key` 视图的情况下测试键是否存在。相反，你只需直接对字典使用 `in`：

```
'last' in student_record

True
```

如果你想遍历字典的键，这种方法也适用。你无需直接访问 `dict_key` 视图：

```
for key in student_record:

    print(f"key:  {key}")

 key: first

 key: last

 key: gpa

 key: major

 key: minor

 key: advisor
```

### `get` 方法

尝试使用方括号语法访问字典中不存在的键会导致错误：

```
student_record['name']

KeyError                             Traceback (most recent call last)

<ipython-input-18-962c04650d3e> in <module>()

----> 1 student_record['name']

   KeyError: 'name'
```

这种类型的错误会停止在笔记本之外运行的程序执行。避免这些错误的一种方法是在访问键之前测试该键是否在字典中：

```
if 'name' in student_record:

    student_record['name']
```

此示例使用了一个 `if` 语句，该语句仅在键 `'name'` 存在于字典中时才访问它。（有关 `if` 语句的更多信息，请参见第 5 章。）

为了方便起见，字典有一个 `get()` 方法，它旨在安全地访问缺失的键。默认情况下，如果键缺失，此方法会返回 `None` 常量：

```
print( student_record.get('name') )

None
```

你还可以提供第二个参数，该参数是在键缺失时要返回的值：

```
student_record.get('name', 'no-name')

'no-name'
```

你还可以将多个 `get` 语句链接在一起：

```
student_record.get('name', admission_record.get('first', 'no-name'))

 'Julia'
```

此示例尝试从字典 `student_record` 中获取键 `'name'` 的值，如果缺失，则尝试从字典 `admission_record` 中获取键 `'first'` 的值，如果该键也缺失，则返回默认值 `'no-name'`。

### 有效的键类型

你可以更改某些对象的值，但其他对象的值是静态的。值可以被更改的对象称为*可变*对象。正如你已经看到的，列表是可变对象；其他值可以被更改的对象也是可变的。另一方面，你无法更改不可变对象的值。不可变对象包括整数、字符串、range 对象、二进制字符串和元组。

除某些元组外，不可变对象可以用作字典的键：

```
{ 1         : 'an integer',

  'string'  : 'a string',

  ('item',) : 'a tuple',

  range(12) : 'a range',

  b'binary' : 'a binary string' }
```

可变对象（例如列表）不能用作字典的有效键。如果你尝试将列表用作键，将会遇到错误：

```
{('item',): 'a tuple',

1: 'an integer',

b'binary': 'a binary string',

range(0, 12): 'a range',

'string': 'a string',

['a', 'list'] : 'a list key' }

   ---------------------------------------------------------------------------

   TypeError                                Traceback (most recent call last)

   <ipython-input-31-1b0e555de2b5> in <module>()

   ----> 1 { ['a', 'list'] : 'a list key' }

   TypeError: unhashable type: 'list'
```

内容不可变的元组可以用作字典键。因此，由数字、字符串和其他元组组成的元组都是有效的键：

# 集合

以下是关于Python集合（Set）的详细说明，包括其概念、创建方法、操作以及集合运算。

---

## 字典键与哈希

首先，需要了解字典键的哈希要求。

```python
tuple_key = (1, 'one', 1.0, ('uno',))

{ tuple_key: 'some value' }

{(1, 'one', 1.0, ('uno',)): 'some value'}
```

如果元组包含可变对象（例如列表），则该元组不是有效的键：

```python
bad_tuple = ([1, 2], 3)

{ bad_tuple: 'some value' }

TypeError                                 Traceback (most recent call last)

<ipython-input-28-b2cddfdda91e> in <module>()

      1 bad_tuple = ([1, 2], 3)

----> 2 { bad_tuple: 'some value' }

TypeError: unhashable type: 'list'
```

### `hash` 方法

你可以将字典视为一种类似索引列表的结构，它存储值，并带有一个能快速可靠地将键对象映射到相应索引号的方法。这个方法被称为哈希函数，可以在不可变的 Python 对象上以 `__hash__()` 方法的形式找到。它被设计为在后台使用，但也可以直接调用：

```python
a_string = 'a string'

a_string.__hash__()

a_tuple = 'a','b',

a_tuple.__hash__()

a_number = 13

a_number.__hash__()
```

这个 `hash` 函数使用对象的值来产生一致的输出。因此，对于可变对象，无法产生一致的哈希值。你无法获取可变对象（例如列表）的哈希值：

```python
a_list = ['a','b']

a_list.__hash__()

TypeError Traceback (most recent call last) <ipython-input-40-c4f99d4ea902> in

<module>()

      1 a_list = ['a','b']

----> 2 a_list.__hash__()

TypeError: 'NoneType' object is not callable
```

字典和列表是 Python 中最常用的数据结构之一。它们为你提供了组织数据以实现有意义且快速查找的绝佳方式。

**注意**

尽管键/值查找机制不依赖于数据的顺序，但从 Python 3.7 开始，键的顺序反映了它们被插入的顺序。

## 集合

Python 的集合数据结构实现了你可能在数学中熟悉的集合概念。*集合*是一个无序的唯一项集合。你可以将集合视为一个不允许重复对象的魔法袋。集合中的项可以是任何可哈希类型。

在 Python 中，集合表示为用花括号括起来的逗号分隔的项列表：

```python
{ 1, 'a', 4.0 }
```

你可以使用 `set()` 构造函数或直接使用花括号来创建集合。但是，当你使用空花括号时，你创建的是一个空字典，而不是空集合。如果你想创建一个空集合，必须使用 `set()` 构造函数：

```python
empty_set = set()

empty_set

set()

empty_set = {}

empty_set

{}
```

你可以使用构造函数或花括号来创建带有初始值的集合。你可以提供任何类型的序列作为参数，将根据序列中的唯一项返回一个集合：

```python
letters = 'a', 'a', 'a', 'b', 'c'

unique_letters = set(letters)

unique_letters

{'a', 'b', 'c'}

unique_chars = set('mississippi')

unique_chars

{'i', 'm', 'p', 's'}

unique_num = {1, 1, 2, 3, 4, 5, 5}

unique_num

{1, 2, 3, 4, 5}
```

与字典键非常相似，集合会对其内容进行哈希处理以确定唯一性。因此，集合的内容必须是可哈希的，因此也是不可变的。列表不能是集合的成员：

```python
bad_set = { ['a','b'], 'c' }

TypeError                                 Traceback (most recent call last)

<ipython-input-12-1179bc4af8b8> in <module>()

----> 1 bad_set = { ['a','b'], 'c' }

TypeError: unhashable type: 'list'
```

你可以使用 `add()` 方法向集合添加项：

```python
unique_num.add(6)

unique_num

{1, 2, 3, 4, 5, 6}
```

你可以使用 `in` 运算符来测试集合的成员资格：

```python
3 in unique_num

True

3 not in unique_num

False
```

你可以使用 `len()` 函数查看集合包含多少项：

```python
len(unique_num)
```

与列表一样，你可以使用 `pop()` 方法从集合中移除并返回一个项：

```python
unique_num.pop()

unique_num

{2, 3, 4, 5, 6}
```

与列表不同，你不能依赖 `pop()` 以任何特定顺序移除集合中的项。如果你想从集合中移除特定项，可以使用 `remove()` 方法：

```python
students = {'Karl', 'Max', 'Tik'}

students.remove('Karl')

students

{'Max', 'Tik'}
```

此方法不返回被移除的项。如果你尝试移除集合中不存在的项，将会遇到错误：

```python
students.remove('Barb')

KeyError                                  Traceback (most recent call last)

<ipython-input-3-a36a5744ac05> in <module>()

----> 1 students.remove('Barb')

KeyError: 'Barb'
```

你可以编写代码在移除项之前测试它是否在集合中，但有一个便捷函数 `discard()`，当你尝试移除不存在的项时，它不会抛出错误：

```python
students.discard('Barb')

students.discard('Tik')

students

{'Max'}
```

你可以使用 `clear()` 方法移除集合中的所有内容：

```python
students.clear()

students

set()
```

请记住，因为集合是无序的，所以它们不支持索引：

```python
unique_num[3]

TypeError                                 Traceback (most recent call last)

<ipython-input-16-fecab0cd5f95> in <module>()

----> 1 unique_num[3]



TypeError: 'set' object does not support indexing
```

你可以使用等于运算符 `==` 和不等于运算符 `!=` 来测试相等性（这些运算符在第 5 章中讨论过）。由于集合是无序的，因此由包含相同元素但顺序不同的序列创建的集合是相等的：

```python
first = {'a','b','c','d'}

second = {'d','c','b','a'}

first == second

True

first != second

False
```

### 集合运算

你可以对集合执行多种运算。许多集合运算既可以作为集合对象的方法使用，也可以作为独立的运算符（`<`、`<=`、`>`、`>=`、`&`、`|` 和 `^`）使用。集合方法可用于在集合与其他集合之间执行运算，也可用于集合与其他可迭代对象（即可以迭代的数据类型）之间。集合运算符仅适用于集合与其他集合（或冻结集合）之间。

#### 不相交

如果两个集合没有共同的元素，则它们是不相交的。使用 Python 集合，你可以使用 `disjoint()` 方法来测试这一点。如果你将一个偶数集合与一个奇数集合进行测试，它们没有共同的数字，因此 `disjoint()` 的结果为 `True`：

```python
# 假设 evens = {2, 4, 6, 8}
# 假设 odds = {1, 3, 5, 7}
# evens.disjoint(odds)  # 结果为 True
```

```
even = set(range(0,10,2))
```

```
even
```

```
{0, 2, 4, 6, 8}
```

```
odd = set(range(1,11,2))
```

```
odd
```

```
{1, 3, 5, 7, 9}
```

```
even.isdisjoint(odd)
```

```
True
```

## 子集

如果集合 `B` 中的所有元素都能在另一个集合 `A` 中找到，那么集合 `B` 就是集合 `A` 的子集。`subset()` 方法用于测试当前集合是否是另一个集合的子集。以下示例测试小于 21 的正 3 的倍数集合是否是小于 21 的正整数的子集：

```
nums = set(range(21))
```

```
nums
```

```
{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20}
```

```
threes = set(range(3,21,3))
```

```
threes
```

```
{3, 6, 9, 12, 15, 18}
```

```
threes.issubset(nums)
```

```
True
```

你可以使用 `<=` 运算符来测试左侧的集合是否是右侧集合的子集：

```
threes <= nums
```

```
True
```

如本章前面所述，该运算符的方法版本可以与非集合参数一起使用。以下示例测试 3 的倍数集合是否在 0 到 20 的范围内：

```
threes.issubset(range(21))
```

```
True
```

该运算符不能与非集合对象一起使用：

```
threes <= range(21)
```

```
TypeError                                 Traceback (most recent call last)

    <ipython-input-30-dbd51effe302> in <module>()

    ----> 1 threes <= range(21)

TypeError: '<=' not supported between instances of 'set' and 'range'
```

## 真子集

如果一个集合的所有元素都包含在第二个集合中，但第二个集合中的元素并非全部都在第一个集合中，那么第一个集合就是第二个集合的真子集。这相当于说第一个集合是第二个集合的子集，并且它们不相等。你可以使用 `<` 运算符来测试真子集：

```
threes < nums
```

```
True
```

```
threes < {'3','6','9','12','15','18'}
```

```
False
```

## 超集和真超集

超集是子集的反向概念：如果一个集合包含另一个集合的所有元素，那么它就是第二个集合的超集。类似地，如果一个集合是另一个集合的超集，并且它们不相等，那么它就是真超集。Python 集合有一个 `issuperset()` 方法，它接受另一个集合或任何其他可迭代对象作为参数：

```
nums.issuperset(threes)
```

```
True
```

```
nums.issuperset([1,2,3,4])
```

```
True
```

你可以使用大于或等于运算符 `>=` 来测试超集，使用大于运算符 `>` 来测试真超集：

```
nums >= threes
```

```
True
```

```
nums > threes
```

```
True
```

```
nums >= nums
```

```
True
```

```
nums > nums
```

```
False
```

## 并集

两个集合的并集是一个包含两个集合中所有元素的集合。对于 Python 集合，你可以使用 `union()` 方法（适用于集合和其他可迭代对象），以及独立的竖线运算符 `|`，它返回两个集合的并集：

```
odds = set(range(0,12,2))
```

```
odds
```

```
{0, 2, 4, 6, 8, 10}
```

```
evens = set(range(1,13,2))
```

```
evens
```

```
{1, 3, 5, 7, 9, 11}
```

```
odds.union(evens)
```

```
{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11}
```

```
odds.union(range(0,12))
```

```
{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11}
```

```
odds | evens
```

```
{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11}
```

## 交集

两个集合的交集是一个包含两个集合共有元素的集合。你可以使用 `intersection()` 方法或与运算符 `&` 来执行交集运算：

```
under_ten = set(range(10))
```

```
odds = set(range(1,21,2))
```

```
under_ten.intersection(odds)
```

```
{1, 3, 5, 7, 9}
```

```
under_ten & odds
```

```
{1, 3, 5, 7, 9}
```

## 差集

两个集合的差集是第一个集合中存在但第二个集合中不存在的所有元素。你可以使用 `difference()` 方法或减号运算符 `–` 来执行差集运算：

```
odds.difference(under_ten)
```

```
{11, 13, 15, 17, 19}
```

```
odds - under_ten
```

```
{11, 13, 15, 17, 19}
```

## 对称差集

两个集合的对称差集是一个包含仅存在于原始集合之一中的任何元素的集合。Python 集合有一个 `symmetric_difference()` 方法，以及脱字符运算符 `^`，用于计算对称差集：

```
under_ten = set(range(10))
```

```
over_five = set(range(5, 15))
```

```
under_ten.symmetric_difference(over_five)
```

```
{0, 1, 2, 3, 4, 10, 11, 12, 13, 14}
```

```
under_ten ^ over_five
```

```
{0, 1, 2, 3, 4, 10, 11, 12, 13, 14}
```

## 更新集合

Python 集合提供了多种就地更新集合内容的方法。除了使用 `update()` 添加内容到集合外，你还可以使用基于各种集合运算的变体进行更新。

以下示例展示了如何从另一个集合进行更新：

```
unique_num = {0, 1, 2}
```

```
unique_num.update( {3, 4, 5, 7} )
```

```
unique_num
```

```
{0, 1, 2, 3, 4, 5, 7}
```

以下示例展示了如何从列表进行更新：

```
unique_num.update( [8, 9, 10] )
```

```
unique_num
```

```
{0, 1, 2, 3, 4, 5, 7, 8, 9, 10}
```

以下示例展示了如何从一个范围更新差集：

```
unique_num.difference_update( range(0,12,2) )
```

```
unique_num
```

```
{1, 3, 5, 7, 9}
```

以下示例展示了如何更新交集：

```
unique_num.intersection_update( { 2, 3, 4, 5 } )
```

```
unique num
```

```
{3, 5}
```

以下示例展示了如何更新对称差集：

```
unique_num.symmetric_difference_update( {5, 6, 7 } )
```

```
unique_num
```

```
{3, 6, 7}
```

以下示例展示了如何使用并集运算符进行更新：

```
unique_letters = set("mississippi")
```

```
unique_letters
```

```
{'i', 'm', 'p', 's'}
```

```
unique_letters |= set("Arkansas")
```

```
unique_letters
```

```
{'A', 'a', 'i', 'k', 'm', 'n', 'p', 'r', 's'}
```

以下示例展示了如何使用差集运算符进行更新：

```
unique_letters -= set('Arkansas')
```

```
unique_letters
```

```
{'i', 'm', 'p'}
```

以下示例展示了如何使用交集运算符进行更新：

```
unique_letters &= set('permanent')
```

```
unique_letters
```

```
{'m', 'p'}
```

```
unique_letters ^= set('mud')  2 unique_letters
```

```
{'d', 'p', 'u'}
```

### 冻结集合

由于集合是可变的，它们不能用作字典键，甚至不能作为集合中的元素。在 Python 中，冻结集合是类似集合但不可变的对象。你可以在任何不改变其内容的操作中使用冻结集合代替集合，如下例所示：

点击此处查看代码图片

```
froze = frozenset(range(10))

froze

frozenset({0, 1, 2, 3, 4, 5, 6, 7, 8, 9})

froze < set(range(21))

True

froze & set(range(5, 15))

frozenset({5, 6, 7, 8, 9})

froze ^ set(range(5, 15))

frozenset({0, 1, 2, 3, 4, 10, 11, 12, 13, 14})

froze | set(range(5,15))

frozenset({0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14})
```

### 总结

Python 的内置数据结构提供了多种表示和组织数据的方式。字典和集合都是序列类型的补充。字典以高效的方式将键映射到值。集合则将数学集合运算实现为数据结构。在顺序不是最佳操作原则的情况下，字典和集合都是很好的选择。

### 问题

1. 创建包含以下键/值对的字典的三种方法是什么：
   ```
   {'name': 'Smuah', 'height':62}
   ```

2. 如何将字典 `student` 中与键 `gpa` 关联的值更新为 `'4.0'`？

3. 给定字典 `data`，如果键 `settings` 可能缺失，如何安全地访问其对应的值？

4. 可变对象和不可变对象之间的区别是什么？

5. 如何从字符串 `"lost and lost again"` 创建一个集合？