# 5. 使用 Pydantic 和 Pandera 进行数据验证入门

## 引言

数据验证已经存在了很长时间。在关系型数据库管理系统（RDBMS）早期采用阶段，基本的验证规则被定义在有限的范围内。随着 SQL 在关系型系统中的发展，它通过编写 SQL 代码提供了更多指定更好验证规则的机会。在大数据和机器学习的现代时代，数据验证占据了更重要的地位。数据质量直接影响基于这些数据构建的分析模型所获得的洞察和智能。在本章中，我们将探讨两个主要的数据验证库，即 Pydantic 和 Pandera，并深入研究其特性、功能和实际应用。

到本章结束时，你将能够：
*   深入理解数据验证的概念和原理。
*   学习数据验证库 Pydantic 及其关键概念。
*   使用 Pydantic 模型定义数据模式，并探索验证器和自定义验证逻辑。
*   学习 Pandera，并使用 Pandera 的 `DataFrameSchema` 和 `Column` 类定义数据模式。
*   探索 Pandera 中的检查功能，以指定数据约束和属性，并对数据执行统计测试。
*   学习使用 Pandera 进行惰性验证，并通过装饰器将 Pandera 集成到现有数据工程项目中。

## 数据验证简介

随着政府机构和企业采用计算机系统，软件开发获得了更多发展动力，导致在软件应用程序开发项目中纳入了数据验证规范和规则。如今，Web 应用程序和移动应用程序在现代计算中日益占据主导地位，包括但不限于消费和获取服务、通信等。因此，实时的客户端数据验证已成为确保数据准确性和完整性的常见做法。

随着我们现已进入完全成熟的机器学习和分析开发阶段，先进的数据验证技术已成为数据质量措施的一部分。数据质量是数据管理过程的重要组成部分。

### 优质数据的必要性

整个机器学习建模和数据分析过程都建立在若干假设之上。就像通用线性模型假设底层数据呈正态分布一样，机器学习模型和数据分析也基于对数据的假设。在我们的项目中，此类假设可能如下所示：
*   我们的数据是当前的，即被认为能够反映现实情况。
*   如果模型超过准确率阈值，则该模型是好的；否则，需要重新训练模型和算法。
*   来自源系统或我们提取数据的地方的数据应该是准确的。

数据验证可确保这些假设成立。

模型的构建和决策的制定都基于一个理念：输入的底层数据是优质、一致且准确的。因此，任何瓶颈和不一致都可能归因于模型本身，并需要进一步的开发周期。

以下是一些事实：
*   数据质量直接影响从模型中获得的洞察和智能；它影响从模型获得的洞察的准确性、有效性和可信度。
*   组织越来越多地从各种数据系统和外部来源获得更多数据；拥有良好的数据验证流程以确保质量和一致性至关重要。
*   数据验证是一个持续的过程，需要定期审查这些来源中各个字段或数据点的质量和一致性，从而形成对组织运营和绩效的统一视图。
*   某些行业在数据隐私、数据完整性和数据准确性方面有合规和监管要求；数据验证流程可能有助于遵守各种法规。

以下是数据质量最常见的陷阱：
*   不完整的数据
*   不一致的数据
*   过时的数据

### 定义

数据验证是确保收集或输入的数据符合某些预定义标准和规则的过程。

其目标是确保来自源系统的数据的准确性、可靠性和完整性。

数据验证过程通常通过识别和防止错误、不一致或不准确来运作。

数据验证过程可能发生在数据输入、数据导入或数据处理期间，涉及根据预定义规则检查数据，以确保其符合预期标准。

### 数据验证原则

当将这些验证原则应用于数据集时，我们可以详细审视它们；然而，理解它们的含义和目的是很重要的。以下是数据验证的一些基本原则。

#### 数据准确性

数据准确性是验证数据没有错误的过程。数据准确性的目标是执行转换和清理操作，以根据项目或业务上下文保持数据的可接受性和正确性。只要下游流程期望的数据类型相同，数据不准确可能不一定会影响下游数据管道。然而，它肯定会对下游流程可能生成的结果质量和智能产生重大影响。因此，保持数据准确和最新非常重要。

#### 数据唯一性

数据唯一性是指行中的每一行或每个数据点，或者列中的每个数据点，都不是另一个数据点的副本，也不是任何形式的重复。在许多情况下，由于性质和上下文（例如，数值格式的分类区间），数据点可能在多个实例中重复出现。然而，在某些情况下，当数据点是涉及多个输入数据点的转换输出时，测试和验证唯一性可能是有益的。另一个应用是，当数据点是一个小数点后必须有“n”位数字的浮点数时，验证数据点的唯一性当然会有所帮助。

