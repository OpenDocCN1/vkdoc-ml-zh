# 11. 使用 Pandas、Scikit-Learn 和 PySpark 进行朴素贝叶斯分类

本章重点介绍朴素贝叶斯算法的开发、训练和评估。朴素贝叶斯分类是一种著名的监督机器学习技术，因其在分类任务中的简单性和易于实现而广受认可。它计算效率高，适用于大型数据集和实时应用。由于依赖于简单的概率计算，它也能在相对较小的数据集上表现良好。该模型的概率性质使其结果易于解释，并能理解做出特定分类决策的原因。

该算法之所以被称为“朴素”，是因为它假设特征之间相互独立。这意味着不相关的特征不会显著影响分类性能。然而，虽然这一假设使模型计算高效，但在现实世界的数据集中可能并不总是成立。在许多现实场景中，特征并非真正独立，这可能导致性能欠佳。由于独立性假设，朴素贝叶斯可能无法捕捉特征之间的复杂关系，与更复杂的算法相比，这可能导致准确率降低。

在本章中，我们将探讨朴素贝叶斯的概念及其在多分类任务中的应用。该算法估计每个标签下特征的概率分布，并利用贝叶斯定理来确定给定特征下每个标签的概率。为了实施和评估朴素贝叶斯分类器，我们使用了 Scikit-Learn 和 PySpark 库。分析将突出这两个库之间的相似性，从而实现从 Scikit-Learn 到 PySpark 的无缝过渡，并利用 PySpark 提供的分布式计算能力。此外，我们还将对比 Pandas 和 PySpark 的功能，表明 PySpark 是在大数据项目中处理大规模数据的可行选择。



## 数据集

在本节中，我们将为多分类任务创建一个模拟数据集。首先，我们生成一个适用于 Scikit-Learn 的 Pandas DataFrame，然后将其转换为 PySpark DataFrame。我们使用内置函数 `make_classification` 来创建数据集：

```
[In]: from sklearn.datasets import make_classification
[In]: import pandas as pd
[In]: import numpy as np
[In]: X, y = make_classification(
n_samples=1000,
n_features=3,
n_informative=3,
n_redundant=0,
n_clusters_per_class=1,
n_classes=3,
weights=[0.3, 0.3, 0.4],
flip_y=0.1,
random_state=42
)
[In]: X = np.abs(X)
[In]: pandas_df = pd.DataFrame(data=X, columns=['Feature 1', 'Feature 2', 'Feature 3'])
[In]: pandas_df['label'] = y
```

该数据集具有以下特征：

*   n_samples=1000：生成 1000 个样本（数据点）。

*   n_features=3：每个数据点包含三个特征。

*   n_informative=3：所有三个特征对分类任务都是信息性的（相关的）。

*   n_redundant=0：没有冗余特征。

*   n_clusters_per_class=1：每个类别形成一个单一的簇。

*   n_classes=3：指定分类问题中有三个类别。

*   weights=[0.3, 0.3, 0.4]：指定类别权重。类别 1 和类别 2 的权重各为 0.3，类别 3 的权重为 0.4。这样做是为了影响数据集中类别分布的平衡性。

*   flip_y=0.1：向类别标签中引入随机噪声，使其更接近真实场景。

`X = np.abs(X)` 这一行通过对特征矩阵 X 取绝对值，确保所有特征值均为非负数。这是为了保证特征非负，因为朴素贝叶斯假设输入为非负。最后两行创建了一个名为 pandas_df 的 Pandas DataFrame 来组织数据集。

我们可以使用 PySpark 的 `createDataFrame` 方法将 Pandas DataFrame 转换为 PySpark DataFrame。以下代码首先创建一个新的 Spark Session，然后将 pandas_df 转换为 spark_df。

```
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.getOrCreate()
[In]: spark_df = spark.createDataFrame(pandas_df)
```

现在，我们准备对这两个 DataFrame 进行一些探索性数据分析。首先，检查每个 DataFrame 的前五行：

