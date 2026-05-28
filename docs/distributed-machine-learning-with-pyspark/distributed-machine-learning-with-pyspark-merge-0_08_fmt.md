# 8. 使用 Pandas、Scikit-Learn 和 PySpark 进行决策树分类

在本章中，我们将继续探讨作为监督学习形式之一的分类问题。我们的目标是开发、训练并评估一个决策树分类模型，用于根据鸢尾花的特征测量值预测其品种。我们将使用著名的鸢尾花数据集，该数据集包含三种不同鸢尾花（山鸢尾、变色鸢尾和维吉尼亚鸢尾）的四个特征测量值（花萼长度、花萼宽度、花瓣长度和花瓣宽度）。

决策树分类器具有若干优势。一个关键优势是可解释性。决策树分类器易于可视化和解释，因为树结构代表了一系列基于特征的简单决策，这使得非专业人士也能理解。另一个优势是决策树分类器能够处理非线性问题。它们能够对特征与目标变量之间的非线性关系进行建模，因此适用于复杂数据集。此外，决策树分类器不做任何假设；它们不假设数据具有特定的分布，这在数据分布未知或非标准时非常有用。最后，还有特征重要性的优势。决策树能提供关于特征在分类过程中重要性的信息，有助于特征选择和理解模型的行为。

决策树分类器相较于我们在前一章构建的逻辑回归模型，其优势在于捕捉非线性关系。逻辑回归假设特征与目标的对数几率之间存在线性关系，而决策树可以有效地对复杂的非线性交互进行建模。这使得决策树在处理那些线性模型无法充分捕捉的复杂依赖关系的数据时，成为一个强大的选择。

另一方面，决策树分类器也并非没有缺点。首先，它们容易过拟合，因为倾向于过度学习训练数据，导致在新数据上表现不佳。还存在不稳定性问题，因为数据中的微小变化可能导致树结构的显著变化，从而引起模型的不稳定。最后，决策树分类器往往对主导类别存在偏差。在数据不平衡的数据集中，决策树可能会偏向多数类，导致在少数类上的性能欠佳。然而，正如将在下一章中看到的，这些问题可以通过采用一种称为随机森林的技术得到有效缓解。

在本章中，我们将利用两个强大的库来构建决策树分类器：`Scikit-Learn` 和 `PySpark`。通过比较它们的 Python 代码，我们将突出它们之间的相似性，使数据科学家能够更轻松地从 `Scikit-Learn` 切换到 `PySpark`，并利用 `PySpark` 分布式计算能力带来的优势。

此外，我们还将展示 `Pandas` 与 `PySpark` 的可比性，特别是在数据加载和探索方面。通过演示 `Pandas` 如何执行与 `PySpark` 类似的任务，我们旨在为数据科学家提供灵活性，使他们能够根据具体需求和数据规模在这两个库之间进行选择。

到本章结束时，你将全面了解如何使用决策树模型进行分类。你将能够同时使用 `Scikit-Learn` 和 `PySpark`，实现无缝过渡，并充分利用 `PySpark` 的分布式计算能力。此外，你将深入了解 `PySpark` 如何作为 `Pandas` 的替代方案来处理更大的数据集，从而为你的数据分析工作流程提供灵活性。

## 数据集

对于本章介绍的项目，我们将使用著名的鸢尾花数据集来构建一个决策树分类器。该数据集包含四个不同属性（花萼长度、花萼宽度、花瓣长度和花瓣宽度）的测量值，涉及三种不同的鸢尾花：山鸢尾、变色鸢尾和维吉尼亚鸢尾。该数据集默认随 `Scikit-Learn` 预装，包含 150 条记录，对应每种鸢尾花各 50 个样本。

我们可以通过编写几行代码来访问鸢尾花数据集。在以下代码中，我们定义了一个名为 `create_pandas_dataframe()` 的 Python 函数，该函数使用 `Scikit-Learn` 库和 `Pandas` 从数据集中创建一个 `pandas` DataFrame：

```
[In]: from sklearn.datasets import load_iris
[In]: import pandas as pd
[In]: def create_pandas_dataframe():
        """
        从鸢尾花数据集创建一个 pandas DataFrame。
        返回:
          pandas.DataFrame: 包含特征数据、
          目标标签和目标名称的 DataFrame。
        """
        iris = load_iris()
        X = iris.data
        y = iris.target
        feature_names = iris.feature_names
        target_names = iris.target_names
        pandas_df = pd.DataFrame(X, columns=feature_names)
        pandas_df['target'] = y
        pandas_df['target_names'] = pandas_df['target'].map(
            dict(zip(range(len(target_names)), target_names)))
        return pandas_df
```

前两行代码导入了必要的库。`load_iris` 是一个允许我们加载鸢尾花数据集的函数，`pd` 是 `Pandas` 库的别名。接下来，代码定义了 `create_pandas_dataframe()` 函数，该函数不接受任何参数。在函数内部，`load_iris()` 函数加载鸢尾花数据集，其中包含特征数据、目标标签、特征名称和目标名称。然后，代码从加载的数据集中提取特征数据 (`X`) 和目标标签 (`y`)。它还提取了特征名称和目标名称。

接下来，使用提取的特征数据 (`X`) 和特征名称作为列，创建了一个名为 `pandas_df` 的 `Pandas` DataFrame。接着，将目标标签 (`y`) 作为名为 `target` 的新列添加到 DataFrame 中。此外，通过使用 `zip()` 函数创建的字典将目标标签映射到对应的目标名称，从而创建了一个名为 `target_names` 的列。最后，`create_pandas_dataframe()` 函数返回包含特征数据、目标标签和目标名称的 DataFrame。

我们可以通过以下代码行调用该函数来创建 `pandas_df` DataFrame：

```
[In]: pandas_df = create_pandas_dataframe()
```

我们可以使用以下代码将 `pandas_df` 转换为 `PySpark` DataFrame：