#### 数据完整性

数据完整性是验证数据集所需的数据是否实际存在的过程。换句话说，数据完整性是在某个字段或数据点被视为强制或关键时，计算缺失值或部分可用值。一个很好的例子是电子邮件地址的验证；电子邮件地址通常由字符串、数字和可接受的特殊字符组合而成，后跟“@”符号、域名以及“.com”。如果缺少其中任何一个元素，都可以称为数据不完整。电话号码是另一个很好的例子。相关的区号加上电话号码数字，就构成了完整的数据。此外，它总共应该是十位数字（以美国为例）。

#### 数据范围

数据范围是验证数值数据点是否落在某些可接受的边界内，或分类数据点是否属于一组预定义类别的过程。例如，一个人的年龄不能超过 110 岁左右（链接到最年长的人）。同样，信用评分等不能有负值（信用评分从 300 开始）。

#### 数据一致性

数据一致性是验证和确认所包含的值是否一致，并与同一数据集内的其他数据保持一致的过程。在某些罕见情况下，数据的范围有明确定义的上下限。然而，业务规则可能表明数据应位于一个子范围内，该子范围的上下限完全在给定范围之内。

#### 数据格式

数据格式是验证数据是否遵循与预期不同的编码，以及数据类型是否为适当格式的过程。数据格式是验证的重要组成部分。当数据在数据系统之间传输时，一个系统很可能不支持另一个系统的原生编码。因此，数据类型经常被转换或强加到数据字段上。一个常见的问题是将日期读取为字符串。另一个问题是，当标识号被转换为字符串时，它可能会被自动转换为整数；问题在于它可能会丢失所有尾随的零，而这正是整数被转换为字符串的原因。

#### 参照完整性

参照完整性是验证和确认表之间存在的数据关系的过程。为了进一步说明这一点，考虑一个示例，其中管道正在将关系表从源写入目标数据库系统。确保参照完整性得以维护可能很重要，这样当使用特定的连接条件连接这些表时，它们能够继续产生相似的结果。

数据验证可以在开发阶段执行，或者您可以设置一个自动化流程来检查各种数据点。在本章中，我们将介绍 Pydantic 库。

## Pydantic 简介

Pydantic 是用于执行数据验证的最流行的 Python 库。它的工作方式是，在将数据加载到 Pydantic 模型时，Pydantic 会强制执行数据类型和约束。Pydantic 一些最有效的应用包括：
*   验证 JSON 文档和数据集
*   从外部文件和环境变量加载配置数据
*   在将来自 Web API 的输入数据插入数据库之前对其进行验证

Pydantic 提供了一种相对简单的方法来声明传入数据的模式并验证 Pydantic 模型。请注意，当您将数据加载到 Pydantic 模型时，验证会自动发生。Pydantic 会自动捕获错误并对传入数据强制执行约束。在我们深入探讨 Pydantic 的精彩世界之前，让我们先回顾一下 Python 中类型注解的概念。

### 类型注解速览

在 Python 编程实践中，类型注解或类型提示是一项可选功能，用于指定输入参数的类型或给定函数返回的变量类型。类型注解是一种最佳实践，它能告知开发者应向函数传递何种类型的数据，以及当函数返回变量或值时，应期望得到何种类型的数据。Python 类型注解仅指定函数、变量、方法等预期的数据类型。Python 并不会强制执行这些注解。因此，默认情况下，仅凭类型注解无法进行类型检查。不过，类型注解确实能提升代码的可读性和文档质量。

让我们看一个在 Python 中为变量添加注解的简单示例：

```python
myVariable: int = 0
```

现在，这个 Python 变量是可变的。接下来，我们看看如何修改这个值：

```python
myVariable = 1
print(myVariable)
```

执行 `print` 函数后，`myVariable` 的值将是：

```
1
```

现在，我们再进一步：

```python
myVariable = 9768.3491
print(myVariable)
```

此时 `myVariable` 的类型实际上是 `float` 而非 `int`；`myVariable` 的值也发生了变化。我们得到以下输出：

```
9768.3491
```

让我们看一个函数的示例：

```python
def addition(a: int, b: int) -> float:
    c: int = 0
    c = a + b
    return c
```

因此，当我们调用这个函数时：

```python
addition(33, 44)
```

我们会得到：

```
77
```

请注意，指定这个 `addition` 函数返回 `float` 类型，同时在函数内部显式定义了一个整数，这并不是一个好的编程实践。这里只是想说明 Python 不会强制执行数据类型。

这是一个更好的示例：

