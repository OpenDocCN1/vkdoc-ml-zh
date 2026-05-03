# 1. 轻松过渡

从 Pandas 和 Scikit-Learn 过渡到 PySpark 相对容易的关键因素之一在于功能上的相似性。阅读本章并执行其中描述的代码后，这种相似性将变得显而易见。

测试代码最简单的方法之一是注册一个在线 Databricks Community Edition 账户并创建工作区。Databricks 提供了关于如何创建集群、上传数据和创建笔记本的详细文档。此外，也可以通过 `pip install pyspark` 命令在本地安装 Spark。

另一个选择是 Google Colab。PySpark 默认预装在 Colab 中；否则，可以在 Colab 笔记本中使用 `!pip install pyspark` 命令进行安装。该命令会在 Colab 环境中安装 PySpark 及其依赖项。虽然两者都提供类似 Jupyter 的笔记本，但 Databricks 的一个优势在于 Colab 是单核实例，而 Databricks 提供多节点集群用于并行处理。这一特性使得 Databricks 更适合在协作团队环境中处理更大的数据集和更复杂的计算任务。

尽管 Pandas 和 Scikit-Learn 主要是为小规模数据处理和分析设计的工具，而 PySpark 是一个大数据处理框架，但 PySpark 提供了与 Pandas 和 Scikit-Learn 类似的功能。这包括 DataFrame 操作和机器学习算法。PySpark 中这些熟悉的功能有助于习惯使用 Pandas 和 Scikit-Learn 的数据科学家更平滑地过渡。

在本章中，我们将更深入地探讨促成从这些小数据工具（Pandas 和 Scikit-Learn）轻松过渡到 PySpark 的因素。更具体地说，我们将重点关注 PySpark 与 Pandas 的集成，以及 PySpark 与 Pandas 和 Scikit-Learn 在语法上的相似性。

## PySpark 与 Pandas 的集成

PySpark 与 Pandas 集成良好。PySpark 中的 DataFrame 和 MLlib（机器学习库）设计都受到了 Pandas（和 Scikit-Learn）概念的启发。PySpark 中的 DataFrame 概念与 Pandas 类似，数据以行和列的形式存储，类似于关系型数据库表和 Excel 表格。因此，可以轻松地将 PySpark DataFrame 转换为 Pandas DataFrame，反之亦然，这使得两个包之间的集成变得简单直接。

PySpark DataFrame 可以通过使用 `toPandas()` 方法转换为 Pandas DataFrame，而 Pandas DataFrame 则可以使用 `createDataFrame()` 方法转换为 PySpark DataFrame。

让我们从一个示例开始，演示如何使用 PySpark 的 `createDataFrame()` 方法将 Pandas DataFrame 转换为 PySpark DataFrame：

步骤 1：导入 Pandas

```
[In]: import pandas as pd
```

步骤 2：创建一个 Pandas DataFrame

```
[In]: data = {'Country': ['United States', 'Brazil', 'Russia'], 'River': ['Mississippi', 'Amazon', 'Volga']}
[In]: pandas_df = pd.DataFrame(data)
```

步骤 3：将 Pandas DataFrame 转换为 PySpark DataFrame

```
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.appName("BigRivers").getOrCreate()
[In]: pyspark_df = spark.createDataFrame(pandas_df)
```

步骤 4：显示 PySpark DataFrame 的内容

| Country | River |
| --- | --- |
| United States | Mississippi |
| Brazil | Amazon |
| Russia | Volga |

```
[In]: pyspark_df.show()
[Out]:
```

请注意，在步骤 3 中使用 PySpark 的 `createDataFrame()` 方法将 Pandas DataFrame 转换为 PySpark DataFrame 之前，我们需要创建一个名为 `spark` 的 Spark Session。实现这一目标需要两行代码。在第一行（`from pyspark.sql import SparkSession`）中，我们从 `pyspark.sql` 模块导入了 `SparkSession` 类。在第二行中，我们创建了一个名为 `spark` 的 Spark Session 新实例（`spark = SparkSession.builder.appName("BigRivers").getOrCreate()`）。这行代码各部分的作用如下：

