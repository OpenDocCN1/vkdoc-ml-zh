# 5 执行控制

对一个正确问题的近似答案，远比对一个近似问题的精确答案有价值得多。

约翰·图基

## 本章内容

*   复合语句简介

*   相等运算

*   比较运算

*   布尔运算

*   `if` 语句

*   `while` 循环

*   `for` 循环

到目前为止，在本书中，您看到的语句都是作为独立单元，逐行顺序执行的。当您可以将语句分组在一起，使它们作为一个单元执行时，编程会变得更加强大和有趣。连接在一起的简单语句可以执行更复杂的行为。

## 复合语句

第 2 章 “Python 基础” 介绍了简单语句，每个语句执行一个操作。本章将介绍复合语句，它允许您控制一组语句的执行。这种执行仅在条件为真时发生。本章涵盖的复合语句包括 `for` 循环、`while` 循环、`if` 语句、`try` 语句和 `with` 语句。

### 复合语句结构

一个复合语句由一个或多个控制语句以及一组执行受控的语句组成。控制语句以一个指示复合语句类型的关键字开头，后跟一个特定于该语句类型的表达式，然后是一个冒号：

```
<关键字> <表达式>:
```

受控语句可以通过两种方式之一进行分组。第一种也是更常见的方式是将它们分组为一个*代码块*，这是一组一起运行的语句。在 Python 中，代码块使用缩进定义。一组具有相同缩进的语句被分组到同一个代码块中。当出现一个缩进级别低于其他语句的语句时，该组结束。该最后一条语句不属于该代码块，并且无论控制语句如何都会执行。这就是代码块的样子：

```
<控制语句>:

    <受控语句 1>

    <受控语句 2>

    <受控语句 3>

<结束块的语句>
```

使用缩进来定义代码块是 Python 区别于大多数其他流行语言（这些语言使用其他机制，如花括号，来对代码进行分组）的特性之一。

另一种对受控语句进行分组的方法是直接将它们列在控制语句之后，并用分号分隔受控语句：

```
<控制语句>:<受控语句 1>;<受控语句 2>;
```

只有当受控语句非常少，并且您认为将复合语句限制在一行内会增强（而非削弱）程序的可读性时，才应使用第二种风格。

### 求值为 `True` 或 `False`

`if` 语句、`while` 循环和 `for` 循环都是复合语句，它们依赖于一个必须求值为 `True` 或 `False` 的控制表达式。幸运的是，在 Python 中，几乎所有东西都可以求值为其中之一。用作复合语句控制的四种最常用的内置表达式是相等运算、比较运算、布尔运算和对象求值。

#### 相等运算

Python 提供了相等运算符 `==`、不等运算符 `!=` 以及身份运算符 `is`。相等和不等运算符均用于比较两个对象的值，并返回常量 `True` 或 `False`。清单 5.1 将两个变量赋值为整数值 `1`，另一个变量赋值为 `2`。随后使用相等运算符表明前两个变量相等，而第三个不相等。接着使用不等运算符进行相同操作，其结果与相同输入下相等运算符的结果相反。

清单 5.1 **相等运算**

```
# 为变量赋值

a, b, c = 1, 1, 2

# 检查值是否相等

a == b

True

a == c

False

a != b

False

a != c

True
```

你可以使用相等/不等运算符比较不同类型的对象。对于数值类型（如浮点数和整数），会比较它们的值。例如，比较整数 `1` 和浮点数 `1.0`，它们会被判定为相等：

```
1 == 1.0

True
```

大多数其他跨类型比较都会返回 `False`，无论值是什么。比较字符串和整数始终返回 `False`，无论值是什么：

```
'1' == 1

False
```

Web 表单通常将所有用户输入报告为字符串。一个常见问题出现在尝试比较来自 Web 表单的、表示数字但类型为字符串的用户输入与实际数字时。字符串输入与数字比较时始终判定为 `False`，即使该输入是相同值的字符串版本。

#### 比较运算

你使用比较运算符来比较对象的顺序。“顺序”的含义取决于所比较对象的类型。对于数字，比较的是数轴上的顺序；对于字符串，则使用字符的 Unicode 值。比较运算符包括小于（`<`）、小于等于（`<=`）、大于（`>`）和大于等于（`>=`）。清单 5.2 演示了各种比较运算符的行为。