```
def greet(name: str) -> str:
    return "Hello " + name
```

当我们调用这个函数时：

```
greet("your name")
```

它会返回：

```
Hello your name
```

`Pydantic` 是 Python 中用于数据验证的最广泛使用的库。像苹果等大型科技公司以及大型政府机构都在使用 `Pydantic`（并且也在为其做贡献）。`Pydantic` 最近发布了一个名为 `Pydantic V2` 的重大升级。根据发布说明，`Pydantic` 的验证和序列化逻辑已用 `Rust` 编程语言重写，因此 `V2` 版本将比早期版本快数十倍。

鉴于 `Pydantic` 能够生成 `JSON Schema`，它将成为与其他支持 `JSON Schema` 的工具集成的默认选择。`Pydantic` 提供了一个严格模式，在该模式下数据强制转换将不可用。

**注意**

数据强制转换是将数据从一种类型转换为另一种类型的过程，以便数据在特定上下文中可用。

### 设置与安装

以下是从 `PyPI` 安装 `Pydantic` 的方法：

```
pip install pydantic
```

`Pydantic` 也可在 `conda` 环境中使用。

### `Pydantic` 模型

模型是你在 `Pydantic` 中定义 Schema 的方式。`Pydantic` 模型本质上就是编写继承自 `BaseModel` 的类。定义类后，你就可以开始为数据集定义 Schema。一旦数据通过 `Pydantic` 模型传递，`Pydantic` 将确保源数据中的每个数据字段都符合 `Pydantic` 类中定义的 Schema。

这是一个示例。让我们导入一个逗号分隔值格式的数据文件：

```
from pydantic import BaseModel
import pandas as pd
data = pd.read_csv("/home/parallels/Downloads/MOCK_DATA.csv")
```

让我们检查输出，确认数据已加载以供测试：

```
print(data.head(2))
```

将输出：

```
       name  age
0  Ermentrude Azema   22
1       Joey Morteo   56
```

现在让我们为 `Pydantic` 模型类定义 Schema：

```
class TestPerson(BaseModel):
    name: str
    age: int
```

现在让我们创建一个 `Pydantic` 模型对象：

```
for _, person in data.iterrows():
    person = person.to_dict()
    pydantic_model = TestPerson(**person)
    print(pydantic_model.name)
```

将输出：

```
name='Ermentrude Azema' age=22
name='Joey Morteo' age=56
```

#### 嵌套模型

`Pydantic` 通过将 `Pydantic` 模型用作类型，帮助定义复杂的层次化数据结构。这是一个示例。

让我们导入所需的库和数据：

```
import pydantic, json
from typing import List
with open("/home/parallels/Documents/data2.json") as f:
    data = json.load(f)
print(data[0])
```

数据集中第一条记录如下所示：

```
{
    'first_name': 'Angil',
    'last_name': 'Boshers',
    'interests': {
        'first': 'sports',
        'second': 'mountains'
    }
}
```

现在让我们定义 `Pydantic` 模型：

```
class Interests(pydantic.BaseModel):
    first: str
    second: str

class Stu(pydantic.BaseModel):
    first_name: str
    last_name: str
    interests: List[Interests]
```

父模型和直接子模型都有各自的 `Pydantic` 模型类。父 `Pydantic` 模型类使用 `List` 方法引用直接子模型类。

让我们创建模型对象并打印模型输出：

```
for i in data:
    output = Stu(**i)
    print(output)
```

将输出：

```
first_name='Angil' last_name='Boshers' interests=[Interests(first='sports', second='mountains')]
first_name='Aggi' last_name='Oswick' interests=[Interests(first='long-drives', second='beaches')]
```

### 字段

`Pydantic` 中的 `field` 函数用于为给定字段指定默认值，以防该字段在源文档中缺失。

这是一个说明 `field` 函数的示例。首先，让我们导入库并定义 `Pydantic` 模型：

```
import pydantic

class Person(pydantic.BaseModel):
    name: str = pydantic.Field(default='John Doe')
    age: int = pydantic.Field(default=999)
```

如你所见，默认值指定为 `John Doe`。现在让我们定义 `Pydantic` 模型对象并观察输出：

```
newPerson = Person()
print(newPerson)
```

将输出：

```
name='John Doe' age=999
```

### JSON Schema

`Pydantic` 允许我们从创建的 `Pydantic` 模型类生成 `JSON Schema`。你可能还记得，`JSON Schema` 就是一个 JSON 数据结构。`JSON Schema` 断言了 JSON 文档的结构。

让我们重新审视之前的嵌套模型示例。我们导入必要的库，创建 `Pydantic` 模型类，创建 `Pydantic` 模型对象，并打印 `JSON Schema`：