*   `SparkSession.builder`：启动配置和构建新 Spark Session 的过程。
*   `appName("BigRivers")`：`appName` 方法为 Spark 应用程序设置一个名称。
*   `getOrCreate()`：此方法要么检索现有的 Spark Session（如果存在），要么创建一个新的（如果未找到）。它确保一次只有一个 Spark Session 处于活动状态。

这个 Spark Session 概念在 Apache Spark 中是基础性的，因为它是以结构化方式与其特性和 API 进行交互的入口点。它提供了一个环境，用于管理配置、创建 Spark DataFrame 和 Dataset 对象、执行 Spark 操作以及在集群中协调任务。

如果我们是在 Google Colab 这样的环境中运行代码，这一点尤其重要。然而，在使用 Databricks 笔记本时，Spark Session 已经创建并初始化，因此我们可以直接使用 `spark` 对象与 Spark 特性交互、运行查询、创建 DataFrame 以及使用各种 Spark 库。现在我们可以进行反向操作：创建一个 PySpark DataFrame，然后使用 `toPandas()` 方法将其转换为 Pandas DataFrame：

步骤 1：导入 Spark Session

```
[In]: from pyspark.sql import SparkSession
```

步骤 2：创建一个 Spark Session

```
[In]: spark = SparkSession.builder.appName("BigRivers").getOrCreate()
```

步骤 3：创建一个 PySpark DataFrame

```
[In]: data = [('United States', 'Mississippi'), ('Brazil', 'Amazon'), ('Russia', 'Volga')]
[In]: spark_df = spark.createDataFrame(data, ["Country", "River"])
```

步骤 4：将 Spark DataFrame 转换为 Pandas DataFrame

```
[In]: pandas_df = spark_df.toPandas()
```

步骤 5：显示数据

|   | Country | River |
| --- | --- | --- |
| 0 | United States | Mississippi |
| 1 | Brazil | Amazon |
| 2 | Russia | Volga |

```
[In]: print(pandas_df)
[Out]:
```

请注意，Pandas 在输出中显示了一个索引列，为每一行提供了唯一标识符，而 PySpark 在其 DataFrame 输出中并未明确显示索引列。

正如我们在本节中所看到的，由于两个库之间的紧密集成，在 Pandas 和 PySpark 之间切换非常容易。

## 语法相似性

促成从小数据工具（Pandas 和 Scikit-Learn）平滑过渡到 PySpark 大数据的另一个关键因素是语法的熟悉度。在许多情况下，PySpark 与 Pandas 和 Scikit-Learn 共享相似的语法。例如，在 Databricks 或 Google Colab 上，可以使用方括号（`[]`）直接从 PySpark DataFrame 中选择列，就像在 Pandas 中一样。类似地，PySpark 提供了类似于 Pandas 的 DataFrame API，而 Spark MLlib（机器学习库）包含了 Scikit-Learn 中各种机器学习算法的实现。

在本节中，我们将举例说明 PySpark 代码与 Pandas 和 Scikit-Learn 的相似之处，从而帮助数据科学家在这些工具之间轻松过渡。



## 加载数据

让我们从加载和读取数据开始。我们可以使用 Pandas 中的 `read_csv()` 函数来加载一个名为 data.csv 的 CSV 文件：

```
[In]: import pandas as pd
[In]: pandas_df = pd.read_csv('data.csv')
```

第一行代码导入 Pandas 库，并将其重命名为 pd 以方便使用。第二行代码使用 `read_csv()` 函数读取名为 data.csv 的 CSV 文件。文件内容被存储在一个名为 pandas_df 的 DataFrame 中。

在 PySpark 中，我们可以使用 `spark.read.csv()` 方法完成同样的操作：

```
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.appName("ReadCSV").getOrCreate()
[In]: spark_df = spark.read.csv('data.csv', header=True, inferSchema=True)
```

这段代码首先导入 SparkSession 类，并创建一个名为 spark 的新 Spark Session，其应用名称为 ReadCSV。如果已存在同名的会话，则会复用该会话；否则，将创建一个新的会话。然后，代码使用 `spark.read.csv()` 方法读取名为 data.csv 的 CSV 文件。`header` 参数设置为 `True`，表示 CSV 文件的第一行包含列名。`inferSchema` 参数设置为 `True`，以启用 PySpark 自动推断每列的数据类型。文件内容被存储在一个名为 spark_df 的 DataFrame 中。

