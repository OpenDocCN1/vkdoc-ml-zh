## 14 面向对象编程

任何傻瓜都能写出计算机能理解的代码。优秀的程序员写出人类能理解的代码。

——马丁·福勒

本章内容

*   关联状态与函数
*   类与对象
*   特殊函数
*   类继承

面向对象的编程方法是最流行的方法之一。它试图通过将功能与数据相结合来模拟对象及其关系。如果你考虑用代码模拟一辆汽车，面向对象的方法是将执行加速或刹车等操作的方法，以及油箱中的燃油量等数据，都附加到同一个对象上。其他方法则会将数据（本例中为油量）与函数定义分开，可能将数据作为参数传递给函数。面向对象方法的一大优势在于能够创建人类易于理解的复杂系统表示。

### 状态与函数的分组

与函数式方法不同，面向对象编程将数据和功能捆绑在一起，形成所谓的*对象*。可以说，Python 中的一切都是对象；即使是基本类型也拥有方法和数据。例如，一个 `int` 对象不仅仅保存一个值；它还有方法。其中一个方法是 `to_bytes()` 方法，它可以将值转换为其字节表示形式：

```python
my_num = 13
my_num.to_bytes(8, 'little')
# b'\r\x00\x00\x00\x00\x00\x00\x00'
```

更复杂的数据类型，如列表、字符串、字典和 Pandas `DataFrame`，都结合了数据和功能。在 Python 中，附加到对象上的函数被称为*方法*。Python 面向对象能力的强大之处在于，你可以使用提供的库中的对象，也可以设计自己的对象。

#### 类与实例

对象由类定义。可以将类视为对象的模板。当你实例化一个类时，你会得到该类的对象。创建基本类定义的语法如下：

```python
class <类名>():
    <语句>
```

你可以使用 `pass` 语句定义一个什么都不做的简单类：

```python
class DoNothing():
    pass
```

实例化类的语法如下：

```python
<类名>()
```

因此，要从 `DoNothing` 类创建一个名为 `do_nothing` 的实例，你可以像这样实例化该对象：

```python
do_nothing = DoNothing()
```

如果你检查这个对象的类型：

```python
type(do_nothing)
# __main__.DoNothing
```

你会发现它是一个由 `DoNothing` 类定义的新类型。你可以通过使用内置的 `isinstance()` 函数来确认这一点，该函数用于测试一个对象是否是某个特定类的实例：

```python
isinstance(do_nothing, DoNothing)
# True
```

定义附加到类上的方法最常见的方式是将函数定义缩进到类的内部作用域中，使用以下语法：

```python
class <类名>():
    def <函数名>():
        <语句>
```

该函数的第一个参数是调用它的实例。按照惯例，这个参数被命名为 `self`。下面的示例定义了一个类 `DoSomething`，其中包含方法 `return_self()`，该方法返回 `self`，然后创建一个实例并演示 `return_self()` 的返回值实际上就是该实例本身：

```python
class DoSomething():
    def return_self(self):
        return self

do_something = DoSomething()
do_something == do_something.return_self()
# True
```

> **注意**
> 虽然在方法定义中你需要将 `self` 作为参数，但在调用方法时，你不需要指定 `self`，因为它会在后台自动传递。

除了 `self` 参数之外，你可以像定义其他函数一样定义方法。你也可以使用 `self` 对象在类定义中创建和访问对象变量，语法如下：

```
self.<变量名>
```

类似地，方法和属性将附加到从该类实例化的对象上：

```python
class AddAttribute():
    def add_score(self):
        self.score = 14

add_attribute = AddAttribute()
add_attribute.add_score()
add_attribute.score
# 14
```

要在同一个类中从一个方法调用另一个方法，你可以使用以下语法：

```
self.<方法名>
```

代码清单 14.1 演示了如何在同一个类中从一个方法调用另一个方法。

**代码清单 14.1 内部调用方法**

```python
class InternalMethodCaller():
    def method_one(self):
        print('调用方法一')

    def method_two(self, n):
        print(f'方法二调用方法一 {n} 次')
        for _ in range(n):
            self.method_one()

internal_method_caller = InternalMethodCaller()
internal_method_caller.method_one()
# 调用方法一

internal_method_caller.method_two(2)
# 方法二调用方法一 2 次
# 调用方法一
# 调用方法一
```