```
[In]: from pyspark.sql import SparkSession
[In]: def convert_to_spark_df(pandas_df):
        """
        将 pandas DataFrame 转换为 PySpark DataFrame。
        参数:
          pandas_df (pandas.DataFrame): 要转换的 pandas DataFrame。
        返回:
          pyspark.sql.DataFrame: 一个 PySpark DataFrame。
        """
        spark = \
            SparkSession.builder.appName("PandasToSpark").getOrCreate()
        spark_df = spark.createDataFrame(pandas_df)
        return spark_df
```

代码首先导入了 `SparkSession` 类，这是使用 Spark 功能的入口点。然后定义了 `convert_to_spark_df()` 函数。该函数接受一个参数 `pandas_df`，即我们要转换为 `PySpark` DataFrame 的 `pandas` DataFrame。在函数内部，使用 `SparkSession.builder.appName()` 方法创建了一个应用名称为“PandasToSpark”的 Spark Session。然后使用 Spark Session 的 `createDataFrame()` 方法将提供的 `Pandas` DataFrame (`pandas_df`) 转换为 `PySpark` DataFrame (`spark_df`)。最后，函数返回转换后的 `PySpark` DataFrame (`spark_df`)。

我们可以调用 `convert_to_spark_df()` 函数将 `pandas_df` 转换为 `spark_df`，如下所示：

```
[In]: spark_df = convert_to_spark_df(pandas_df)
```

我们可以探索两个 DataFrame（`pandas_df` 和 `spark_df`），以更深入地了解数据集的结构、内容和特征。首先，我们展示 DataFrame 的前五行。

在 `Pandas` 中，我们可以使用 `head()` 方法：

|    | 花萼长度 (cm) | 花萼宽度 (cm) | 花瓣长度 (cm) | 花瓣宽度 (cm) | 目标 | 目标名称 |
| --- | --- | --- | --- | --- | --- | --- |
| 0 | 5.1 | 3.5 | 1.4 | 0.2 | 0 | setosa |
| 1 | 4.9 | 3 | 1.4 | 0.2 | 0 | setosa |
| 2 | 4.7 | 3.2 | 1.3 | 0.2 | 0 | setosa |
| 3 | 4.6 | 3.1 | 1.5 | 0.2 | 0 | setosa |
| 4 | 5 | 3.6 | 1.4 | 0.2 | 0 | setosa |

从输出中我们可以观察到，标签已经被转换为数值格式。因此，在建模步骤中我们无需再进行标签编码。此外，所有特征都以厘米为单位，这意味着不需要进行标准化缩放，因为特征已经处于统一的尺度上。不过值得注意的是，虽然基于树的算法并不严格要求缩放，但一些实践者仍会建议进行此操作。

在 PySpark 中，我们可以使用 `show(5)` 方法来显示前五行：

| 花萼长度 (cm) | 花萼宽度 (cm) | 花瓣长度 (cm) | 花瓣宽度 (cm) | 目标 | 目标名称 |
| --- | --- | --- | --- | --- | --- |
| 5.1 | 3.5 | 1.4 | 0.2 | 0 | setosa |
| 4.9 | 3 | 1.4 | 0.2 | 0 | setosa |
| 4.7 | 3.2 | 1.3 | 0.2 | 0 | setosa |
| 4.6 | 3.1 | 1.5 | 0.2 | 0 | setosa |
| 5 | 3.6 | 1.4 | 0.2 | 0 | setosa |

```
[In]: spark_df.show(5)
[Out]:
```

`pandas_df` 和 `spark_df` 的输出是相同的，唯一的区别是 PySpark 的输出中缺少了索引。与 Pandas DataFrame 相比，PySpark 输出中缺少索引，这是由于这两个框架处理数据的方式不同。在 Pandas 中，索引作为行的唯一标识符，便于快速访问和操作。然而，PySpark DataFrame 是为分布式处理而构建的，其中单独的行索引相关性较低。

我们可以使用 Pandas 的 `shape` 属性打印 `pandas_df` 的形状（即行数和列数）：

```
[In]: print(pandas_df.shape)
[Out]: (150, 6)
```

我们可以通过以下 PySpark 代码实现相同的结果：

```
[In]: print((spark_df.count(), len(spark_df.columns)))
[Out]: (150, 6)
```

由于 PySpark 没有 `shape` 属性，我们结合了两种方法：`count()` 和 `len()` 来分别获取行数和列数。

为了进一步探索数据集，我们可以打印特征的汇总统计信息。在 Pandas 中，我们可以使用 `describe()` 方法：

|   | 花萼长度 (cm) | 花萼宽度 (cm) | 花瓣长度 (cm) | 花瓣宽度 (cm) |
| --- | --- | --- | --- | --- |
| **计数** | 150 | 150 | 150 | 150 |
| **均值** | 5.84 | 3.06 | 3.76 | 1.20 |
| **标准差** | 0.83 | 0.44 | 1.77 | 0.76 |
| **最小值** | 4.3 | 2 | 1 | 0.1 |
| **25%** | 5.1 | 2.8 | 1.6 | 0.3 |
| **50%** | 5.8 | 3 | 4.3 | 1.3 |
| **75%** | 6.4 | 3.3 | 5.1 | 1.8 |
| **最大值** | 7.9 | 4.4 | 6.9 | 2.5 |

```
[In]: pandas_df.drop(columns=['target']).describe()
[Out]:
```

使用 Pandas 的 `drop()` 方法与 `describe()` 方法结合，有效地排除了目标变量，因为包含它不会提供更多信息。回想一下，之前当我们使用 `head()` 方法检查前五行时，有两个目标变量：`target` 和 `target_names`。值得注意的是，前者是数值型的，而后者不是。Pandas 会自动忽略非数值变量；因此，在 `drop()/describe()` 代码中无需包含 `target_names`。

与 Pandas 的 `describe()` 等效的 PySpark 方法是 `summary()`。然而，PySpark 不会自动从计算中排除非数值变量，因此我们需要显式地从计算中排除 `target_names` 变量：