从前面的例子可以看出，尽管代码存在一些差异（例如，PySpark 要求我们显式指定 `header` 和 `inferSchema` 选项，而在 Pandas 中，如果我们不指定这些选项，它们会使用默认值），但 Pandas 和 PySpark 读取数据的方式相当相似。

## 选择列

Pandas 和 PySpark 都提供了多种选择列的方法，并且在某种程度上，它们的列选择语法具有相似性。让我们从探索方括号方法开始，首先看 Pandas：

|   | 总统 | 任职年份 |
| --- | --- | --- |
| 0 | 乔治·华盛顿 | 1789 |
| 1 | 亚伯拉罕·林肯 | 1861 |
| 2 | 富兰克林·D·罗斯福 | 1933 |
| 3 | 约翰·F·肯尼迪 | 1961 |
| 4 | 巴拉克·奥巴马 | 2009 |

```
[In]: import pandas as pd
[In]: pandas_df = pd.DataFrame({'President': ['George Washington', 'Abraham Lincoln', 'Franklin D. Roosevelt', 'John F. Kennedy', 'Barack Obama'], 'Year of Office': [1789, 1861, 1933, 1961, 2009]})
[In]: pandas_df[["President", "Year of Office"]]
[Out]:
```

第一行代码导入 Pandas 库，并将其重命名为 pd 以方便使用。第二行代码创建一个名为 pandas_df 的新 Pandas DataFrame。该 DataFrame 使用一个字典进行初始化，其中键代表列名，值代表每列的数据。

在这个例子中，有两列，分别名为 President 和 Year of Office，每列有五行数据。第三行代码从 DataFrame 中选择特定的列。使用双括号表示法 `pandas_df[["President", "Year of Office"]]` 来选择这两列。生成的 DataFrame 包含这些被选中的列。

在 Databricks 或 Google Colab 笔记本中运行 PySpark 代码时，我们可以像在 Pandas 中一样，使用双括号表示法来选择列：

| President | Year of Office |
| --- | --- |
| George Washington | 1789 |
| Abraham Lincoln | 1861 |
| Franklin D. Roosevelt | 1933 |
| John F. Kennedy | 1961 |
| Barack Obama | 2009 |

```
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.appName("Presidents").getOrCreate()
[In]: spark_df = spark.createDataFrame([
("George Washington", 1789),
("Abraham Lincoln", 1861),
("Franklin D. Roosevelt", 1933),
("John F. Kennedy", 1961),
("Barack Obama", 2009)],
["President", "Year of Office"])
[In]: spark_df[["President", "Year of Office"]].show()
[Out]:
```

代码首先导入 SparkSession 类，然后创建一个名为 spark 的新 Spark Session，其应用名称为 Presidents。接着，它创建一个名为 spark_df 的新 PySpark DataFrame。该 DataFrame 使用一个元组列表进行初始化，每个元组代表一行数据，列名则作为一个单独的列表指定。在这个例子中，有两列，分别名为 President 和 Year of Office，每列有五行数据。然后，代码使用双括号表示法从 DataFrame 中选择列。生成的 DataFrame 包含这些被选中的列。最后一行代码使用 `show()` 方法显示生成的 DataFrame。

从双括号表示法 `pandas_df[["President", "Year of Office"]]` 和 `spark_df[["President", "Year of Office"]]` 可以明显看出，Pandas 和 PySpark 选择列的方式是相同的。

注意

PySpark 中的 `show()` 方法用于显示 DataFrame 的结果，而 Pandas 没有类似的方法，因为结果会立即显示。这是因为 PySpark 使用惰性求值来优化数据处理，通过将计算推迟到必要时才进行，而 Pandas 则使用即时求值。不过，Pandas 中的 `head()` 方法可用于显示 DataFrame 的前几行，可以看作是 PySpark 中用于子集显示的 `show()` 方法的等效方法。在 Databricks 上，PySpark 提供了 `display()` 方法，例如 `display(spark_df)`，它允许在 Databricks 笔记本环境中可视化数据。

在 PySpark 中，我们可以选择使用 `select()` 方法来选择特定的列。另一方面，在 Pandas 中，我们可以使用 `filter()` 函数实现相同的结果，如下所示：

在 PySpark 中：

