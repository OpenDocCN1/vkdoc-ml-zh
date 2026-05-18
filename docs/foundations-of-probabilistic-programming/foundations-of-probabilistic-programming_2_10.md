## 9 Pandas

需要说明的是，*添加*数据。

爱德华·R·塔夫特

本章内容

*   Pandas DataFrame 简介

*   创建 DataFrame

*   DataFrame 内省

*   访问数据

*   操作 DataFrame

*   操作 DataFrame 数据

Pandas DataFrame 构建于 NumPy 数组之上，可能是最常用的数据结构。DataFrame 就像是代码中的超级电子表格。它们是数据科学中使用的主要工具之一。本章将介绍如何创建 DataFrame、操作 DataFrame、访问 DataFrame 中的数据以及操作这些数据。

### 关于 DataFrame

Pandas DataFrame 与电子表格类似，由列和行组成。每一列都是一个 `pandas.Series` 对象。从某些方面来说，DataFrame 类似于一个带有列标签和索引标签的二维 NumPy 数组。然而，与 NumPy 数组不同的是，DataFrame 可以包含不同的数据类型。你可以将 `pandas.Series` 对象视为带标签的一维 NumPy 数组。与 NumPy 数组一样，`pandas.Series` 对象只能包含一种数据类型。`pandas.Series` 对象可以使用许多与数组相同的方法，例如 `min()`、`max()`、`mean()` 和 `medium()`。

通常的惯例是将 Pandas 包导入并别名为 `pd`：

```
import pandas as pd
```

### 创建 DataFrame

你可以从多种来源的数据创建 DataFrame，包括字典和列表，更常见的是通过读取文件来创建。你可以使用 `DataFrame` 构造函数创建一个空的 DataFrame：

点击此处查看代码图片

```
df = pd.DataFrame()

print(df)

Empty DataFrame

Columns: []

Index: []
```

不过，最佳实践是使用数据初始化 DataFrame。

#### 从字典创建 DataFrame

你可以从字典列表或字典创建 DataFrame，其中每个键是一个列标签，该键对应的值包含该列的数据。清单 9.1 展示了如何通过为每一列创建一个数据列表，然后创建一个以列名作为键、这些列表作为值的字典来创建 DataFrame。该清单展示了如何将此字典传递给 `DataFrame` 构造函数以构建 DataFrame。

清单 9.1 **从字典创建 DataFrame**

点击此处查看代码图片

```
first_names = ['shanda', 'rolly', 'molly', 'frank',

               'rip', 'steven', 'gwen', 'arthur']

last_names = ['smith', 'brocker', 'stein', 'bach',

              'spencer', 'de wilde', 'mason', 'davis']

ages = [43, 23, 78, 56, 26, 14, 46, 92]

data = {'first':first_names,

        'last':last_names,

        'ages':ages}

participants = pd.DataFrame(data)
```

生成的 DataFrame `participants` 在 Colab 或 Jupyter notebook 中显示如下：

|  | **first** | **last** | **ages** |
| --- | --- | --- | --- |
| **0** | shanda | smith | 43 |
| **1** | rolly | brocker | 23 |
| **2** | molly | stein | 78 |
| **3** | frank | bach | 56 |
| **4** | rip | spencer | 26 |
| **5** | steven | de wilde | 14 |
| **6** | gwen | mason | 46 |
| **7** | arthur | davis | 92 |

**注意**

在本章中，代码示例生成的 DataFrame 表格将在代码之后以表格形式呈现。

你可以看到顶部的列标签、每行中的数据以及左侧的索引标签。

#### 从列表的列表创建 DataFrame

你可以创建一个列表的列表，每个子列表包含一行数据，数据顺序与列的顺序一致：

点击此处查看代码图片

```
data = [["shanda", "smith", 43],

        ["rolly", "brocker", 23],

        ["molly", "stein", 78],

        ["frank", "bach", 56],

        ["rip", "spencer", 26],

        ["steven", "de wilde", 14],

        ["gwen", "mason", 46],

        ["arthur", "davis", 92]]
```

然后你可以将其用作数据参数：

点击此处查看代码图片

```
participants = pd.DataFrame(data)

participants
```

你会得到与从字典创建 DataFrame 相同的结果：

|  | **0** | **1** | **2** |
| --- | --- | --- | --- |
| **0** | shanda | smith | 43 |
| **1** | rolly | brocker | 23 |
| **2** | molly | stein | 78 |
| **3** | frank | bach | 56 |
| **4** | rip | spencer | 26 |
| **5** | steven | de wilde | 14 |
| **6** | gwen | mason | 46 |
| **7** | arthur | davis | 92 |

请注意，生成的 DataFrame 使用了整数列名。如果未提供列名，这是默认情况。你可以显式地提供一个字符串列表作为列名：

点击此处查看代码图片

```
column_names = ['first', 'last', 'ages']
```

类似地，你也可以提供一个列表作为索引标签：