```
import pydantic, json
from typing import List
with open("/home/parallels/Documents/data2.json") as f:
    data = json.load(f)
print(data[0])

class Interests(pydantic.BaseModel):
    first: str
    second: str

class Stu(pydantic.BaseModel):
    first_name: str
    last_name: str
    interests: List[Interests]

print(Stu.schema_json(indent=3))

# 如果 BaseModel 类中的 schema_json 方法
# 已弃用，请改用 model_json_schema 方法：
#print(Stu.model_json_schema(indent=3))
```

将输出：

# Pydantic 与 Pandera 数据验证入门

## JSON Schema 示例

```json
{
   "title": "Stu",
   "type": "object",
   "properties": {
      "first_name": {
         "title": "First Name",
         "type": "string"
      },
      "last_name": {
         "title": "Last Name",
         "type": "string"
      },
      "interests": {
         "title": "Interests",
         "type": "array",
         "items": {
            "$ref": "#/definitions/Interests"
         }
      }
   },
   "required": [
      "first_name",
      "last_name",
      "interests"
   ],
   "definitions": {
      "Interests": {
         "title": "Interests",
         "type": "object",
         "properties": {
            "first": {
               "title": "First",
               "type": "string"
            },
            "second": {
               "title": "Second",
               "type": "string"
            }
         },
         "required": [
            "first",
            "second"
         ]
      }
   }
}
```

## 约束类型

约束类型是 Pydantic 提供的一种方法，用于规定 Pydantic 模型类中每个字段可以取的值。Pydantic 提供以下类型：

*   对于 `int`，有 `conint` 方法；`conint` 可用于为连续变量设置上下限。

*   对于 `str`，有 `constr` 方法；`constr` 可以在需要时强制字符串为大写或小写。

*   对于浮点数，有 `confloat` 方法；同样，它有助于为浮点数、小数等设置上下限。

```python
from pydantic import BaseModel, validator, conint, confloat

class Person(BaseModel):
    name: str
    age: conint(ge=30)
    blood_sugar_level: confloat(gt=4, lt=10)
```

## Pydantic 中的验证器

Pydantic 提供了一种使用验证器方法来验证字段数据的方式。你甚至可以将验证绑定到某个类型，而不是模型。值得注意的是，如果指定了默认字段值，验证器将不会执行。验证器有以下几种类型：

*   **后置验证器**在 Pydantic 内部解析之后运行。

*   **前置验证器**在 Pydantic 内部解析之前运行。

*   **包装验证器**在 Pydantic 内部解析之前或之后运行。

字段验证器能够对 Pydantic 模型的特定字段进行验证。让我们看一个关于相同概念的示例。

这里，我们导入必要的库和数据文件（在此示例中，是一个平面文件）：

```python
from pydantic import BaseModel, field_validator
import csv
data = csv.DictReader(open("/home/parallels/Downloads/MOCK_DATA.csv"))
```

该数据集是关于人员的，包含两个变量：姓名和年龄。姓名是字符串，年龄是整数。我们的想法是验证有多少人年龄小于 21 岁。现在，让我们定义一个带有验证器的 Pydantic 模型类：

```python
class Person(BaseModel):
    name: str
    age: int

    @field_validator('age')
    def agelimit(cls, value: int) -> int:
        if value < 21:
            raise ValueError("Less than 21 years old")
        return value
```

因此，当我们像这样运行模型输出时：

```python
for person in data:
    pydantic_model = Person(**person)
    print(pydantic_model)
```

我们会得到以下输出：

```
name='Ermentrude Azema' age=22
name='Joey Morteo' age=56
name='Jordan Corp' age=56
name='Jehu Kettlesing' age=25
name='Adham Reeson' age=40
name='Ronda Colomb' age=32
name='Bond Dufton' age=47
name='Pearce Mantione' age=47
name='Reginald Goldman' age=39
name='Morris Dalrymple' age=32
name='Lemar Gariff' age=39
name='Leonhard Ruberti' age=63
name='Alyssa Rozier' age=63
name='Cassey Payze' age=22
name='Lonnie Mingaud' age=56
name='Town Quenby' age=39
Traceback (most recent call last):
  File ~/anaconda3/lib/python3.11/site-packages/spyder_kernels/py3compat.py:356 in compat_exec
    exec(code, globals, locals)
  File ~/Documents/pyd_model.py:25
    pydantic_model = Person(**person)
  File pydantic/main.py:341 in pydantic.main.BaseModel.__init__
pydantic.core.ValidationError: 1 validation error for Person
age
  Less than 21 years old (type=value_error)
```