| President | Year of Office |
| --- | --- |
| George Washington | 1789 |
| Abraham Lincoln | 1861 |
| Franklin D. Roosevelt | 1933 |
| John F. Kennedy | 1961 |
| Barack Obama | 2009 |

```
[In]: spark_df.select("President", "Year of Office").show()
[Out]:
```

在 Pandas 中：

|   | President | Year of Office |
| --- | --- | --- |
| 0 | George Washington | 1789 |
| 1 | Abraham Lincoln | 1861 |
| 2 | Franklin D. Roosevelt | 1933 |
| 3 | John F. Kennedy | 1961 |
| 4 | Barack Obama | 2009 |

```
[In]: pandas_df.filter(items = ["President", "Year of Office"])
[Out]:
```

尽管 `select()` 和 `filter()` 方法执行的具体操作不同，但生成的对象都有一个共同特点：它们都包含原始 DataFrame 数据的一个子集。



## 数据聚合

Pandas 和 PySpark 不仅能够以相同的方式读取数据和选择列，它们还能以类似的方式进行数据聚合。

让我们通过示例来说明这一点。首先，我们创建一个包含电影和收入列的 Pandas DataFrame，并按电影进行聚合：

步骤 1：导入 pandas 并重命名为 pd

```
[In]: import pandas as pd
```

步骤 2：创建一个名为 `pandas_data` 的 Python 字典形式的示例数据，其中包含两个键：`movie` 和 `revenue`

```
[In]: pandas_data = {
'movie': ['Avengers', 'Frozen', 'Star Wars',
'The Lion King', 'Harry Potter'],
'revenue': [90000000, 70000000, 110000000, 80000000, 100000000]}
```

步骤 3：从字典创建一个 DataFrame

```
[In]: pandas_df = pd.DataFrame(pandas_data)
```

步骤 4：使用 `groupby()` 方法按电影分组，然后使用 `agg()` 方法并传入一个字典（指定我们要计算收入列的平均值）来计算每部电影的平均收入

```
[In]: agg_pandas_df = pandas_df.groupby('movie').agg({'revenue':'mean'})
```

步骤 5：显示数据

|   | movie | revenue |
| --- | --- | --- |
| 0 | Avengers | 90000000 |
| 1 | Frozen | 70000000 |
| 2 | Harry Potter | 100000000 |
| 3 | Star Wars | 110000000 |
| 4 | The Lion King | 80000000 |

```
[In]: print(agg_pandas_df)
[Out]:
```

以下是使用 PySpark 按电影聚合收入的等效代码：

步骤 1：导入 SparkSession 类并创建一个 Spark Session

```
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.appName("MovieRevenueAggregation").getOrCreate()
```

步骤 2：创建示例数据

```
[In]: spark_data = [('Avengers', 90000000), ('Frozen', 70000000), ('Harry Potter', 100000000), ('Star Wars', 110000000), ('The Lion King', 80000000)]
spark_df = spark.createDataFrame(spark_data, ['movie', 'revenue'])
```

步骤 3：按电影聚合

```
[In]: agg_spark_df = spark_df.groupBy('movie').agg({'revenue': 'mean'})
```

步骤 4：显示数据

| movie | revenue |
| --- | --- |
| Avengers | 90000000 |
| Frozen | 70000000 |
| Harry Potter | 100000000 |
| Star Wars | 110000000 |
| The Lion King | 80000000 |

```
[In]: agg_spark_df.show()
[Out]:
```

从前面的代码示例可以看出，Pandas 和 PySpark 都实现了 `groupby()` 和 `agg()` 方法来聚合数据。这并非巧合，因为 PySpark DataFrame 的结构正是受到了 Pandas 的启发。

## 数据过滤

在展示了 Pandas 和 PySpark 在加载和读取数据、选择列以及聚合数据方面具有相似的语法之后，我们现在可以看看它们如何使用相似的语法来过滤数据的示例。

我们从 Pandas 开始。我们创建一个名为 `pandas_df` 的 DataFrame，包含四行数据和两列：`animal` 和 `muscle_power`，然后对其进行过滤，只显示 `muscle_power` 大于 350 的行：

步骤 1：导入 Pandas 库并重命名为 pd

```
[In]: import pandas as pd
```

步骤 2：创建一个示例 DataFrame

```
[In]: pandas_df = pd.DataFrame({
'animal': ['Lion', 'Elephant', 'Tiger', 'Gorilla'],
'muscle_power': [400, 6000, 350, 800]})
```