|   | Feature 1 | Feature 2 | Feature 3 | label |
| --- | --- | --- | --- | --- |
| 0 | 0.23 | 0.39 | 0.89 | 2 |
| 1 | 0.75 | 1.75 | 3.16 | 2 |
| 2 | 0.32 | 0.09 | 0.96 | 1 |
| 3 | 4.28 | 1.59 | 1.66 | 2 |
| 4 | 0.32 | 0.74 | 1.15 | 0 |

```
[In]: print(pandas_df.head())
[Out]:
```

| Feature 1 | Feature 2 | Feature 3 | label |
| --- | --- | --- | --- |
| 0.23 | 0.39 | 0.89 | 2 |
| 0.75 | 1.75 | 3.16 | 2 |
| 0.32 | 0.09 | 0.96 | 1 |
| 4.28 | 1.59 | 1.66 | 2 |
| 0.32 | 0.74 | 1.15 | 0 |

```
[In]: spark_df.show(5)
[Out]:
```

我们可以看到，Pandas 的 `head()` 方法和 PySpark 的 `show()` 方法生成的前五行是相同的，唯一的区别是 Pandas 生成了一个索引，而 PySpark 没有。

接下来，我们使用 Pandas 的 `describe()` 方法和 PySpark 的 `summary()` 方法生成汇总统计信息：

|   | Feature 1 | Feature 2 | Feature 3 | label |
| --- | --- | --- | --- | --- |
| **count** | 1000 | 1000 | 1000 | 1000 |
| **mean** | 1.13 | 1.12 | 1.07 | 1.1 |
| **std** | 0.79 | 0.72 | 0.72 | 0.83 |
| **min** | 0.00 | 0.01 | 0.00 | 0 |
| **25%** | 0.50 | 0.60 | 0.50 | 0 |
| **50%** | 0.98 | 1.03 | 0.99 | 1 |
| **75%** | 1.61 | 1.48 | 1.52 | 2 |
| **max** | 4.28 | 3.90 | 3.95 | 2 |

```
[In]: print(pandas_df.describe())
[Out]:
```

| summary | Feature 1 | Feature 2 | Feature 3 | label |
| --- | --- | --- | --- | --- |
| **count** | 1000 | 1000 | 1000 | 1000 |
| **mean** | 1.13 | 1.12 | 1.07 | 1.1 |
| **stddev** | 0.79 | 0.72 | 0.72 | 0.83 |
| **min** | 0.00 | 0.01 | 0.00 | 0 |
| **25%** | 0.50 | 0.60 | 0.50 | 0 |
| **50%** | 0.98 | 1.03 | 0.99 | 1 |
| **75%** | 1.61 | 1.48 | 1.52 | 2 |
| **max** | 4.28 | 3.90 | 3.95 | 2 |

```
[In]: spark_df.summary().show()
[Out]:
```

两个数据集的输出都是一个包含四列（Feature 1、Feature 2、Feature 3 和 label）的汇总统计表。表中的每一行都提供了每列的具体统计信息，各列代表以下信息：

*   count：此行显示每列中非缺失值的数量。在本例中，数据集包含 1000 个数据点，所有列都有 1000 个非缺失值。

*   mean：显示每列的平均值。它表示每列中所有值的算术平均值。例如，Feature 1 的平均值约为 1.13，而 Feature 2 的平均值约为 1.12。

*   std/stddev：此行表示每列的标准差。标准差衡量的是数值围绕均值的分散程度。例如，Feature 1 的标准差约为 0.79，而 Feature 2 的标准差约为 0.72。

*   min：此行显示每列的最小值。它表示每列中存在的最小值。例如，Feature 1 的最小值为 0，而 Feature 2 的最小值为 0.01。

*   0.25、0.50、0.75：这些行表示每列数据的四分位数。0.25 行对应第一四分位数（第 25 百分位数），0.50 行对应第二四分位数（中位数或第 50 百分位数），0.75 行对应第三四分位数（第 75 百分位数）。例如，Feature 1 的第一四分位数约为 0.5，中位数为 0.98，第三四分位数为 1.61。

*   max：此行显示每列的最大值。它表示每列中存在的最大值。例如，Feature 1 的最大值为 4.28，而 Feature 2 的最大值为 3.9。

现在，让我们使用 Pandas 的 `value_counts()` 方法和 PySpark 的 `groupBy()` 与 `count()` 方法计算目标变量的分布：