## Pandera 简介

Pandera 是一个采用 MIT 许可证的开源 Python 库，用于在数据清洗和预处理过程中执行数据验证。Pandera 与 Pandas 的数据框数据结构配合良好。Pandera 支持声明式模式定义，即你可以定义预期的数据结构，包括数据类型、约束和其他属性。一旦定义了模式，Pandera 就能验证传入的 Pandas 数据框对象是否符合用户定义的模式（包括数据类型和约束），并检查字段是否存在等。此外，Pandera 还支持自定义函数和用户定义的验证检查，这对于传入的数据集可能具有非常特定的领域性。

Pandera 还支持统计验证，例如假设检验。请注意，统计验证与数据验证截然不同；统计验证是指假设数据应具有某种属性或满足某个条件，然后通过对总体数据的样本应用各种统计方法和公式来验证该假设。Pandera 支持惰性验证；因此，当数据框需要根据一长串数据验证规则进行验证时，Pandera 会在抛出错误之前执行所有定义的验证。

### 安装与设置

以下是在你的系统中安装 Pandera 的方法：

```bash
pip install pandera
```

这是一个使用 Pandera 进行数据验证的简单示例。我特意没有在代码中使用别名，因为对于初次接触这些内容的人来说，别名可能会造成混淆。

让我们导入库：

```python
import pandas
import pandera
```

如前所述，声明模式定义：

```python
panderaSchema = pandera.DataFrameSchema(
    {
        "id": pandera.Column(pandera.Int),
        "name": pandera.Column(pandera.String)
    }
)
```

现在，引入数据：

```python
data = {
    "id": [123, 2435, 234],
    "name": ["john", "doe", None]
}
pandas_df = pandas.DataFrame(data)
```

现在，让我们根据 `pandera` 定义的模式来验证 `pandas` 数据框：

```
Pandera 提供了一个基于类的 API，类似于 `Pydantic`。这意味着在 Pandera 中，你可以像定义 `Pydantic` 模型类一样定义模式模型。

### Pandera 中的数据框模式

Pandera 提供了一个数据框模式类，用于定义模式的规范，然后用于验证 Pandas 提供的数据框对象的列（和索引）。Pandera 的 `Column` 类支持列检查、数据类型验证和数据强制转换，并指定处理缺失值的规则。

以下是一个示例：

让我们导入库、方法和数据：

```python
import pandas as pd
from pandera import DataFrameSchema, Column, Check
data = pd.DataFrame({"series": [1, 2, 4, 7, None]})
```

让我们定义 Pandera 模式，该模式强制执行严格条件：

```python
StrictlyNotNullSchema = DataFrameSchema(
{
"series": Column(float, Check(lambda x: x > 0))
}
)
```

让我们根据该模式验证数据框：

```python
try:
    strictNotNull = StrictlyNotNullSchema.validate(data)
    print(strictNotNull)
except errors.SchemaError as e:
    print(e.failure_cases)  # dataframe of schema errors
    print("\n\ninvalid dataframe")
    print(e.data)  # invalid dataframe
```

将会产生：

```
index  failure_case
0      4           NaN
invalid dataframe
series
0     1.0
1     2.0
2     4.0
3     7.0
4     NaN
```

如你所见，该模式严格地强制应用于数据框。要接受空值或缺失值，可以指定一个参数，例如 `nullable=True`。

如下所示：

```python
NullAllowedSchema = DataFrameSchema(
{
"series": Column(float, Check(lambda x: x > 0),
nullable=True)
}
)
```

因此，当我们尝试验证并打印结果时：

```python
try:
    nullAllowed = NullAllowedSchema.validate(data)
    print(nullAllowed)
except errors.SchemaError as e:
    print(e.failure_cases)  # dataframe of schema errors
    print("\n\ninvalid dataframe")
    print(e.data)  # invalid dataframe