| 汇总 | 花萼长度 (cm) | 花萼宽度 (cm) | 花瓣长度 (cm) | 花瓣宽度 (cm) |
| --- | --- | --- | --- | --- |
| **计数** | 150 | 150 | 150 | 150 |
| **均值** | 5.84 | 3.06 | 3.76 | 1.20 |
| **标准差** | 0.83 | 0.44 | 1.77 | 0.76 |
| **最小值** | 4.3 | 2 | 1 | 0.1 |
| **25%** | 5.1 | 2.8 | 1.6 | 0.3 |
| **50%** | 5.8 | 3 | 4.3 | 1.3 |
| **75%** | 6.4 | 3.3 | 5.1 | 1.8 |
| **最大值** | 7.9 | 4.4 | 6.9 | 2.5 |

```
[In]: from pyspark.sql.functions import col
[In]: numeric_columns = [col(column) for column in spark_df.columns if column not in ['target', 'target_names']]
[In]: spark_df.select(numeric_columns).summary().show()
[Out]:
```

通过导入 `col` 函数，我们创建了一个名为 `numeric_columns` 的列引用列表。该列表包含 DataFrame 中除 `target` 和 `target_names` 列之外的所有列，这些列通过条件语句被排除。然后使用 `select()` 方法仅选择指定的数值列。接着，应用 `summary()` 方法生成汇总统计信息，包括每个选定数值列的计数、均值、标准差、最小值、第 25 百分位数、中位数、第 75 百分位数和最大值。最后使用 `show()` 方法显示这些计算出的统计信息。

我们可以观察到，Pandas 和 PySpark 的输出几乎相同，仅在两个外观方面有所不同。在 PySpark 中，统计信息列被标记为 `summary`，而在 Pandas 中则为空白。此外，PySpark 将标准差标记为 `stddev`，而 Pandas 使用 `std`。

实际上，PySpark 确实有一个类似于 Pandas 的 `describe()` 方法。然而，它在其输出中排除了四分位数（第 25、50 和 75 百分位数）。让我们执行与之前相同的代码，但这次使用 `describe()` 方法而不是 `summary()` 方法：

| 汇总 | 花萼长度 (cm) | 花萼宽度 (cm) | 花瓣长度 (cm) | 花瓣宽度 (cm) |
| --- | --- | --- | --- | --- |
| **计数** | 150 | 150 | 150 | 150 |
| **均值** | 5.84 | 3.06 | 3.76 | 1.20 |
| **标准差** | 0.83 | 0.44 | 1.77 | 0.76 |
| **最小值** | 4.3 | 2 | 1 | 0.1 |
| **最大值** | 7.9 | 4.4 | 6.9 | 2.5 |

```
[In]: from pyspark.sql.functions import col
[In]: numeric_columns = [col(column) for column in spark_df.columns if column not in ['target', 'target_names']]
[In]: spark_df.select(numeric_columns).describe().show()
[Out]:
```

当用户不需要四分位数时，`describe()` 方法可能会派上用场。

为了更深入地挖掘数据集，我们可以检查 `pandas_df` 和 `spark_df` DataFrame 中的空值。

从 Pandas 开始，我们可以使用 `isnull()` 和 `sum()` 函数：

```
[In]: print(pandas_df.isnull().sum())
[Out]:
sepal length (cm)    0
sepal width (cm)     0
petal length (cm)    0
petal width (cm)     0
target               0
target_names         0
dtype: int64
```

在 PySpark 中，我们可以使用 `isNull()` 和 `sum()` 函数：


| 花萼长度 (cm) | 花萼宽度 (cm) | 花瓣长度 (cm) | 花瓣宽度 (cm) | 目标 | 目标名称 |
| --- | --- | --- | --- | --- | --- |
| 0 | 0 | 0 | 0 | 0 | 0 |

```
[In]: from pyspark.sql.functions import col, sum
[In]: null_counts = spark_df.select([sum(col(column).isNull().cast("integer")).alias(column) for column in spark_df.columns])
[In]: null_counts.show()
[Out]:
```

PySpark 代码首先利用 `select()` 方法作用于 `spark_df` DataFrame。在该方法内部，一个列表推导式遍历 DataFrame 中的每一列，通过 `spark_df.columns` 属性访问。对于每一列，应用一系列函数：`col()` 函数引用当前列，接着使用 `isNull()` 函数判断空值。为了将生成的布尔值转换为整数（`True` 为 1，`False` 为 0），使用了 `cast("integer")`。然后使用 `sum()` 函数对这些整数求和。`alias(column)` 函数为列分配名称。最后，`show()` 方法打印结果。

Pandas 和 PySpark 的输出都表明鸢尾花数据集中没有缺失记录。

## 决策树分类

在本节中，我们将使用鸢尾花数据集构建、训练并评估一个决策树分类器。该数据集包含三种鸢尾花（山鸢尾、维吉尼亚鸢尾和变色鸢尾）各 50 个样本的四个特征（花萼和花瓣的长度与宽度）。由于这些特征采用相同的测量单位（厘米），因此无需进行缩放。无论是否缩放，决策树都能同样出色地执行。

使用鸢尾花数据集，决策树分类器将根据输入数据的特征，遵循一组规则进行预测。它从根节点开始，评估一个特征以决定遵循哪个分支。例如，如果花瓣长度小于 2.5 厘米，它可能遵循左分支；如果大于或等于 2.5 厘米，则可能遵循右分支。此过程持续进行，直到到达一个叶节点，分类器会根据到达该叶节点的训练样本的多数类别来分配一个类别标签。这个分配的类别标签即为给定输入的预测结果。

我们将使用 Scikit-Learn 和 PySpark 来构建和训练决策树分类器，并使用鸢尾花数据集评估其性能。此比较旨在帮助已经熟悉 Scikit-Learn 库的数据科学家顺利过渡到 PySpark，以便利用其提供的分布式机器学习环境。这能够处理更大规模的数据，并提高训练和评估的速度。

在开始模型构建过程之前，让我们看看 Scikit-Learn 和 PySpark 在建模步骤以及它们使用的函数和类方面有何异同。