#### 私有方法与变量

任何有权访问该对象的人都可以访问该对象的方法和变量。到目前为止你看到的方法和变量被称为*公有*的，因为它们代表旨在直接使用的数据和功能。有时在定义类的过程中，你需要定义不希望被直接使用的变量或方法。这些被称为*私有*属性，它们的实现细节可能会随着类的演变而改变。私有属性由公有方法在内部使用。Python 没有阻止访问私有属性的机制，但私有属性的名称通常以下划线开头，如下例所示：

```python
class PrivatePublic():
    def _private_method(self):
        print('私有')

    def public_method(self):
        # 调用私有方法
        self._private_method()
        # ... 执行其他操作
```

#### 类变量

使用语法 `self.<变量名>` 定义的变量被称为*实例变量*。这些变量绑定到类的各个实例。每个实例的实例变量可以有不同的值。你也可以将变量绑定到类。这些*类变量*由该类的所有实例共享。代码清单 14.2 演示了一个同时包含类变量和实例变量的类。该类的两个实例共享类变量的数据，但实例变量具有唯一的值。请注意，类变量并未附加到实例对象 `self` 上。

**代码清单 14.2 类变量与实例变量**

```python
class ClassyVariables():
    class_variable = '黄色'

    def __init__(self, color):
        self.instance_variable = color

red = ClassyVariables('红色')
blue = ClassyVariables('蓝色')

red.instance_variable
# '红色'
red.class_variable
# '黄色'
blue.class_variable
# '黄色'
blue.instance_variable
# '蓝色'
```

### 特殊方法

在 Python 中，一些特殊的方法名称被保留用于特定功能。这些方法包括用于运算符和容器功能的方法，以及对象初始化方法。这些方法中最常用的是 `__init__()` 方法，它在每次实例化对象时被调用。它通常用于设置对象的初始属性值。代码清单 14.3 定义了一个类 `Initialized`，其中包含一个 `__init__()` 方法，该方法接受一个额外的参数 `n`。当你实例化这个类的一个实例时，你必须为这个参数提供一个值，然后它会被赋值给变量 `count`。这个变量随后可以通过类中的其他方法以 `self.count` 的形式访问，或者通过实例化对象以 `<对象>.<属性>` 的形式访问。

**代码清单 14.3 `__init__` 方法**

```python
class Initialized():
    def __init__(self, n):
        self.count = n

    def increment_count(self):
        self.count += 1

initialized = Initialized(2)
initialized.count
# 2
initialized.increment_count()
initialized.count
# 3
```

## 表示方法

`__repr__()` 和 `__str__()` 方法用于控制对象的表示形式。`__repr__()` 方法旨在提供对象的技术性描述。理想情况下，该描述包含重新创建对象所需的信息。当您将对象用作语句时，会看到这种表示形式。`__str__()` 方法旨在定义一种不那么严格但更人性化的表示形式。这是您将对象转换为字符串时的输出，就像 `print()` 函数自动执行的那样。清单 14.4 展示了 `__repr__()` 和 `__str__()` 的使用。

清单 14.4 `__repr__` 和 `__str__`

点击此处查看代码图片

```
class Represented():

    def __init__(self, n):

        self.n = n

    def __repr__(self):

        return f'Represented({self.n})'

    def __str__(self):

        return 'Object demonstrating __str__ and __repr__'

represented = Represented(13)

represented

Represented(13)

r = eval(represented.__repr__())

type(r)

__main__.Represented

r.n

str(represented)

'Object demonstrating __str__ and __repr__'

print(represented)

Object demonstrating __str__ and __repr__
```

## 富比较方法

富比较方法用于定义对象在使用 Python 内置运算符时的行为。清单 14.5 展示了如何为各种比较运算符定义方法。`CompareMe` 类使用变量 `score` 来确定比较结果，并且仅在必要时才回退到变量 `time`。

清单 14.5 比较方法

点击此处查看代码图片