步骤 3：过滤 DataFrame，只显示 `muscle_power` 大于 350 的行：

```
[In]: filtered_pandas_df = pandas_df[pandas_df['muscle_power'] > 350]
```

步骤 4：打印结果

|   | animal | muscle_power |
| --- | --- | --- |
| 0 | Lion | 400 |
| 1 | Elephant | 6000 |
| 2 | Gorilla | 800 |

```
[In]: print(filtered_pandas_df)
[Out]:
```

在 PySpark 中，我们遵循以下步骤：

步骤 1：导入 SparkSession 类并创建一个 Spark Session

```
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.appName("FilterByMusclePower").getOrCreate()
```

步骤 2：创建一个名为 `spark_data` 的元组列表，每个元组包含一个动物和对应的肌肉力量值

```
[In]: spark_data = [("Lion", 400), ("Elephant", 6000), ("Tiger", 350), ("Gorilla", 800)]
```

步骤 3：从 `spark_data` 列表创建一个名为 `spark_df` 的 PySpark DataFrame，列名为 `animal` 和 `muscle_power`

```
[In]: spark_df = spark.createDataFrame(spark_data, ["animal", "muscle_power"])
```

步骤 4：过滤 DataFrame，只显示 `muscle_power` 大于 350 的行：

```
[In]: filtered_spark_df = spark_df[spark_df['muscle_power'] > 350]
```

步骤 5：使用 `show` 方法显示过滤后的 DataFrame

| animal | muscle_power |
| --- | --- |
| Lion | 400 |
| Elephant | 6000 |
| Gorilla | 800 |

```
[In]: filtered_spark_df.show()
[Out]:
```

我们还可以分别在 Pandas 和 PySpark 中使用 `query()` 和 `filter()` 方法，来只显示 `muscle_power` 大于 350 的行。这些方法等效于方括号表示法 `pandas_df[pandas_df['muscle_power'] > 350]` 和 `spark_df[spark_df['muscle_power'] > 350]`。

在 Pandas 中：

|   | animal | muscle_power |
| --- | --- | --- |
| 0 | Lion | 400 |
| 1 | Elephant | 6000 |
| 2 | Gorilla | 800 |

```
[In]: pandas_df.query("muscle_power > 350")
[Out]:
```

在 PySpark 中：

| animal | muscle_power |
| --- | --- |
| Lion | 400 |
| Elephant | 6000 |
| Gorilla | 800 |

```
[In]: spark_df.filter("muscle_power > 350").show()
[Out]:
```

在 Spark DataFrame 中，至少还有两种方法可以实现相同的过滤结果：

| animal | muscle_power |
| --- | --- |
| Lion | 400 |
| Elephant | 6000 |
| Gorilla | 800 |

1.  使用带有 `where` 子句的类 SQL 语法：

    ```
    [In]: spark_df.where("muscle_power > 350").show()
    [Out]:
    ```

| animal | muscle_power |
| --- | --- |
| Lion | 400 |
| Elephant | 6000 |
| Gorilla | 800 |

1.  使用列表达式：

    ```
    [In]: from pyspark.sql.functions import col
    [In]: spark_df.filter(col("muscle_power") > 350).show()
    [Out]:
    ```

前面的示例表明，Pandas 和 PySpark 都提供了多种数据过滤方法，并且在很大程度上，它们各自的方法具有相似性。



## 连接数据

在 Pandas 和 PySpark 中，连接两个 DataFrame 都需要指定两个 DataFrame 之间的公共列以及要执行的连接类型（内连接、外连接、左连接、右连接）。Pandas 和 PySpark 中连接 DataFrame 的代码语法可能相似，如下例所示。这里，我们将演示内连接，但其他连接类型的语法保持不变；只需修改连接类型即可。

在 Pandas 中，我们可以使用 `merge()` 方法在公共列上合并或连接两个 DataFrame（pandas_df1 和 pandas_df2），并创建一个名为 merged_pandas_df 的新 DataFrame。例如，我们想根据职位名称合并两个 DataFrame，以便比较和分析同一职位在不同美国城市的薪资差异。合并后的 DataFrame 允许我们并排查看来自 DataFrame 1 和 DataFrame 2 中公共职位的薪资信息。