## Scikit-Learn 与 PySpark 的相似之处

下表比较了在多类决策树分类的模型构建中，Scikit-Learn 使用的类与函数及其在 PySpark 中的对应项：

| 任务 | Scikit-Learn | PySpark |
| --- | --- | --- |
| 数据准备 |   |   |
| 数据拆分 | `train_test_split` | `randomSplit` |
| 特征缩放 | `StandardScaler` | `StandardScaler` |
| 类别编码 | `OneHotEncoder` | `OneHotEncoder` |
| 缺失值处理 | `SimpleImputer` | `Imputer` |
| 特征向量化 | 不适用 | `VectorAssembler` |
| 模型训练 |   |   |
| 模型类 | `DecisionTreeClassifier` | `DecisionTreeClassifier` |
| 训练 | `fit()` | `fit()` |
| 模型评估 |   |   |
| 准确率 | `accuracy_score` | `MultiClassificationEvaluator` |
| 精确率 | `precision_score` |
| 召回率 | `recall_score` |
| F1 分数 | `f1_score` |
| 预测 |   |   |
| 生成预测 | `predict` | `transform` |

这些比较表明，Scikit-Learn 和 PySpark 都遵循相同的建模步骤：数据准备、模型训练、模型评估和预测。在某些情况下，两个平台中函数或类的名称甚至相同。这些包括：`StandardScaler`，用于通过移除均值并缩放到单位方差来标准化特征；`OneHotEncoder`，用于将分类数据转换为独热编码特征；`DecisionTreeClassifier`，代表一个决策树分类器模型；以及 `fit()` 方法，用于训练机器学习模型。

然而，也存在显著差异。例如，Scikit-Learn 不需要 `VectorAssembler` 步骤来将特征组合成单个向量。原因在于，对于像 Scikit-Learn 这样的非分布式系统，此步骤并非必需。类似地，PySpark 的评估类 `MultiClassificationEvaluator` 旨在处理所有性能指标，这与 Scikit-Learn 不同，后者针对评估指标（如 `accuracy_score`、`precision_score`、`recall_score` 和 `f1_score`）存在独立的函数。

此外，Scikit-Learn 提供了一个名为 `confusion_matrix` 的专用函数来计算混淆矩阵，而在 PySpark 中，此计算通常手动执行。不过，存在一个 RDD 类（`MulticlassMetrics`）可以自动计算混淆矩阵以及所有其他评估指标（精确率、召回率、F1 分数），如下所示：

```
[In]: from pyspark.mllib.evaluation import MulticlassMetrics
[In]: metrics = MulticlassMetrics(predictions)
[In]: precision = metrics.precision()
[In]: recall = metrics.recall()
[In]: f1_score = metrics.fMeasure()
[In]: confusion_matrix = metrics.confusionMatrix().toArray()
```

然而，在本书中，我们的重点是 ML 库而非 RDD 的 mllib，因为 RDD 使用较少，并且可能在某个时候被弃用。

在本节的后续小节中，我们将把这些建模步骤转化为代码。值得注意的是，我们使用默认超参数构建模型，这意味着 Scikit-Learn 和 PySpark 的结果可能会有所不同。我们将在第 16 章中演示如何微调模型的超参数。

### 使用 Scikit-Learn 进行决策树分类

在本小节中，我们将编写 Scikit-Learn 代码来构建、训练和评估决策树分类模型的性能，并利用该模型根据鸢尾花的特征测量值预测其品种。我们遵循的步骤与之前描述的相同：数据准备、模型训练、评估和预测。然而，在这些步骤之前，我们需要导入必要的库并创建一个包含鸢尾花数据的 DataFrame。

**步骤 1：导入必要的库**

在此步骤中，我们导入必要的库来加载鸢尾花数据、将数据拆分为训练集和测试集、构建决策树分类器并计算准确率分数：

```
[In]: from sklearn.datasets import load_iris
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.tree import DecisionTreeClassifier
[In]: from sklearn.metrics import accuracy_score
```

**步骤 2：读取数据**

在此步骤中，我们定义一个函数来加载并返回鸢尾花数据集的特征（`X`）和标签（`y`）。

```
[In]: def load_iris_dataset():
         iris = load_iris()
         X = iris.data
         y = iris.target
         return X, y
```

**步骤 3：数据准备**

在此步骤中，我们定义一个函数将数据拆分为训练集和测试集。

```
[In]: def split_data(X, y, test_size=0.2, random_state=42):
         X_train, X_test, y_train, y_test = train_test_split(X, y,
                                   test_size=test_size, random_state=random_state)
         return X_train, X_test, y_train, y_test
```

**步骤 4：模型训练**

在此步骤中，我们定义一个函数，使用提供的特征（`X`）和标签（`y`）构建并返回一个决策树分类器。


# 使用决策树分类器处理鸢尾花数据集

本文档详细介绍了如何使用决策树分类器对鸢尾花数据集进行分类，涵盖了从数据加载、模型构建到评估分析的完整流程。

## 代码定义

### 构建决策树
以下函数定义了决策树分类器的构建过程。

```python
[In]: def build_decision_tree(X, y):
clf = DecisionTreeClassifier()
clf.fit(X, y)
return clf
```

### 模型评估
此步骤中的代码定义了一个函数，用于评估并返回决策树分类器（`clf`）在测试特征（`X_test`）和标签（`y_test`）上的准确率分数。

```python
[In]: def evaluate_model(clf, X_test, y_test):
y_pred = clf.predict(X_test)
accuracy = accuracy_score(y_test, y_pred)
return accuracy
```

## 数据加载与模型构建

此步骤包含多个子步骤，用于调用先前定义的函数。

### 加载鸢尾花数据集
```python
[In]: X, y = load_iris_dataset()
```

### 拆分数据集
使用 `split_data` 函数将数据集拆分为训练集和测试集。
```python
[In]: X_train, X_test, y_train, y_test = split_data(X, y)
```

### 构建模型
使用训练数据构建决策树模型。
```python
[In]: clf = build_decision_tree(X_train, y_train)
```