```

将会产生：

```
series
0     1.0
1     2.0
2     4.0
3     7.0
4     NaN
```

### Pandera 中的数据强制转换

除了验证之外，Pandera 还可以执行数据强制转换。让我们稍微讨论一下数据强制转换。数据强制转换是将给定的数据列从一种类型转换为用户指定的另一种类型的过程。其目的是使数据能够以特定的数据格式被访问。

数据强制转换需要谨慎处理：在某些情况下，如果获取了超出范围的值或无效数据但仍被强制转换，数据强制转换可能会隐藏错误。常见的例子包括将元组转换为列表、将整数转换为字符串等。

如下所示。

让我们初始化一个 Pandas 数据框：

```python
import pandas as pd
from pandera import DataFrameSchema, Column
df = pd.DataFrame({"values": [1,4,5,7,9]})
```

现在，让我们定义模式，要求传入的数据必须是字符串。因此，我们启用 `coerce=True`：

```python
schema = DataFrameSchema(
{
"values": Column(str, coerce=True)
})
dataValidated = schema.validate(df)
```

我们执行验证，看起来没问题。现在，让我们检查一下这里验证后数据的数据类型：

```python
print(dataValidated.iloc[0].map(type))
```

将会产生：

```
values    
Name: 0, dtype: object
```

因此，整数值已被强制转换为字符串。

### Pandera 中的检查

Pandera 中的检查类似于 `Pydantic` 中的约束类型和字段条件指定。检查用于指定数据框、序列等各种对象的属性，这些属性会通过 Pandera 方法进行验证。为了方便阅读代码，可以提前将检查定义为一个变量，然后在 `DataFrameSchema` 对象中调用该变量。

具体实现如下所示。

让我们定义检查。在此示例中，我们检查给定数字是奇数还是偶数。我们在检查中定义如下：

```python
from pandera import (Check,
                     DataFrameSchema,
                     Column,
                     errors)
import pandas as pd
odd = Check.isin([1,3,5,7,9])
```

让我们定义 Pandera 模式和数据：

```python
schema = DataFrameSchema({
"series": Column(int, odd)
})
data = pd.DataFrame({"series": range(10)})
```

让我们尝试验证该模式：

```python
try:
    schema.validate(data)
except errors.SchemaError as e:
    print("\n\ndataframe of schema errors")
    print(e.failure_cases)  # dataframe of schema errors
    print("\n\ninvalid dataframe")
    print(e.data)  # invalid dataframe
```

将输出：

```
dataframe of schema errors
index  failure_case
0      0             0
1      2             2
2      4             4
3      6             6
4      8             8
invalid dataframe
series
0       0
1       1
2       2
3       3
4       4
5       5
6       6
7       7
8       8
9       9
```

因此，输出中同时给出了索引和元素。

默认情况下，Pandera 通过向量化操作执行这些数据验证。

> **注意**
> 向量化操作是一种对向量、矩阵或元素数组执行操作的方式，无需逐个循环遍历每个元素。

您还可以指定是否需要逐元素检查操作。对于之前的示例，实现方式如下：

```python
```

# Pandera 中的统计验证

Pandera 支持假设检验，无需显式加载 `scipy` 或其他统计库。我们来简单了解一下假设检验。

假设检验主要是一个复杂的统计过程，涉及统计分析等内容。假设检验使我们能够根据样本数据对总体得出结论或推断。它首先需要明确想要提出的主张。后续的统计过程则是设计分析方案，使得分析结果能够判断所提出的主张是真还是假。

现在，让我们学习一些专业术语（统计词汇）。如前所述，该过程从对数据提出主张开始。我们称之为**零假设**。剩余的过程是关于验证该主张。然后我们提出**备择假设**，即与我们主张相反的情况。

接下来，我们开始使用各种复杂的公式，主要是为了计算所主张的指标与测量指标之间的差异是偶然因素导致，还是确实存在经验证据反对我们的主张。Pandera 提供了两种类型的假设检验，即单样本 t 检验和双样本 t 检验。单样本 t 检验用于确定具有样本均值的样本集是否确实来自总体。双样本 t 检验用于比较两组数据，并判断是否有统计证据表明某一组确实优于另一组。

让我们看一个例子。

我生成了一个数据集，其中包含 iOS 和 Android 操作系统的某种分数。这是一个随机生成的数据集。示例输出如下所示：

```
os hours
0      ios   4.5
1  Android   5.1
2      ios   5.1
3  Android   5.1
4      ios   5.7
```

让我们详细看一下。

首先，我们导入必要的库、方法和数据：

```
import pandas as pd
from pandera import DataFrameSchema, Column, Check, Hypothesis
df = pd.read_csv("/home/parallels/data.csv")
df[['hours','os']]  = df['float\tname'].str.split('\t',expand=True)
data = df[['os','hours']]
```

请注意，数据是随机生成的，我需要将列拆分为两列，然后重新排列列的顺序。

我的主张是 iOS 的分数优于 Android。再次强调，这些是随机生成的浮点数，并不代表任何真实情况。以下是它们在统计语言中的解释：

用统计语言表述：

**零假设**：iOS 和 Android 之间的价值分数没有显著差异。

**备择假设**：iOS 和 Android 之间的价值分数存在显著差异。

需要注意的是，备择假设并不一定意味着给定主张的反面是正确的。它仅仅意味着我们没有足够的数据来证明我们的主张是正确的。

代码如下所示：

```
schema = DataFrameSchema(
{
"hours" : Column(
float, [
Hypothesis.two_sample_ttest(
sample1="ios",
sample2="Android",
groupby="os",
relationship="greater_than",
alpha=0.02,
equal_var=True
)
], coerce=True
),
"os" : Column(str)
})
```

让我们尝试运行验证：

```
try:
schema.validate(data)
except errors.SchemaError as exc:
print(exc)
```

我们得到：

```
SchemaError:  failed series or dataframe validator 0:

