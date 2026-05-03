# 2. 使用 Pandas 进行数据整理



## 引言

数据整理是将来自不同数据源的数据集进行转换、操作和准备的过程。数据清洗和数据操作是描述同一过程的其他术语。在本章中，我们将介绍最古老的 Python 数据分析库 Pandas。我由衷地认为，Pandas 库在 Python 语言被广泛用于数据分析和操作任务方面发挥了重要作用。在本章中，我们将探讨 Pandas 2.0（Pandas 的一个主要版本），研究其数据结构、处理缺失值、执行数据转换、组合多个数据对象以及其他相关主题。

学完本章，你将了解：

*   Pandas 2.0 的核心数据结构：Series 和 DataFrame，以及它们如何在数据整理中使用
*   用于访问和切片 Pandas 数据结构的各种索引技术
*   如何有效处理缺失值，并通过重塑、数据透视和数据聚合来执行数据转换
*   如何使用合并、连接和拼接操作符组合多个 Pandas 对象

Pandas 是一个为数据操作和转换而构建的 Python 库。Pandas 构建在 NumPy 之上，NumPy 是另一个用于科学和数值计算的 Python 库。

Pandas 主要由用户社区驱动，是开源的，并且可以免费修改、分发和用于商业目的。Pandas 诞生于 2011 年，在过去十年中经历了重大演变，最近发布了一个主要版本，其代码库经过重写，功能得到增强，并且能够与其他库良好交互。Pandas 库每月下载量超过 1 亿次，是数据操作和转换的事实标准（来源：[`http://library.io`](http://library.io/)）。

即使不在生产环境中使用 Pandas 和 NumPy，了解其数据结构和方法也肯定有助于熟悉其他数据操作库。Pandas 为许多其他 Python 库设定了标准。

## 数据结构

Pandas 主要提供两种数据结构：Series 和 DataFrame。Series 是一个 n 维数组，而 DataFrame 是一个二维的带标签数据结构，支持类似电子表格或 SQL 表输出的表格结构。

### Series

Series 数据结构允许处理带标签和不带标签的数据数组。数据可以是整数、浮点数、文本字符串或任何其他 Python 对象。

Pandas 库提供的方法被称为对象。要调用 Series 对象，需要初始化其构造函数并提供适当的参数。一个空的初始化如下所示：

```
pd.Series()
```

以下是一个简单的 Series 初始化示例：

```
a = [1,2,3,4,5]
b = pd.Series(data=np.random.rand(5), index=a)
```

也可以使用 Python 字典生成 Series。示例如下：

```
simple_dict1 = {
"library" : "pandas",
"version" : 2.0,
"year" : 2023
}
simple_series = pd.Series(simple_dict1)
```

还可以获取和设置给定 Series 中的值。以下是获取值的方法：

```
simple_series["year"]
```

我们将得到：

```

```

要修改 Series 中的值，可以执行以下操作：

```
simple_series["year"] = 2024
```

我们将得到：

```
simple_series
library    pandas
version       2.0
year         2024
dtype: object
```

在此输出中，`dtype` 是 NumPy 的数据类型对象，它包含了数据的类型，在此例中为对象。

### DataFrame

DataFrame 是 Pandas 中最常用的数据结构。Pandas DataFrame 是一个二维的结构化对象数组，可以包含整数、浮点数、文本字符串或其他 Python 对象（Series、另一个 DataFrame 等）。

在编写生产代码时，可能会从以不同格式存储的外部数据集生成 DataFrame。然而，理解 DataFrame 的关键属性至关重要。其中一些属性如下：

*   **Data**：数据点或实际数据。它可以是一个或多个 Series 或字典数据类型。数据可能包含整数、浮点数、字符串或任何其他 Python 对象。
*   **Index**：数据点的行标签以及 DataFrame 本身的索引。
*   **Columns**：数据点的列标签。
*   **Dtype**：DataFrame 的数据类型。

一个简单的初始化如下所示：

```
data = {
"i": [121, 144, 169],
"j": [11, 12, 13]
}
df_data = pd.DataFrame(data=data, index=["a","b","c"])
print(df_data)
i   j
a  121  11
b  144  12
c  169  13
```

## 索引

索引简单来说就是 DataFrame 中的行引用。索引有助于标识 Pandas 数据结构。也可以说，索引是一种引用指针的方式，该指针标识 Pandas DataFrame 中的特定行或行子集。这是一种提高数据检索性能的方法。



### 基本索引方法

让我们来看一个在 Pandas 数据框中进行索引的示例。首先，我们将加载数据集并重命名某些冗长的列：

```
df = pd.read_csv(
"/your-path-to-the-file/winequality/winequality-red.csv"
,sep=";"
,usecols=['residual sugar', 'chlorides', 'total sulfur dioxide', 'density', 'pH', 'alcohol', 'quality']
)
df = df.rename({
'residual sugar': 'rSugar',
'total sulfur dioxide': 'total_so2'
},
axis=1
)
```

以下是从数据框中返回单行的方法：

```
print(df.iloc[5])
```

将输出

```
rSugar        1.8000
chlorides     0.0750
total_so2    40.0000
density       0.9978
pH            3.5100
alcohol       9.4000
quality       5.0000
Name: 5, dtype: float64
```

通过将列表作为参数传递，可以按以下方式返回多行：

```
print(df.loc[[5,9,22]])
```

将输出

```
rSugar  chlorides  total_so2  density    pH  alcohol  quality
5      1.8      0.075       40.0   0.9978  3.51      9.4        5
9      6.1      0.071      102.0   0.9978  3.35     10.5        5
22     1.6      0.106       37.0   0.9966  3.17      9.5        5
```

也可以按以下方式将列表作为参数传递：

```
pinot_noir = [1,4,7,22,39]
df.iloc[pinot_noir]
```

将输出

```
rSugar  chlorides  total_so2  density    pH  alcohol  quality
1      2.6      0.098       67.0   0.9968  3.20      9.8        5
4      1.9      0.076       34.0   0.9978  3.51      9.4        5
7      1.2      0.065       21.0   0.9946  3.39     10.0        7
22     1.6      0.106       37.0   0.9966  3.17      9.5        5
39     5.9      0.074       87.0   0.9978  3.33     10.5        5
```

此外，还可以按以下方式将范围作为参数传递：

```
df.loc[5:25]
```

将输出

```
rSugar  chlorides  total_so2  density    pH  alcohol  quality
5      1.8      0.075       40.0   0.9978  3.51      9.4        5
6      1.6      0.069       59.0   0.9964  3.30      9.4        5
7      1.2      0.065       21.0   0.9946  3.39     10.0        7
8      2.0      0.073       18.0   0.9968  3.36      9.5        7
9      6.1      0.071      102.0   0.9978  3.35     10.5        5
10     1.8      0.097       65.0   0.9959  3.28      9.2        5
11     6.1      0.071      102.0   0.9978  3.35     10.5        5
12     1.6      0.089       59.0   0.9943  3.58      9.9        5
13     1.6      0.114       29.0   0.9974  3.26      9.1        5
14     3.8      0.176      145.0   0.9986  3.16      9.2        5
15     3.9      0.170      148.0   0.9986  3.17      9.2        5
16     1.8      0.092      103.0   0.9969  3.30     10.5        7
17     1.7      0.368       56.0   0.9968  3.11      9.3        5
18     4.4      0.086       29.0   0.9974  3.38      9.0        4
19     1.8      0.341       56.0   0.9969  3.04      9.2        6
20     1.8      0.077       60.0   0.9968  3.39      9.4        6
21     2.3      0.082       71.0   0.9982  3.52      9.7        5
22     1.6      0.106       37.0   0.9966  3.17      9.5        5
23     2.3      0.084       67.0   0.9968  3.17      9.4        5
24     2.4      0.085       40.0   0.9968  3.43      9.7        6
25     1.4      0.080       23.0   0.9955  3.34      9.3        5
```

条件过滤看起来像下面这样：

```
print(df.loc[df["quality"] > 7])
```

将输出

```
rSugar  chlorides  total_so2  density    pH  alcohol  quality
267      3.6      0.078       37.0  0.99730  3.35     12.8        8
278      6.4      0.073       13.0  0.99760  3.23     12.6        8
390      1.4      0.045       88.0  0.99240  3.56     12.9        8
440      2.2      0.072       29.0  0.99870  2.88      9.8        8
455      5.2      0.086       19.0  0.99880  3.22     13.4        8
481      2.8      0.080       17.0  0.99640  3.15     11.7        8
495      2.6      0.070       16.0  0.99720  3.15     11.0        8
498      2.6      0.070       16.0  0.99720  3.15     11.0        8
588      2.0      0.060       50.0  0.99170  3.72     14.0        8
828      2.3      0.065       45.0  0.99417  3.46     12.7        8
1061     1.8      0.071       16.0  0.99462  3.21     12.5        8
1090     1.9      0.083       74.0  0.99451  2.98     11.8        8
1120     2.5      0.076       17.0  0.99235  3.20     13.1        8
1202     1.8      0.068       12.0  0.99516  3.35     11.7        8
1269     1.8      0.044       87.0  0.99080  3.50     14.0        8
1403     1.7      0.061       13.0  0.99600  3.23     10.0        8
1449     2.0      0.056       29.0  0.99472  3.23     11.3        8
1549     1.8      0.074       24.0  0.99419  3.24     11.4        8
```

在数据框内部，也可以使用“query”方法进行条件索引。示例如下：

```
hq_wine = df.loc[df["quality"] > 7]
print(hq_wine.query('(rSugar < pH)'))
```

将输出

```
rSugar  chlorides  total_so2  density    pH  alcohol  quality
390      1.4      0.045       88.0  0.99240  3.56     12.9        8
440      2.2      0.072       29.0  0.99870  2.88      9.8        8
481      2.8      0.080       17.0  0.99640  3.15     11.7        8
495      2.6      0.070       16.0  0.99720  3.15     11.0        8
498      2.6      0.070       16.0  0.99720  3.15     11.0        8
588      2.0      0.060       50.0  0.99170  3.72     14.0        8
828      2.3      0.065       45.0  0.99417  3.46     12.7        8
1061     1.8      0.071       16.0  0.99462  3.21     12.5        8
1090     1.9      0.083       74.0  0.99451  2.98     11.8        8
1120     2.5      0.076       17.0  0.99235  3.20     13.1        8
1202     1.8      0.068       12.0  0.99516  3.35     11.7        8
1269     1.8      0.044       87.0  0.99080  3.50     14.0        8
1403     1.7      0.061       13.0  0.99600  3.23     10.0        8
1449     2.0      0.056       29.0  0.99472  3.23     11.3        8
1549     1.8      0.074       24.0  0.99419  3.24     11.4        8
```

可以使用 `.loc` 和 `.iloc` 方法来检索数据框的特定行。`.iloc` 方法是一种位置索引，它通过引用基于整数的位置来选择位置，从而尝试访问行和列；而 `.loc` 是一种基于标签的索引，通过标签来访问行和列。

假设我们有一个索引顺序不固定的数据框，例如：

```
idx_series = pd.Series(["peach", "caramel", "apples", "melon", "orange", "grapes"], index=[74, 75, 76, 1, 2, 3])
```

这将生成一个如下的 Pandas 序列：

```
74      peach
75    caramel
76     apples
1       melon
2      orange
3      grapes
dtype: object
```

当使用 loc 方法时，Pandas 会尝试定位作为参数传递的标签。这里，标签“1”对应的值是 melon：

```
idx_series.loc[1]
Out[107]: 'melon'
```

相比之下，iloc 方法会尝试定位作为参数传递的整数位置。整数引用从 0 开始，到列表长度减 1 结束。因此，第一行将返回 caramel：

```
idx_series.iloc[1]
Out[108]: 'caramel'
```



### 多重索引

多重索引允许通过引用多个索引，从行和列中同时选择数值。可以使用适当的方法从数组、元组和数据框中创建多重索引。最简单的方法是使用数组定义多重索引，然后直接将其传入数据框或序列。Pandas 会自动将其整合到其数据结构中。示例如下：

首先，我们来创建多重索引：

```
import numpy as np
multi_index_arr = [
np.array(
['chunk1','chunk1','chunk1','chunk1','chunk1',
'chunk2','chunk2','chunk2','chunk2','chunk2']
),
np.array(
['a','b','c','d','e',
'f','g','h','i','j']
)
]
```

现在将这个数组传入数据框。为了方便，这里初始化了一个包含随机数的数据框：

```
df = pd.DataFrame(
np.random.randn(10,5),
index=multi_index_arr
)
print(df)
```

以下是该数据框的结果：

```
0         1         2         3         4
chunk1 a  0.681923 -0.298217  1.269076  0.202422  0.419290
b -1.397923 -1.308753  1.624954 -0.968342 -2.025347
c  0.003117 -0.677547 -0.262260  0.684654  1.064416
d -0.811899  1.571434  0.169619  0.424022  0.108066
e -0.366923  0.365224 -0.173412 -1.852635  0.019401
chunk2 f  1.102141  1.069164 -0.446039 -1.744530  1.069642
g  1.335156  1.166403 -0.880927  0.031362  0.268140
h  0.381478 -2.620881 -0.669324 -0.892250  2.069332
i -0.063832 -1.076992 -0.944367  0.616954  1.183344
j -0.110984  0.858179 -0.899983 -0.829470 -1.078892
```

这也可以扩展到列：

让我们定义列索引：

```
multi_index_col = [
np.array(
['chunk1a','chunk1a','chunk1a',
'chunk2a','chunk2a','chunk2a']
),
np.array(
['k','l','m',
'n','o','p']
)
]
```

现在将行和列的多重索引都引用到数据框。为简单起见，我们初始化一个包含随机数的数据框：

```
df = pd.DataFrame(
np.random.randn(10,6),
index=multi_index_arr,
columns=multi_index_col
)
print(df)
```

结果如下所示：

```
chunk1a                       chunk2a
k         l         m         n         o         p
chunk1 a  0.933805  1.117525 -1.159911 -1.503417  1.863563  0.179210
b -1.690026  0.995953 -0.763078  1.580060  1.132970 -0.930042
c -0.325951 -0.148638 -1.481013  1.211841 -1.341596  0.582081
d -0.661698  0.838978 -0.734689  0.341315 -1.776477 -1.236906
e  0.460951 -2.022154 -0.236631  1.863633  0.466077 -1.603421
chunk2 f -0.122058  0.141682  1.123913  0.300724 -2.104929 -1.036110
g -0.332314  0.000606  0.239429  0.869499 -0.482440 -1.219407
h -1.035517 -0.698888  2.486068 -1.379440 -0.503729  1.170276
i -0.213047  2.714762  0.463097 -1.864837 -0.603789  0.351523
j -0.346246  0.720362  1.328618 -0.271097  1.034155  0.665097
```

因此，如果我们想检索一个子集，可以这样做：

```
print(df.loc['chunk1']['chunk1a'])
```

将得到

```
k         l         m
a  0.933805  1.117525 -1.159911
b -1.690026  0.995953 -0.763078
c -0.325951 -0.148638 -1.481013
d -0.661698  0.838978 -0.734689
e  0.460951 -2.022154 -0.236631
```

以下是访问单个元素的方法：

```
print(df.loc['chunk1','a']['chunk2a','p'])
```

将得到

```
0.1792095
```

### 时间差索引

时间差可以定义为给定输入之间经过的时间差。它们以时间单位表示，即秒、分钟、天等。

以下是创建时间差索引的示例：

```
timedelta_index = pd.timedelta_range(start='0 minutes', periods=50, freq='36S')
```

将得到

```
TimedeltaIndex(['0 days 00:00:00', '0 days 00:00:36', '0 days 00:01:12',
'0 days 00:01:48', '0 days 00:02:24', '0 days 00:03:00',
'0 days 00:03:36', '0 days 00:04:12', '0 days 00:04:48',
'0 days 00:05:24', '0 days 00:06:00', '0 days 00:06:36',
'0 days 00:07:12', '0 days 00:07:48', '0 days 00:08:24',
'0 days 00:09:00', '0 days 00:09:36', '0 days 00:10:12',
'0 days 00:10:48', '0 days 00:11:24', '0 days 00:12:00',
'0 days 00:12:36', '0 days 00:13:12', '0 days 00:13:48',
'0 days 00:14:24', '0 days 00:15:00', '0 days 00:15:36',
'0 days 00:16:12', '0 days 00:16:48', '0 days 00:17:24',
'0 days 00:18:00', '0 days 00:18:36', '0 days 00:19:12',
'0 days 00:19:48', '0 days 00:20:24', '0 days 00:21:00',
'0 days 00:21:36', '0 days 00:22:12', '0 days 00:22:48',
'0 days 00:23:24', '0 days 00:24:00', '0 days 00:24:36',
'0 days 00:25:12', '0 days 00:25:48', '0 days 00:26:24',
'0 days 00:27:00', '0 days 00:27:36', '0 days 00:28:12',
'0 days 00:28:48', '0 days 00:29:24'],
dtype='timedelta64[ns]', freq='36S')
```

让我们用新创建的时间差索引初始化一个序列。

代码可能如下所示：

```
series_a = pd.Series(np.arange(50), index=timedelta_index)
```

将得到

```
0 days 00:00:00     0
0 days 00:00:36     1
0 days 00:01:12     2
0 days 00:01:48     3
0 days 00:02:24     4
0 days 00:03:00     5
0 days 00:03:36     6
0 days 00:04:12     7
0 days 00:04:48     8
0 days 00:05:24     9
0 days 00:06:00    10
0 days 00:06:36    11
0 days 00:07:12    12
0 days 00:07:48    13
0 days 00:08:24    14
0 days 00:09:00    15
0 days 00:09:36    16
0 days 00:10:12    17
0 days 00:10:48    18
0 days 00:11:24    19
0 days 00:12:00    20
0 days 00:12:36    21
0 days 00:13:12    22
0 days 00:13:48    23
0 days 00:14:24    24
0 days 00:15:00    25
0 days 00:15:36    26
0 days 00:16:12    27
0 days 00:16:48    28
0 days 00:17:24    29
0 days 00:18:00    30
0 days 00:18:36    31
0 days 00:19:12    32
0 days 00:19:48    33
0 days 00:20:24    34
0 days 00:21:00    35
0 days 00:21:36    36
0 days 00:22:12    37
0 days 00:22:48    38
0 days 00:23:24    39
0 days 00:24:00    40
0 days 00:24:36    41
0 days 00:25:12    42
0 days 00:25:48    43
0 days 00:26:24    44
0 days 00:27:00    45
0 days 00:27:36    46
0 days 00:28:12    47
0 days 00:28:48    48
0 days 00:29:24    49
Freq: 36S, dtype: int64
```

以下是检索单个值的方法：

```
series_a['0 days 00:27:36']
```

将得到

```
46
```

以下是查看时间范围内数据子集的方法：

```
s['0 days 00:12:30':'0 days 00:27:30']
```

将得到

```
0 days 00:12:36    21
0 days 00:13:12    22
0 days 00:13:48    23
0 days 00:14:24    24
0 days 00:15:00    25
0 days 00:15:36    26
0 days 00:16:12    27
0 days 00:16:48    28
0 days 00:17:24    29
0 days 00:18:00    30
0 days 00:18:36    31
0 days 00:19:12    32
0 days 00:19:48    33
0 days 00:20:24    34
0 days 00:21:00    35
0 days 00:21:36    36
0 days 00:22:12    37
0 days 00:22:48    38
0 days 00:23:24    39
0 days 00:24:00    40
0 days 00:24:36    41
0 days 00:25:12    42
0 days 00:25:48    43
0 days 00:26:24    44
0 days 00:27:00    45
Freq: 36S, dtype: int64
```

虽然最简单的索引形式是引用特定行，但 Pandas 库提供了更复杂的索引，例如多重索引、时间差索引、周期索引和分类索引。

## 数据提取与加载

Pandas 的强大功能之一是其能够以多种格式导入和导出数据。这也是介绍各种数据格式的好时机。数据格式和文件格式已经发展到满足大数据日益增长的需求。当前估计表明，全球每月以各种格式创建、消费和复制约 10 ZB 的数据。



### CSV

CSV 代表逗号分隔值（comma-separated values）；CSV 格式是最古老、最常见的数据存储类型之一。它也被称为表格存储，数据以行和列的形式存储。

数据存储在文本文件中。文件的每一行对应一条记录。顾名思义，数据点之间可以用逗号分隔。每行的数据点数量相同。通常，第一行包含列定义，也称为表头或元数据。CSV 数据文件以其简单性和快速解析能力而闻名。

以下是如何导入 CSV 格式的数据：

```
import pandas as pd
new_df = pd.read_csv("path to the csv file", usecols = ["col1", "col4", "col7",...]
```

其中 `usecols` 参数有助于仅选择项目所需的列。

以下是导出数据的方法：

```
final_df = pd.to_csv(index=False)
```

以下是创建压缩 CSV 文件的方法：

```
final_df = pd.to_csv('final_output.zip', index=False, compression=compression_opts)
```

### JSON

JSON 代表 JavaScript 对象表示法（JavaScript Object Notation）。它更常用于在应用程序（Web 应用、移动应用等）和 Web 服务器之间传输数据。此外，它是 API 进行请求和响应通信的事实标准数据格式。

数据以键值对的形式存储，并以嵌套结构组织。JSON 支持多种数据类型，例如整数、字符串、数组和布尔值等。

以下是如何使用 Pandas 导入 JSON 文档：

```
json_df = pd.read_json('path to file.json')
```

以下是导出方法：

```
final_df.to_json('path to file.json')
```

### HDF5

HDF5 代表分层数据格式，第 5 版（Hierarchical Data Format, version 5）。与 CSV 和 JSON 不同，HDF5 是一种压缩格式，其数据针对高效存储进行了优化，并提供更快的读写操作。

在处理 HDF5 中的海量数据时，可以尝试仅访问大数据集的子集，而无需将整个数据集加载到内存中。因此，HDF5 对物理内存非常友好。

HDF5 支持大型且复杂的异构数据。顾名思义，HDF5 数据格式具有分层结构。该结构包含一个或多个组，每个组可以包含各种类型的数据及其元数据：

```
new_df = pd.read_hdf('path to file.h5', key = 'the key to specific dataset')
print(new_df)
```

如果整个文件只包含一个数据帧，则可以忽略 `key` 参数。

以下是将数据帧写入现有 HDF5 存储的方法：

```
exportstore = pd.HDFStore("an_existing_store.h5", "w")
exportstore.put('new data', new_df)
print(exportstore.keys())
```

### Feather

Feather 最初由 Apache Arrow 开发，用于更快的数据交换，是一种专为数据科学和分析而设计的快速高效的数据格式。要使用 Feather，需要先安装 PyArrow 和 Feather-format Python 包作为使用 Feather 数据格式的先决条件。

以下是读取 Feather 文件的示例：

```
import pandas as pd
import numpy as np
feather_df = pd.read_feather('location to your file.feather', use_threads=True)
```

这里，使用 `use_threads` 是为了利用多线程实现文件读取操作的并行化。

以下是将数据帧导出到 Feather 的方法：

```
final_df.to_feather('location to destination file.feather')
```

### Parquet

Parquet 由 Apache Parquet 项目开发，是一种开源的列式文件格式。Parquet 专为大数据处理、数据仓库和分析应用而设计。Parquet 支持跨语言，并支持多种压缩算法以进行数据交换和传输。Parquet 性能更优，对于给定的请求或查询，只需读取大数据集的一个子集。Parquet 被认为是读取繁重数据管道的不错格式。

示例如下：

```
import pandas as pd
import pyarrow
new_df = pd.read_parquet("location to file.parquet", engine='pyarrow')
print(df)
```

另一个使用 fastparquet 作为加载引擎的示例：

```
import pandas as pd
import fastparquet
new_df = pd.read_parquet("location to file.parquet", engine='fastparquet')
print(df)
```

以下是将数据帧导出到 Parquet 的方法：

```
final_df.to_parquet('location to the destination file.parquet', 'compression=snappy')
```

有多种压缩类型可供选择，例如 snappy、gzip、lz4、zstd 等。这些只是试图减小文件大小的不同算法。

### ORC

ORC 代表优化行列式（Optimized Row Columnar）。它被认为比平面文件格式更高效。ORC 是一种列式文件格式，其中列值彼此相邻存储。ORC 文件格式支持多种压缩算法。每个 ORC 文件包含一个或多个数据条带。每个条带包含一个头部、按行存储的数据以及一个尾部。尾部包含给定条带中存储的数据点的元数据和汇总统计信息。ORC 被认为是写入繁重数据管道的不错格式：

```
import pandas as pd
new_df = pd.read_orc("location to your file.orc", columns=["col1", "col5",...])
final_df.to_orc("location to destination file.orc", engine='pyarrow')
```

### Avro

Avro 是一种基于行的文件格式。可以认为 Avro 是作为 Apache Hadoop 的一部分为大数据处理而发展起来的。Avro 模式使用 JSON 定义，并支持二进制编码以实现更快的访问和高效的存储。二进制编码是一种序列化和反序列化数据的过程，用于高效地处理和存储大量数据。Avro 还提供容器文件，这是一种特殊的文件，内部包含多个数据，能够将不同类型的数据（图像、音频、文本等）存储在一起。

Pandas 本身不支持处理 Avro 文件；但是，可以使用第三方库来加载 Avro 数据。示例如下：

```
import fastavro
import pandas as pd
with open('location to Avro file', 'rb') as readAvro:
avro_read = fastavro.reader(readAvro)
avro_data = list(avro_read)
new_df = pd.Dataframe(avro_data)
print(new_df)
```

### Pickle

就像可以序列化和反序列化数据以实现高效存储一样，Python pickle 能够序列化和反序列化 Python 对象。当说一个 Python 对象被 pickled 时，pickle 模块会在将其写入文件之前序列化该 Python 对象。当说它被 unpickled 时，文件会被反序列化，从而重建回其原始形式。与其他文件格式不同，pickle 仅限于 Python 语言。

以下是将数据帧导出到 pickle 的方法：

```
pd.to_pickel(given_data_frame, "location to file.pkl")
```

以下是将 pickle 读取到数据帧的方法：

```
unpkl_df = pd.read_pickle("location to file.pkl")
```

### 分块加载

分块加载是指以相对较小的块大小导入整个大型数据集，而不是尝试一次性加载整个数据集。通过利用分块加载的概念，可以处理多个大型数据集（大于内存的数据集）而不会遇到内存问题。以下是利用分块加载的一种方法：

```
import pandas as pd
df = pd.DataFrame()
partition: int = 5000
# specifying partition chunks as 5000 rows
# will load every 5000 rows at every iteration
file = "/location-to-folder/filepath.csv"
for chunk in pd.read_csv(file, chunksize=partition):
df = pd.concat(chunk,ignore_index=True)
```

## 缺失值



### 背景

在数据分析与数据处理任务中，缺失数据是一种常见现象。当某个事件或观测中的数据点没有可用值时，就会出现缺失数据或缺失值。你可能会在数据集的某些位置看到没有填充值的空白区域。缺失值的产生有多种原因。

最常见的想法是数据录入阶段可能出现的错误。在手动数据录入过程中，忘记输入某个值的情况并不少见；另一种情况是，数据值被错误输入，在质量检查时这些数据值被移除，因此某个观测值和变量就被标记为缺失值。

此类数据录入错误也可能发生在自动化机器或传感器机制中。在某些情况下，执行读取的设备可能正在维护中，或存在其他问题，导致在特定时间点无法读取数值，从而产生缺失值。

数据的常见来源之一是调查，无论是面对面进行还是在线进行。调查并不容易；受访者可能选择不回答问题，或者提供与平时回答不同的值（例如，在 1-5 分的调查量表中给出极端回答）。此外，给定调查中各个数据点之间的关系也很重要。有时，一个问题的缺失值可能导致后续多个问题的缺失值。例如，假设我们有一项关于机动车保险的调查，而受访者没有机动车；这可能导致跳过大部分问题。

### 数据管道中的缺失值

缺失值也可能发生在数据清洗和传输过程中。当对数据集应用过滤器或条件转换时，此类操作的输出可能导致缺失值，表明这些特定变量不符合过滤条件。同样，当数据从源系统迁移或传输到分析系统的暂存层时，由于数据格式不一致或缺乏对特定编码类型的支持，也可能出现缺失值。

例如，如果源系统使用 ISO-8859 编码，而目标系统使用 UTF-8 编码，那么在迁移过程中字符可能会被错误解释；在某些字段中，数据可能被截断为特定字符或直接切断，导致出现缺失值或毫无意义的数据。

在 Python 编程中，有几种表示数据集中缺失值的方式。让我们详细了解一下。

#### None

None 是数据分析中表示缺失值的常见方式。None 有自己的数据类型，称为“NoneType”。None 特指完全没有值，这与整数取值为 0 不同；None 也不等同于空字符串。

#### NaN

NaN 代表非数字（Not a Number）。非数字或 NaN 表示在给定位置的值缺失。NaN 是浮点类型。下面是一个示例。

让我们创建一个数据框：

```
df = pd.DataFrame(np.random.randn(2,3), index=[1,2,3,4,5], columns=["a","b","c"])
```

数据框看起来像这样：

```
a         b         c
1  1.720131 -0.623429  0.196984
2 -1.093708 -0.190980 -1.063972
```

现在让我们重新索引该数据框：

```
df.reindex([1,2,3,4,5])
```

将得到：

```
a         b         c
1  1.720131 -0.623429  0.196984
2 -1.093708 -0.190980 -1.063972
3       NaN       NaN       NaN
4       NaN       NaN       NaN
5       NaN       NaN       NaN
```

#### NaT

NaT 代表非时间（Not a Time）。NaT 表示 DateTime 数据类型的缺失值。

#### NA

NA 代表不可用（Not Available）。在 Pandas 库中，NA 历来被用来表示所有数据类型的缺失值。与其为不同数据类型使用 None、NaN 或 NaT，不如使用 NA 来表示整数、布尔值和字符串数据类型中的缺失值。

### 处理缺失值

Pandas 库提供了几种处理缺失值的方法和手段。让我们看看其中一些。

#### isna() 方法

Pandas 的 isna() 方法用于返回一个布尔值，指示数据中是否存在缺失值。如果数据框中每个元素存在缺失值，该方法返回 true；如果没有缺失值，则返回 false。它适用于 NA 和 NaN 类型。

当我们对前面的示例使用此方法时：

```
df2.isna()
```

将得到：

```
a      b      c
1  False  False  False
2  False  False  False
3   True   True   True
4   True   True   True
5   True   True   True
```

注意

你也可以使用“`isnull()`”代替“`isna()`”以获得更好的可读性。

#### notna() 方法

notna() 可以看作是 isna() 方法的逆操作。此方法不是识别缺失值，而是检测非缺失值；因此，如果值未缺失，该方法返回 true；如果值确实缺失，则返回 false。以下是对同一示例的应用效果：

```
df2.notna()
```

将返回：

```
a      b      c
1   True   True   True
2   True   True   True
3  False  False  False
4  False  False  False
5  False  False  False
```

#### 何时使用哪种方法？

isna() 方法识别缺失值，而 notna() 方法识别非缺失值。

我的看法是，如果你要处理缺失数据、分析缺失数据并执行统计插补，那么我建议使用 isna() 方法。你可以分别使用 sum() 和 count() 方法来求和和计数缺失值的数量。

效果如下：

```
df2.isna().sum()
df2.isna().count()
df2["b"].isna().sum()
```

如果你希望完全忽略缺失值行，只需要没有缺失值的数据点，那么我建议使用 notna() 方法：

```
fillna() with values
fillna(0)
interpolate()
```

注意

当你完全确定存在可以轻松推测的笔误时，请使用带有迭代插补器（以及简单插补器）的多变量插补：[`https://scikit-learn.org/stable/modules/impute.html`](https://scikit-learn.org/stable/modules/impute.html)。

例如，一项研究包含特定年龄范围内的测试对象，同时勾选了一些可以通过合理估计回溯的其他选项。

## 数据转换

数据转换是为机器学习管道构建高质量数据集时最关键的操作之一。广义上讲，它可以被视为将数据从一种形态转换为另一种形态的过程。数据转换的目标是为下游消费准备数据或创建数据集。



### 数据探索

当开始一个新项目或新任务时，重要的是要有个起点。第一步往往至关重要。因此，第一步的目标应尽可能接近了解数据集。幸运的是，Pandas 为数据科学家和从业者提供了一些统计工具。让我们详细看看其中一些工具。

以下是一个用于说明的数据集：

```
data = {'product': ['Apple', 'Orange', 'Melon', 'Peach', 'Pineapple'],
'price': [22, 27, 25, 29, 35],
'quantity': [5, 7, 4, 9, 3]
}
```

让我们将其转换为一个数据框：

```
df = pd.DataFrame(data)
```

现在

```
df['price'].describe()
```

将输出

```
count     5.000000
mean     27.600000
std       4.878524
min      22.000000
25%      25.000000
50%      27.000000
75%      29.000000
max      35.000000
Name: price, dtype: float64
```

对于产品变量

```
df['product'].describe()
```

将输出

```
count         5
unique        5
top       Apple
freq          1
Name: product, dtype: object
```

也可以获取单个值用于转换目的。以下是一些示例。

计算水果的平均价格

```
df['price'].mean()
```

将输出

```
27.6
```

这是另一个获取唯一产品集合的示例：

```
df['product'].unique()
```

将输出

```
Out[17]: array(['Apple', 'Orange', 'Melon', 'Peach', 'Pineapple'], dtype=object)
```

即时创建一个新变量

```
df['total_cost'] = df['price']*df['quantity']
```

将输出

```
product  price  quantity  total_cost
0      Apple     22         5         110
1     Orange     27         7         189
2      Melon     25         4         100
3      Peach     29         9         261
4  Pineapple     35         3         105
```

让我们添加另一列，包含销售日期。这些日期是随机生成的。我们的数据集如下所示：

```
product  price  quantity  total_cost                  sale_date
0      Apple     22         5         110 2023-12-09 22:59:06.997287
1     Orange     27         7         189 2024-01-03 11:03:12.484349
2      Melon     25         4         100 2023-12-10 01:59:22.598255
3      Peach     29         9         261 2023-12-21 05:28:01.462895
4  Pineapple     35         3         105 2023-12-17 23:38:42.783745
```

对 sale_date 运行 describe 函数，我们得到

```
df['sale_date'].describe()
count                                5
mean     2023-12-18 17:49:41.265306112
min         2023-12-09 22:59:06.997287
25%      2023-12-10 01:59:22.598255104
50%      2023-12-17 23:38:42.783745024
75%      2023-12-21 05:28:01.462895104
max         2024-01-03 11:03:12.484349
Name: sale_date, dtype: object
```

此外，还可以使用 sort_values() 方法对数据集进行排序：

```
df.sort_values('price')
```

将输出

```
product  price  quantity  total_cost                  sale_date
0      Apple     22         5         110 2023-12-09 22:59:06.997287
2      Melon     25         4         100 2023-12-10 01:59:22.598255
1     Orange     27         7         189 2024-01-03 11:03:12.484349
3      Peach     29         9         261 2023-12-21 05:28:01.462895
4  Pineapple     35         3         105 2023-12-17 23:38:42.783745
```

### 组合多个 Pandas 对象

Pandas 库提供了多种方法和策略来组合一个或多个数据结构，无论是序列还是数据框。不仅如此，Pandas 还提供了比较两个相同数据结构的数据集以了解它们之间差异的能力。

需要了解的最重要的功能之一是 Pandas 库提供的 merge 方法。merge() 方法提供了类似关系数据库的连接操作。这些由 merge() 方法提供的连接是在内存中执行的。

merge() 方法提供了左连接、右连接、内连接、外连接和交叉连接选项。

让我们详细了解一下它们。为了便于说明，我们假设严格关注具有行和列的数据框。

#### 左连接

给定两个数据框，对它们进行左连接意味着保留左侧数据框中的所有内容，并添加来自右侧数据框的任何匹配信息。假设我们有两个数据框，分别是 a 和 b。该过程首先保留数据框 a 中的所有内容，然后从数据框 b 中寻找可能的匹配。匹配通常基于数据框中的公共列或键进行搜索。当找到匹配时，将匹配的列从 b 中取出并与 a 合并。如果存在匹配的行但信息仅部分填充，则剩余部分将填充为 NA。

具体如下所示。让我们初始化两个数据框：

```
left_df1 = pd.DataFrame({
'ID': [1, 2, 3, 4, 5],
'Name': ['Alice', 'Bob', 'Charlie', 'David', 'John'],
'DeptID': [101, 102, 103, 104, 103]
})
```

将输出

```
ID     Name  DeptID
0   1    Alice     101
1   2      Bob     102
2   3  Charlie     103
3   4    David     104
4   5     John     103
right_df1 = pd.DataFrame({
'DeptID': [101, 102, 103],
'DeptName': ['HR', 'IT', 'Marketing']
})
```

将输出

```
DeptID   DeptName
0     101         HR
1     102         IT
2     103  Marketing
```

#### 使用 merge() 进行左连接

现在让我们使用 merge() 方法执行左连接：

```
leftjoin = pd.merge(
left_df1,
right_df1,
on="DeptID",
how="left"
)
print(leftjoin)
```

将输出

```
ID     Name  DeptID   DeptName
0   1    Alice     101         HR
1   2      Bob     102         IT
2   3  Charlie     103  Marketing
3   4    David     104        NaN
4   5     John     103  Marketing
```

在前面的示例中，您可以看到 left_df 中的所有内容都被保留，而只有来自 right_df 的匹配列被合并。left_df 中有一行在 right_df 中没有匹配的行，该行被填充为 NaN，作为缺失值。

#### 右连接

右连接可以看作是左连接的逆操作。

给定两个数据框，对它们进行右连接意味着保留右侧数据框中的所有内容，并添加来自左侧数据框的任何匹配信息。

在我们之前的示例中，我们有两个数据框，分别是 left_df 和 right_df。

右连接将首先保留数据框 right_df 中的所有内容，然后从数据框 left_df 中寻找可能的匹配。匹配通常基于数据框中的公共列或键进行搜索。当找到匹配时，将匹配的列从 left_df 中取出并与 right_df 合并。如果存在匹配的行但信息仅部分填充，则剩余部分将填充为 NA。

数据框 right_df 包含以下数据：

```
DeptID   DeptName
0     101         HR
1     102         IT
2     103  Marketing
```

数据框 left_df 包含以下数据：

```
ID     Name  DeptID
0   1    Alice     101
1   2      Bob     102
2   3  Charlie     103
3   4    David     104
4   5     John     103
```

让我们尝试对这些数据框执行右连接：

```
rightjoin = pd.merge(
left_df1,
right_df1,
on="DeptID",
how="right"
)
print(rightjoin)
```

将输出

```
ID     Name  DeptID   DeptName
0   1    Alice     101         HR
1   2      Bob     102         IT
2   3  Charlie     103  Marketing
3   5     John     103  Marketing
```

在右连接的情况下，右侧的表保持不变。将扫描左侧的表以根据部门 ID 列识别匹配信息。然后，匹配的列将被提取出来并附加到右侧的表中。



#### 外连接

在关系型数据库领域中，这些被称为全外连接。外连接是指，给定两个数据框，两者都会被保留，不会丢失任何数据。外连接产生的缺失值数量最多，但它会保留两个数据框中的所有数据：

```
outerjoin = pd.merge(
left_df1,
right_df1,
on="DeptID",
how="outer"
)
print(outerjoin)
```

将输出

```
ID     Name  DeptID   DeptName
0   1    Alice     101         HR
1   2      Bob     102         IT
2   3  Charlie     103  Marketing
3   5     John     103  Marketing
4   4    David     104        NaN
```

外连接试图捕获两个表中的所有数据，并在无法匹配的位置分配一个缺失值（非数字）。

#### 内连接

内连接是全外连接的反向操作。这种连接是指，给定两个数据框，仅返回匹配的列作为结果数据框。两个数据框中的部分匹配项将被排除，仅返回完全匹配的项。

以下是相同示例的代码：

```
innerjoin = pd.merge(
left_df1,
right_df1,
on="DeptID",
how="inner"
)
print(innerjoin)
```

将输出

```
Out[174]:
ID     Name  DeptID   DeptName
0   1    Alice     101         HR
1   2      Bob     102         IT
2   3  Charlie     103  Marketing
3   5     John     103  Marketing
```

如您所见，存在部分匹配的行已从结果数据集中排除。

#### 交叉连接

交叉连接也称为笛卡尔连接。它是指给定数据框中的每一行都与另一个数据框中的行进行分配或合并。不需要公共键。

以下是其实现方式。

让我们初始化两个数据框。

```
df_one = pd.DataFrame({
'ID': [1, 2],
'Name': ['John', 'Doe']
})
```

将输出

```
ID  Name
0   1  John
1   2   Doe
df_two = pd.DataFrame({
'ItemID': [101, 102],
'Item': ['Laptop', 'Cable']
})
```

将输出

```
ItemID    Item
0     101  Laptop
1     102   Cable
```

现在让我们执行交叉连接：

```
cross_join = pd.merge(
df_one,
df_two,
how="cross"
)
print(cross_join)
```

将输出

```
ID   Name  ItemID    Item
0   1  Alice     101  Laptop
1   1  Alice     102   Cable
2   2    Bob     101  Laptop
3   2    Bob     102   Cable
```

## 数据重塑

数据重塑是在保留数据集中包含的值和观测值的同时，转换数据结构的过程。进行数据重塑的目的是在改变数据维度的同时，发现新的信息和见解，而无需更改数值。

### pivot( )

数据重塑最常见的概念是数据透视表。由 Microsoft Excel 推广开来，数据透视表可以重新排列行和列的数据。常见应用包括为仪表盘、进一步分析等准备数据。

以下是一个数据透视表的示例。假设我们有以下数据集：

```
data = {
'Date':
['2023-06-01', '2023-06-01', '2023-06-01', '2023-06-02', '2023-06-02', '2023-06-02'],
'City':
['Cupertino', 'Portland', 'Fort Worth', 'Cupertino', 'Portland', 'Fort Worth'],
'Temperature':
[56, 65, 69, 59, 61, 66]
}
df = pd.DataFrame(data)
```

将输出

```
Date        City  Temperature
0  2023-06-01   Cupertino           56
1  2023-06-01    Portland           65
2  2023-06-01  Fort Worth           69
3  2023-06-02   Cupertino           59
4  2023-06-02    Portland           61
5  2023-06-02  Fort Worth           66
```

现在让我们重塑数据，使城市列中的值成为列类别：

```
pivoted_df = df.pivot(
index='Date',
columns='City',
values='Temperature'
)
print(pivoted_df)
```

将输出

```
City        Cupertino  Fort Worth  Portland
Date
2023-06-01         56          69        65
2023-06-02         59          66        61
```

这样，日期不再重复，列中的每个城市都成为了一个类别，温度数据以更易于呈现的格式显示。当您需要在不执行任何聚合操作的情况下重塑数据时，`pivot()` 函数非常有用。

### pivot_table( )

`pivot_table()` 方法与 `pivot()` 方法类似，但提供了更多功能。`pivot_table()` 有助于在重塑整个数据集的同时聚合数值数据。`pivot_table()` 方法提供了类似电子表格数据透视的功能。

让我们看一个示例：

```
data = {
'Month':
['January', 'January', 'January', 'January',
'February', 'February', 'February', 'February'],
'Region':
['North', 'East', 'West', 'South',
'North', 'East', 'West', 'South' ],
'Product':
['Product A', 'Product A', 'Product A', 'Product A',
'Product B', 'Product B', 'Product B', 'Product B'],
'Sales':
[200, 150, 180, 210, 190, 175, 225, 250]
}
df = pd.DataFrame(data)
print(df)
```

将输出

```
Month Region    Product  Sales
0   January  North  Product A    200
1   January   East  Product A    150
2   January   West  Product A    180
3   January  South  Product A    210
4  February  North  Product B    190
5  February   East  Product B    175
6  February   West  Product B    225
7  February  South  Product B    250
pivot_table_df = df.pivot_table(
index='Product',
columns='Region',
values='Sales',
aggfunc='mean'
)
print(pivot_table_df)
Region     East  North  South  West
Product
Product A   150    200    210   180
Product B   175    190    250   225
```

在上面的输出中，`pivot_table()` 方法将区域类别转换为单独的列，并将产品类别转换为索引（单独的行），对于每个产品和区域的组合，它通过计算统计平均值来聚合总销售额。这样，您可以获得给定区域的产品销售表现。

### stack( )

`stack()` 方法与数据透视表的概念有些相似且密切相关。`stack()` 方法也通过将列类别转换为索引来重塑数据。让我们看一个示例来观察 `stack()` 方法的功能。

以下是一个包含给定两年内两种产品季度销售额的数据集：

```
data1 = {
'Product': ['Product A', 'Product B'],
'2023-Q1': [100, 80],
'2023-Q2': [150, 130],
'2023-Q3': [200, 180],
'2023-Q4': [250, 230],
'2024-Q1': [300, 280],
'2024-Q2': [350, 330],
'2024-Q3': [400, 380],
'2024-Q4': [450, 430]
}
df1 = pd.DataFrame(data1).set_index('Product')
print(df1)
```

将输出

```
2023-Q1  2023-Q2  2023-Q3  ...  2024-Q2  2024-Q3  2024-Q4
Product                               ...
Product A      100      150      200  ...      350      400      450
Product B       80      130      180  ...      330      380      430
```

以下是我们如何堆叠每种产品的季度销售额：

```
quartSales = df1.stack()
print(quartSales)
```

将输出

```
Product
Product A  2023-Q1    100
2023-Q2    150
2023-Q3    200
2023-Q4    250
2024-Q1    300
2024-Q2    350
2024-Q3    400
2024-Q4    450
Product B  2023-Q1     80
2023-Q2    130
2023-Q3    180
2023-Q4    230
2024-Q1    280
2024-Q2    330
2024-Q3    380
2024-Q4    430
```

结果数据框包含一个之前是列字段的索引。现在，结果数据框有两个索引：一个是产品类型，另一个是销售季度列。



### unstack( )

unstack() 是 stack() 方法的逆操作。然而，这两种方法是互斥的。你可以直接调用 unstack() 方法，而无需先使用 stack() 方法。让我们看一个例子：

```
d
data2 = {
'Product':
['Product A', 'Product A', 'Product B', 'Product B'],
'Month':
['January', 'February', 'January', 'February'],
'Sales':
[120, 110, 150, 140]
}
df3 = pd.DataFrame(data2).set_index(['Product','Month'])
print(df3)
```

会输出

```
Sales
Product   Month
Product A January     120
February    110
Product B January     150
February    140
```

下面是如何使用 unstack() 方法将窄表变宽：

```
wideSales = df3.unstack()
print(wideSales)
Sales
Month     February January
Product
Product A      110     120
Product B      140     150
```

### melt( )

melt() 方法执行的是 pivot() 函数的逆函数。melt() 方法会将宽数据格式转换为窄数据格式。

让我们通过以下示例了解 melt() 方法的功能：

```
melt_data = {
'Product':
['Product A', 'Product B', 'Product C'],
'January':
[300, 200, 400],
'February':
[350, 250, 450],
'March':
[400, 300, 500]
}
melt_df = pd.DataFrame(melt_data)
print(melt_df)
```

会输出

```
Product  January  February  March
0  Product A      300       350    400
1  Product B      200       250    300
2  Product C      400       450    500
```

现在，让我们应用 melt() 方法，我们希望识别产品，并根据月份和销售额变量来衡量产品：

```
melted_df = melt_df.melt(
id_vars=['Product'],
var_name='Month',
value_name='Sales'
)
print(melted_df)
Product     Month  Sales
0  Product A   January    300
1  Product B   January    200
2  Product C   January    400
3  Product A  February    350
4  Product B  February    250
5  Product C  February    450
6  Product A     March    400
7  Product B     March    300
8  Product C     March    500
```

上述结果数据框已成功将宽数据框转换为窄数据框。

### crosstab( )

crosstab() 是一个强大的统计工具，用于计算两个或多个因素的交叉制表。crosstab() 函数为给定的列构建一个频率表。

这是一个例子：

```
ctdata = {
'Person':
[1, 2, 3, 4,
5, 6, 7, 8, 9],
'State':
['NY', 'WA', 'CO', 'NY',
'WA', 'CO', 'NY', 'WA', 'CO'],
'Likes':
['Mountains', 'Mountains', 'Mountains', 'Mountains',
'Mountains', 'Oceans', 'StateParks', 'StateParks', 'StateParks']
}
ctdf = pd.DataFrame(ctdata)
print(ctdf)
```

会输出

```
Person State       Likes
0       1    NY   Mountains
1       2    WA   Mountains
2       3    CO   Mountains
3       4    NY   Mountains
4       5    WA   Mountains
5       6    CO      Oceans
6       7    NY  StateParks
7       8    WA  StateParks
8       9    CO  StateParks
```

现在，让我们分别使用“State”和“Likes”列构建一个频率表。

```
crossstab_df = pd.crosstab(
ctdf['State'],
ctdf['Likes'],
rownames=["Region"],
colnames=["FavoriteActivity"]
)
print(crossstab_df)
```

会输出以下内容：

```
FavoriteActivity  Mountains  Oceans  StateParks
Region
CO                        1       1           1
NY                        2       0           1
WA                        2       0           1
```

上述数据框提供了人数统计。你也可以通过使用“normalize”参数来获取喜欢某项活动的人数百分比：

```
normalActMatrix = pd.crosstab(
ctdf['State'],
ctdf['Likes'],
rownames=["Region"],
colnames=["FavoriteActivity"],
normalize=True
)
print(normalActMatrix)
```

会输出

```
FavoriteActivity  Mountains    Oceans  StateParks
Region
CO                 0.111111  0.111111    0.111111
NY                 0.222222  0.000000    0.111111
WA                 0.222222  0.000000    0.111111
```

你还可以添加“margins”参数来获取频率表的行和与列和：

```
freqTable = pd.crosstab(
ctdf['State'],
ctdf['Likes'],
rownames=["Region"],
colnames=["FavoriteActivity"],
margins=True
)
print(freqTable)
```

会输出

```
FavoriteActivity  Mountains  Oceans  StateParks  All
Region
CO                        1       1           1    3
NY                        2       0           1    3
WA                        2       0           1    3
All                       5       1           3    9
```

这也可以与“normalize”参数一起使用：

```
normalFreqTable = pd.crosstab(
ctdf['State'],
ctdf['Likes'],
rownames=["Region"],
colnames=["FavoriteActivity"],
margins=True,
normalize=True
)
print(normalFreqTable)
```

会输出

```
FavoriteActivity  Mountains    Oceans  StateParks       All
Region
CO                 0.111111  0.111111    0.111111  0.333333
NY                 0.222222  0.000000    0.111111  0.333333
WA                 0.222222  0.000000    0.111111  0.333333
All                0.555556  0.111111    0.333333  1.000000
```

### factorize( )

factorize() 方法是数据重塑主题中另一个需要了解的重要方法。factorize() 方法有助于对给定的分类变量（可能是字符串）进行数值分类。一些下游分析模型可能需要对分类变量进行数值编码。让我们看一个例子：

```
factdata = {
'Person':
[1, 2, 3, 4, 5],
'Likes':
['Mountains', 'Oceans', 'Oceans', 'Parks', 'Parks']
}
fact_df = pd.DataFrame(factdata)
print(fact_df)
```

会输出

```
Person      Likes
0       1  Mountains
1       2     Oceans
2       3     Oceans
3       4      Parks
4       5      Parks
```

“factorize()”方法返回两个对象：标签和唯一值。在我们的示例中如下所示：

```
labels, uniques = pd.factorize(fact_df['Likes'])
print("The labels are:", labels)
print("The unique values are:", uniques.values)
```

会输出

```
The labels are: [0 1 1 2 2]
The unique values are: ['Mountains' 'Oceans' 'Parks']
```

让我们将标签合并到现有的数据框中：

```
fact_df['Likes_Coded'] = labels
print(fact_df)
```

会输出

```
Person      Likes  Likes_Coded
0       1  Mountains            0
1       2     Oceans            1
2       3     Oceans            1
3       4      Parks            2
4       5      Parks            2
```



### compare( )

`compare()` 方法允许你比较两个数据框或序列，并找出它们之间的差异。让我们看一个简单的例子，观察两个数据框之间的差异。

下面是一个简单的示例：

```
ctdata = {
'Person':
[1, 2, 3, 4, 5, 6, 7, 8, 9],
'State':
['NY', 'WA', 'CO', 'NY',
'WA', 'CO', 'NY', 'WA', 'CO'],
'Likes':
['Mountains', 'Mountains', 'Mountains', 'Mountains',
'Mountains', 'Oceans', 'StateParks', 'StateParks', 'StateParks']
}
ct_df = pd.DataFrame(ctdata)
print(ct_df)
```

会输出：

```
Person State       Likes
0       1    NY   Mountains
1       2    WA   Mountains
2       3    CO   Mountains
3       4    NY   Mountains
4       5    WA   Mountains
5       6    CO      Oceans
6       7    NY  StateParks
7       8    WA  StateParks
8       9    CO  StateParks
```

让我们复制一份：

```
ct_df1 = ct_df.copy()
```

现在，修改一些值：

```
ct_df1.loc[3,'State'] = 'NC'
ct_df1.loc[5,'State'] = 'CA'
```

现在我们有了一个名为“ct_df1”的不同数据集。

它的内容如下：

```
Person State       Likes
0       1    NY   Mountains
1       2    WA   Mountains
2       3    CO   Mountains
3       4    NC   Mountains
4       5    WA   Mountains
5       6    CA      Oceans
6       7    NY  StateParks
7       8    WA  StateParks
8       9    CO  StateParks
```

现在，让我们执行比较：

```
ct_df.compare(ct_df1)
```

会输出：

```
State
self other
3    NY    NC
5    CO    CA
```

让我们尝试在比较时保留整个数据集。这可以通过使用“keep_shape”参数来实现：

```
ct_df.compare(ct_df1, keep_shape=True)
```

会得到：

```
Person       State       Likes
self other  self other  self other
0    NaN   NaN   NaN   NaN   NaN   NaN
1    NaN   NaN   NaN   NaN   NaN   NaN
2    NaN   NaN   NaN   NaN   NaN   NaN
3    NaN   NaN    NY    NC   NaN   NaN
4    NaN   NaN   NaN   NaN   NaN   NaN
5    NaN   NaN    CO    CA   NaN   NaN
6    NaN   NaN   NaN   NaN   NaN   NaN
7    NaN   NaN   NaN   NaN   NaN   NaN
8    NaN   NaN   NaN   NaN   NaN   NaN
```

注意数据集中缺失值的用法。在比较过程中，如果相应行和列上的值相似，则会分配缺失值。只有数据框之间不同的值才会显示。

### groupby( )

`groupby()` 方法是使用 Pandas 库进行数据分析时需要了解的重要方法和函数。`groupby()` 方法通过对数据进行分组来执行聚合和汇总。`groupby()` 方法通常涉及以下三个步骤中的一个或多个：

*   **拆分**，根据特定标准对数据集进行划分的过程
*   **应用**，对所有拆分后的数据集应用函数的过程
*   **合并**，将拆分后的数据集合并回原始维度的过程

让我们看一个详细的例子：

```
groupbydata = {
'Person':
['Alice', 'Bob', 'Charlie', 'David'],
'Dept':
['Marketing', 'Marketing', 'DigitalM', 'DigitalM'],
'Sales':
[20, 12, 35, 42]
}
grp_df = pd.DataFrame(groupbydata)
print(grp_df)
```

会输出：

```
Person       Dept  Sales
0    Alice  Marketing     20
1      Bob  Marketing     12
2  Charlie   DigitalM     35
3    David   DigitalM     42
```

现在让我们对这个数据集应用 `groupby()`。这里我们想要获取每个部门的总销售额。这意味着要对数据集进行分组，并计算每个类别的总和。

操作如下：

```
grp_df1 = grp_df.groupby('Dept')['Sales'].sum()
print(grp_df1)
```

会输出：

```
Dept
DigitalM     77
Marketing    32
Name: Sales, dtype: int64
```

## 结论

到目前为止，你已经对构成使用 Pandas 进行有效数据操作支柱的核心概念和技术有了扎实的理解。通过本章，你了解了核心数据结构、索引方法、数据重塑策略、高级数据整理方法、组合多个 Pandas 对象以及处理缺失值。这里讨论的概念和思想即使你不积极使用 Pandas 库，也会对你有所帮助；这些概念将为你学习其他各种数据整理库奠定基础。你可能会在接下来的章节中看到，这里看到的一些方法和方法论可能会以某种形式重复出现。

