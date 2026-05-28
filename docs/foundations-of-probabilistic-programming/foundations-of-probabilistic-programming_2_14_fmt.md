## 13 函数式编程

控制复杂性是计算机编程的精髓。

——布莱恩·克尼汉

本章内容

*   函数式编程简介
*   状态与作用域
*   函数式函数
*   列表推导式
*   生成器

正如你在本书中目前所见，一个 Python 程序，在最基本的层面上，由一系列语句组成，这些语句可以是简单语句或复合语句。组织这些语句的方式会影响性能、可读性和修改的便利性。一些被广泛采用的方法包括过程式编程、函数式编程和面向对象编程。本章将介绍函数式编程的一些概念，包括推导式和生成器，这两者都借鉴自纯函数式语言。

### 函数式编程简介

函数式编程基于函数的数学定义。从这个意义上说，函数将输入映射到输出。对于任何输入，只能有一个输出；换句话说，对于不同的输入，其输出始终相同。某些编程语言，如 `Haskell` 和 `Erlang`，严格遵循这一限制。`Python` 足够灵活，可以在不严格遵循的情况下采用一些函数式概念。`Python` 中的函数式编程有时被称为轻量函数式编程。

#### 作用域与状态

程序的*状态*包括在程序某个特定时间点存在的名称、定义和值，包括函数定义、导入的模块以及分配给变量的值。状态具有所谓的*作用域*——即状态所适用的程序区域。作用域是分层的。当你缩进一个代码块时，这段代码就拥有了一个嵌套作用域。它从周围未缩进的代码继承作用域，但不会直接改变外部作用域。

代码清单 13.1 在外部作用域中为变量 `a` 和 `b` 设置了值。然后，函数的代码块将 `a` 设置为不同的值，并打印这两个变量。你可以看到，当函数被调用时，它使用自己对变量 `a` 的定义，但继承了外部作用域中 `b` 的定义。在外部作用域中，函数为 `a` 赋的值被忽略，因为它超出了作用域。

代码清单 13.1 继承作用域

```python
a = 'a outer'

b = 'b outer'

def scoped_function():

    a = 'a inner'

    print(a)

    print(b)

scoped_function()
```

```
a inner

b outer

print(a)
```

```
a outer

print(b)
```

```
b outer
```

#### 依赖全局状态

到目前为止，本书中的代码大多采用过程式方法呈现。在这种方法中，当前状态由当前行之前已执行的语句定义。该状态在程序中共享，并在整个过程中被修改。这意味着，一个利用状态来确定其输出的函数，在相同输入下可能会产生不同的输出。让我们看一些对比过程式方法和函数式方法的例子。

代码清单 13.2 创建了一个函数 `describe_the_wind()`，它使用在外部作用域中定义的变量 `wind` 返回一个句子。你可以看到，该函数的输出将根据这个变量而不同。

代码清单 13.2 依赖外部作用域

```python
wind = 'Southeast'

def describe_the_wind():

    return f'The wind blows from the {wind}'

describe_the_wind()
```

```
'The wind blows from the Southeast'

wind = 'North'

describe_the_wind()
```

```
'The wind blows from the North'
```

一种更函数式的方法是将变量作为参数传递。这样，无论外部状态如何，该函数对于传递给它的同一个值都会返回相同的结果：

```python
def describe_the_wind(wind):

    return f'The wind blows from the {wind}'

describe_the_wind('Northeast')
```

```
'The wind blows from the Northeast'
```

#### 改变状态

除了不依赖外部状态外，函数式函数也不应直接改变外部状态。代码清单 13.3 展示了一个在函数 `change_wind()` 内部改变外部状态变量 `WIND` 的程序。注意使用了关键字 `global`，它表示要改变一个外部状态变量，而不是在内部状态中定义一个新变量。

代码清单 13.3 修改外部作用域

```python
WINDS = ['Northeast', 'Northwest', 'Southeast', 'Southwest']

WIND = WINDS[0]

def change_wind():

    global WIND

    WIND = WINDS[(WINDS.index(WIND) + 1)%3]

WIND
```

```
'Northeast'

change_wind()

WIND
```

```
'Northwest'

for _ in WINDS:

    print(WIND)

    change_wind()
```

```
Northwest

Southeast

Northeast

Northwest
```

一种更函数式的获取相同输出的方法是将 `winds` 变量移到内部状态中，并让函数 `change_wind()` 接受一个参数来确定输出，如代码清单 13.4 所示。

代码清单 13.4 不修改外部作用域

```python
def change_wind(wind_index):

    winds = ['Northeast', 'Northwest', 'Southeast', 'Southwest']

    return winds[wind_index]

print( change_wind(0) )
```

```
Northeast

print( change_wind(1) )
```

```
Northwest

print( change_wind(2) )
```