```

正如我们之前讨论的，目前没有足够的数据来判断我们的主张是否正确，并且我们的结论大约有 98% 的准确度。置信度由一个名为“alpha”的参数指定。

# 惰性验证

到目前为止，我们看到当调用 `validate` 方法时，一旦模式中指定的某个假设被证伪，就会立即引发 `SchemaError`。因此，程序会停止执行后续提供的其他指令。在许多情况下，查看在不同列和检查上引发的所有错误可能更有用。惰性验证正是实现这一点的选项。我们在之前的章节中已经看到了代码的惰性求值。惰性求值是指代码仅在真正需要时才执行。

让我们看一个例子。

让我们验证一个包含一条记录的简单表格。首先，导入库和方法，然后定义 Pandera 模式：

```
import pandas as pd
import json
from pandera import (Column,
Check,
DataFrameSchema,
DateTime,
errors)

# 定义约束条件
check_ge_3 = Check(lambda x: x > 3)
check_le_15 = Check(lambda x: x  0.0)
check_gpa_4 = Check(lambda x: x <= 4.0)

# 模式
schema = DataFrameSchema(
columns={
"StudentName" : Column(str, Check.equal_to("John")),
"CreditsTaken" : Column(int, [check_ge_3, check_le_15]),
"GPA" : Column(float, [check_gpa_0, check_gpa_4]),
"Date" : Column(DateTime),
},
strict=True
)
```

现在，让我们加载数据：

```
df = pd.DataFrame(
{
"StudentName" : ["JohnDoe", "JaneDoe","DoeJohn"],
"CreditsTaken" : [9,12,18],
"GPA" : [3.7, 4.01, 3.5],
"Date" : None,
}
)
```

让我们执行惰性验证：

```
try:
schema.validate(df, lazy=True)
except errors.SchemaErrors as e:
print(json.dumps(e.message, indent=2))
print("dataframe of schema errors")
print(e.failure_cases)  # 模式错误的数据框
print("invalid dataframe")
print(e.data)  # 无效的数据框
```

将会输出：

# Pandera 装饰器

Pandera 可以通过使用装饰器集成到您当前的生产流程中。

> **注意**
> Python 装饰器是一种增强或修改已有函数行为的方式。从编程角度讲，装饰器是一个接受函数作为输入参数并返回另一个函数的函数。当您在现有函数上使用装饰器时，装饰器会扩展当前函数的能力，使其包含装饰器函数的能力。

这是一个简单的装饰器示例：

```python
def decoratortest(func):
    def anotherfunc():
        func()
        print("the decorator function is now executing this line")
    return anotherfunc

@decoratortest
def hello_world():
    print("this is the main hello world function")

hello_world()
```

当您运行此程序时，您会得到：

```
this is the main hello world function
the decorator function is now executing this line
```

这里，原始函数 `hello_world` 被应用了一个名为 `decoratortest` 的装饰器。如您所见，`decoratortest` 是另一个函数，它接受一个函数作为输入并返回一个不同的函数。

当装饰器应用于 `hello_world()` 函数时，`hello_world()` 函数成为装饰器函数的输入。请注意，实际执行的是包装了 `hello_world()` 函数的装饰器函数，而不是 `hello_world()` 函数本身。

Pandera 提供了两个装饰器来对函数返回的变量执行验证。这两个装饰器用于在数据处理前后进行验证。它们分别被称为 `@check_input` 和 `@check_output`。这些装饰器接受 `DataFrameSchema` 对象作为参数。

让我们看一个例子，其中我们有一个现有的 Python 代码，它添加两列并将结果存储在第三列中，所有这些都在 `pandas` 数据框内完成。我们将利用装饰器的概念。让我们看看没有 Pandera 验证参数的代码：

```python
import pandas as pd

data = pd.DataFrame({
    "a": [1,4,7,9,5],
    "b": [12,13,15,16,19],
})

def addition(dataframe):
    dataframe["c"] = dataframe["a"] + dataframe["b"]
    return dataframe