| 0 | 302 |
| --- | --- |
| 1 | 296 |
| 2 | 402 |

```
[In]: print(pandas_df["label"].value_counts())
[Out]:
```

| label | count |
| --- | --- |
| 0 | 302 |
| 1 | 296 |
| 2 | 402 |

```
[In]: spark_df.groupBy("label").count().show()
[Out]:
```

从输出中可以看出，三个类别的计数总和为 1000。标签 0 和标签 1 各约占 30%，标签 2 约占总数量的 40%。这种分布使得样本相对平衡。换句话说，虽然标签 2 的比例高于标签 0 和标签 1，但整体上仍较为均衡，尤其是与某个类别在数据集中占据绝对主导地位的情况相比。

## 朴素贝叶斯分类

在本节中，我们将使用 Scikit-Learn 和 PySpark 库构建、训练并评估一个朴素贝叶斯分类器。代码使用了我们在上一节生成的分类数据集，并将其划分为训练集和测试集。训练一个朴素贝叶斯分类器，然后用于对测试集进行预测。计算包括准确率、精确率、召回率和 F1 分数在内的各种评估指标，以评估模型的性能。



## 使用 Scikit-Learn 实现朴素贝叶斯

步骤 1：导入必要的库

```
[In]: import numpy as np
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.naive_bayes import GaussianNB
[In]: from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score
```

在此步骤中，我们导入分类任务所需的基本库。其中包括用于处理数组数据的 numpy；用于将数据集拆分为训练集和测试集的 `train_test_split`；我们用于分类的高斯朴素贝叶斯分类器 `GaussianNB`；以及用于评估模型性能的 `accuracy_score`、`precision_score`、`recall_score` 和 `f1_score`。

步骤 2：生成数据

```
[In]: X, y = make_classification(
n_samples=1000,
n_features=3,
n_informative=3,
n_redundant=0,
n_clusters_per_class=1,
n_classes=3,
weights=[0.3, 0.3, 0.4],
flip_y=0.1,
random_state=42
)
[In]: X = np.abs(X)
```

此步骤已在“数据集”部分详细说明。简而言之，我们使用 Scikit-Learn 内置的 `make_classification` 函数生成分类数据集。该数据集包含 1000 个样本、3 个特征和标签。根据朴素贝叶斯算法的要求，数据为非负值。

步骤 3：准备数据

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

在此步骤中，我们使用 `train_test_split` 将生成的数据拆分为训练集和测试集。这种拆分至关重要，它使我们能用 80% 的数据（X_train, y_train）训练模型，并用剩余的 20%（X_test, y_test）评估其性能，以确保模型具有良好的泛化能力。这种 80-20 的拆分有助于我们评估模型对未见数据做出准确预测的能力。

步骤 4：训练模型

```
[In]: nb = GaussianNB()
[In]: nb.fit(X_train, y_train)
```

这里，我们创建一个高斯朴素贝叶斯分类器实例（nb），并使用 `fit()` 方法将其拟合到训练数据（X_train, y_train）上。

步骤 5：对测试数据进行预测

```
[In]: y_pred = nb.predict(X_test)
```

在此步骤中，我们使用训练好的模型（nb），通过 `predict()` 方法对测试数据（X_test）进行预测。

步骤 6：计算并打印评估指标

```
[In]: accuracy = accuracy_score(y_test, y_pred)
[In]: precision = precision_score(y_test, y_pred, average='weighted')
[In]: recall = recall_score(y_test, y_pred, average='weighted')
[In]: f1 = f1_score(y_test, y_pred, average='weighted')
[In]: print("Accuracy:", accuracy)
[In]: print("Precision:", precision)
[In]: print("Recall:", recall)
[In]: print("F1-score:", f1)
[Out]:
Accuracy: 0.44
Precision: 0.45
Recall: 0.44
F1-score: 0.43
```

在最后一步中，我们使用各种评估指标来评估模型的性能。我们使用以下方法计算每个指标：

*   准确率：使用 `accuracy_score` 方法计算
*   精确率：使用 `precision_score` 方法并采用加权平均策略计算
*   召回率：使用 `recall_score` 方法并采用加权平均策略计算
*   F1 分数：使用 `f1_score` 方法并采用加权平均策略计算