## 模型评估与结果分析

### 在测试数据上评估模型
```python
[In]: accuracy = evaluate_model(clf, X_test, y_test)
```

### 打印准确率
```python
[In]: print("Accuracy:", accuracy)
[Out]: 1.00
```

## 代码逐步解析

### 步骤 1：导入必要的库
在此步骤中，代码导入了多个模块和类，用于准备数据、构建和训练模型、评估其性能以及对测试数据进行预测。

代码首先导入 `load_iris` 函数以加载鸢尾花数据集。然后引入 `train_test_split` 函数，该函数用于将数据集拆分为训练子集和测试子集。这是为了确保模型的性能是在其训练期间未见过的数据上进行评估的。下一个导入语句引入了 `DecisionTreeClassifier` 类，该类代表决策树分类器。最后一个导入语句引入了 `accuracy_score` 函数，该函数计算分类模型预测的准确率。它将预测标签与实际标签进行比较，并返回正确预测实例的比例。

### 步骤 2：读取数据
在此步骤中，代码定义了一个函数来加载并返回鸢尾花数据集。在函数内部，代码使用 `load_iris` 函数加载鸢尾花数据集，包括特征和目标标签。然后，它将鸢尾花数据集的特征数据和目标标签分别赋值给变量 `X` 和 `y`。该函数最终将特征矩阵 `X` 和目标数组 `y` 作为元组返回。

### 步骤 3：数据准备
在此步骤中，代码定义了一个名为 `split_data` 的函数，用于将数据集拆分为训练集和测试集。它接受以下参数：

*   `X`：数据集的特征数据（矩阵）。
*   `y`：数据集的目标标签（数组）。
*   `test_size`：要包含在测试拆分中的数据集比例（默认为 0.2，即 20%）。
*   `random_state`：随机数生成器的种子（默认为 42）。它确保拆分的可重复性。

函数内部的行使用 `train_test_split` 函数将数据拆分为训练集和测试集。它以特征矩阵 `X` 和目标标签 `y` 作为输入。`test_size` 参数决定应分配给测试的数据比例，此处为 0.2 或 20%。`random_state` 参数设置拆分过程中数据随机打乱的种子。

该函数返回四个变量：

*   `X_train`：训练集的特征数据
*   `X_test`：测试集的特征数据
*   `y_train`：训练集的目标标签
*   `y_test`：测试集的目标标签

最后，该函数将这四个集合（训练和测试的特征数据及标签）作为元组返回。

### 步骤 4：模型训练
此步骤中的代码定义了一个名为 `build_decision_tree` 的函数，用于使用特征 `X` 和标签 `y` 构建并返回一个决策树分类器。该函数接受两个参数：

*   `X`：数据集的特征数据（矩阵）
*   `y`：数据集的目标标签（数组）

函数内部的第一行创建了一个 `DecisionTreeClassifier` 类的实例。这代表一个基于决策树的分类模型。分类器使用其默认设置进行初始化，我们可以通过向此构造函数传递各种参数来自定义其行为。我们将在第 16 章中展示如何自定义模型的参数。

下一行在提供的特征矩阵 `X` 和目标标签 `y` 上训练决策树分类器。`fit` 方法学习数据中的模式和关系，使分类器能够基于所学知识进行预测。

最后，该函数将训练好的决策树分类器（`clf`）作为输出返回。这使我们能够使用训练好的分类器对新的、未见过的数据进行预测。

训练好模型后，我们可以检查哪些特征驱动了模型的预测：

```python
[In]: feature_names = ['sepal length', 'sepal width', 'petal length', 'petal width']
[In]: feature_importances = clf.feature_importances_
[In]: importances_with_names = list(zip(feature_names, feature_importances))
[In]: importances_with_names_sorted = sorted(importances_with_names, key=lambda x: x[1], reverse=True)
[In]: for name, importance in importances_with_names_sorted:
print(f"{name}: {importance:.4f}")
[Out]:
petal length: 0.9061
petal width: 0.0772
sepal length: 0.0167
sepal width: 0.0000
```

我们可以用一行代码打印特征重要性：

```python
[In]: clf.feature_importances_
```

然而，我们需要添加特征名称并按降序对重要性进行排序。代码首先定义一个列表 `feature_names`，其中包含鸢尾花数据集中四个特征的名称。然后，它提取由训练好的决策树分类器 `clf` 计算出的特征重要性。属性 `feature_importances_` 提供了分类器分配给每个特征的重要性分数。

进入下一步，代码使用 `zip` 函数将 `feature_names` 列表和 `feature_importances` 数组组合成一个元组列表。然后，代码使用 `sorted` 函数，并设置 `reverse=True` 参数，根据重要性分数（其中 `x[1]` 指每个元组的第二个元素，即重要性分数）对元组列表（`importances_with_names`）进行降序排序。

接下来的步骤涉及一个循环，该循环遍历排序后的元组列表 `importances_with_names_sorted`，并将每个元组解包为变量 `name` 和 `importance`。在循环内部，有一行代码打印特征名称（`name`）及其对应的重要性分数（`importance`）。

输出结果显示了每个特征对分类器预测的相对影响。重要性分数较高的特征对模型的决策过程贡献更大，而分数较低的特征影响相对较小。在我们的模型中，花瓣长度和宽度对鸢尾花数据集的决策树分类器决策起着更关键的作用。花萼宽度的分数为 0，表示它是冗余的，对预测没有贡献。

我们还可以查看实际值与预测值之间的样本对比：

|   | 实际值 | 预测值 |
|---|--------|--------|
| 0 | 1      | 1      |
| 1 | 0      | 0      |
| 2 | 2      | 2      |
| 3 | 1      | 1      |
| 4 | 1      | 1      |

```python
[In]: import pandas as pd
[In]: y_pred_test = clf.predict(X_test)
[In]: comparison_df = pd.DataFrame({'Actual': y_test[:5], 'Predicted': y_pred_test[:5]})
[In]: print(comparison_df)
[Out]:
```