final_df = addition(data)
print(final_df)
```

这是一个简单的例子，我们添加两列并将它们的结果存储在第三列中。现在让我们看看 Pandera 数据验证器与装饰器的集成。首先，我们导入必要的函数、数据和方法：

```python
import pandas as pd
from pandera import DataFrameSchema, Column, Check, check_input

data = pd.DataFrame({
    "a": [1,4,7,9,5],
    "b": [12,13,15,16,19],
})
```

现在我们通过 `DataFrameSchema` 定义 Pandera 数据验证。请注意，为了可读性，检查是单独定义并在 `DataFrameSchema` 中调用的。您也可以直接将检查合并到 `DataFrameSchema` 中：

```python
check_a = Check(lambda x: x <= 10)
check_b = Check(lambda x: x <= 20)

validateSchema = DataFrameSchema({
    "a": Column(int,
        check_a
    ),
    "b": Column(int,
        check_b
    ),
})
```

现在，让我们利用 Pandera 装饰器。我们将使用 `@check_input` 来验证输入列。`DataFrameSchema` 作为参数传递给 `@check_input` 装饰器：

```python
@check_input(validateSchema)
def addition(dataframe):
    dataframe["c"] = dataframe["a"] + dataframe["b"]
    return dataframe

final_df = addition(data)
print(final_df)
```

执行代码后，我们将得到：
```python
{
"DATA": {
    "DATAFRAME_CHECK": [
        {
            "schema": null,
            "column": "StudentName",
            "check": "equal_to(John)",
            "error": "Column 'StudentName' failed element-wise validator number 0: equal_to(John) failure cases: JohnDoe, JaneDoe, DoeJohn"
        },
        {
            "schema": null,
            "column": "CreditsTaken",
            "check": "",
            "error": "Column 'CreditsTaken' failed element-wise validator number 1: > failure cases: 18"
        },
        {
            "schema": null,
            "column": "GPA",
            "check": "",
            "error": "Column 'GPA' failed element-wise validator number 1: > failure cases: 4.01"
        }
    ]
},
"SCHEMA": {
    "SERIES_CONTAINS_NULLS": [
        {
            "schema": null,
            "column": "Date",
            "check": "not_nullable",
            "error": "non-nullable series 'Date' contains null values:0    None1    None2    NoneName: Date, dtype: object"
        }
    ],
    "WRONG_DATATYPE": [
        {
            "schema": null,
            "column": "Date",
            "check": "dtype('datetime64[ns]')",
            "error": "expected series 'Date' to have type datetime64[ns], got object"
        }
    ]
}
}
```

dataframe of schema errors
```
schema_context        column       check check_number failure_case index
0         Column   StudentName       equal_to(John)     0    JohnDoe     0
1         Column   StudentName       equal_to(John)     0    JaneDoe     1
2         Column   StudentName       equal_to(John)     0    DoeJohn     2
3         Column  CreditsTaken                  1         18     2
4         Column           GPA                  1       4.01     1
5         Column          Date       not_nullable       None     None    0
6         Column          Date       not_nullable       None     None    1
7         Column          Date       not_nullable       None     None    2
8         Column          Date  dtype('datetime64[ns]') None   object None
```

invalid dataframe
```
  StudentName  CreditsTaken   GPA  Date
0     JohnDoe             9  3.70  None
1     JaneDoe            12  4.01  None
2     DoeJohn            18  3.50  None
```

请注意，在前面的示例中，`SchemaError` 来自 `errors` 类。这种 “try..except” 块的详尽输出也是查看详细验证结果的好方法。

```
a   b   c
0  1  12  13
1  4  13  17
2  7  15  22
3  9  16  25
4  5  19  24
```

现在，假设我们更改 `check_b` 的验证条件。当前它看起来像：

```
check_b = Check(lambda x: x <= 20)
```

修改后的检查将如下所示：

```
check_b = Check(lambda x: x <= 18)
```

执行代码后，我们得到以下结果：

```
SchemaError: error in check_input decorator of function 'addition':  failed element-wise validator 0:
>
failure cases:
index  failure_case
0      4            19
```

## 结论

数据验证仍然是工程数据管道的关键方面之一。我们讨论了数据验证的各种原则以及对优质数据的需求。接着，我们了解了 `Pydantic`，这是一个专注于确保 Python 对象符合特定数据模型的数据验证库。然后，我们讨论了 `Pandera`，这是另一个专注于对类数据帧对象应用验证检查的数据验证库；这些检查对于数据分析和数据预处理任务非常有效。在下一章中，我们将讨论另一个数据验证库，它主要关注数据管道中的数据质量保证和数据验证。