```
Southeast

print( change_wind(3) )
```

```
Southwest
```

#### 改变可变数据

一种更隐蔽的改变外部状态的方式是传递可变对象。请记住，可变对象是指内容可以被改变的对象，例如列表和字典。如果你在外部状态中设置一个变量，将其作为参数传递给一个函数，然后在函数的内部状态中改变其值，该变量在外部状态中的版本将保留其原始值。示例如下：

```python
b = 1

def foo(a):

    a = 2

foo(b)

print(b)
```

然而，如果你将一个可变对象（例如字典）作为参数传递给函数，那么在该函数中对这个对象所做的任何更改也会反映在外部状态中。以下示例定义了一个函数，该函数接受一个字典作为参数并更改其一个值：

```python
d = {"vehicle": "ship", "owner": "Joseph Bruce Ismay"}

def change_mutable_data(data):

    '''一个改变可变数据的函数。'''

    data['owner'] = 'White Star Line'

change_mutable_data(d)

print(d)
```

```
{'vehicle': 'ship', 'owner': 'White Star Line'}
```

你可以看到，字典 `d` 在传递给这个函数后，其值在外部状态中被改变了。

以这种方式改变可变对象的外部作用域可能导致难以察觉的错误。如果你的数据结构不是太大，一种避免这种情况的方法是在内部作用域中制作一个副本，然后操作这个副本：

```python
d = {"vehicle": "ship", "owner": "Joseph Bruce Ismay"}

def change_owner(data):

    new_data = data.copy()

    new_data['owner'] = 'White Star Line'

    return new_data

changed = change_owner(d)

changed
```

```
{'owner': 'White Star Line', 'vehicle': 'ship'}
```

通过对副本进行操作，可以更容易地看出值是在哪里被更改的。

#### 函数式编程函数

`Python` 中有三个内置函数源自函数式编程领域，分别是 `map()`、`filter()` 和 `reduce()`。

`map()` 函数应用于一个值序列，并返回一个 `map` 对象。输入的序列可以是任何可迭代类型——即任何可以迭代的对象，例如 `Python` 序列。返回的 `map` 对象也是可迭代的，因此你可以遍历它，或者将其转换为列表来查看结果：

```python
def grow_flowers(d):

    return d * "❀"

gardens = map(grow_flowers, [0,1,2,3,4,5])

type(gardens)
```

```
map

list(gardens)
```

```
['', '❀', '❀❀', '❀❀❀', '❀❀❀❀', '❀❀❀❀❀']
```

你可以向 `map()` 提供一个接受多个参数的函数，并提供多个输入值序列：

点击此处查看代码图片

```python
l1 = [0,1,2,3,4]
l2 = [11,10,9,8,7,6]

def multi(d1, d2):
    return d1 * d2

result = map(multi, l1, l2)
print( list(result) )
# [0, 10, 18, 24, 28]
```

注意在这个例子中，其中一个输入序列比另一个长。`map()` 函数会在到达最短输入序列的末尾时停止。

`reduce()` 函数也接受一个函数和一个可迭代对象作为参数。然后它使用该函数，基于输入返回一个单一值。例如，如果你想从账户余额中减去一笔金额，你可以使用 `for` 循环来实现，如下所示：

点击此处查看代码图片

```python
initial_balance = 10000
debits = [20, 40, 300, 3000, 1, 234]

balance = initial_balance
for debit in debits:
    balance -= debit

balance
```

你可以使用 `reduce()` 函数达到相同的结果，如下所示：

点击此处查看代码图片

```python
from functools import reduce

inital_balance = 10000
debits = [20, 40, 300, 3000, 1, 234]

def minus(a, b):
    return a - b

balance = reduce(minus, debits, initial_balance)
balance
```

`operator` 模块将所有标准运算符作为函数提供，包括用于标准数学运算的函数。你可以使用 `operator.sub()` 函数作为 `reduce()` 的参数，来替代 `minus()` 函数：

点击此处查看代码图片

```python
from functools import reduce
import operator

initial_balance = 10000
debits = [20, 40, 300, 3000, 1, 234]
reduce(operator.sub, debits, initial_balance)
```

`filter()` 函数接受一个函数和一个可迭代对象作为参数。该函数应根据每个项目返回 `True` 或 `False`。结果是一个仅包含那些使函数返回 `True` 的输入值的可迭代对象。例如，要从一个字符串中只获取大写字母，你可以定义一个测试字符是否大写的函数，并将其与字符串一起传递给 `filter()`：

点击此处查看代码图片

```python
charles = 'ChArlesTheBald'

def is_cap(a):
    return a.isupper()

retval = filter(is_cap, charles)
list(retval)
# ['C', 'A', 'T', 'B']
```