清单 5.2 **比较运算**

```
a, b, c = 1, 1, 2

a < b

False

a < c

True

a <= b

True

a > b

False

a >= b

True
```

在某些情况下，你可以在不同类型的对象之间使用比较运算符，例如数值类型之间，但大多数跨类型比较是不允许的。如果你对不可比较的类型（如字符串和列表）使用比较运算符，则会发生错误。

#### 布尔运算

布尔运算基于布尔数学，你可能在数学或哲学课程中学过。这些运算最早由 19 世纪的数学家乔治·布尔形式化。在 Python 中，布尔运算符是 `and`、`or` 和 `not`。`and` 和 `or` 运算符各接受两个参数；`not` 运算符只接受一个参数。

如果 `and` 运算符的两个参数都判定为 `True`，则其结果为 `True`；否则结果为 `False`。如果 `or` 运算符的任一参数判定为 `True`，则其结果为 `True`；否则结果为 `False`。如果 `not` 运算符的参数判定为 `False`，则其返回 `True`；否则返回 `False`。清单 5.3 演示了这些行为。

清单 5.3 **布尔运算**

```
True and True

True

True and False

False

True or False

True

False or False

False

not False

True

not True

False
```

`and` 和 `or` 运算符都是短路运算符。这意味着它们只会根据确定输出所需的最小程度来评估输入表达式。例如，假设你有两个方法 `returns_false()` 和 `returns_true()`，并将它们用作 `and` 运算符的输入，如下所示：

```
returns_false() and returns_true()
```

如果 `returns_false()` 返回 `False`，则不会调用 `returns_true()`，因为 `and` 运算的结果已经确定。类似地，假设你将它们用作 `or` 运算的参数，如下所示：

```
returns_true() or returns_false()
```

在这种情况下，如果第一个方法返回 `True`，则不会调用第二个方法。

`not` 运算符始终返回布尔常量 `True` 或 `False`。另外两个布尔运算符则返回最后一个被评估的表达式的结果。这在对象评估中非常有用。

#### 对象评估

Python 中的所有对象都会评估为 `True` 或 `False`。这意味着你可以将对象用作布尔运算的参数。评估为 `False` 的对象包括常量 `None` 和 `False`、任何值为零的数值，或任何长度为零的对象。这包括空序列，例如空字符串（`""`）或空列表（`[]`）。几乎所有其他对象都评估为 `True`。

由于 `or` 运算符返回它评估的最后一个表达式，你可以利用它在变量评估为 `False` 时创建默认值：

```
a = ''

b = a or 'default value'

b

'default value'
```

由于此示例将第一个变量赋值为空字符串（其长度为零），因此该变量评估为 `False`。`or` 运算符评估此变量，然后评估并返回第二个表达式。

## `if` 语句

`if` 语句是一个复合语句。`if` 语句让你能够根据当前状态来分支代码的行为。你可以使用 `if` 语句仅在满足特定条件时执行某个操作，或者使用更复杂的结构根据多个条件在多个操作之间进行选择。控制语句以关键字 `if` 开头，后跟一个表达式（其计算结果为 `True` 或 `False`），然后是一个冒号。被控制的语句可以跟在同一行，用分号分隔：

```
if True:message="It's True!";print(message)

It's True!
```

或者作为一个缩进的代码块，用换行符分隔：

```
if True:

message="It's True"

print(message)

It's True
```

在这两个示例中，控制表达式都是保留常量 `True`，它始终计算结果为 `True`。有两个被控制的语句：第一个将字符串赋值给变量 message，第二个打印该变量的值。通常使用块语法（如第二个示例所示）更具可读性。

如果控制表达式的计算结果为 `False`，程序将继续执行并跳过被控制的语句：

# 海象运算符

当你给变量赋值时，Python 不会返回值。一种常见的情况是先进行变量赋值，然后检查该变量的值。例如，你可能将一个函数返回的值赋给一个变量，如果该值不是`None`，你就可以使用返回的对象。Python`re`模块（将在第 15 章“其他主题”中介绍）的`search`方法如果在字符串中找到匹配项，则返回一个`match`对象，否则返回`None`。因此，如果你想使用`match`对象，需要首先确保它不是`None`：