加权平均策略在计算指标时考虑了数据集中每个类别的比例，从而解决了类别不平衡问题。我们打印这些指标，以评估高斯朴素贝叶斯分类器在测试数据上的表现。

对输出结果的解释如下：

*   准确率（0.44）：准确率衡量模型预测的整体正确性。它表示正确预测的实例（包括真正例和真负例）数量与测试数据集中实例总数的比率。在本例中，准确率为 0.44，意味着模型正确预测了测试数据中 44% 的实例。

*   精确率（0.45）：精确率衡量模型做出准确正预测的能力。它是真正例预测数量与模型做出的所有正预测总数之比。计算出的精确率为 0.45，表明模型做出的正预测中有 45% 是正确的。

*   召回率（0.44）：召回率，也称为灵敏度或真正例率，衡量模型正确识别数据集中所有相关实例的能力。它是真正例预测数量与数据集中实际正实例总数之比。召回率得分为 0.44 表明模型成功识别了所有实际正实例中的 44%。

*   F1 分数（0.43）：F1 分数是精确率和召回率的加权平均值。它是一个单一指标，平衡了精确率和召回率之间的权衡。较高的 F1 分数表明精确率和召回率之间取得了更好的平衡。在本例中，F1 分数为 0.43。

总体而言，模型的性能一般，考虑到我们使用的是模拟数据集，这并不令人意外。



### 使用 PySpark 实现朴素贝叶斯

以下代码使用 PySpark 构建、训练并评估朴素贝叶斯分类器：