步骤 1：创建第一个 DataFrame

|   | 职位名称 | 薪资 | 地点 |
| --- | --- | --- | --- |
| 0 | 软件工程师 | 100000 | 旧金山 |
| 1 | 数据分析师 | 75000 | 纽约 |
| 2 | 项目经理 | 90000 | 西雅图 |

```
[In]: import pandas as pd
[In]: pandas_data1 = {'Job Title': ['Software Engineer', 'Data Analyst', 'Project Manager'],
'Salary': [100000, 75000, 90000],
'Location': ['San Francisco', 'New York', 'Seattle']}
[In]: pandas_df1 = pd.DataFrame(pandas_data1)
[In]: print(pandas_df1)
[Out]:
```

步骤 2：创建第二个 DataFrame

|   | 职位名称 | 薪资 | 地点 |
| --- | --- | --- | --- |
| 0 | 软件工程师 | 120000 | 洛杉矶 |
| 1 | 数据科学家 | 95000 | 芝加哥 |
| 2 | 项目经理 | 90000 | 波士顿 |

```
[In]: pandas_data2 = {'Job Title': ['Software Engineer', 'Data Scientist', 'Project Manager'],
'Salary': [120000, 95000, 90000],
'Location': ['Los Angeles', 'Chicago', 'Boston']}
[In]: pandas_df2 = pd.DataFrame(pandas_data2)
[In]: print(pandas_df2)
[Out]:
```

步骤 3：在“职位名称”列上执行内连接

|   | 职位名称 | 薪资 _x | 地点 _x | 薪资 _y | 地点 _y |
| --- | --- | --- | --- | --- | --- |
| 0 | 软件工程师 | 100000 | 旧金山 | 120000 | 洛杉矶 |
| 1 | 项目经理 | 90000 | 西雅图 | 90000 | 波士顿 |

```
[In]: merged_pandas_df = pd.merge(pandas_df1, pandas_df2, on='Job Title', how='inner')
[In]: print(merged_pandas_df)
[Out]:
```

在 PySpark 中，我们可以使用 `join()` 方法执行相同的操作：

步骤 1：创建第一个 DataFrame

| 职位名称 | 薪资 | 地点 |
| --- | --- | --- |
| 软件工程师 | 100000 | 旧金山 |
| 数据分析师 | 75000 | 纽约 |
| 项目经理 | 90000 | 西雅图 |

```
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.getOrCreate()
[In]: spark_data1 = {'Job Title': ['Software Engineer', 'Data Analyst', 'Project Manager'],
'Salary': [100000, 75000, 90000],
'Location': ['San Francisco', 'New York', 'Seattle']}
[In]: Spark_df1 = spark.createDataFrame(pd.DataFrame(spark_data1))
[In]: spark_df1.show()
[Out]:
```

步骤 2：创建第二个 DataFrame

| 职位名称 | 薪资 | 地点 |
| --- | --- | --- |
| 软件工程师 | 120000 | 洛杉矶 |
| 数据科学家 | 95000 | 芝加哥 |
| 项目经理 | 90000 | 波士顿 |

```
[In]: spark_data2 = {'Job Title': ['Software Engineer', 'Data Scientist', 'Project Manager'],
'Salary': [120000, 95000, 90000],
'Location': ['Los Angeles', 'Chicago', 'Boston']}
[In]: spark_df2 = spark.createDataFrame(pd.DataFrame(spark_data2))
[In]: spark_df2.show()
[Out]:
```

步骤 3：在“职位名称”列上执行内连接

| 职位名称 | 薪资 | 地点 | 薪资 | 地点 |
| --- | --- | --- | --- | --- |
| 软件工程师 | 100000 | 旧金山 | 120000 | 洛杉矶 |
| 项目经理 | 90000 | 西雅图 | 90000 | 波士顿 |

```
[In]: joined_spark_df = spark_df1.join(spark_df2, on='Job Title', how='inner')
[In]: joined_spark_df.show()
[Out]:
```

请注意，在 Pandas 中，当被合并的两个 DataFrame 中存在重叠的列名时，会自动将后缀 `_x` 和 `_y` 添加到列标签上。这有助于区分合并后的 DataFrame 中同名的列。在前面的示例中，pandas_df1 和 pandas_df2 都有一个名为“薪资”的列和一个名为“地点”的列。执行合并时，Pandas 会自动将后缀 `_x` 和 `_y` 附加到列标签上以进行区分。