输出结果表明模型已正确分类了每个品种，因为所有五个预测值都与实际标签值匹配。这导致了 100% 的准确率。然而，为了更全面地了解模型性能，有必要使用整个样本点集来计算整体准确率。

### 步骤 5：模型评估
此步骤中的代码定义了一个名为 `evaluate_model` 的函数，用于评估训练好的分类器模型在测试数据集上的性能并计算其准确率。该函数接受三个参数：

*   `clf`：要评估的训练好的分类器模型
*   `X_test`：测试数据集的特征数据（矩阵）
*   `y_test`：测试数据集的真实目标标签（数组）

## 模型评估

函数内部的第一行使用分类器`clf`对测试数据集的特征矩阵`X_test`进行标签预测。分类器的`predict`方法将特征数据作为输入，并返回给定数据的预测标签。

下一行使用`accuracy_score`函数计算预测标签（`y_pred`）与真实标签（`y_test`）相比的准确率。`accuracy_score`函数衡量预测标签中正确预测相对于真实标签的比例。

最后，该函数将计算出的准确率作为输出返回。准确率分数表示训练好的分类器在测试数据上的表现如何。

### 步骤 6：使用函数

在此步骤中，我们主要使用之前定义的函数。在此步骤的代码序列中，使用`load_iris_dataset`函数加载鸢尾花数据集。然后使用`split_data`函数将数据集分割为训练集和测试集，默认测试集大小为 20%。使用`build_decision_tree`函数利用训练数据构建决策树分类器。使用`evaluate_model`函数评估分类器在测试数据上的准确率，该函数使用`accuracy_score`计算准确率。最后，打印模型在测试数据上的准确率，得到 1.00（或 100%）的准确率，表明在此特定执行中达到了完美拟合。

## 使用 PySpark 进行决策树分类

在本小节中，我们将提供与上一小节中 Scikit-Learn 代码等效的 PySpark 代码。我们将准备鸢尾花数据集，并使用它来构建、训练和评估一个决策树分类器，该分类器将用于根据鸢尾花的特征测量值预测其品种。我们从导入必要的库开始。

### 步骤 1：导入必要的库

```
[In]: import pandas as pd
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.feature import VectorAssembler
[In]: from pyspark.ml.classification import DecisionTreeClassifier
[In]: from pyspark.ml.evaluation import MulticlassClassificationEvaluator
[In]: from sklearn.datasets import load_iris
```

### 步骤 2：创建 PySpark DataFrame

此步骤包含两个子步骤：加载数据集并将其转换为 PySpark。

#### 2.1. 加载鸢尾花数据集并将其作为 Pandas DataFrame 返回

```
[In]: def load_iris_dataset():
iris = load_iris()
X = iris.data
y = iris.target
feature_names = iris.feature_names
target_names = iris.target_names
pandas_df = pd.DataFrame(X, columns=feature_names)
pandas_df['label'] = y
return pandas_df
```

#### 2.2. 使用 SparkSession (`spark`) 将 Pandas DataFrame 转换为 Spark DataFrame

```
[In]: def convert_to_spark_df(pandas_df, spark):
return spark.createDataFrame(pandas_df)
```

### 步骤 3：数据准备

此步骤包含两个子步骤：将特征组合成单个向量，以及将数据分割为训练集和测试集。

#### 3.1. 准备特征向量

```
[In]: def prepare_feature_vector(spark_df):
assembler = VectorAssembler(inputCols=spark_df.columns[:-1],
outputCol="features")
transformed_data = assembler.transform(spark_df)
return transformed_data
```

#### 3.2. 将数据集分割为训练集和测试集

```
[In]: def split_data(transformed_data, train_ratio=0.8, seed=42):
train_data, test_data =
transformed_data.randomSplit([train_ratio, 1 - train_ratio],
seed=seed)
return train_data, test_data
```

### 步骤 4：模型训练

在此步骤中，我们使用提供的训练数据（Spark DataFrame）构建并训练一个决策树分类器。

```
[In]: def build_decision_tree(train_data):
dt = DecisionTreeClassifier(labelCol="label",
featuresCol="features")
model = dt.fit(train_data)
return model
```

### 步骤 5：模型评估

在此步骤中，我们使用测试数据评估`DecisionTreeClassifier`。

```
[In]: def evaluate_model(model, test_data):
predictions = model.transform(test_data)
evaluator =
MulticlassClassificationEvaluator(labelCol="label",
predictionCol="prediction", metricName="accuracy")
accuracy = evaluator.evaluate(predictions)
return accuracy, predictions
```

### 步骤 6：使用函数

在此步骤中，我们使用之前定义的函数。我们从创建 SparkSession 开始。

#### 6.1. 创建 SparkSession

```
[In]: spark = SparkSession.builder.appName("IrisDecisionTree").getOrCreate()
```

#### 6.2. 加载鸢尾花数据集并将其转换为 Pandas DataFrame

```
[In]: pandas_df = load_iris_dataset()
```

#### 6.3. 将 Pandas DataFrame 转换为 PySpark DataFrame

```
[In]: spark_df = convert_to_spark_df(pandas_df, spark)
```

#### 6.4. 准备特征向量

```
[In]: transformed_data = prepare_feature_vector(spark_df)
```

#### 6.5. 将数据集分割为训练集和测试集

```
[In]: train_data, test_data = split_data(transformed_data)
```

#### 6.6. 构建决策树模型

```
[In]: model = build_decision_tree(train_data)
```

#### 6.7. 评估模型并获取预测结果

```
[In]: accuracy, predictions = evaluate_model(model, test_data)
```

#### 6.8. 打印准确率

```
[In]: print("Accuracy:", accuracy)
[Out]: 0.94
```

让我们总结一下代码在每个步骤中的作用：

### 步骤 1：导入必要的库

在此步骤中，我们导入代码中将要使用的必要库和模块。这些库包括 Pandas、用于分布式数据处理的 PySpark，以及用于机器学习任务（如创建特征向量和训练决策树分类器）的特定模块。我们还从 Scikit-Learn 导入了`load_iris`函数，用于加载鸢尾花数据集。