步骤 1：导入必要的库

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.classification import NaiveBayes
[In]: from pyspark.ml.linalg import Vectors
[In]: from sklearn.datasets import make_classification
[In]: from pyspark.ml.evaluation import MulticlassClassificationEvaluator
[In]: import numpy as np
```

在第一步中，我们导入所需的库和模块，为机器学习任务搭建环境。这些库包括：用于操作 Spark 基于 DataFrame 的 API 的 `SparkSession`；用于从 PySpark 机器学习库中使用朴素贝叶斯分类器的 `NaiveBayes`；用于在 Spark 中处理特征向量的 `Vectors`；用于生成模拟分类数据集以进行训练和测试的 `make_classification`；用于评估模型的 `MulticlassClassificationEvaluator`；以及用于处理数组的 `numpy`。

步骤 2：创建 Spark 会话

```
[In]: spark = SparkSession.builder.getOrCreate()
```

在此步骤中，使用 `SparkSession.builder` 创建一个 Spark 会话。该会话允许我们与 Spark 的 DataFrame 和机器学习 API 进行交互。

步骤 3：生成具有非负特征和三个类别的数据集

```
[In]: X, y = make_classification(
n_samples=1000,
n_features=3,
n_informative=3,
n_redundant=0,
n_clusters_per_class=1,
n_classes=3,
weights=[0.3, 0.3, 0.4],
flip_y=0.1,
random_state=42
)
[In]: X = np.abs(X)
[In]: data = [(Vectors.dense(x.tolist()), float(label)) for x, label in zip(X, y)]
[In]: spark_df = spark.createDataFrame(data, ["features", "label"])
```

此步骤在“数据集”部分已有详细解释。简而言之，我们使用 `sklearn.datasets` 中的 `make_classification` 创建一个模拟数据集。该数据集包含三个类别和非负的特征值。通过对生成的特征矩阵应用 `np.abs`，确保特征值为非负。然后，使用 `createDataFrame()` 方法将数据转换为一个包含特征和标签的 Spark DataFrame，即 `spark_df`。

步骤 4：将数据拆分为训练集和测试集

```
[In]: train_df, test_df = spark_df.randomSplit([0.8, 0.2], seed=42)
```

使用 `randomSplit` 将数据集拆分为训练集和测试集。80% 的数据分配给训练集（`train_df`），20% 的数据保留给测试集（`test_df`）。设置 `seed=42` 以确保结果可重现。

步骤 5：训练模型

```
[In]: nb = NaiveBayes(featuresCol="features", labelCol="label", modelType="multinomial")
[In]: nb_model = nb.fit(train_df)
```

在这里，我们使用 `NaiveBayes` 类创建一个专为多分类任务设计的朴素贝叶斯分类器。该分类器非常适用于需要预测多个类别（本例中为三个类别：0、1 和 2）的场景。

`featuresCol` 参数指定输入特征列，`labelCol` 参数指定目标标签列，`modelType` 参数设置为 `"multinomial"`，以表明我们处理的是一个多分类问题。

接下来，我们使用 `fit()` 方法将朴素贝叶斯模型拟合到我们的训练数据（由 `train_df` 表示）。这个过程涉及模型从训练数据中学习，以便对新的、未见过的数据进行预测。

步骤 6：对测试数据进行预测

```
[In]: predictions = nb_model.transform(test_df)
```

使用 PySpark 的 `transform()` 方法，将训练好的模型（`nb_model`）应用于测试数据（`test_df`）进行预测。这将生成一个名为 `predictions` 的 DataFrame。

步骤 7：使用各种指标评估模型

```
[In]: evaluator = MulticlassClassificationEvaluator(metricName="accuracy")
[In]: accuracy = evaluator.evaluate(predictions)
[In]: evaluator = MulticlassClassificationEvaluator(metricName="weightedPrecision")
[In]: precision = evaluator.evaluate(predictions)
[In]: evaluator = MulticlassClassificationEvaluator(metricName="weightedRecall")
[In]: recall = evaluator.evaluate(predictions)
[In]: evaluator = MulticlassClassificationEvaluator(metricName="f1")
[In]: f1 = evaluator.evaluate(predictions)
[In]: print("Accuracy:", accuracy)
[In]: print("Precision:", precision)
[In]: print("Recall:", recall)
[In]: print("F1-score:", f1)
[Out]:
Accuracy: 0.39
Precision: 0.22
Recall: 0.39
F1-score: 0.23
```

在这个（最后的）评估步骤中，计算了几个指标来评估模型的性能。我们使用 `MulticlassClassificationEvaluator` 为每个指标创建一个评估器，这些指标包括准确率、加权精确率、加权召回率和 F1 分数。

对于每个指标，评估器都会应用于模型在测试数据上生成的预测结果。然后，结果分别存储在其对应的变量中：`accuracy`、`precision`、`recall` 和 `f1`。这些指标提供了模型性能不同方面的洞察，例如正确分类实例的能力、处理类别不平衡的能力（加权指标）以及平衡精确率和召回率的能力（F1 分数）。

最后，我们打印出这些指标，以便全面了解模型在各种评估标准下的表现。

让我们理解这些指标告诉我们什么：

*   **准确率 (0.39)**：这表明模型正确预测了测试数据集中 39% 的实例。

*   **精确率 (0.22)**：精确率为 0.22 表明模型做出的所有正向预测中，只有 22% 是正确的。

*   **召回率 (0.39)**：召回率为 0.39 表明模型成功捕获了所有实际正向实例中的 39%。

*   **F1 分数 (0.23)**：F1 分数提供了精确率和召回率之间的平衡。在这种情况下，F1 分数为 0.23，表明模型在精确率和召回率之间存在权衡。

总体而言，模型的性能一般，考虑到我们使用的是模拟数据集，这在某种程度上是意料之中的。在现实场景中，我们会更深入地探究导致这种性能水平的原因。一个需要调查的潜在因素是过拟合，它通常会导致性能下降。

## 整合所有代码

在本章前面的部分中，我们分别展示了每个建模步骤的代码示例。然而，在本节中，我们的目标是将所有这些步骤中的相关代码合并到一个代码块中。这使得读者可以将代码作为一个整体单元来执行。

### Scikit-Learn

步骤 1：导入必要的库

```
[In]: import numpy as np
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.naive_bayes import GaussianNB
[In]: from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, confusion_matrix
```

步骤 2：生成数据

```
[In]: X, y = make_classification(
n_samples=1000,
n_features=3,
n_informative=3,
n_redundant=0,
n_clusters_per_class=1,
n_classes=3,  # 指定三个类别
weights=[0.3, 0.3, 0.4],  # 指定类别权重
flip_y=0.1,
random_state=42
)
[In]: X = np.abs(X)
```

步骤 3：将数据拆分为训练集和测试集

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

步骤 4：训练模型

```
# 创建一个高斯朴素贝叶斯分类器
[In]: nb = GaussianNB()
# 将模型拟合到训练数据
[In]: nb.fit(X_train, y_train)
```

步骤 5：对测试数据进行预测

```
[In]: y_pred = nb.predict(X_test)
```

步骤 6：计算并打印评估指标

```
# 计算指标
[In]: accuracy = accuracy_score(y_test, y_pred)
[In]: precision = precision_score(y_test, y_pred, average='weighted')
[In]: recall = recall_score(y_test, y_pred, average='weighted')
[In]: f1 = f1_score(y_test, y_pred, average='weighted')
[In]: print("Accuracy:", accuracy)
[In]: print("Precision:", precision)
[In]: print("Recall:", recall)
[In]: print("F1-score:", f1)
```



### PySpark

步骤 1：导入必要的库

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.classification import NaiveBayes
[In]: from pyspark.ml.linalg import Vectors
[In]: from sklearn.datasets import make_classification
[In]: from pyspark.ml.evaluation import MulticlassClassificationEvaluator, MulticlassClassificationEvaluator
[In]: import numpy as np
```