我真正推荐使用 `lambda` 函数的少数情况之一，是在使用 `map()`、`filter()` 和 `reduce()` 函数时。当你进行简单的比较时——例如找出所有大于 3 且小于 10 的数字——你可以以一种简洁易读的方式使用 `lambda` 函数和 `range()`：

点击此处查看代码图片

```python
nums = filter(lambda x: x > 3, range(10))
list(nums)
# [4, 5, 6, 7, 8, 9]
```

### 列表推导式

列表推导式是借鉴自函数式编程语言 Haskell 的语法（参见 [`docs.python.org/3/howto/functional.html`](https://docs.python.org/3/howto/functional.html)）。Haskell 是一种完全函数式的编程语言，其语法设计便于采用纯函数式方法。你可以将列表推导式视为返回列表的单行 `for` 循环。尽管列表推导式源于函数式编程，但其使用已成为所有 Python 方法中的标准做法。

#### 列表推导式基本语法

列表推导式的基本语法如下：

点击此处查看代码图片

```python
[ <返回的项目> for <源项目> in <可迭代对象> ]
```

例如，给定一个名字列表，你想将名字改为首字母大写格式（即首字母大写），你可以使用 `x.title()` 作为返回的项目，并将每个名字作为源项目：

点击此处查看代码图片

```python
names = ['tim', 'tiger', 'tabassum', 'theodora', 'tanya']
capd = [x.title() for x in names]
capd
# ['Tim', 'Tiger', 'Tabassum', 'Theodora', 'Tanya']
```

这相当于使用 `for` 循环的以下过程：

点击此处查看代码图片

```python
names = ['tim', 'tiger', 'tabassum', 'theodora', 'tanya']
capd = []
for name in names:
    capd.append(name.title())
capd
# ['Tim', 'Tiger', 'Tabassum', 'Theodora', 'Tanya']
```

#### 替换 `map` 和 `filter`

你可以使用列表推导式来替代 `map()` 和 `filter()` 函数。例如，以下代码将数字 0 到 5 映射到一个函数，该函数将它们插入到一个字符串中：

点击此处查看代码图片

```python
def count_flower_petals(d):
    return f"{d} petals counted so far"

counts = map(count_flower_petals, range(6))
list(counts)
# ['0 petals counted so far', '1 petals counted so far', '2 petals counted so far',
#  '3 petals counted so far', '4 petals counted so far', '5 petals counted so far']
```

你可以用下面这个简单得多的列表推导式来替换这段代码：

点击此处查看代码图片

```python
[f"{x} petals counted so far" for x in range(6)]
# ['0 petals counted so far', '1 petals counted so far', '2 petals counted so far',
#  '3 petals counted so far', '4 petals counted so far', '5 petals counted so far']
```

你还可以使用以下语法，为列表推导式添加一个条件：

点击此处查看代码图片

```python
[ <返回的项目> for <源项目> in <可迭代对象> if <条件> ]
```

通过使用条件，你可以轻松复制 `filter()` 函数的功能。例如，以下 `filter()` 示例仅返回大写字母：

点击此处查看代码图片

```python
characters = ['C', 'b', 'c', 'A', 'b', 'P', 'g', 'S']

def cap(a):
    return a.isupper()

retval = filter(cap, characters)
list(retval)
# ['C', 'A', 'P', 'S']
```

你可以用以下使用条件的列表推导式来替换这个函数：

点击此处查看代码图片

```python
characters = ['C', 'b', 'c', 'A', 'b','P', 'g', 'S']
[x for x in characters if x.isupper()]
# ['C', 'A', 'P', 'S']
```

#### 多个变量

如果源可迭代对象中的项目是序列，你可以通过使用多个变量来解包它们：

点击此处查看代码图片

```python
points = [(12, 3), (-1, 33), (12, 0)]
[ f'x: {x} y: {y}' for x, y in points ]
# ['x: 12 y: 3', 'x: -1 y: 33', 'x: 12 y: 0']
```

你可以通过在同一个列表推导式中使用多个 `for` 语句，来执行相当于嵌套 `for` 循环的操作：

点击此处查看代码图片

```python
list_of_lists = [[1,2,3], [4,5,6], [7,8,9]]
[x for y in list_of_lists for x in y]
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

#### 字典推导式

字典推导式的语法与列表推导式类似。不过，列表推导式是向`list`中追加单个值，而字典推导式则是向字典中添加键值对。以下示例使用两个`lists`中的值来构建一个字典：

点击此处查看代码图片

```
names = ['James', 'Jokubus', 'Shaemus']

scores = [12, 33, 23]

{ name:score for name in names for score in scores}

{'James': 23, 'Jokubus': 23, 'Shaemus': 23}
```

### 生成器

在处理大范围数值时，使用`range`对象而非`list`的一大优势在于，`range`对象会按需计算结果。这意味着它的内存占用始终很小。生成器允许你使用自己的计算逻辑按需创建值，其工作方式与`range`对象类似。

#### 生成器表达式

创建生成器的一种方法是通过生成器表达式，其语法与列表推导式相同，只是将方括号替换为圆括号。以下示例展示了如何基于相同的计算创建一个`list`和一个生成器，并打印它们：

点击此处查看代码图片

```
l_ten = [x**3 for x in range(10)]

g_ten = (x**3 for x in range(10))

print(f"l_ten is a {type(l_ten)}")

l_ten is a <class 'list'>

print(f"l_ten prints as: {l_ten}")

l_ten prints as: [0, 1, 8, 27, 64, 125, 216, 343, 512, 729]

print(f"g_ten is a {type(g_ten)}")

g_ten is a <class 'generator'>

print(f"g_ten prints as: {g_ten}")

g_ten prints as: <generator object <genexpr> at 0x7f3704d52f68>
```

打印`list`时，你可以看到其内容；但生成器则不然。要从生成器中获取值，你必须请求下一个值，这可以通过使用`next()`函数来实现：

```
next(g_ten)
```

或者，更常见的是，你可以在`for`循环中遍历生成器：

点击此处查看代码图片

```
for x in g_ten:

    print(x)
```

由于生成器仅按需生成值，因此无法对其进行索引或切片：

点击此处查看代码图片

```
g_ten[3]

TypeError                          Traceback (most recent call last)

<ipython-input-6-e7b8f961aa33> in <module>()

---->  2 g_ten[3]

TypeError: 'generator' object is not subscriptable
```

生成器相对于`lists`的一个重要优势在于其内存占用。以下示例使用`sys.getsizeof()`函数来比较列表和生成器的大小：

点击此处查看代码图片

```
import sys

x = 100000000

l_big = [x for x in range(x)]

g_big = (x for x in range(x))

print( f"l_big is {sys.getsizeof(l_big)} bytes")

l_big is 859724472 bytes

print( f"g_big is {sys.getsizeof(g_big)} bytes")

g_big is 88 bytes
```

#### 生成器函数

你可以使用生成器函数来创建复杂的生成器。生成器函数看起来与普通函数类似，但将`return`语句替换为了`yield`语句。生成器会维护其自身的内部状态，并按需返回值：

点击此处查看代码图片

```
def square_them(numbers):

    for number in numbers:

        yield number * number

s = square_them(range(10000))

print(next(s))

print(next(s))

print(next(s))

print(next(s))
```

生成器相对于`lists`的另一个优势是能够创建无限生成器——即没有终点的生成器。无限生成器会按需返回任意数量的值。例如，你可以创建一个生成器，它可以根据需要多次递增一个数字：

点击此处查看代码图片

```
def counter(d):

    while True:

        d += 1

        yield d

c = counter(10)

print(next(c))

print(next(c))

print(next(c))
```

清单 13.5 `生成器管道`

点击此处查看代码图片

```
evens = (x*2 for x in range(5000000))

three_factors = (x//3 for x in evens if x%3 == 0)

titles = (f"this number is {x}" for x in three_factors)

capped = (x.title() for x in titles)

print(f"The first call to capped: {next(capped)}")

The first call to capped: This Number Is 0

print(f"The second call to capped: {next(capped)}") The second call to capped: This

Number Is 2

print(f"The third call to capped: {next(capped)}")

The third call to capped: This Number Is 4
```

使用生成器是让代码性能更优的好方法。当你需要遍历一个由计算值组成的长序列时，应该考虑使用生成器。

### 本章小结

函数式编程是一种组织程序的方法，对于设计可并发运行的软件非常有用。它基于这样一个理念：函数内部状态应该被调用它的代码的外部状态所改变，或者应该改变该外部状态。对于给定的输入，函数应始终返回相同的值。三个源自函数式编程世界的 Python 内置函数是`map()`、`filter()`和`reduce()`。使用列表推导式和生成器都是创建值序列的非常 Pythonic 的方式。当你要遍历大量值或不确定需要多少值时，建议使用生成器。

### 练习题

1.   以下代码会打印什么？

点击此处查看代码图片

```
a = 1

b = 2

def do_something(c):

    c = 3

    a = 4

    print(a)

    return c

b = do_something(b)

print(a + b)
```

2.   使用`map()`函数处理字符串`'omni'`，并返回列表`['oo','mm', 'nn', 'ii']`。

3.   使用`sum()`函数（该函数用于对序列内容求和）结合列表推导式，计算 100 以下所有正偶数的总和。

4.   编写一个生成器表达式，返回 1,000 以内的立方数。

5.   斐波那契数列以 0 和 1 开始，之后的每个数字都是前两个数字之和。编写一个生成器函数来计算斐波那契数列。