然而，PySpark 并非如此。当被连接的 DataFrame 中存在重叠的列名时，它不会自动将 `_x` 和 `_y` 等后缀附加到列标签上。

## 保存数据

Pandas 和 PySpark 都提供了将 DataFrame 内容保存/写入 CSV 文件的方法。在下面的示例中，我们使用 Pandas 的 `to_csv()` 方法将一个名为 pandas_df 的 DataFrame 写入当前工作目录下名为 pandas_output.csv 的 CSV 文件。参数 `index=False` 指定行索引不应写入输出文件。如果未指定 `index` 参数或将其设置为 `True`，则行索引将作为单独的一列包含在输出文件中：

```
[In]: pandas_df.to_csv('pandas_output.csv', index=False)
```

在 PySpark 中，我们可以使用 `write()` 方法将一个名为 spark_df 的 DataFrame 写入名为 spark_output.csv 的 CSV 文件：

```
[In]: spark_df.write.csv('spark_output.csv', header=True, mode='overwrite')
```

参数 `header=True` 指定我们希望在输出文件中包含一个包含列名的标题行。如果未指定此参数或将其设置为 `False`，则输出文件将没有标题行。`mode='overwrite'` 表示如果输出文件已存在，它将被覆盖。`mode` 参数的其他可能值包括 `append`，它将数据追加到现有文件，以及 `ignore`，如果文件已存在，则不执行任何操作。

注意

在 PySpark 中写入 CSV 文件时，数据会被分割成多个 CSV 文件并写入到一个目录中。指定的输出文件名是目录的名称，而不是文件本身。



## 建模步骤

Scikit-Learn 和 PySpark 都遵循相同的基本建模步骤：数据准备、模型训练、模型评估和预测。从这里的对比可以看出，与特定步骤相关的某些类或函数名称在两个框架中可能完全相同。在此，我们介绍 Scikit-Learn 中常见的机器学习函数及其在 PySpark 中的对应项。

1.  数据准备：

    Scikit-Learn：函数包括用于将数据拆分为训练集和测试集的 `train_test_split`、用于特征缩放的 `StandardScaler`、用于对分类变量进行独热编码的 `OneHotEncoder`，以及用于处理缺失值的 `SimpleImputer`。

    PySpark：对 Spark DataFrame 的操作，例如用于拆分数据的 `randomSplit`、用于特征缩放的 `StandardScaler`、用于对分类变量进行独热编码的 `OneHotEncoder`，以及用于处理缺失值的 `Imputer`。此外，`VectorAssembler` 用于将多个特征列合并为单个向量列。

2.  模型训练：

    Scikit-Learn：模型具有用于训练的 `fit` 方法，您需要将特征（X_train）和目标变量（y_train）作为输入提供。

    PySpark：模型也具有 `fit` 方法，您需要提供一个包含特征和目标变量列的 DataFrame（train_data）。在训练模型之前，需要执行 `VectorAssembler` 步骤，将特征列合并为单个向量列。

3.  模型评估：

    Scikit-Learn：提供各种评估指标，例如用于回归的 `mean_squared_error` 和 `r2_score`，以及用于分类的 `accuracy_score`。这些指标直接应用于预测值和实际目标变量值（y_pred, y_test）。

    PySpark：可以使用 `RegressionEvaluator` 或 `BinaryClassificationEvaluator` 来计算评估指标，例如回归任务的均方误差或二分类任务的 areaUnderROC。这些评估器需要指定预测值和实际值（predictions, y_test）以及相应的列。

4.  预测：

    Scikit-Learn：模型具有用于对新数据生成预测的 `predict` 方法，您需要提供特征（X_test）并获取预测的目标变量值（y_pred）。

    PySpark：模型具有用于对新数据生成预测的 `transform` 方法，您需要提供一个包含特征的 DataFrame（test_data），并在新列（prediction）中获取预测值。在进行预测之前，需要执行 `VectorAssembler` 步骤，以确保新数据具有相同的特征向量结构。

## 流水线

流水线（一种简化和自动化数据预处理、特征工程、模型训练和评估步骤序列的方法）的概念在 Scikit-Learn 和 PySpark 中非常相似，尽管在具体语法和使用的类上存在差异。以下是这两个库处理流水线概念的方式：