### 步骤 2：创建 PySpark DataFrame

此步骤包含两个子步骤：

#### 2.1. 加载鸢尾花数据集并以 Pandas DataFrame 形式返回

这里定义了一个自定义函数`load_iris_dataset`。该函数使用 Scikit-Learn 的`load_iris`函数加载鸢尾花数据集，并提取特征数据 (`X`)、目标标签 (`y`) 以及其他相关信息，如特征名称和目标名称。然后，它创建一个包含数据和标签的 Pandas DataFrame 并返回。

#### 2.2. 使用 SparkSession (`spark`) 将 Pandas DataFrame 转换为 Spark DataFrame

在此子步骤中，定义了另一个自定义函数`convert_to_spark_df`。它接收Pandas DataFrame作为输入参数，以及一个SparkSession (`spark`)，并使用`spark.createDataFrame`方法将Pandas DataFrame转换为Spark DataFrame。生成的Spark DataFrame将用于PySpark中的后续处理。

## 步骤 3：数据准备

此步骤包含两个子步骤：

### 3.1. 准备特征向量

这里定义了一个自定义函数`prepare_feature_vector`。该函数接收一个Spark DataFrame作为输入(`spark_df`)，并通过使用PySpark的`VectorAssembler`将特征列组合成一个向量列来准备特征向量。结果是一个包含特征向量列的新DataFrame。

### 3.2. 将数据集拆分为训练集和测试集

在此子步骤中，定义了一个自定义函数`split_data`。它接收转换后的数据（包含特征向量的DataFrame）作为输入，以及训练集比例和随机种子的参数。该函数使用PySpark提供的`randomSplit`方法将数据拆分为训练集和测试集。

## 步骤 4：模型训练

在此步骤中，定义了一个自定义函数`build_decision_tree`。它接收训练数据作为输入，并使用PySpark机器学习库(`pyspark.ml`)中的`DecisionTreeClassifier`构建一个决策树分类器。返回训练好的模型。

决策树的一个重要特性是，一旦模型训练完成，我们可以提取特征重要性，以识别驱动预测的特征以及冗余特征。我们可以使用`featureImportances`属性来实现此任务。

在以下代码中，我们为该属性添加了几行代码，以将特征名称附加到特征重要性上，并按降序排序：

```
[In]: feature_importances = model.featureImportances.toArray()
[In]: feature_names = spark_df.columns[:-1]
[In]: feature_importance_tuples = [(feature_name, importance) for feature_name, importance in zip(feature_names, feature_importances)]
[In]: sorted_feature_importance_tuples = sorted(feature_importance_tuples, key=lambda x: x[1], reverse=True)
[In]: for feature_name, importance in sorted_feature_importance_tuples:
    print(f"{feature_name}: {importance}")
[Out]:
petal length (cm): 0.52
petal width (cm): 0.50
sepal length (cm): 0.0
sepal width (cm): 0.0
```

在上述代码中，我们首先获取特征重要性及其对应的名称，创建一个将每个特征与其重要性分数配对的元组列表。接下来，我们根据重要性分数按降序对该列表进行排序，从而能够识别出最具影响力的特征。最后，我们打印排序后的特征重要性。

输出结果表明，花瓣的长度和宽度是模型预测的主要驱动力。在PySpark模型中，花萼的长度和宽度是冗余的，因为它们的重要性分数均为0。

## 步骤 5：模型评估

在此步骤中，定义了一个自定义函数`evaluate_model`。它接收训练好的模型和测试数据作为输入。该函数使用模型进行预测，并使用PySpark的`MulticlassClassificationEvaluator`评估其性能。它返回模型的准确率和预测结果。

在步骤 6 中调用这些函数后，我们得到一个输出，显示准确率为 94%。这表明决策树模型在测试数据上进行正确预测方面相当有效。它正确分类了绝大多数实例的目标变量，这通常是模型预测能力良好的标志。

值得注意的是，PySpark模型的准确率低于Scikit-Learn的100%。这种差异是因为我们使用默认超参数构建模型，而这两个框架具有不同的默认设置。我们将在第16章中探讨如何自定义算法超参数。

## 整合所有步骤

在本章前面的部分中，我们提供了独立演示每个建模步骤的代码。然而，在本节中，我们的目标是将所有这些步骤中的相关代码合并到一个代码块中。这使得数据科学家能够将代码作为一个整体来执行。

### Scikit-Learn

```
[In]: from sklearn.datasets import load_iris
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.tree import DecisionTreeClassifier
[In]: from sklearn.metrics import accuracy_score
[In]: def load_iris_dataset():
        """
        加载并返回鸢尾花数据集的特征 (X) 和标签 (y)。
        返回:
            X (numpy.ndarray): 鸢尾花数据集的特征矩阵。
            y (numpy.ndarray): 鸢尾花数据集的目标标签。
        """
        iris = load_iris()
        X = iris.data
        y = iris.target
        return X, y
[In]: def split_data(X, y, test_size=0.2, random_state=42):
        """
        将数据拆分为训练集和测试集。
        参数:
            X (numpy.ndarray): 数据集的特征矩阵。
            y (numpy.ndarray): 数据集的目标标签。
            test_size (float): 包含在测试集中的数据比例 (默认=0.2)。
            random_state (int): 随机数生成的种子 (默认=42)。
        返回:
            X_train (numpy.ndarray): 训练集的特征矩阵。
            X_test (numpy.ndarray): 测试集的特征矩阵。
            y_train (numpy.ndarray): 训练集的目标标签。
            y_test (numpy.ndarray): 测试集的目标标签。
        """
        X_train, X_test, y_train, y_test = train_test_split(X, y,
                                                            test_size=test_size, random_state=random_state)
        return X_train, X_test, y_train, y_test
[In]: def build_decision_tree(X, y):
        """
        使用提供的特征 (X) 和标签 (y) 构建并返回一个决策树分类器。
        参数:
            X (numpy.ndarray): 数据集的特征矩阵。
            y (numpy.ndarray): 数据集的目标标签。
        返回:
            clf (DecisionTreeClassifier): 训练好的决策树分类器。
        """
        clf = DecisionTreeClassifier()
        clf.fit(X, y)
        return clf
[In]: def evaluate_model(clf, X_test, y_test):
        """
        评估并返回提供的分类器 (clf) 在测试特征 (X_test) 和标签 (y_test) 上的准确率分数。
        参数:
            clf (DecisionTreeClassifier): 训练好的决策树分类器。
            X_test (numpy.ndarray): 测试集的特征矩阵。
            y_test (numpy.ndarray): 测试集的目标标签。
        返回:
            accuracy (float): 分类器在测试数据上的准确率分数。
        """
        y_pred = clf.predict(X_test)
        accuracy = accuracy_score(y_test, y_pred)
        return accuracy
[In]: X, y = load_iris_dataset()
[In]: X_train, X_test, y_train, y_test = split_data(X, y)
[In]: clf = build_decision_tree(X_train, y_train)
[In]: accuracy = evaluate_model(clf, X_test, y_test)
[In]: print("Accuracy:", accuracy)
```

