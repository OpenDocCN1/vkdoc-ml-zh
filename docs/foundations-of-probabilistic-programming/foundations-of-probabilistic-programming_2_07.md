## 6 函数

在我们对测量的渴望中，我们常常测量那些我们能够测量的东西，而不是我们想要测量的东西……并且忘记了它们之间的区别。

乔治·乌德尼·尤尔

本章内容

*   定义函数

*   文档字符串

*   位置参数和关键字参数

*   通配符参数

*   返回语句

*   作用域

*   装饰器

*   匿名函数

我们要讨论的最后一个，或许也是最强大的复合语句是函数。函数提供了一种将代码块包装成对象并为其命名的方法。然后可以通过使用该名称来调用该代码，从而允许同一段代码在多个位置被多次调用。

### 定义函数

函数定义定义了一个函数对象，该对象包装了可执行的代码块。定义不会运行代码块，而只是定义函数。定义描述了如何调用函数、函数的名称、可以传递给它的参数以及调用时将执行什么。函数的组成部分包括控制语句、可选的文档字符串、受控代码块以及返回语句。

#### 控制语句

函数定义的第一行是控制语句，其形式如下：

点击此处查看代码图片

```
def <函数名称> (<参数>):
```

`def` 关键字表示一个函数定义，`<函数名称>` 是定义用于调用函数的名称的地方，`<参数>` 是定义可以传递给函数的任何参数的地方。例如，以下函数被定义为名称 `do_nothing`，并带有一个名为 `not_used` 的参数：

```
def do_nothing(not_used):

   pass
```