点击此处查看代码图片

```python
import re

s = '2020-12-14'

match = re.search(r'(\d\d\d\d)-(\d\d)-(\d\d)', s)

if match:

print(f"Matched items: {match.groups(1)}")

else:

print(f"No match found in {s}")
```

Python 3.8 引入了一个新的运算符，即赋值运算符（`:=`）。由于它形似海象的头部，因此被称为*海象运算符*。该运算符将一个值赋给一个变量并返回该值。你可以使用它重写上面的`match`示例：

点击此处查看代码图片

```python
import re

s = '2020-12-14'

if match := re.search(r'(\d\d\d\d)-(\d\d)-(\d\d)', s):

print(f"Matched items: {match.groups(1)}")

else:

print(f"No match found in {s}")
```

这个运算符使代码更简洁、更具可读性。

下面是一个使用成员测试作为控制表达式的示例：

点击此处查看代码图片

```python
snack = 'apple'

fruit = {'orange', 'apple', 'pear'}

if snack in fruit:

print(f"Yeah, {snack} is good!"
```

Yeah, apple is good!

此示例检查变量`snack`的值是否在集合`fruit`中。如果在，则打印一条鼓励信息。

如果你想在控制表达式为`False`时运行一个备用的代码块，可以使用`else`语句。`else`语句由关键字`else`后跟一个冒号和一个代码块组成，该代码块仅在其前面的控制表达式计算结果为`False`时执行。这让你可以在代码中分支逻辑。可以将其理解为根据当前状态选择要执行的操作。代码清单 5.4 展示了在零食相关的`if`语句中添加了一个`else`语句。只有当控制表达式`snack`in`fruit`为`False`时，第二个打印语句才会执行。

代码清单 5.4 **`else`语句**

点击此处查看代码图片

```python
snack = 'cake'

fruit = {'orange', 'apple', 'pear'}

if snack in fruit:

print(f"Yeah, {snack} is good!")

else:

print(f"{snack}!? You should have some fruit")
```

cake!? You should have some fruit

如果你希望代码中有多个分支，可以嵌套`if`和`else`语句，如代码清单 5.5 所示。在此例中，有三种选择：余额为正数时一种，余额为零时一种，余额为负数时一种。

代码清单 5.5 **嵌套的`else`语句**

点击此处查看代码图片

```python
balance = 2000.32

account_status = None

if balance > 0:

account_status = 'Positive'

else:

if balance == 0:

account_status = 'Empty'

else:

account_status = 'Overdrawn'

print(account_status)
```

Positive

虽然这段代码是合法的并且能按预期工作，但可读性稍差。为了以更简洁的方式实现相同的分支逻辑，你可以使用`elif`语句。这种语句添加在初始的`if`语句之后。它有自己的控制表达式，仅当上一个语句的表达式计算结果为`False`时才会被求值。代码清单 5.6 实现了与代码清单 5.5 相同的逻辑，但将嵌套的`else`和`if`语句替换为了`elif`。

代码清单 5.6 **`elif`语句**

点击此处查看代码图片

```python
balance = 2000.32

account_status = None

if balance > 0:

account_status = 'Positive'

elif balance == 0:

account_status = 'Empty'

else:

account_status = 'Overdrawn'

print(account_status)
```

Positive

通过将多个`elif`语句与一个`if`语句链接起来，如代码清单 5.7 所示，你可以执行复杂的选择。通常会在末尾添加一个`else`语句，以捕获所有控制表达式均为`False`的情况。

代码清单 5.7 **链接`elif`语句**

点击此处查看代码图片

```python
fav_num = 13

if fav_num in (3,7):

print(f"{fav_num} is lucky")

elif fav_num == 0:

print(f"{fav_num} is evocative")

elif fav_num > 20:

print(f"{fav_num} is large")

elif fav_num == 13:

print(f"{fav_num} is my favorite number too")

else:

print(f"I have no opinion about {fav_num}")
```

is my favorite number too

### `while`循环