### PySpark

# 决策树分类模型实战：鸢尾花数据集

```python
import pandas as pd
from pyspark.sql import SparkSession
from pyspark.ml.feature import VectorAssembler
from pyspark.ml.classification import DecisionTreeClassifier
from pyspark.ml.evaluation import MulticlassClassificationEvaluator
from sklearn.datasets import load_iris
```

```python
def load_iris_dataset():
    """
    加载鸢尾花数据集，并将其作为 Pandas DataFrame 返回。
    返回:
        pandas.DataFrame: 作为 Pandas DataFrame 的鸢尾花数据集。
    """
    iris = load_iris()
    X = iris.data
    y = iris.target
    feature_names = iris.feature_names
    target_names = iris.target_names
    pandas_df = pd.DataFrame(X, columns=feature_names)
    pandas_df['label'] = y
    return pandas_df
```

```python
def convert_to_spark_df(pandas_df, spark):
    """
    使用提供的 SparkSession 将 Pandas DataFrame 转换为 Spark DataFrame。
    参数:
        pandas_df (pandas.DataFrame): 输入的 Pandas DataFrame。
        spark (pyspark.sql.SparkSession): SparkSession。
    返回:
        pyspark.sql.DataFrame: 作为 Spark DataFrame 的输入数据。
    """
    return spark.createDataFrame(pandas_df)
```

```python
def prepare_feature_vector(spark_df):
    """
    为 Spark DataFrame 准备特征向量。
    参数:
        spark_df (pyspark.sql.DataFrame): 输入的 Spark DataFrame。
    返回:
        pyspark.sql.DataFrame: 包含特征向量列的 Spark DataFrame。
    """
    assembler = VectorAssembler(inputCols=spark_df.columns[:-1],
                                outputCol="features")
    transformed_data = assembler.transform(spark_df)
    return transformed_data
```

```python
def split_data(transformed_data, train_ratio=0.8, seed=42):
    """
    将数据集拆分为训练集和测试集。
    参数:
        transformed_data (pyspark.sql.DataFrame): 包含特征向量的 Spark DataFrame。
        train_ratio (float, 可选): 用于训练的数据比例。默认值为 0.8。
        seed (int, 可选): 用于结果可复现的随机种子。默认值为 42。
    返回:
        Tuple[pyspark.sql.DataFrame, pyspark.sql.DataFrame]: 包含训练集和测试集 Spark DataFrame 的元组。
    """
    train_data, test_data = transformed_data.randomSplit([train_ratio, 1 - train_ratio], seed=seed)
    return train_data, test_data
```

```python
def build_decision_tree(train_data):
    """
    使用提供的训练数据构建决策树分类器。
    参数:
        train_data (pyspark.sql.DataFrame): 作为 Spark DataFrame 的训练数据。
    返回:
        pyspark.ml.classification.DecisionTreeClassificationModel: 训练好的决策树模型。
    """
    dt = DecisionTreeClassifier(labelCol="label", featuresCol="features")
    model = dt.fit(train_data)
    return model
```

```python
def evaluate_model(model, test_data):
    """
    使用提供的测试数据评估给定的决策树模型。
    参数:
        model (pyspark.ml.classification.DecisionTreeClassificationModel): 训练好的决策树模型。
        test_data (pyspark.sql.DataFrame): 作为 Spark DataFrame 的测试数据。
    返回:
        Tuple[float, pyspark.sql.DataFrame]: 包含准确率和预测结果的元组。
    """
    predictions = model.transform(test_data)
    evaluator = MulticlassClassificationEvaluator(labelCol="label", predictionCol="prediction", metricName="accuracy")
    accuracy = evaluator.evaluate(predictions)
    return accuracy, predictions
```

```python
spark = SparkSession.builder.appName("IrisDecisionTree").getOrCreate()
pandas_df = load_iris_dataset()
spark_df = convert_to_spark_df(pandas_df, spark)
transformed_data = prepare_feature_vector(spark_df)
train_data, test_data = split_data(transformed_data)
model = build_decision_tree(train_data)
accuracy, predictions = evaluate_model(model, test_data)
print("Accuracy:", accuracy)
```

## 总结

在本章中，我们继续探讨了作为监督学习形式的分类问题。我们使用了 Scikit-Learn 和 PySpark 机器学习库，演示了如何创建、训练和评估一个决策树分类模型，以根据特征测量值预测鸢尾花的种类。我们还比较了 `Pandas` 和 `PySpark` 在加载和探索数据方面的用法。

我们还探讨了决策树分类器的优势和局限性。与逻辑回归等线性模型相比，这些模型擅长捕捉非线性关系，因此对于复杂数据集非常有价值。然而，它们容易过拟合，对数据变化敏感，并且在数据不平衡的情况下可能表现出类别偏差。在下一章中，我们将介绍随机森林的概念来解决这些问题。