1.  导入 `Pipeline` 类：

    在 Scikit-Learn 中，我们从 sklearn.pipeline 导入 `Pipeline` 类。

    在 PySpark 中，我们从 pyspark.ml 包中导入 `Pipeline` 类。

2.  定义流水线步骤：

    在 Scikit-Learn 中，我们使用元组列表来定义流水线的步骤，每个元组包含一个名称和一个转换器或估计器的实例。

    在 PySpark 中，我们使用 Transformer 和 Estimator 对象的列表来定义流水线步骤。每个对象代表流水线的一个不同阶段，例如预处理和模型训练。然而，PySpark 不像 Scikit-Learn 那样要求显式命名步骤。

3.  创建流水线：

    在 Scikit-Learn 中，我们通过将步骤列表传递给 `Pipeline` 类来创建流水线。

    在 PySpark 中，我们也通过将步骤列表传递给 `Pipeline` 类来创建流水线。

4.  拟合流水线：

    在 Scikit-Learn 中，我们通过调用流水线对象的 `fit` 方法并提供训练数据（X_train 和 y_train）来将流水线拟合到训练数据。

    在 PySpark 中，我们通过调用流水线对象的 `fit` 方法并提供训练数据来将流水线拟合到训练数据。

5.  转换与训练：

    在 Scikit-Learn 中，流水线首先应用预处理步骤，然后是模型训练步骤。它按顺序转换数据并训练模型。

    在 PySpark 中，流水线也按顺序应用转换和模型训练。流水线的每个阶段处理数据并将其传递到下一个阶段，直到最终的模型训练。

6.  进行预测：

    在 Scikit-Learn 中，我们通过调用 `predict` 方法，使用流水线对新数据（X_test）进行预测。

    在 PySpark 中，我们通过调用流水线对象的 `transform` 方法并提供新数据，使用拟合后的流水线对新数据进行预测。

现在，让我们将这些步骤转化为代码。在 Scikit-Learn 中，我们首先导入所需的库：

步骤 1：导入必要的库

```
[In]: from sklearn.pipeline import Pipeline
[In]: from sklearn.preprocessing import ...
[In]: from sklearn.feature_selection import ...
[In]: from sklearn.linear_model import ...
```

步骤 2：定义流水线步骤

```
[In]: steps = [
('preprocessing', PreprocessingStep()),
('feature_selection', FeatureSelectionStep()),
('regressor', RegressionModel())
]
```

步骤 3：创建流水线

```
[In]: pipeline = Pipeline(steps)
```

步骤 4：将流水线拟合到数据

```
[In]: pipeline.fit(X_train, y_train)
```

步骤 5：进行预测

```
[In]: y_pred = pipeline.predict(X_test)
```

在 PySpark 中，我们也从导入所需的库开始：

步骤 1：导入必要的库

```
[In]: from pyspark.ml import Pipeline
[In]: from pyspark.ml.feature import ...
[In]: from pyspark.ml.regression import ...
```

步骤 2：定义流水线阶段

```
[In]: stages = [
PreprocessingStep(),
FeatureSelectionStep(),
ModelStep()
]
```

步骤 3：创建流水线

```
[In]: pipeline = Pipeline(stages=stages)
```

步骤 4：将流水线拟合到数据

```
[In]: pipelineModel = pipeline.fit(trainData)
```

步骤 5：进行预测

```
[In]: predictions = pipelineModel.transform(testData)
```

## 总结

在本章中，我们探讨了有助于从小数据工具（Pandas 和 Scikit-Learn）平滑过渡到使用 PySpark 进行大数据处理的因素。具体来说，我们重点介绍了 PySpark 与 Pandas 之间的集成，以及 PySpark、Pandas 和 Scikit-Learn 在语法上的相似之处。我们还展示了这两个库如何使用相似的建模和流水线步骤，尽管在具体语法和使用的类上存在差异。

在下一章中，我们将开启机器学习之旅，这也是本书的核心内容。我们将介绍 k 折交叉验证，这是一种帮助我们在一系列不同算法中选择性能最佳模型的技术。使用这种方法，我们可以筛选出表现最佳的模型，以便进行进一步的评估和测试。