点击此处查看代码图片

```
index_labels = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
```

然后在初始化时使用这些标签，通过参数 `columns` 和 `index`：

点击此处查看代码图片

```
participants = pd.DataFrame(data,

                            columns=column_names,

                            index=index_labels)
```

|  | **first** | **last** | **ages** |
| --- | --- | --- | --- |
| **a** | shanda | smith | 43 |
| **b** | rolly | brocker | 23 |
| **c** | molly | stein | 78 |
| **d** | frank | bach | 56 |
| **e** | rip | spencer | 26 |
| **f** | steven | de wilde | 14 |
| **g** | gwen | mason | 46 |
| **h** | arthur | davis | 92 |

#### 从文件创建 DataFrame

虽然可以从字典和列表创建 DataFrame，但绝大多数情况下，你会从现有的数据源创建 DataFrame。文件是这些数据源中最常见的。Pandas 提供了从文件创建 DataFrame 的函数，支持许多常见的文件类型，包括 CSV、Excel、HTML、JSON 和 SQL 数据库连接。

假设你想从 FiveThirtyEight 网站 [`data.fivethirtyeight.com`](https://data.fivethirtyeight.com) 的数据集 `college_majors` 中打开一个 CSV 文件。解压并将 CSV 文件上传到 Colab 后，你只需将其路径提供给 Pandas 的 `read_csv` 函数即可打开它：

点击此处查看代码图片

```
college_majors = pd.read_csv('/content/all-ages.csv')

college_majors
```

|  | **Major** | **Major_category** | **Total** | **Unemployment_rate** |
| --- | --- | --- | --- | --- |
| **0** | GENERAL AGRICULTURE | Agriculture & Natural Resources | 128148 | 0.026147 |
| **1** | AGRICULTURE PRODUCTION AND MANAGEMENT | Agriculture & Natural Resources | 95326 | 0.028636 |
| **2** | AGRICULTURAL ECONOMICS | Agriculture & Natural Resources | 33955 | 0.030248 |
| **...** | ... | ... | ... | ... |
| **170** | MISCELLANEOUS BUSINESS & MEDICAL ADMINISTRATION | Business | 102753 | 0.052679 |
| **171** | HISTORY | Humanities & Liberal Arts | 712509 | 0.065851 |
| **172** | UNITED STATES HISTORY | Humanities & Liberal Arts | 17746 | 0.073500 |

Pandas 使用 CSV 文件中的数据来确定列标签和列类型。



### 与 DataFrame 数据交互

将数据加载到 DataFrame 后，你应该先查看一下它。Pandas 提供了多种访问 DataFrame 中数据的方式。你可以按行、按列、按单个单元格或这些方式的组合来查看数据。你也可以根据数据的值来提取数据。

**注意**

当我第一次加载不熟悉的数据时，我会先快速查看前几行，并检查数据的汇总统计信息。查看 DataFrame 的前几行能让我了解新数据的大致情况，并确认数据符合我的预期。

#### 头部与尾部

要查看 DataFrame 的前几行，你可以使用 `head` 方法，它会返回前五行：

```
college_majors.head()
```

|  | **专业** | **专业类别** | **总人数** | **失业率** |
| --- | --- | --- | --- | --- |
| **0** | 通用农业 | 农业与自然资源 | 128148 | 0.026147 |
| **1** | 农业生产与管理 | 农业与自然资源 | 95326 | 0.028636 |
| **2** | 农业经济学 | 农业与自然资源 | 33955 | 0.030248 |
| **3** | 动物科学 | 农业与自然资源 | 103549 | 0.042679 |
| **4** | 食品科学 | 农业与自然资源 | 24280 | 0.049188 |

`head` 方法接受一个可选参数，用于指定要返回的行数。你可以像这样指定返回前三行：

```
college_majors.head(3)
```

|  | **专业** | **专业类别** | **总人数** | **失业率** |
| --- | --- | --- | --- | --- |
| **0** | 通用农业 | 农业与自然资源 | 128148 | 0.026147 |
| **1** | 农业生产与管理 | 农业与自然资源 | 95326 | 0.028636 |
| **2** | 农业经济学 | 农业与自然资源 | 33955 | 0.030248 |

`tail` 方法的工作方式与 `head` 类似，但返回的是底部的行。它也接受一个可选参数来指定要返回的行数：

```
college_majors.tail()
```

|  | **专业** | **专业类别** | **总人数** | **失业率** |
| --- | --- | --- | --- | --- |
| **168** | 酒店管理 | 商业 | 200854 | 0.051447 |
| **169** | 管理信息系统与统计 | 商业 | 156673 | 0.043977 |
| **170** | 其他商业与医疗管理 | 商业 | 102753 | 0.052679 |
| **171** | 历史学 | 人文与通识教育 | 712509 | 0.065851 |
| **172** | 美国历史 | 人文与通识教育 | 17746 | 0.073500 |

#### 描述性统计

在查看了 DataFrame 的一些行之后，我喜欢了解数据的整体形态。一个用于此目的的工具是 DataFrame 的 `describe` 方法，它可以生成数据的各种描述性统计信息。你可以像下面这样不带参数地调用 `describe`：

```
college_majors.describe()
```

|  | **总人数** | **失业率** |
| --- | --- | --- |
| **计数** | 1.730000e+02 | 173.000000 |
| **均值** | 2.302566e+05 | 0.057355 |
| **标准差** | 4.220685e+05 | 0.019177 |
| **最小值** | 2.396000e+03 | 0.000000 |
| **25%** | 2.428000e+04 | 0.046261 |
| **50%** | 7.579100e+04 | 0.054719 |
| **75%** | 2.057630e+05 | 0.069043 |
| **最大值** | 3.123510e+06 | 0.156147 |

此方法会计算包含数值数据的列的计数、均值、标准差、最小值、最大值和分位数。它接受可选参数来控制处理哪些数据类型以及返回的分位数范围。要更改分位数，你可以使用 `percentiles` 参数：

点击此处查看代码图片

```
college_majors.describe(percentiles=[0.1, 0.9])
```

|  | **总人数** | **失业率** |
| --- | --- | --- |
| **计数** | 1.730000e+02 | 173.000000 |
| **均值** | 2.302566e+05 | 0.057355 |
| **标准差** | 4.220685e+05 | 0.019177 |
| **最小值** | 2.396000e+03 | 0.000000 |
| **10%** | 9.775600e+03 | 0.037053 |
| **50%** | 7.579100e+04 | 0.054719 |
| **90%** | 6.739758e+05 | 0.080062 |
| **最大值** | 3.123510e+06 | 0.156147 |

此示例指定了 10% 和 90% 的分位数，而不是默认的 25% 和 75%。请注意，无论参数如何，50% 分位数都会被包含在内。

如果你想查看非数值列计算的统计信息，可以指定要处理的数据类型。你可以通过使用 `include` 关键字来实现。传递给此关键字的值应该是一个数据类型序列，可以是 NumPy 数据类型，例如 `np.object`。在 Pandas 中，字符串的类型是 `object`，因此以下代码会包含字符串数据类型的列：

点击此处查看代码图片

```
import numpy as np

college_majors.describe(include=[np.object])
```

这也会找到数据类型的字符串名称，对于 `np.object` 来说，就是 `object`。以下代码会根据类型返回相应的统计信息：

点击此处查看代码图片

```
college_majors.describe(include=['object'])
```

因此，对于字符串，你会得到计数、唯一值的数量、出现频率最高的值以及该最高值的频率：

|  | **专业** | **专业类别** |
| --- | --- | --- |
| **计数** | 173 | 173 |
| **唯一值** | 173 | 16 |
| **最高频** | 地球科学 | 工程学 |
| **频次** | 1 | 29 |

你可以传递字符串 `all` 而不是数据类型列表，来为所有列生成统计信息：

点击此处查看代码图片

```
college_majors.describe(include='all')
```

|  | **专业** | **专业类别** | **总人数** | **失业率** |
| --- | --- | --- | --- | --- |
| **计数** | 173 | 173 | 1.730000e+02 | 173.000000 |
| **唯一值** | 173 | 16 | NaN | NaN |
| **最高频** | 地球科学 | 工程学 | NaN | NaN |
| **频次** | 1 | 29 | NaN | NaN |
| **均值** | NaN | NaN | 2.302566e+05 | 0.057355 |
| **标准差** | NaN | NaN | 4.220685e+05 | 0.019177 |
| **最小值** | NaN | NaN | 2.396000e+03 | 0.000000 |
| **25%** | NaN | NaN | 2.428000e+04 | 0.046261 |
| **50%** | NaN | NaN | 7.579100e+04 | 0.054719 |
| **75%** | NaN | NaN | 2.057630e+05 | 0.069043 |
| **最大值** | NaN | NaN | 3.123510e+06 | 0.156147 |

**注意**

当某个统计量不适用于某种数据类型时（例如字符串的标准差），会插入非数字值 `NAN`。

如果你想排除某些数据类型而不是指定要包含哪些，Pandas 提供了 `exclude` 参数，它接受与 `include` 相同类型的参数：

点击此处查看代码图片

```
college_majors.describe(exclude=['int'])
```

#### 访问数据

在你使用 `head` 或 `tail` 初步查看了 DataFrame，并使用 `describe` 了解了数据的整体形态之后，你就可以开始查看数据以及单独的列、行或单元格了。

这是本章前面提到的 `participants` DataFrame：

```
participants
```

|  | **名** | **姓** | **年龄** |
| --- | --- | --- | --- |
| **a** | shanda | smith | 43 |
| **b** | rolly | brocker | 23 |
| **c** | molly | stein | 78 |
| **d** | frank | bach | 56 |
| **e** | rip | spencer | 26 |
| **f** | steven | de wilde | 14 |
| **g** | gwen | mason | 46 |
| **h** | arthur | davis | 92 |



#### 方括号语法

要在 Pandas DataFrame 中访问列或行，你需要使用方括号语法。这种语法非常适合在交互式会话中探索和处理数据，并且使用它是一种最佳实践。

要访问单个列，你可以在方括号中提供列名作为参数，就像使用字典键一样：

点击此处查看代码图片

```
participants['first']

a    shanda

b     rolly

c     molly

d     frank

e       rip

f    steven

g      gwen

h    arthur

Name: first, dtype: object
```

你可以看到，这会返回该列的数据及其索引、标签和数据类型。如果列名不包含破折号或特殊字符，并且该列名与 DataFrame 的现有属性不同，你可以将列作为属性来访问。

例如，以下是访问 `ages` 列的方法：

点击此处查看代码图片

```
participants.ages

a    43

b    23

c    78

d    56

e    26

f    14

g    46

h    92

Name: ages, dtype: int64
```

这对于 `first` 或 `last` 列不起作用，因为这些列名已经是 DataFrame 的属性。

要访问多个列，你需要将列标签指定为一个列表：

点击此处查看代码图片

```
participants[['last', 'first']]
```

|  | **last** | **first** |
| --- | --- | --- |
| **a** | smith | shanda |
| **b** | brocker | rolly |
| **c** | stein | molly |
| **d** | bach | frank |
| **e** | spencer | rip |
| **f** | de wilde | steven |
| **g** | mason | gwen |

这将返回一个仅包含所请求列的 DataFrame。

方括号语法被重载，允许你同时获取行和列。要指定行，你需要使用切片作为参数。如果切片使用整数，那么这些整数表示要返回的行号。例如，要返回 DataFrame `participants` 的第 3、4 和 5 行，你可以使用切片 `3:6`：

```
participants[3:6]
```

|  | **first** | **last** | **ages** |
| --- | --- | --- | --- |
| **d** | frank | bach | 56 |
| **e** | rip | spencer | 26 |
| **f** | steven | de wilde | 14 |

你也可以使用索引标签进行切片。当你使用标签进行切片时，最后一个值会被包含在内。因此，要获取行 `a`、`b` 和 `c`，你可以使用 `a:c` 进行切片：

```
participants['a':'c']
```

|  | **first** | **last** | **ages** |
| --- | --- | --- | --- |
| **a** | shanda | smith | 43 |
| **b** | rolly | brocker | 23 |
| **c** | molly | stein | 78 |

你可以通过使用一个布尔值列表来指示要返回哪些行。该列表应为每行提供一个布尔值：`True` 表示需要的行，`False` 表示其他行。以下示例返回第二、第三和第六行：

点击此处查看代码图片

```
mask = [False, True, True, False, False, True, False, False]

participants[mask]
```

|  | **first** | **last** | **ages** |
| --- | --- | --- | --- |
| **b** | rolly | brocker | 23 |
| **c** | molly | stein | 78 |
| **f** | steven | de wilde | 14 |

方括号语法提供了一种非常方便且易于阅读的数据访问方式。它常用于交互式会话中实验和探索 DataFrame，但在处理大型数据集时，其性能并非最优。在生产代码或处理大型数据集时，推荐使用 DataFrame 的 `loc` 和 `iloc` 索引器来索引 DataFrame。这些索引器使用的方括号语法与你在此处看到的非常相似。`loc` 索引器使用标签进行索引，而 `iloc` 使用索引位置。

#### 按标签优化访问

使用 `loc` 索引器，你可以提供一个单独的标签，该行的值将被返回。例如，要获取标签为 `c` 的行的值，你只需提供 `c` 作为参数：

点击此处查看代码图片

```
participants.loc['c']

first    molly

last     stein

ages        78

Name: c, dtype: object
```

你可以提供一个标签切片，同样地，最后一个标签会被包含在内：

```
participants.loc['c':'f']
```

|  | **first** | **last** | **ages** |
| --- | --- | --- | --- |
| **c** | molly | stein | 78 |
| **d** | frank | bach | 56 |
| **e** | rip | spencer | 26 |
| **f** | steven | de wilde | 14 |

或者你可以提供一个布尔值序列：

点击此处查看代码图片

```
mask = [False, True, True, False, False, True, False, False]

participants.loc[mask]
```

|  | **first** | **last** | **ages** |
| --- | --- | --- | --- |
| **b** | rolly | brocker | 23 |
| **c** | molly | stein | 78 |
| **f** | steven | de wilde | 14 |

可选的第二个参数可以指示要返回哪些列。例如，如果你想要返回 `first` 列的所有行，你可以使用一个切片、一个逗号和列标签来指定所有行：

点击此处查看代码图片

```
participants.loc[:, 'first']

a    shanda

b     rolly

c     molly

d     frank

e       rip

f    steven

g      gwen

h    arthur

Name: first, dtype: object
```

你可以提供一个列标签列表：

点击此处查看代码图片

```
participants.loc[:'c', ['ages', 'last']]
```

|  | **ages** | **last** |
| --- | --- | --- |
| **a** | 43 | smith |
| **b** | 23 | brocker |
| **c** | 78 | stein |

或者你可以提供一个布尔值列表：

点击此处查看代码图片

```
participants.loc[:'c', [False, True, True]]
```

|  | **last** | **ages** |
| --- | --- | --- |
| **a** | smith | 43 |
| **b** | brocker | 23 |
| **c** | stein | 78 |

#### 按索引优化访问

`iloc` 索引器使你能够使用索引位置来选择行和列。与你之前看到的方括号用法非常相似，你可以使用单个值来指定单行：

点击此处查看代码图片

```
participants.iloc[3]

first    frank

last      bach

ages        56

Name: d, dtype: object
```

或者你可以通过使用切片来指定多行：

```
participants.iloc[1:4]
```

|  | **first** | **last** | **ages** |
| --- | --- | --- | --- |
| **b** | rolly | brocker | 23 |
| **c** | molly | stein | 78 |
| **d** | frank | bach | 56 |

你可以选择性地通过使用第二个切片来指示要返回哪一列：

```
participants.iloc[1:4, :2]
```

|  | **first** | **last** |
| --- | --- | --- |
| **b** | rolly | brocker |
| **c** | molly | stein |
| **d** | frank | bach |



#### 掩码与过滤

DataFrame 的一个强大功能是根据值来选择数据。你可以对列使用比较运算符，以查看哪些值满足某些条件。例如，如果你想查看 `college_majors` DataFrame 中哪些行的专业类别（Major_category）值为 `Humanities & Liberal Arts`，你可以使用相等运算符（`==`）：

点击此处查看代码图片

```
college_majors.Major_category == 'Humanities & Liberal Arts'

0      False

1      False

2      False

3      False

       ...

169    False

170    False

171     True

172     True

Name: Major_category, Length: 173, dtype: bool
```

这会生成一个 `pandas.Series` 对象，其中每一行如果匹配条件，则包含 `True`。一个布尔值序列本身有点意思，但真正的威力在于将其与索引器结合使用来过滤结果。请记住，`loc` 会为输入序列中每个 `True` 值返回对应的行。你可以基于比较运算符和某一行来创建条件，例如，下面展示了大于运算符和 `Total` 行的用法：

点击此处查看代码图片

```
total_mask = college_majors.loc[:, 'Total'] > 1200000
```

你可以将结果用作掩码，只选择满足此条件的行：

点击此处查看代码图片

```
top_majors = college_majors.loc[total_mask]

top_majors
```

|  | **专业** | **专业类别** | **总人数** | **失业率** |
| --- | --- | --- | --- | --- |
| **25** | 通识教育 | 教育 | 1438867 | 0.043904 |
| **28** | 初等教育 | 教育 | 1446701 | 0.038359 |
| **114** | 心理学 | 心理学与社会工作 | 1484075 | 0.069667 |
| **153** | 护理学 | 健康 | 1769892 | 0.026797 |
| **158** | 通用商学 | 商业 | 2148712 | 0.051378 |
| **159** | 会计学 | 商业 | 1779219 | 0.053415 |
| **161** | 商业管理与行政 | 商业 | 3123510 | 0.058865 |

你可以使用 `min()` 方法来检查生成的 DataFrame 是否满足条件：

```
top_majors.Total.min()

```

现在，假设你想查看哪些专业类别的失业率最低。你可以对单个列以及整个 DataFrame 使用 `describe` 方法。例如，如果你对 `Unemployment_rate` 列使用 `describe`，你可以看到底部百分位的最高失业率是 0.046261：

点击此处查看代码图片

```
college_majors.Unemployment_rate.describe()

count    173.000000

mean       0.057355

std        0.019177

min        0.000000

25%        0.046261

50%        0.054719

75%        0.069043

max        0.156147

Name: Unemployment_rate, dtype: float64
```

你可以为所有失业率小于或等于此值的行创建一个掩码：

点击此处查看代码图片

```
employ_rate_mask = college_majors.loc[:, 'Unemployment_rate'] <= 0.046261
```

你可以使用此掩码生成一个仅包含这些行的 DataFrame：

点击此处查看代码图片

```
employ_rate_majors = college_majors.loc[employ_rate_mask]
```

然后，你可以使用 `pandas.Series` 对象的 `unique` 方法来查看生成的 DataFrame 中包含哪些专业类别：

点击此处查看代码图片

```
employ_rate_majors.Major_category.unique()

array(['Agriculture & Natural Resources', 'Education', 'Engineering',

       'Biology & Life Science', 'Computers & Mathematics',

       'Humanities & Liberal Arts', 'Physical Sciences', 'Health',

       ‘Business’], dtype=object)
```

所有这些类别都至少有一行的失业率满足该条件。

#### Pandas 布尔运算符

你可以将三个布尔运算符 AND（`&`）、OR（`|`）和 NOT（`~`）与你的条件结果结合使用。你可以使用 `&` 或 `|` 来组合条件，创建更复杂的条件。你可以使用 `~` 来创建一个与你的条件相反的掩码。

例如，你可以使用 AND 基于之前的掩码创建一个新掩码，以查看最受欢迎专业中哪些专业类别的失业率较低。为此，你可以在现有掩码之间使用 `&` 运算符来生成一个新掩码：

点击此处查看代码图片

```
total_rate_mask = employ_rate_mask & total_mask

total_rate_mask

0      False

1      False

2      False

3      False

4      False

      ...

168    False

169    False

170    False

171    False

172    False

Length: 173, dtype: bool
```

通过查看生成的 DataFrame，你可以看到哪些最受欢迎的专业失业率最低：

点击此处查看代码图片

```
college_majors.loc[total_rate_mask]
```

|  | **专业** | **专业类别** | **总人数** | **失业率** |
| --- | --- | --- | --- | --- |
| **25** | 通识教育 | 教育 | 1438867 | 0.043904 |
| **28** | 初等教育 | 教育 | 1446701 | 0.038359 |
| **153** | 护理学 | 健康 | 1769892 | 0.026797 |

你可以将 `~` 运算符与你的失业率掩码结合使用，创建一个所有行的失业率都高于底部百分位的 DataFrame：

点击此处查看代码图片

```
lower_rate_mask = ~employ_rate_mask

lower_rate_majors = college_majors.loc[lower_rate_mask]
```

你可以通过对 `Unemployment_rate` 列使用 `min` 方法来验证这一点，以确保它高于底部百分位的最高失业率：

点击此处查看代码图片

```
lower_rate_majors.Unemployment_rate.min()

0.046261360999999994
```

要选择所有满足热门专业条件或失业率条件的行，你可以使用 `|` 运算符：

点击此处查看代码图片

```
college_majors.loc[total_mask | employ_rate_mask]
```

生成的 DataFrame 包含满足任一条件的所有行。



### 操作 DataFrame

一旦你在 DataFrame 中获得了所需的数据，你可能想要修改这个 DataFrame。你可以重命名列或索引，可以添加新的列和行，也可以删除列和行。

使用 DataFrame 的 `rename` 方法可以轻松更改列的标签。以下是如何使用 DataFrame 的 `columns` 属性来查看当前列名：

点击此处查看代码图片

```
participants.columns

Index(['first', 'last', 'ages'], dtype='object')
```

然后，你可以通过提供一个将每个旧列名映射到新列名的字典来重命名你选择的列。例如，下面是将列 `ages` 的标签更改为 `Age` 的方法：

点击此处查看代码图片

```
participants.rename(columns={'ages': 'Age'})
```

|  | **first** | **last** | **Age** |
| --- | --- | --- | --- |
| **a** | shanda | smith | 43 |
| **b** | rolly | brocker | 23 |
| **c** | molly | stein | 78 |
| **d** | frank | bach | 56 |
| **e** | rip | spencer | 26 |
| **f** | steven | de wilde | 14 |
| **g** | gwen | mason | 46 |
| **h** | arthur | davis | 92 |

默认情况下，`rename` 方法会使用新的列标签返回一个新的 DataFrame。因此，如果你再次检查原始 DataFrame 的列名，你会看到旧的列名：

点击此处查看代码图片

```
participants.columns

Index(['first', 'last', 'ages'], dtype='object')
```

许多 DataFrame 方法都是这样工作的（保留原始状态）。这些方法中的许多都提供了一个可选的 `inplace` 参数，如果将其设置为 `True`，则会修改原始 DataFrame：

点击此处查看代码图片

```
participants.rename(columns={'ages':'Age'}, inplace=True)

participants.columns

Index(['first', 'last', 'Age'], dtype='object')
```

你可以使用索引器语法来创建新列。为此，你只需像该列已存在一样，使用索引器和指定的值来访问它：

点击此处查看代码图片

```
participants['Zip Code'] = [94702, 97402, 94223, 94705,

97503, 94705, 94111, 95333]

participants
```

|  | **first** | **last** | **Age** | **Zip Code** |
| --- | --- | --- | --- | --- |
| **a** | shanda | smith | 43 | 94702 |
| **b** | rolly | brocker | 23 | 97402 |
| **c** | molly | stein | 78 | 94223 |
| **d** | frank | bach | 99 | 94705 |
| **e** | rip | spencer | 26 | 97503 |
| **f** | steven | de wilde | 14 | 94705 |
| **g** | gwen | mason | 46 | 94111 |
| **h** | arthur | davis | 92 | 95333 |

你可以使用列之间的运算（例如字符串拼接）来为新列创建值。如果你决定添加一列包含参与者的全名，你可以从现有的名字和姓氏列中构建这些值：

点击此处查看代码图片

```
participants['Full Name'] = ( participants.loc[:, 'first'] +

participants.loc[:, 'last'] )

participants
```

|  | **first** | **last** | **Age** | **Zip Code** | **Full Name** |
| --- | --- | --- | --- | --- | --- |
| **a** | shanda | smith | 43 | 94702 | shandasmith |
| **b** | rolly | brocker | 23 | 97402 | rollybrocker |
| **c** | molly | stein | 78 | 94223 | mollystein |
| **d** | frank | bach | 99 | 94705 | frankbach |
| **e** | rip | spencer | 26 | 97503 | ripspencer |
| **f** | steven | de wilde | 14 | 94705 | stevende wilde |
| **g** | gwen | mason | 46 | 94111 | gwenmason |
| **h** | arthur | davis | 92 | 95333 | arthurdavis |

你可以使用相同的语法来更新一列。例如，如果你决定全名列中的值应该在名字之间有一个空格，你可以使用相同的列名来分配新值：

点击此处查看代码图片

```
participants['Full Name'] = ( participants.loc[:, 'first'] +

' ' +

participants.loc[:, 'last'] )

participants
```

|  | **first** | **last** | **Age** | **Zip Code** | **Full Name** |
| --- | --- | --- | --- | --- | --- |
| **a** | shanda | smith | 43 | 94702 | shanda smith |
| **b** | rolly | brocker | 23 | 97402 | rolly brocker |
| **c** | molly | stein | 78 | 94223 | molly stein |
| **d** | frank | bach | 99 | 94705 | frank bach |
| **e** | rip | spencer | 26 | 97503 | rip spencer |
| **f** | steven | de wilde | 14 | 94705 | steven de wilde |
| **g** | gwen | mason | 46 | 94111 | gwen mason |
| **h** | arthur | davis | 92 | 95333 | arthur davis |

### 操作数据

Pandas 提供了许多方法来更改 DataFrame 中的数据。你可以使用之前用过的相同索引器来设置值。你可以对整个 DataFrame 或单个列进行操作。你还可以应用函数来更改列中的元素，或从多行或多列创建新值。

要使用索引器更改数据，你需要像选择查看数据一样，选择新数据要存放的位置，然后分配一个新值。例如，要将列 `h` 中的 `arthur` 更改为 `Paul`，你可以使用 `loc`：

点击此处查看代码图片

```
participants.loc['h', 'first'] = 'Paul'

participants
```

|  | **first** | **last** | **Age** | **Zip Code** | **Full Name** |
| --- | --- | --- | --- | --- | --- |
| **a** | shanda | smith | 43 | 94702 | shanda smith |
| **b** | rolly | brocker | 23 | 97402 | rolly brocker |
| **c** | molly | stein | 78 | 94223 | molly stein |
| **d** | frank | bach | 99 | 94705 | frank bach |
| **e** | rip | spencer | 26 | 97503 | rip spencer |
| **f** | steven | de wilde | 14 | 94705 | steven de wilde |
| **g** | gwen | mason | 46 | 94111 | gwen mason |
| **h** | paul | davis | 92 | 95333 | arthur davis |

或者，你可以使用 `iloc` 将行 `c` 中 Molly 的年龄设置为 `99`：

点击此处查看代码图片

```
participants.iloc[3, 2] = 99

participants
```

|  | **first** | **last** | **Age** | **Zip Code** | **Full Name** |
| --- | --- | --- | --- | --- | --- |
| **a** | shanda | smith | 43 | 94702 | shanda smith |
| **b** | rolly | brocker | 23 | 97402 | rolly brocker |
| **c** | molly | stein | 78 | 94223 | molly stein |
| **d** | frank | bach | 99 | 94705 | frank bach |
| **e** | rip | spencer | 26 | 97503 | rip spencer |
| **f** | steven | de wilde | 14 | 94705 | steven de wilde |
| **g** | gwen | mason | 46 | 94111 | gwen mason |
| **h** | paul | davis | 92 | 95333 | arthur davis |

如果你将其视为你之前在列表和字典中使用过的索引赋值的变体，这应该会显得相当直观。

在本章前面，你使用了列之间的运算来为新列构建值。你也可以使用就地运算符，例如 `+=`、`-=` 和 `/=`，来更改列中的值。例如，要将每个参与者的年龄减去 1，你可以使用 `-=` 运算符：

点击此处查看代码图片

```
participants.Age -= 1

participants
```

|  | **first** | **last** | **Age** | **Zip Code** | **Full Name** |
| --- | --- | --- | --- | --- | --- |
| **a** | shanda | smith | 42 | 94702 | shanda smith |
| **b** | rolly | brocker | 22 | 97402 | rolly brocker |
| **c** | molly | stein | 77 | 94223 | molly stein |
| **d** | frank | bach | 98 | 94705 | frank bach |
| **e** | rip | spencer | 25 | 97503 | rip spencer |
| **f** | steven | de wilde | 13 | 94705 | steven de wilde |
| **g** | gwen | mason | 45 | 94111 | gwen mason |
| **h** | paul | davis | 91 | 95333 | arthur davis |



#### `replace` 方法

`replace` 方法用于在 DataFrame 中查找并替换值。例如，你可以用它来将名称 `rolly` 替换为 `Smiley`：

点击此处查看代码图片

```
participants.replace('rolly', 'Smiley')
```

|  | **first** | **last** | **Age** | **Zip Code** | **Full Name** |
| --- | --- | --- | --- | --- | --- |
| **a** | shanda | smith | 42 | 94702 | shanda smith |
| **b** | smiley | brocker | 22 | 97402 | rolly brocker |
| **c** | molly | stein | 77 | 94223 | molly stein |
| **d** | frank | bach | 98 | 94705 | frank bach |
| **e** | rip | spencer | 25 | 97503 | rip spencer |
| **f** | steven | de wilde | 13 | 94705 | steven de wilde |

```
 ...
```

该方法也适用于正则表达式。以下是构建一个正则表达式的方法，该表达式匹配以 `s` 开头的单词，并将 `s` 替换为 `S`：

点击此处查看代码图片

```
participants.replace(r'(s)([a-z]+)', r'S\2', regex=True)
```

|  | **first** | **last** | **Age** | **Zip Code** | **Full Name** |
| --- | --- | --- | --- | --- | --- |
| **a** | shanda | smith | 42 | 94702 | Shanda Smith |
| **b** | rolly | brocker | 22 | 97402 | rolly brocker |
| **c** | molly | stein | 77 | 94223 | molly Stein |
| **d** | frank | bach | 98 | 94705 | frank bach |
| **e** | rip | spencer | 25 | 97503 | rip Spencer |
| **f** | steven | de wilde | 13 | 94705 | Steven de wilde |
| **g** | gwen | mason | 45 | 94111 | gwen mason |
| **h** | paul | davis | 91 | 95333 | arthur davis |

DataFrame 和 `pandas.Series` 对象都有一个 `apply()` 方法，可以对值调用函数。对于 `pandas.Series` 对象，`apply()` 方法会对 `pandas.Series` 对象中的每个值单独调用你选择的函数。

假设你定义了一个函数，用于将传入的任何字符串首字母大写：

点击此处查看代码图片

```
def cap_word(w):

    return w.capitalize()
```

然后，如果你将其作为参数传递给 `first` 列的 `apply()` 方法，它就会将每个名字首字母大写：

点击此处查看代码图片

```
participants.loc[:, 'first'].apply(cap_word)

a    Shanda

b     Rolly

c     Molly

d     Frank

e       Rip

f    Steven

g      Gwen

h      Paul

Name: first, dtype: object
```

对于 DataFrame，`apply` 将一行作为参数，使你能够从该行的列中生成新值。假设你定义了一个使用 `first` 和 `Age` 列值的函数：

点击此处查看代码图片

```
def say_hello(row):

    return f'{row["first"]} is {row["Age"]} years old.'
```

然后你可以将该函数应用于整个 DataFrame：

点击此处查看代码图片

```
participants.apply(say_hello, axis=1)

a    shanda is 42 years old.

b     rolly is 22 years old.

c     molly is 77 years old.

d     frank is 98 years old.

e       rip is 25 years old.

f    steven is 13 years old.

g      gwen is 45 years old.

h      paul is 91 years old.

dtype: object
```

你可以使用此方法跨行或跨列调用函数。使用 `axis` 参数来指示你的函数应处理行还是列。

### 交互式显示

如果你在 Colab 中处理 DataFrame，可以尝试运行以下代码片段：

点击此处查看代码图片

```
%load_ext google.colab.data_table
```

这会使你的 DataFrame 输出变为交互式，并允许你以交互方式筛选和选择。

### 总结

Pandas DataFrame 是在类似电子表格的环境中处理数据的强大工具。你可以从多种来源创建 DataFrame，但从文件创建 DataFrame 是最常见的方式。你可以通过添加新列和新行来扩展 DataFrame。你可以使用强大的索引器访问数据本身，也可以使用它们来设置数据。DataFrame 为探索和操作数据提供了绝佳的方式。

### 问题

使用下表回答以下问题：

| **样本量 (mg)** | **%P** | **%Q** |
| --- | --- | --- |
| 0.24 | 40 | 60 |
| 2.34 | 34 | 66 |
| 0.0234 | 12 | 88 |

1.   创建一个表示此表的 DataFrame。

2.   添加一个名为“Total Q”的新列，其中包含每个样本中 Q 的含量（以 mg 为单位）。

3.   将 %P 和 %Q 列除以 100。