```
class CompareMe():

    def __init__(self, score, time):

        self.score = score

        self.time = time

    def __lt__(self, O):

        """ Less than"""

        print('called __lt__')

        if self.score == O.score:

            return self.time > O.time

        return self.score < O.score

    def __le__(self, O):

        """Less than or equal"""

        print('called __le__')

        return self.score <= O.score

    def __eq__(self, O):

        """Equal"""

        print('called __eq__')

        return (self.score, self.time) == (O.score, O.time)

    def __ne__(self, O):

        """Not Equal"""

        print('called __ne__')

        return (self.score, self.time) != (O.score, O.time)

    def __gt__(self, O):

        """Greater Than"""

        print('called __gt__')

        if self.score == O.score:

            return self.time < O.time

        return self.score > O.score

    def __ge__(self, O):

        """Greater Than or Equal"""

        print('called __ge__')

        return self.score >= O.score
```

清单 14.6 使用一些不同的值实例化 `CompareMe` 类，然后测试一些比较运算符。

清单 14.6 尝试运算符

点击此处查看代码图片

```
high_score  = CompareMe(100, 100)

mid_score   = CompareMe(50, 50)

mid_score_1 = CompareMe(50, 50)

low_time    = CompareMe(100, 25)

high_score > mid_score

called __gt__

True

high_score >= mid_score_1

called __ge__

True

high_score == low_time

called __eq__

False

mid_score == mid_score_1

called __eq__

True

low_time > high_score

called __gt__

True
```

可以定义将一个属性与一个对象进行比较的比较方法。清单 14.7 创建了一个类，该类直接将其 `score` 属性与另一个对象进行比较。这允许您将一个对象与任何其他可与 `int` 进行比较的类型进行比较。（为简洁起见，此清单仅实现了小于和等于方法。）

清单 14.7 与对象比较

点击此处查看代码图片

```
class ScoreMatters():

    def __init__(self, score):

        self.score = score

    def __lt__(self, O):

        return self.score < O

    def __eq__(self, O):

        return self.score == O

my_score = ScoreMatters(14)

my_score == 14.0

True

my_score < 15

True
```

在 Python 代码中定义令人困惑或不合逻辑的比较是很重要的。您需要在这些定义中牢记最终用户。例如，清单 14.8 定义了一个类，它总是比与之比较的任何东西都大——甚至比它自己还大。这可能会导致该类的最终用户感到困惑。

清单 14.8 一个令人困惑的“大”类

点击此处查看代码图片

```
class ImAllwaysBigger():

    def __gt__(self, O):

        return True

    def __ge__(self, O):

        return True

i_am_bigger = ImAllwaysBigger()

no_i_am_bigger = ImAllwaysBigger()

i_am_bigger > "Anything"

True

i_am_bigger > no_i_am_bigger

True

no_i_am_bigger > i_am_bigger

True

i_am_bigger > i_am_bigger

True
```

## 数学运算符方法

Python 中有用于数学运算的特殊方法。清单 14.9 定义了一个类，该类实现了 `+`、`-` 和 `*` 运算符的方法。这个类基于其 `.value` 变量返回新对象。

清单 14.9 选定的数学运算

点击此处查看代码图片

```
class MathMe():

    def __init__(self, value):

        self.value = value

    def __add__(self, O):

        return MathMe(self.value + O.value)

    def __sub__(self, O):

        return MathMe(self.value - O.value)

    def __mul__(self, O):

        return MathMe(self.value * O.value)

m1 = MathMe(3)

m2 = MathMe(4)

m3 = m1 + m2

m3.value

m4 = m1 - m3

m4.value

m5 = m1 * m3

m5.value
```