此处的代码块由一个 `pass` 语句组成，它什么也不做。Python 风格指南 PEP8 对函数命名有约定（参见 [`www.python.org/dev/peps/pep-0008/#function-and-variable-names`](https://www.python.org/dev/peps/pep-0008/#function-and-variable-names)）。



#### 文档字符串

函数定义的下一部分是文档字符串，即 *docstring*，其中包含函数的文档说明。它可以省略，Python 编译器不会报错。然而，强烈建议为所有方法（除了最显而易见的方法）提供文档字符串。文档字符串传达了你在编写函数时的意图、函数的功能以及应如何调用它。PEP8 提供了关于文档字符串内容的指导（参见 [`www.python.org/dev/peps/pep-0008/#documentation-strings`](https://www.python.org/dev/peps/pep-0008/#documentation-strings)）。文档字符串由紧跟在控制语句之后的单行字符串或多行字符串组成，并用三对双引号括起来：

点击此处查看代码图片

```
def do_nothing(not_used):

   """此函数不执行任何操作。"""

   pass
```

对于单行文档字符串，引号与文本位于同一行。对于多行文档字符串，引号通常位于文本的上方和下方，如代码清单 6.1 所示。

代码清单 6.1 **多行文档字符串**

点击此处查看代码图片

```
def do_nothing(not_used):

    """

    此函数不执行任何操作。

    此函数使用 pass 语句来

    避免执行任何操作。

    参数：

        not_used - 任意类型的参数，

                   该参数未被使用。

    """

    pass
```

文档字符串的第一行应是一个总结函数功能的语句。如果需要更详细的解释，则在第一句之后留一个空行。关于文档字符串第一行之后的内容，有许多不同的约定，但通常你需要解释函数的功能、它接受的参数以及它预期返回的内容。文档字符串对于阅读你代码的人以及各种读取并显示文档字符串第一行或全部内容的实用程序都很有用。例如，如果你对函数 `do_nothing()` 调用 `help()` 函数，则会显示文档字符串，如代码清单 6.2 所示。

代码清单 6.2 **来自 `help` 的文档字符串**

点击此处查看代码图片

```
help(do_nothing)

Help on function do_nothing in module __main__:

do_nothing(not_used)

此函数不执行任何操作。

此函数使用 pass 语句来避免执行任何操作。

参数：

    not_used - 任意类型的参数，

               该参数未被使用。
```

#### 参数

参数允许你将值传递给函数，这些值可以在函数的代码块中使用。参数类似于在调用函数时赋予函数的变量，每次调用函数时参数都可以不同。函数不必接受任何参数。对于不应接受参数的函数，你可以将函数名后的括号留空：

点击此处查看代码图片

```
def no_params():

print("我不听任何人的")
```

当你调用函数时，你在函数名后的括号内传递参数的值。参数值可以根据传递时的位置设置，也可以根据关键字设置。可以定义函数，要求其参数以其中一种方式或组合方式传递。传递给函数的值会附加到函数定义中定义的名称的变量上。代码清单 6.3 定义了三个参数：`first`、`second` 和 `third`。这些变量随后可用于后续的代码块，该代码块会打印出每个参数的值。

代码清单 6.3 **按位置或关键字传递参数**

点击此处查看代码图片

```
def does_order(first, second, third):

'''打印参数。'''

print(f'第一个:  {first}')

print(f'第二个: {second}')

print(f'第三个:  {third}')

does_order(1, 2, 3)

第一个:  1

第二个: 2

第三个:  3

does_order(first=1, second=2, third=3)

第一个:  1

第二个: 2

第三个:  3

does_order(1, third=3, second=2)

第一个:  1

第二个: 2

第三个:  3
```

代码清单 6.3 定义了函数 `does_order()`，然后调用了它三次。第一次，它使用参数的位置 `(1, 2, 3)` 来赋值变量。它将第一个值赋给第一个参数 `first`，第二个值赋给第二个参数 `second`，第三个值赋给第三个参数 `third`。

第二次调用函数 `does_order()` 时，它使用了关键字赋值，使用参数名称 `(first=1, second=2, third=3)` 显式地赋值。在第三次调用中，第一个参数按位置赋值，另外两个参数使用关键字赋值。请注意，在所有三种情况下，参数都被赋予了相同的值。

关键字赋值不依赖于关键字的位置。例如，你可以在 `second=2` 之前的位置赋值 `third=3`，而不会出现问题。但是，你不能在位置赋值的左侧使用关键字赋值：

点击此处查看代码图片

```
does_order(second=2, 1, 3)

File "<ipython-input-9-eed80203e699>", line 1

does_order(second=2, 1, 3)

^

SyntaxError: positional argument follows keyword argument
```

你可以通过在函数定义中参数左侧放置一个 `*` 来要求该参数只能使用关键字方法调用。星号右侧的所有参数只能使用关键字调用。代码清单 6.4 展示了如何将参数 `third` 设为必需的关键字参数，然后使用关键字语法调用它。

代码清单 6.4 **需要关键字的参数**

点击此处查看代码图片

```
def does_keyword(first, second, *, third):

'''打印参数。'''

print(f'第一个: {first}')

print(f'第二个: {second}')

print(f'第三个: {third}')

does_keyword(1, 2, third=3)

第一个: 1

第二个: 2

第三个:  3
```

如果你尝试使用位置语法调用必需的关键字参数，则会收到错误：

点击此处查看代码图片

```
does_keyword(1, 2, 3)

TypeError Traceback (most recent call last)

<ipython-input-15-88b97f8a6c32> in <module>

----> 1 does_keyword(1, 2, 3)

TypeError: does_keyword() takes 2 positional arguments but 3 were given
```


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。


你可以通过在函数定义中为其分配一个默认值来使参数变为可选。如果在函数调用期间没有为该参数提供值，则将使用此默认值。清单 6.5 定义了一个函数 `does_defaults()`，其第三个参数的默认值为 `3`。然后该函数被调用了两次：第一次对所有三个参数使用位置赋值，第二次对第三个参数使用默认值。

清单 6.5 **带默认值的参数**

点击此处查看代码图片

```
def does_defaults(first, second, third=3):

'''打印参数。'''

print(f'第一个:  {first}')

print(f'第二个: {second}')

print(f'第三个:  {third}')

does_defaults(1, 2, 3)

第一个:  1

第二个: 2

第三个:  3

does_defaults(1, 2)

第一个:  1

第二个: 2

第三个:  3
```

与函数调用期间关键字参数和位置参数顺序的限制类似，你不能在函数定义中将带有默认值的参数放在不带默认值的参数的左侧：

点击此处查看代码图片

```
def does_defaults(first=1, second, third=3):

'''打印参数。'''

print(f'第一个: {first}')

print(f'第二个: {second}')

print(f'第三个: {third}')

File "<ipython-input-19-a015eaeb01be>", line 1

def does_defaults(first=1, second, third=3):

^

SyntaxError: non-default argument follows default argument
```

默认值是在函数定义中定义的，而不是在函数调用中。这意味着，如果你使用可变对象（例如列表或字典）作为默认值，它将为函数创建一次。每次你使用该默认值调用该函数时，都会使用同一个列表或字典对象。如果未预料到这一点，可能会导致一些难以察觉的问题。清单 6.6 定义了一个以列表作为默认参数的函数。代码块将 `1` 追加到列表中。请注意，每次调用该函数时，列表都会保留之前调用的值。

清单 6.6 **可变默认值**

点击此处查看代码图片

```
def does_list_default(my_list=[]):

'''使用列表作为默认值。'''

my_list.append(1)

print(my_list)

does_list_default()

[1]

does_list_default()

[1, 1]

does_list_default()

[1, 1, 1]
```

通常，最佳实践是避免使用可变对象作为默认参数，以避免难以追踪的错误和混淆。清单 6.7 演示了一种处理可变参数类型默认值的常见模式。函数定义中的默认值设置为 `None`。代码块测试参数是否已赋值。如果没有，则创建一个新列表并分配给该变量。由于列表是在代码块中创建的，因此每次在没有为参数提供值的情况下调用该函数时，都会创建一个新列表。

清单 6.7 **代码块中的默认模式**

点击此处查看代码图片

```
def does_list_param(my_list=None):

'''在代码中分配默认值以避免混淆。'''

my_list = my_list or []

my_list.append(1)

print(my_list)

does_list_param()

[1]

does_list_param()

[1]

does_list_param()

[1]
```

从 Python 3.8 开始，你可以将参数限制为仅位置赋值。函数定义中正斜杠（`/`）左侧的参数被限制为仅位置赋值。清单 6.8 定义了函数 `does_positional`，使其第一个参数 `first` 仅为位置参数。

清单 6.8 **仅位置参数（Python 3.8 及更高版本）**

点击此处查看代码图片

```
def does_positional(first, /, second, third):

'''演示一个位置参数。'''

print(f'第一个:  {first}')

print(f'第二个: {second}')

print(f'第三个:  {third}')

does_positional(1, 2, 3)

第一个:  1

第二个: 2

第三个:  3
```

如果你尝试使用关键字赋值的方式为 `first` 调用 `does_positional`，则会收到错误：

点击此处查看代码图片

```
does_positional(first=1, second=2, third=3)

TypeError Traceback (most recent call las t)

<ipython-input-24-7b1f45f64358> in <module>

----> 1 does_positional(first=1, second=2, third=3)

TypeError: does_positional() got some positional-only arguments passed as

keyword arguments: 'first'
```

清单 6.9 修改了 `does_positional`，以使用仅位置参数和仅关键字参数。参数 `first` 仅为位置参数，参数 `second` 可以使用位置或关键字赋值设置，最后一个参数 `third` 仅为关键字参数。

清单 6.9 **仅位置参数和仅关键字参数**

点击此处查看代码图片

```
def does_positional(first, /, second, *, third):

'''演示位置参数和关键字参数。'''

print(f'第一个:  {first}')

print(f'第二个: {second}')

print(f'第三个:  {third}')

does_positional(1, 2, third=3)

第一个:  1

第二个: 2

第三个:  3
```

你可以在函数定义中使用通配符来接受数量不确定的位置参数或关键字参数。当函数调用外部 API 的函数时，通常会这样做。该函数可以传递这些参数，而无需定义外部 API 的所有参数。

要对位置参数使用通配符，可以使用 `*` 字符。清单 6.10 演示了使用位置通配符参数 `*args` 定义函数。代码块接收函数调用中给出的任何位置参数，作为名为 `args` 的列表中的项。此函数遍历该列表并打印每个项。然后使用参数 `'Donkey'`、`3` 和 `['a']` 调用该函数，每个参数都从列表中访问并打印出来。

清单 6.10 **位置通配符参数**

点击此处查看代码图片

```
def does_wildcard_positions(*args):

'''演示位置参数的通配符。'''

for item in args:

print(item)

does_wildcard_positions('Donkey', 3, ['a'])

Donkey

['a']
```

要定义带有关键字通配符参数的函数，你需要定义一个以 `**` 开头的参数。例如，清单 6.11 使用参数 `**kwargs` 定义了函数 `does_wildcard_keywords`。在代码块中，关键字参数作为字典 `kwargs` 中的键和值可用。

清单 6.11 **关键字通配符参数**

点击此处查看代码图片

```
def does_wildcard_keywords(**kwargs):

'''演示关键字参数的通配符。'''

for key, value in kwargs.items():

print(f'{key} : {value}')

does_wildcard_keywords(one=1, name='Martha')

one : 1

name : Martha
```

你可以在同一个函数中同时使用位置和关键字通配符参数：只需先定义位置参数，再定义关键字参数。清单 6.12 演示了一个同时使用位置和关键字参数的函数。

清单 6.12 **位置和关键字通配符参数**

点击此处查看代码图片

```
def does_wildcards(*args, **kwargs):

'''演示通配符参数。'''

print(f'位置参数: {args}')

print(f'关键字参数: {kwargs}')

does_wildcards(1, 2, a='a', b=3)

位置参数: (1, 2)

关键字参数: {'a': 'a', 'b': 3}
```



#### 返回语句

返回语句定义了函数在被调用时计算出的值。返回语句由关键字 `return` 后跟一个表达式组成。该表达式可以是一个简单的值、一个更复杂的计算，或者是对另一个函数的调用。清单 6.13 定义了一个函数，它接受一个数字作为参数，并返回该数字加 1 的结果。

清单 6.13 **返回值**

点击此处查看代码图片

```
def adds_one(some_number):

    '''演示返回语句。'''

    return some_number + 1

adds_one(1)

```

每个 Python 函数都有一个返回值。如果你没有显式定义返回语句，函数会返回特殊值 `None`：

点击此处查看代码图片

```
def returns_none():

    '''演示默认返回值。'''

    pass

returns_none() == None

True
```

这个示例省略了返回语句，然后测试返回的值是否等于 `None`。

### 函数中的作用域

*作用域*指的是代码中定义的对象的可访问性。在全局作用域中定义的变量可以在整个代码中访问，而在局部作用域中定义的变量只能在该作用域内访问。清单 6.14 定义了一个变量 `outer` 和一个变量 `inner`。这两个变量在函数 `shows_scope` 的代码块中都是可访问的，你可以在其中打印它们。

清单 6.14 **局部作用域和全局作用域**

点击此处查看代码图片

```
outer = 'Global scope'

def shows_scope():

    '''演示局部变量。'''

    inner = 'Local scope'

    print(outer)

    print(inner)

shows_scope()

Global scope

Local scope
```

变量 `inner` 是函数的局部变量，因为它是在函数的代码块中定义的。如果你尝试在函数外部调用 `inner`，它将是未定义的：

点击此处查看代码图片

```
print(inner)

NameError Traceback (most recent call last)

<ipython-input-39-9504624e1153> in <module>

----> 1 print(inner)

NameError: name 'inner' is not defined
```

理解作用域在你使用装饰器时会很有用，下一节将对此进行描述。

### 装饰器

装饰器使你能够设计出修改其他函数的函数。装饰器通常用于按照约定或由第三方库来设置日志记录。虽然你可能不需要自己编写装饰器，但了解它们的工作原理是很有用的。本节将逐步介绍相关的概念。

在 Python 中，一切都是对象，包括函数。这意味着你可以将一个变量指向一个函数。清单 6.15 定义了函数 `add_one(n)`，它接受一个数字并对其加 `1`。接着，它创建了变量 `my_func`，该变量的值是函数 `add_one()`。

**注意**

当你没有调用函数时，不要在变量赋值中使用括号。省略括号意味着你引用的是函数对象，而不是返回值。你可以看到清单 6.15 打印了 `my_func`，它确实是一个函数对象。然后你可以通过给 `my_func` 加上括号和参数来调用该函数，它会返回参数加 `1` 的结果。

清单 6.15 **作为变量值的函数**

点击此处查看代码图片

```
def add_one(n):

'''对一个数字加一。'''

return n + 1

my_func = add_one

print(my_func)

<function add_one at 0x1075953a0>

my_func(2)

```

因为函数是对象，所以你可以将它们用于字典或列表等数据结构。清单 6.16 定义了两个函数，并将它们放入一个由变量 `my_functions` 指向的列表中。然后它遍历该列表，在每次迭代中将每个函数赋值给变量 `my_func`，并在 `for` 循环的代码块中调用该函数。

清单 6.16 **调用函数列表**

点击此处查看代码图片

```
def add_one(n):

'''对一个数字加一。'''

return n + 1

def add_two(n):

'''对一个数字加二。'''

return n + 2

my_functions = [add_one, add_two]

for my_func in my_functions:

print(my_func(1))

```

Python 允许你在另一个函数的代码块中定义一个函数。以这种方式定义的函数称为*嵌套函数*。清单 6.17 在函数 `called_nested()` 的代码块中定义了函数 `nested()`。这个嵌套函数随后被用作外部函数的返回值。

清单 6.17 **嵌套函数**

点击此处查看代码图片

```
def call_nested():

'''调用一个嵌套函数。'''

print('outer')

def nested():

'''打印一条消息。'''

print('nested')

return nested

my_func = call_nested()

outer

my_func()

nested
```

你还可以用一个函数包裹另一个函数，在其前后添加功能。清单 6.18 用函数 `wrapper(number)` 包裹了函数 `add_one(number)`。这个包裹函数接受一个参数 `number`，然后将其传递给被包裹的函数。它还在调用 `add_one(number)` 前后有语句。当你调用 `wrapper(1)` 时，可以看到打印语句的顺序，并且它返回了来自 `add_one` 的预期值：`1` 和 `2`。

清单 6.18 **包裹函数**

点击此处查看代码图片

```
def add_one(number):

'''对一个数字加一。'''

print('Adding 1')

return number + 1

def wrapper(number):

'''包裹另一个函数。'''

print('Before calling function')

retval = add_one(number)

print('After calling function')

return retval

wrapper(1)

Before calling function

Adding 1

After calling function

```



更进一步，还可以将函数作为参数使用。你可以将一个函数作为值传递给另一个函数，该函数内部嵌套了一个包裹传入函数的函数定义。例如，代码清单 6.19 首先像之前一样定义了函数 `add_one(number)`。但现在，它在一个新函数 `do_wrapping(some_func)` 的代码块中嵌套定义了函数 `wrapper(number)`。这个新函数接受一个函数作为参数，然后在 `wrapper(number)` 的定义中使用该函数。接着，它返回新定义的 `wrapper(number)` 版本。通过将这个结果赋值给一个变量并调用它，你就可以看到被包裹后的结果。

代码清单 6.19 **嵌套包裹函数**

点击此处查看代码图片

```
def add_one(number):

'''给一个数加 1。'''

print('正在加 1')

return number + 1

def do_wrapping(some_func):

'''返回一个被包裹的函数。'''

print('正在包裹函数')

def wrapper(number):

'''包裹另一个函数。'''

print('调用函数之前')

retval = some_func(number)

print('调用函数之后')

return retval

return wrapper

my_func = do_wrapping(add_one)

正在包裹函数

my_func(1)

调用函数之前

正在加 1

调用函数之后

```

你可以使用 `do_wrapping(some_func)` 来包裹任何你喜欢的函数。例如，如果你有函数 `add_two(number)`，你可以像处理 `add_one(number)` 一样将它作为参数传递：

点击此处查看代码图片

```
my_func = do_wrapping(add_two)

my_func(1)

正在包裹函数

调用函数之前

正在加 2

调用函数之后

```

装饰器提供了一种语法，可以简化这种函数包裹方式。你无需调用 `do_wrapping(some_func)`、将其赋值给变量、再从变量调用函数，只需在函数定义顶部放置 `@do_wrapping` 即可。然后，函数 `add_one(number)` 可以直接被调用，而包裹操作则在幕后进行。

你可以在代码清单 6.20 中看到，`add_one(number)` 以与代码清单 6.18 类似的方式被包裹，但使用了更简洁的装饰器语法。

代码清单 6.20 **装饰器语法**

点击此处查看代码图片

```
def do_wrapping(some_func):

'''返回一个被包裹的函数。'''

print('正在包裹函数')

def wrapper(number):

'''包裹另一个函数。'''

print('调用函数之前')

retval = some_func(number)

print('调用函数之后')

return retval

return wrapper

@do_wrapping

def add_one(number):

'''给一个数加 1。'''

print('正在加 1')

return number + 1

正在包裹函数

add_one(1)

调用函数之前

正在加 1

调用函数之后

```

### 匿名函数

绝大多数情况下，当你定义函数时，你会希望使用命名函数的语法。这就是到目前为止你所看到的情况。然而，还有一种替代方案：未命名的匿名函数。在 Python 中，匿名函数被称为 lambda 函数，其语法如下：

```
lambda <参数>: <语句>
```

其中 `lambda` 是表示 lambda 函数的关键字，`<参数>` 是一个输入参数，`<语句>` 是使用该参数执行的语句。`<语句>` 的结果就是返回值。这就是如何定义一个对输入值加一的 lambda 函数：

```
lambda x: x + 1
```

通常，如果避免使用 lambda 函数，你的代码会更易于阅读、使用和调试，但 lambda 函数的一个有用之处在于，当一个简单函数作为参数传递给另一个函数时。代码清单 6.21 定义了函数 `apply_to_list(data, my_func)`，它接受一个列表和一个函数作为参数。当你调用这个函数，意图是对列表中的每个成员加一时，lambda 函数是一个优雅的解决方案。

代码清单 6.21 **Lambda 函数**

点击此处查看代码图片

```
def apply_to_list(data, my_func):

    '''将函数应用于列表中的项目。'''

    for item in data:

        print(f'{my_func(item)}')

apply_to_list([1, 2, 3], lambda x: x + 1)

```

### 小结

函数是构建复杂程序的重要构建块，是可重用的命名代码块。函数通过文档字符串进行文档化。函数可以通过多种方式接受参数。函数使用 `return` 语句在其执行结束时传递一个值。装饰器是包裹其他函数的特殊函数。匿名函数，或称 lambda 函数，是未命名的。

### 问题

对于问题 1–3，请参考代码清单 6.22。

代码清单 6.22 **用于问题 1–3 的函数**

点击此处查看代码图片

```
def add_prefix(word, prefix='before-'):

   '''在单词前添加前缀。'''

   return f'{prefix}{word}'3

def return_one():

    return 1

    def wrapper():

       print('a')

       retval = return_one()

       print('b')

       print(retval)
```

1.   以下调用的输出是什么：

```
add_prefix('nighttime', 'after-')
```

2.   以下调用的输出是什么：

```
add_prefix('nighttime')
```

3.   以下调用的输出是什么：

```
add_prefix()
```

4.   你应该在函数定义上方放置哪一行，以使用函数 `standard_logging` 来装饰它？

点击此处查看代码图片

```
*standard_logging

**standard_logging

@standard_logging

[standard_logging]
```

5.   以下调用会打印出什么：

```
wrapper()
```

## 第二部分

## 数据科学库