`while`循环由关键字`while`、一个控制表达式、一个冒号以及一个受控代码块组成。`while`循环中的受控语句仅在控制表达式计算结果为`True`时执行；在这方面，它类似于`if`语句。然而，与`if`语句不同的是，只要控制语句保持为`True`，`while`循环就会重复不断地执行受控代码块。下面是一个`while`循环，只要变量`counter`小于五，它就会执行：

点击此处查看代码图片

```python
counter = 0

while counter < 5:

   print(f"I've counted {counter} so far, I hope there aren't more")

   counter += 1
```

注意，变量在每次迭代中都会递增。这保证了循环会退出。以下是运行此循环的输出：

点击此处查看代码图片

```
I've counted 0 so far, I hope there aren't more

I've counted 1 so far, I hope there aren't more

I've counted 2 so far, I hope there aren't more

I've counted 3 so far, I hope there aren't more

I've counted 4 so far, I hope there aren't more
```

你可以看到循环运行了五次，每次递增变量。

**注意**

提供退出条件非常重要，否则你的循环将无限重复。

### `for`循环

`for`循环用于遍历一组对象。这组对象可以是序列、生成器、函数或任何其他可迭代对象。可迭代对象是指能够一次返回一系列项目的对象。`for`循环通常用于将代码块执行指定次数，或对序列中的每个成员执行操作。`for`循环的控制语句由关键字`for`、一个变量、关键字`in`以及可迭代对象组成，后跟一个冒号：

```
for <variable> in <iterable>:
```

变量被赋值为可迭代对象中的第一个值，然后使用该值执行受控代码块，接着变量被赋值为下一个值。只要可迭代对象还有值可以返回，这个过程就会持续。

一种将代码块执行指定次数的常见方法是使用带有`range`对象作为可迭代对象的`for`循环：

点击此处查看代码图片

```python
for i in range(6):

    j = i + 1

    print(j)
```

此示例将值`0`、`1`、`2`、`3`、`4`和`5`赋给变量`i`，并为每个值运行一次代码块。

以下是一个使用列表作为可迭代对象的示例：

点击此处查看代码图片

```
colors = ["Green", "Red", "Blue"]

for color in colors:
    print(f"My favorite color is {color}")
    print("No, wait...")
```

列表中的每个项目都会在代码块中使用，当没有剩余项目时，循环退出。

### `break` 和 `continue` 语句

`break` 语句允许你提前退出 `while` 或 `for` 循环。当该语句被求值时，当前代码块停止执行，循环结束。这通常与嵌套的 `if` 语句结合使用。代码清单 5.8 展示了一个控制表达式始终为 `True` 的循环。一个嵌套的 `if` 语句在其条件满足时调用 `break`，从而在此时结束循环。

代码清单 5.8 `break` 语句

点击此处查看代码图片

```
fish = ['mackerel', 'salmon', 'pike']
beasts = ['salmon', 'pike', 'bear', 'mackerel']
i = 0

while True:
    beast = beasts[i]
    if beast not in fish:
        print(f"Oh no! It's not a fish, it's a {beast}")
        break
    print(f"I caught a {beast} with my fishing net")
    i += 1
```

`continue` 语句在被调用时会跳过循环的一次迭代。它通常也与嵌套的 `if` 语句结合使用。代码清单 5.9 演示了如何使用 `continue` 语句来跳过打印不以字母 *b* 开头的名字。

代码清单 5.9 `continue` 语句

点击此处查看代码图片

```
for name in ['bob', 'billy', 'bonzo', 'fred', 'baxter']:
    if not name.startswith('b'):
        continue
    print(f"Fine fellow that {name}")
```

### 小结

复合语句，如 `if` 语句、`while` 循环和 `for` 循环，是超越简单脚本的代码的基本组成部分。通过分支和重复代码的能力，你可以构建描述复杂行为的动作块。现在你已经掌握了构建更复杂软件的工具。

### 问题

1. 如果变量 `a` 被设置为一个空列表，以下代码会打印什么？

    点击此处查看代码图片

    ```
    if a:
        print(f"Hiya {a}")
    else:
        print(f"Biya {a}")
    ```

2. 如果变量 `a` 被设置为字符串 `"Henry"`，上述代码会打印什么？

3. 编写一个 `for` 循环，打印从 0 到 9 的数字，但跳过 3、5 和 7。