步骤 2：创建 Spark 会话

```
[In]: spark = SparkSession.builder.appName("NaiveBayesMultiClassExample").getOrCreate()
```

步骤 3：生成具有非负特征和三个类别的数据集

```
[In]: X, y = make_classification(
n_samples=1000,
n_features=3,
n_informative=3,
n_redundant=0,
n_clusters_per_class=1,
n_classes=3,
weights=[0.3, 0.3, 0.4],
flip_y=0.1,
random_state=42
)
# 确保特征值为非负
[In]: X = np.abs(X)
# 从 NumPy 数组创建 DataFrame
[In]: data = [(Vectors.dense(x.tolist()), float(label)) for x, label in zip(X, y)]
[In]: spark_df = spark.createDataFrame(data, ["features", "label"])
```

步骤 4：将数据拆分为训练集和测试集

```
[In]: train_df, test_df = spark_df.randomSplit([0.8, 0.2], seed=42)
```

步骤 5：训练模型

```
# 为多类分类创建朴素贝叶斯分类器
[In]: nb = NaiveBayes(featuresCol="features", labelCol="label", modelType="multinomial")
# 将模型拟合到训练数据
[In]: nb_model = nb.fit(train_df)
```

步骤 6：对测试数据进行预测

```
[In]: predictions = nb_model.transform(test_df)
```

步骤 7：使用各种指标评估模型

```
# 创建 MulticlassClassificationEvaluator 来评估模型
[In]: evaluator = MulticlassClassificationEvaluator(metricName="accuracy")
# 计算指标
[In]: accuracy = evaluator.evaluate(predictions)
[In]: evaluator = MulticlassClassificationEvaluator(metricName="weightedPrecision")
[In]: precision = evaluator.evaluate(predictions)
[In]: evaluator = MulticlassClassificationEvaluator(metricName="weightedRecall")
[In]: recall = evaluator.evaluate(predictions)
[In]: evaluator = MulticlassClassificationEvaluator(metricName="f1")
[In]: f1 = evaluator.evaluate(predictions)
# 打印指标
[In]: print("Accuracy:", accuracy)
[In]: print("Precision:", precision)
[In]: print("Recall:", recall)
[In]: print("F1-score:", f1)
```

## 总结

在本章中，我们介绍了朴素贝叶斯分类。我们讨论了该算法的优缺点，并演示了如何构建、训练和评估它。我们利用 Scikit-Learn 和 PySpark 库进行实现。本次分析突出了这两个库之间的相似性，使得从 Scikit-Learn 到 PySpark 的过渡变得无缝，并能利用 PySpark 提供的分布式计算能力。此外，我们对比了 Pandas 和 PySpark 的功能，展示了 PySpark 作为处理大数据项目中大规模数据的可行选择。

在下一章中，我们将介绍一种新的监督学习类型：多层感知器（MLP）。这是一种强大的机器学习方法，广泛用于分类任务。MLP 分类器因其能够捕捉数据中复杂的依赖关系和非线性关系，在解决不同领域的分类问题中展现了其有效性。