还有更多特殊方法，包括用于位运算和定义支持切片的容器类对象的方法。有关特殊方法的完整列表，请参见 [`docs.python.org/3/reference/datamodel.html#special-method-names`](https://docs.python.org/3/reference/datamodel.html#special-method-names)。

### 继承

面向对象编程中最重要且最强大的概念之一就是继承。通过继承，一个类可以将另一个或多个类声明为父类。子类可以使用父类的方法和变量，就像它们是在子类自身定义中声明的一样。清单 14.10 定义了一个 `Person` 类，然后将其作为另一个类 `Student` 的父类。

清单 14.10 `基本继承`

```
class Person():

    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name

class Student(Person):
    def introduce_yourself(self):
        print(f'Hello, my name is {self.first_name}')

barb = Student('Barb', 'Shilala')
barb.first_name
'Barb'
barb.introduce_yourself()
Hello, my name is Barb
```

请注意，方法 `Student.introduce_yourself()` 使用了变量 `Person.first_name`，就像它是在 `Student` 类中声明的一样。如果你检查该实例的类型，会发现它是 `Student`：

```
type(barb)
__main__.Student
```

重要的是，如果你使用 `isinstance()` 函数，你会发现该实例既是 `Student` 类的实例：

```
isinstance(barb, Student)
True
```

也是 `Person` 类的实例：

```
isinstance(barb, Person)
True
```

当你编写的代码期望类之间具有某些共享行为时，继承非常有用。例如，如果你正在实现一个作业编排系统，你可能期望每种类型的作业都有一个 `run()` 方法。与其测试每一种可能的作业类型，不如直接定义一个带有 `run()` 方法的父类。任何继承自该父类（因此也是该父类的实例）的作业都将定义有 `run()` 方法，如清单 14.11 所示。

清单 14.11 `测试基类`

```
class Job():
    def run(self):
        print("I'm running")

class ExtractJob(Job):
    def extract(self, data):
        print('Extracting')

class TransformJob(Job):
    def transform(self, data):
        print('Transforming')

job_1 = ExtractJob()
job_2 = TransformJob()

for job in [job_1, job_2]:
    if isinstance(job, Job):
        job.run()
I'm running
I'm running
```

如果子类定义了一个与父类中同名的变量或方法，子类的实例将使用子类自身的定义。例如，假设你定义了一个带有 `run()` 方法的父类：

```
class Parent():
    def run(self):
        print('I am a parent running carefully')
```

同时，你定义了一个重新定义了该方法的子类：

```
class Child(Parent):
    def run(self):
        print('I am a child running wild')
```

那么子类的实例将使用子类自身的定义：

```
chile = Child()
chile.run()
I am a child running wild
```

有时，显式调用父类的方法会很有用。例如，在子类的 `__init__()` 方法中调用父类的 `__init__()` 方法并不罕见。`super()` 函数可以访问父类及其属性。下面的示例使用 `super()` 从子类 `Student` 中调用 `Person.__init__()`：

```
class person():
    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name

class student(person):
    def __init__(self, school_name, first_name, last_name):
        self.school_name = school_name
        super().__init__(first_name, last_name)

lydia = student('boxford', 'lydia', 'smith')
lydia.last_name
'smith'
```

继承并不局限于单个父类或单层。一个类可以继承自另一个类，而后者本身又继承自另一个类：

```
class A():
    pass

class B(A):
    pass

class C(B):
    pass

c = C()
isinstance(c, B)
True
isinstance(c, A)
True
```

一个类也可以继承自多个父类：

```
class A():
    def a_method(self):
        print(A's method)

class B():
    def b_method(self):
        print(B's method)

class C(A, B):
    pass

c = C()
c.a_method()
A's method
c.b_method()
B's method
```

**注意**
总的来说，我建议在可能的情况下避免构建过于复杂的继承树。当你追踪通过继承树定义的变量和方法之间的交互时，复杂的继承会变得非常难以调试。

**注意**
关于面向对象设计的著作已经很多。我建议在开始一个大型面向对象项目之前进一步研究它，以避免不必要的陷阱。

### 总结

面向对象编程涉及将数据和功能分组到由类定义的对象中。特殊方法允许你定义能够与 Python 运算符和实现容器行为的类一起工作的类。类可以继承其他类的定义。

### 问题

1. 在类定义中，变量 `self` 代表什么？
2. 何时调用 `__init__()` 特殊方法？
3. 给定以下类定义：

```
class Confuzed():
    def __init__(self, n):
        self.n = n

    def __add__(self, O):
        return self.n – O
```

对于以下代码，你期望得到什么结果？

```
c = Confuzed(12)
c + 12
```

4. 以下代码的输出是什么？

```
class A():
    def say_hello(self):
        print('Hello from A')

    def say_goodbye(self):
        print('Goodbye from A')

class B(A):
    def say_goodbye(self):
        print('Goodbye from B')

b = B()
b.say_hello()
b.say_goodbye()
```