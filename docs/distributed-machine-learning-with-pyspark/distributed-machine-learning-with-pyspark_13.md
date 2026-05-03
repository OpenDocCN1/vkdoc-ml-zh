# 13. 使用 Pandas、Surprise 和 PySpark 构建推荐系统

在本章中，我们将探索监督学习的一个新领域——推荐系统。尽管推荐系统属于监督学习范畴，但它们通常既不属于回归（第 3 章至第 6 章），也不属于分类（第 7 章至第 12 章）。它们被视为机器学习中一个独立的领域，称为协同过滤。

虽然推荐系统涉及预测评分或偏好，但它们与传统回归任务不同，因为输出并非连续的数值。它们也不同于分类任务，因为不涉及对实例进行分类或分配离散标签。相反，它们专注于基于用户与物品的交互生成个性化推荐。换句话说，其目标是推荐用户可能感兴趣但尚未自行发现的物品或内容。

推荐系统有多种类型，包括以下几种：

*   **基于内容的过滤**：这种方法推荐与用户过去喜欢或互动过的物品相似的物品。它分析物品的特征或属性，并推荐具有相似属性的物品。例如，如果用户之前喜欢动作片，基于内容的推荐系统可能会向该用户推荐其他动作片。

*   **协同过滤**：这种方法基于相似用户的偏好来推荐物品。它寻找用户行为中的模式和相似性，并基于这些模式做出推荐。协同过滤可进一步分为两种类型：
    *   **基于用户的协同过滤**：该方法寻找具有相似偏好的用户，并推荐这些相似用户喜欢或互动过的物品。

*   **基于物品的协同过滤**：该方法识别与用户喜欢的物品相似的物品，并推荐这些相似物品。

*   **混合推荐系统**：这些系统结合多种方法，以利用不同方法的优势。例如，混合系统可能先使用基于内容的过滤进行初步推荐，然后使用协同过滤技术进行优化。

在本章中，我们将使用协同过滤为亚马逊电子产品开发一个推荐系统。为了实现该算法，我们采用矩阵分解技术，该技术常用于协同过滤推荐系统。在矩阵分解中，用户与物品的交互被建模为一个矩阵。

我们使用 Surprise 和 PySpark 库来实现推荐系统。由于 Scikit-Learn 没有内置构建推荐系统的功能，我们选择使用 Python 的 Surprise 库。Surprise 与 Scikit-Learn 类似，专为中小型数据集设计，与 PySpark 不同，它通常不用于大规模大数据场景。Scikit-Learn 和 Surprise 都非常适合用户可以在单台机器上轻松将整个数据集加载到内存中的任务。

在 Surprise 中，我们使用 SVD（奇异值分解），而在 PySpark 中，我们使用 ALS（交替最小二乘法），两者都基于矩阵分解技术。在 SVD 中，目标是将该矩阵分解为两个低秩矩阵，通常称为用户矩阵和物品矩阵；而在 ALS 中，目标是通过在更新用户矩阵和物品矩阵之间迭代交替，以最小化观测评分与预测评分之间的重构误差，从而求解用户和物品的潜在因子。两者都是强大的库，但 ALS 在协同过滤推荐系统中更常用，因为它能够处理大规模数据集且具有可并行化的特性。

此外，我们还利用 Pandas 和 PySpark 进行数据处理和探索。通过比较 Pandas 和 PySpark 库中的实现，我们可以深入了解它们的相似之处。这有助于旨在从 Pandas 迁移到 PySpark 的数据科学家，使他们能够利用后者强大的分布式计算 API。



## 数据集

本项目使用的数据集是一个开源数据集，包含加州大学圣地亚哥分校计算机科学系提供的各种亚马逊电子产品评分，可通过以下链接获取：

URL: [`http://jmcauley.ucsd.edu/data/amazon/`](http://jmcauley.ucsd.edu/data/amazon/)

贡献者：Julian McAuley

我们首先从 GitHub 上存储了该数据集副本的位置，将 CSV 文件读取到 Pandas DataFrame 中：

```
[In]: import pandas as pd
[In]: column_names = ['user_id', 'product_id', 'rating', 'timestamp']
[In]: pandas_df = pd.read_csv(
'https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/amazon_electronics.csv', names=column_names)
```

我们首先将 Pandas 库导入为 pd，然后定义一个名为 `column_names` 的列表，其中包含 DataFrame 的列名。接着，我们使用 `read_csv()` 方法读取 CSV 文件，并将生成的 DataFrame 赋值给变量 `pandas_df`。

现在，我们应该能够通过首先查看 DataFrame 的形状来探索数据集：

```
[In]: print(pandas_df.shape)
[Out]: (1048576, 4)
```

输出结果显示这是一个大型数据集，包含超过 100 万行（1,048,576）和 4 列。我们可以通过使用 Pandas 的 `columns` 属性来打印这些列的名称：

```
[In]: print(pandas_df.columns)
[Out]: Index(['user_id', 'product_id', 'rating', 'timestamp'], dtype='object')
```

这四列的名称分别是 `user_id`、`product_id`、`rating` 和 `timestamp`。以下是每列的说明：

*   `user_id`：表示唯一的用户 ID。
*   `product_id`：表示产品的唯一 ID。
*   `rating`：表示用户对产品的评分。
*   `timestamp`：表示用户对产品进行评分的时间。此列与本项目无关。

我们可以通过使用 Pandas 的 `head()` 方法显示每列的前五行：

```
[In]: print(pandas_df.head())
[Out]:
user_id product_id  rating   timestamp
0   AKM1MP6P0OYPR  132793040       5  1365811200
1  A2CX7LUOHB2NDG  321732944       5  1341100800
2  A2NWSAGRHCP8N5  439886341       1  1367193600
3  A2WNBOD3WNDNKT  439886341       3  1374451200
4  A1GI0U4ZRJA8WN  439886341       1  1334707200
```

示例数据中的评分列范围从 1 星到 5 星，其中 1 星表示非常差，表明客户对该产品有非常负面的体验；5 星表示优秀，表明客户对该产品有非常正面的体验。评分为 3 表示一般，表明产品满足了客户的基本期望，但可能没有超出预期。

另外两个评分（未在上面的输出中显示）如下：

*   2 星：差。这表明对产品不满意，但可能没有 1 星评分那么严重。
*   4 星：好。这表明客户对该产品有积极的体验。

我们可以通过以下方式确认数据集的评分范围是 1 到 5：

```
[In]: import numpy as np
[In]: sorted_unique_ratings = np.sort(pandas_df["rating"].unique())
[In]: print(sorted_unique_ratings)
[Out]: [1 2 3 4 5]
```

在这段代码中，我们将 NumPy 库导入为 np，然后创建一个名为 `sorted_unique_ratings` 的变量，用于存储 Pandas DataFrame (`pandas_df`) 中 `rating` 列的唯一值。我们使用 NumPy 的 `sort()` 函数对这些唯一值进行升序排序。最后，我们打印排序后的唯一评分。

我们可以使用 Pandas 的 `dtypes` 属性检查这些列的数据类型。这有助于我们了解在构建机器学习算法之前是否需要修改这些数据类型：

```
[In]: print(pandas_df.dtypes)
[Out]:
user_id       object
product_id    object
rating         int64
timestamp      int64
dtype: object
```

输出结果显示 `rating` 列是数值型的，而 `user_id` 和 `product_id` 是类别型的。对于 Surprise 的奇异值分解（SVD）算法，无需对这些类别变量进行手动标签编码或独热编码，因为 Surprise 旨在直接处理像用户 ID 和物品 ID 这样的类别变量。Surprise 在模型训练和预测过程中内部管理这些类别值到数值索引的映射。

然而，在使用 PySpark 推荐算法时情况则不同，因为它需要对类别变量（包括用户 ID 和物品 ID）进行显式索引。我们需要使用 PySpark 的 `StringIndexer` 在训练模型之前将这些类别值转换为数值索引。

让我们对 `pandas_df` DataFrame 中 `user_id` 列的每个唯一值进行出现次数统计。

我们首先按降序排序：

```
[In]: pandas_result_desc =
pandas_df.groupby('user_id').size().reset_index(name='count').sort_values(by='count', ascending=False).head(5)
[In]: print(pandas_result_desc)
[Out]:
user_id  count
611113   A5JLAU2ARJ0BO    412
226172  A231WM2Z2JL0U3    249
240295  A25HBO5V8S8SEA    164
616190    A6FIAB28IS79    146
747120   AT6CZDCP4TRGA    128
```

然后按升序排序：

```
[In]: pandas_result_asc =
pandas_df.groupby('user_id').size().reset_index(name='count').sort_values(by='count', ascending=True).head(5)
[In]: print(pandas_result_asc)
[Out]:
user_id  count
0       A00037441I8XOQJSUWCAG      1
499070         A3EEUT0F9898GS      1
499071         A3EEUZUBZQ4N4D      1
499072         A3EEVD827ZC4JY      1
499073         A3EEW1G63825UL      1
```

输出结果显示，评分最多的用户对 412 个亚马逊电子产品进行了评分，并且每个用户至少对 1 个产品进行了评分。

我们可以按产品而不是用户进行分组。

首先，我们按降序排序：

```
[In]: pandas_result_desc = pandas_df.groupby('product_id').size().reset_index(name='count').sort_values(by='count', ascending=False).head(5)
[In]: print(pandas_result_desc)
[Out]:
product_id  count
30276  B0002L5R78   9487
24439  B0001FTVEK   5345
61285  B000I68BD4   4903
46504  B000BQ7GW8   4275
14183  B00007E7JU   3523
```

然后按升序排序：

```
[In]: pandas_result_asc = pandas_df.groupby('product_id').size().reset_index(name='count').sort_values(by='count', ascending=True).head(5)
[In]: print(pandas_result_asc)
[Out]:
product_id  count
46105  B000BMQOM0      1
17738  B00009R8AJ      1
43278  B000AAJ27W      1
17736  B00009R8AH      1
43279  B000AAJ2CW      1
```

从输出中我们可以看到，评分最多的产品被评了 9,487 次，并且每个产品至少被评过一次。

现在，让我们将之前的 Pandas 步骤转换为 PySpark：

```
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.getOrCreate()
[In]: spark_df = spark.createDataFrame(pandas_df)
```

我们首先导入 `SparkSession` 类，该类作为 Spark 操作的入口点。然后，我们使用默认配置设置创建一个名为 `spark` 的 `SparkSession` 实例，确保我们要么检索现有会话，要么在不存在时创建一个新会话。最后，我们使用 `createDataFrame()` 方法将 Pandas DataFrame (`pandas_df`) 转换为 PySpark DataFrame (`spark_df`)。

现在，我们应该能够打印 `spark_df` 的形状：

```
[In]: print((spark_df.count(), len(spark_df.columns)))
[Out]: (1048576, 4)
```

这确认了 Pandas 产生的计数：1,048,576 行和 4 列。

下一步，我们使用 PySpark 的 `columns` 属性显示列名：

```
[In]: print(spark_df.columns)
[Out]: ['user_id', 'product_id', 'rating', 'timestamp']
```

我们可以按如下方式显示 `spark_df` 的前五行：



```
[In]: spark_df.show(5)
[Out]:
+--------------+----------+------+----------+
|       user_id|product_id|rating| timestamp|
+--------------+----------+------+----------+
| AKM1MP6P0OYPR| 132793040|     5|1365811200|
|A2CX7LUOHB2NDG| 321732944|     5|1341100800|
|A2NWSAGRHCP8N5| 439886341|     1|1367193600|
|A2WNBOD3WNDNKT| 439886341|     3|1374451200|
|A1GI0U4ZRJA8WN| 439886341|     1|1334707200|
+--------------+----------+------+----------+
only showing top 5 rows
```

我们可以验证，确实存在五种不同的评分，范围从 1 星到 5 星，如下所示：

```
[In]: from pyspark.sql.functions import col
[In]: unique_sorted_ratings = spark_df.select("rating").distinct().orderBy(col("rating"))
[In]: unique_sorted_ratings.show()
[Out]:
+------+
|rating|
+------+
|     1|
|     2|
|     3|
|     4|
|     5|
+------+
```

为了获取这些唯一的评分，我们首先导入用于列操作的`col`函数。接着，我们从 DataFrame 中选择 rating 列，并使用`distinct()`方法确保获取唯一值。然后，我们使用带有`col("rating")`表达式的`orderBy()`函数，按升序对唯一的评分值进行排序。最后，我们使用`show()`方法显示结果。

我们可以按如下方式打印 PySpark DataFrame 的架构：

```
[In]: spark_df.printSchema()
[Out]:
root
|-- user_id: string (nullable = true)
|-- product_id: string (nullable = true)
|-- rating: long (nullable = true)
|-- timestamp: long (nullable = true)
```

该输出确认了 Pandas 所指示的数据类型：两个数值列（rating 和 timestamp）和两个分类列（user_id 和 product_id）。PySpark 模型要求 user_id 和 product_id 为数值格式，因此我们在训练模型之前需要进行此转换。

我们可以计算 spark_df 中每个用户的出现次数。

首先按降序进行计数：

```
[In]: spark_df.groupBy("user_id").count().orderBy("count", ascending=False).show(5)
[Out]:
+--------------+-----+
|       user_id|count|
+--------------+-----+
| A5JLAU2ARJ0BO|  412|
|A231WM2Z2JL0U3|  249|
|A25HBO5V8S8SEA|  164|
|  A6FIAB28IS79|  146|
| AT6CZDCP4TRGA|  128|
+--------------+-----+
only showing top 5 rows
```

然后按升序进行相同操作：

```
[In]: spark_df.groupBy("user_id").count().orderBy("count", ascending=True).show(5)
[Out]:
+--------------+-----+
|       user_id|count|
+--------------+-----+
| AFS5ZGZ2M3ZGV|    1|
|A19TDRIKW64Z6I|    1|
|A1K775TKUNZL43|    1|
|A2VS1WG9VKINXI|    1|
| A76QA8ID3NTCC|    1|
+--------------+-----+
only showing top 5 rows
```

这些结果与 Pandas 生成的输出相似；即，最活跃的用户出现了 412 次，而最不活跃的用户出现了 1 次。

除了按用户分组，我们还可以按产品统计评分数量。我们首先通过将 ascending 参数设置为 False 来进行降序操作：

```
[In]: spark_df.groupBy("product_id").count().orderBy("count", ascending=False).show(5)
[Out]:
+----------+-----+
|product_id|count|
+----------+-----+
|B0002L5R78| 9487|
|B0001FTVEK| 5345|
|B000I68BD4| 4903|
|B000BQ7GW8| 4275|
|B00007E7JU| 3523|
+----------+-----+
only showing top 5 rows
```

然后按升序进行相同操作：

```
[In]: spark_df.groupBy("product_id").count().orderBy("count", ascending=False).show(5)
[Out]:
+----------+-----+
|product_id|count|
+----------+-----+
|B00004SC3R|    1|
|B00005KHSJ|    1|
|B00004YK3Q|    1|
|B00000J025|    1|
|B000050GDM|    1|
+----------+-----+
only showing top 5 rows
```

结果证实了 Pandas 的输出：最热门的电子产品被评了 9,487 次，且每个产品至少被评过一次。

## 构建推荐系统

在本节中，我们将使用亚马逊电子产品数据集构建、训练并评估一个协同过滤推荐系统。然后，该模型可以根据用户对其他产品的历史评分，向可能尚未见过这些产品的用户推荐部分产品。我们将演示如何在 Surprise 和 PySpark 中实现这一过程。



## 使用 Surprise 构建推荐系统

我们首先使用 Python 的 Surprise 库来构建一个推荐系统。Surprise 提供了简单便捷的 API，用于实现协同过滤算法、矩阵分解技术以及各种推荐算法。它常用于电影推荐、图书推荐以及其他个性化推荐系统等任务。

Surprise 可以通过以下命令安装：

```
[In]: pip install scikit-surprise
```

要开始构建推荐系统的建模过程，我们需要导入必要的库：

步骤 1：导入必要的库

```
[In]: import pandas as pd
[In]: from surprise import Dataset, Reader, SVD
[In]: from surprise.model_selection import train_test_split
[In]: from surprise.accuracy import rmse
```

这段代码为使用 Surprise 库构建和评估推荐系统奠定了基础。典型的工作流程是：加载数据集、定义数据读取器、使用 `train_test_split` 将数据拆分为训练集和测试集，然后使用导入的函数和类来训练和评估 SVD 推荐模型。

在上述代码中，我们首先导入 Pandas 库用于数据加载。然后从 `surprise` 库中导入 `Dataset`、`Reader` 和 `SVD`。

以下是导入的 Surprise 模块的描述：

*   **Dataset**：提供了一种便捷的方式来加载和管理推荐系统的数据集。

*   **Reader**：定义了与 Surprise 配合使用的输入数据的格式。

*   **SVD**：Surprise 中的一种算法，代表奇异值分解。SVD 是一种矩阵分解技术，在推荐系统中用于预测用户对物品的评分。

接下来，我们导入 `train_test_split` 函数，将数据集拆分为训练集和测试集，用于评估推荐系统的性能。然后导入 `rmse` 函数，用于计算均方根误差（RMSE），这是评估推荐系统预测准确性的常用指标。它衡量预测评分与实际评分之间的平均差异。

步骤 2：从 CSV 文件读取数据

```
[In]: column_names = ['user_id', 'product_id', 'rating', 'timestamp']
[In]: pandas_df =   pd.read_csv('https://raw.githubusercontent.com/abdelaziztestas/'                                'spark_book/main/amazon_electronics.csv', names=column_names)
```

这段代码涉及使用 Pandas 库创建一个 DataFrame。第一行创建了一个名为 `column_names` 的列表，其中包含 DataFrame 的列名。这些列分别是 `user_id`、`product_id`、`rating` 和 `timestamp`。该列表将用于为 DataFrame 分配列名。

第二行从一个指向 GitHub 上 CSV 文件位置的 URL 读取该文件，并创建一个名为 `pandas_df` 的 Pandas DataFrame。`read_csv` 函数用于读取 CSV 文件。参数 `names=column_names` 指定将 `column_names` 列表中的名称用作 Pandas DataFrame 的列名。

步骤 3：定义评分范围并将数据加载到 Surprise 数据集中

```
[In]: reader = Reader(rating_scale=(1, 5))
[In]: data = Dataset.load_from_df(pandas_df[['user_id', 'product_id', 'rating']], reader)
```

此步骤的目的是创建一个 `Dataset` 对象，该对象稍后将用于在 Surprise 中训练和评估推荐系统模型。我们可以将其分解为两个步骤：

1.  使用第一行代码创建 Surprise 库中 `Reader` 类的一个实例，即 `reader = Reader(rating_scale=(1, 5))`。`Reader` 类用于指定输入数据的格式。我们将 `rating_scale` 参数设置为 `(1, 5)`，因为亚马逊电子产品数据中的评分变量范围是 1 到 5。这一步是必要的，因为 Surprise 期望数据采用特定格式，而使用 `Reader` 类有助于定义该格式。



2.  通过第二行代码，即 `data = Dataset.load_from_df(pandas_df[[‘user_id’, ‘product_id’, ‘rating’]], reader)`，从 pandas_df 加载数据来创建一个 Surprise `Dataset` 对象。`load_from_df` 方法由 Surprise 中的 `Dataset` 类提供，用于将数据加载为所需格式。在同一行代码中，`pandas_df[[‘user_id’, ‘product_id’, ‘rating’]]` 从 pandas_df 数据框中选择 `user_id`、`product_id` 和 `rating` 列。这个子集包含了推荐系统所需的数据。`reader` 对象作为参数传递给 `load_from_df`，用于指定数据的格式。

**步骤 4：将数据拆分为训练集和测试集**

```
[In]: trainset, testset = train_test_split(data, test_size=0.2)
```

这行代码使用 Surprise 库中的 `train_test_split` 函数将数据拆分为训练集和测试集。它使用包含待拆分数据的 `data`（`Dataset` 对象）作为参数。`test_size=0.2` 指定了应分配给测试集的数据比例。它被设置为 0.2，意味着 20% 的数据将用于测试，剩余的 80% 将用于训练。生成的训练和测试数据集分别是 `trainset` 和 `testset`。每个数据集都包含三个元素：`user_id`、`product_id` 和 `rating`。

**步骤 5：构建 SVD 模型**

```
[In]: model = SVD()
```

这行代码创建了 Surprise 库中 `SVD` 类的一个对象，并将其赋值给变量 `model`。SVD 是一种常用于推荐系统的矩阵分解技术。

**步骤 6：训练模型**

```
[In]: model.fit(trainset)
```

这行代码用于在训练集上训练 SVD 模型，它调用了模型对象的 `fit()` 方法，该对象是 Surprise 库中 SVD 类的一个实例。`trainset` 包含了 SVD 模型学习和预测所需的用户-项目评分。

在训练过程中，SVD 模型会分析训练集中的用户-项目交互，并学习其中的潜在模式和关系。它利用矩阵分解技术来分解评分矩阵，并估计与用户和项目相关的潜在因子。模型会调整其内部参数，以最小化训练集中预测评分与实际评分之间的差异。

**步骤 7：评估模型**

```
[In]: predictions = model.test(testset)
[In]: rmse_score = rmse(predictions)
[In]: print('RMSE:', rmse_score)
[Out]: RMSE: 1.31
```

这段代码用于使用训练好的 SVD 模型生成预测，并计算均方根误差（RMSE）作为预测准确性的度量。

我们先来看预测结果。

我们可以打印前五个测试用例的预测结果，并将其与实际预测值进行比较，如下所示：

```
[In]: for prediction in test_predictions[:5]:
rounded_prediction = round(prediction.est, 1)
print(f"{prediction.uid}, {prediction.iid}, " +
f"Actual Rating: {prediction.r_ui}, " +
f"Predicted Rating: {rounded_prediction}")
[Out]:
AL7WWD4J3NSOY, B000AYSGHA, Actual Rating: 4.0, Predicted Rating: 4.1
A20THXZ7N0NID, B000DZB8BW, Actual Rating: 3.0, Predicted Rating: 3.6
A3GY647TN2ECRO, B00003006E, Actual Rating: 4.0, Predicted Rating: 4.2
AK6PVIKDN83MW, B0007POE6O, Actual Rating: 1.0, Predicted Rating: 3.6
A28GX1L3BBZHY3, B00005O7H0, Actual Rating: 5.0, Predicted Rating: 4.4
```

这段代码打印了 SVD 推荐系统为前五个测试用例生成的预测结果。它从一个 for 循环开始，遍历 `test_predictions` 集合中的前五个预测。对于每个预测，它计算预测评分，并将其四舍五入到一位小数。脚本随后打印关键信息：用户 ID、项目 ID、实际评分（真实值）以及四舍五入后的预测评分。代码使用了格式化字符串（f-strings）来获得清晰易读的输出。它包含以下细节：

*   `prediction.uid`：与该预测关联的用户 ID
*   `prediction.iid`：与该预测关联的项目（或产品）ID
*   `prediction.r_ui`：该用户-项目对的实际评分（真实值）

从输出中我们可以看到，用户提供的实际评分与预测评分之间存在差异。其中一些差异相当显著。例如，用户 `AK6PVIKDN83MW` 对项目 `B0007POE6O` 的实际评分为 1.0，而模型预测的评分却高得多，为 3.6。类似地，用户 `A28GX1L3BBZHY3` 对项目 `B00005O7H0` 给出了 5.0 的高分，而模型预测的评分则较低，为 4.4。

尽管这只是一个样本，但它让我们了解了模型的性能表现。较大的差异会导致较大的 RMSE 值。在我们的案例中，RMSE 约为 1.31，这表明 SVD 模型做出的预测与测试集中的实际评分相比，平均误差（或残差）约为 1.31 个单位。这意味着模型的预测值平均而言与测试集中的实际评分存在显著偏差。

在现实场景中，我们通常会调查模型过拟合的原因，并努力通过最小化其 RMSE 来提升其性能。不过，我们暂时假设对模型的性能感到满意。以下代码演示了如何利用这个训练好的模型对未见过的数据进行预测：

```
[In]: existing_user_id = 'AK6PVIKDN83MW'
[In]: new_product_id = 'B660005667RX'
[In]: predicted_rating = model.predict(existing_user_id, new_product_id).est
[In]: print(f'Predicted Rating for User {existing_user_id} and Product {new_product_id}: {predicted_rating:.2f}')
[Out]:
Predicted Rating for User AK6PVIKDN83MW and Product B660005667RX: 3.79
```

我们首先定义了一个 `existing_user_id` 和一个 `new_product_id`，分别代表我们想要为其进行预测的特定用户和产品。然后，使用训练好的推荐模型（`model`），调用 `predict` 方法，并将用户和产品 ID 作为参数传入。存储在 `predicted_rating` 中的预测结果随后通过 `print()` 打印出来。

在这个例子中，模型预测用户 `AK6PVIKDN83MW` 对新产品 `B660005667RX` 的评分为 3.79。这个预测值代表了模型认为该用户会给这个新产品打出的估计评分或分数。由于预测评分较高（表明用户喜欢该产品的可能性很大），我们可能会将 `B660005667RX` 作为个性化推荐推荐给该用户。



### 基于 PySpark 的推荐系统

在本小节中，我们将使用 PySpark 构建、训练并评估一个推荐系统。我们将使用与 Scikit-Learn 部分相同的亚马逊电子产品数据集来训练和测试 PySpark 模型。

我们使用 ALS（交替最小二乘法）——一种流行的协同过滤算法，专为大规模构建推荐系统而设计。协同过滤方法通过利用用户的历史行为和偏好（例如用户与物品的交互，如评分）来做出推荐。ALS 特别专注于矩阵分解，以学习代表用户和物品的潜在因子。ALS 将用户-物品交互矩阵分解为两个低维矩阵：一个代表用户，另一个代表物品。这些矩阵捕获了能够解释观察到的用户-物品交互的潜在因子。ALS 试图找到这些潜在因子，使得它们的乘积能够近似于原始的用户-物品矩阵。

为了实现此算法，我们首先导入必要的库。

**步骤 1：导入必要的库**

```
[In]: from pyspark.ml.feature import StringIndexer
[In]: from pyspark.ml.recommendation import ALS
[In]: from pyspark.ml.evaluation import RegressionEvaluator
[In]: from pyspark.sql import SparkSession
```

此代码用于从 PySpark 库中导入处理推荐系统所需的模块。`StringIndexer` 类用于将分类变量转换为数值索引。这是必要的，因为与 Surprise 不同，PySpark 需要数据为索引格式。`StringIndexer` 将用于对用户 ID 和物品 ID 进行索引。`ALS` 是交替最小二乘法类，用于构建基于矩阵分解的协同过滤算法。`RegressionEvaluator` 类用于通过计算 RMSE（均方根误差）来评估模型性能。`SparkSession` 类是在 PySpark 中处理结构化数据的入口点。它提供了创建 DataFrame 和执行各种数据处理操作的功能。

**步骤 2：创建 SparkSession**

```
[In]: spark = SparkSession.builder.getOrCreate()
```

这行代码用于在 PySpark 中创建一个 SparkSession 对象。它提供了一种与各种 Spark 功能（如创建 DataFrame 和执行分布式数据处理操作）进行交互的方式。`builder` 是 `SparkSession` 类的一个方法，允许用户配置和设置 SparkSession 的各种选项。`getOrCreate()` 是 `builder` 的一个方法，用于检查 SparkSession 是否已存在。如果存在，则返回现有的 SparkSession；如果不存在，则创建一个新的 SparkSession。

**步骤 3：从 DataFrame 加载数据**

```
[In]: spark_df = spark.createDataFrame(pandas_df)
```

这行代码用于使用 PySpark 中的 `createDataFrame()` 方法，从现有的 Pandas DataFrame（pandas_df）创建一个 Spark DataFrame（spark_df）。

**步骤 4：将 user_id 和 product_id 列转换为数值**

```
[In]: user_indexer = StringIndexer(inputCol="user_id", outputCol="user_index")
[In]: product_indexer = StringIndexer(inputCol="product_id", outputCol="product_index")
[In]: indexed_data = user_indexer.fit(spark_df).transform(spark_df)
[In]: indexed_data = product_indexer.fit(indexed_data).transform(indexed_data)
```

此代码使用 `StringIndexer` 类将 spark_df 中的分类列转换为数值索引。过程如下：

*   第一行创建了一个 `StringIndexer` 实例，并将其赋值给变量 user_indexer。它指定要索引的输入列是 DataFrame 中的 user_id，索引后的值将存储在一个名为 user_index 的新列中。

*   第二行创建了另一个 `StringIndexer` 实例，并将其赋值给变量 product_indexer。与上一行类似，它指定要索引的输入列是 product_id，索引后的值将存储在一个名为 product_index 的新列中。

*   第三行将 user_indexer 对象的 `fit()` 和 `transform()` 方法应用于 PySpark DataFrame spark_df。`fit()` 方法用于学习并拟合 DataFrame 上的转换，而 `transform()` 方法则应用转换并返回一个新的 DataFrame indexed_data。user_id 列被索引，结果索引值存储在 user_index 列中。

*   最后一行将 `product_indexer` 对象的 `fit()` 和 `transform()` 方法应用于上一步得到的 indexed_data DataFrame。与上一行类似，它拟合 DataFrame 上的转换并应用转换以创建一个新的 DataFrame indexed_data。product_id 列被索引，结果索引值存储在 product_index 列中。

**步骤 5：将数据拆分为训练集和测试集**

```
[In]: train, test = indexed_data.randomSplit([0.8, 0.2], seed=42)
```

这行代码用于将 indexed_data DataFrame 拆分为训练数据集和测试数据集。indexed_data DataFrame 表示分类列 user_id 和 product_id 已使用 `StringIndexer` 转换为数值索引的数据。为了创建用于训练和测试的独立数据集，使用了 `randomSplit()` 函数。该函数将 indexed_data DataFrame 拆分为两个数据集：train 和 test。第一个参数 [0.8, 0.2] 指定了拆分比例。在此例中，80% 的数据分配给训练集（train），其余 20% 分配给测试集（test）。第二个参数 seed=42 设置了一个特定的随机种子以确保可重复性，这意味着每次使用相同的种子值执行代码时，拆分结果都是相同的。

**步骤 6：构建 ALS 模型**

```
[In]: als = ALS(userCol='user_index', itemCol='product_index', ratingCol='rating', coldStartStrategy='drop', nonnegative=True)
```

此代码用于在 PySpark 中创建一个用于协同过滤的 `ALS`（交替最小二乘法）算法实例。正在实例化来自 PySpark MLlib 的 `ALS` 类，并带有以下参数：

*   userCol='user_index' 指定 DataFrame 中代表用户索引的列名。它被设置为 user_index，表示 user_index 列将用作 ALS 模型中的用户标识符。

*   itemCol='product_index' 指定 DataFrame 中代表物品（产品）索引的列名。它被设置为 product_index，表示 product_index 列将用作 ALS 模型中的物品标识符。

*   ratingCol='rating' 指定 DataFrame 中包含评分的列名。它被设置为 rating，表示 rating 列将用作 ALS 模型中的目标变量（评分）。

*   coldStartStrategy='drop' 指定处理冷启动场景的策略，即模型遇到训练数据中不存在的新用户或物品时。这里使用了 drop 策略，这意味着在模型拟合或预测期间，DataFrame 中任何缺少用户或物品索引的行都将被丢弃。

*   nonnegative=True 确保预测值为非负，因为我们的实际评分范围是 1 到 5 星。

**步骤 7：训练模型**

```
[In]: model = als.fit(train)
```

这行代码用于在训练数据集（train）上训练 ALS（交替最小二乘法）模型。它调用了 `als` 对象上的 `fit()` 方法，该对象是 PySpark 中 ALS 算法的一个实例。`fit()` 方法使用训练数据集训练 ALS 模型。



在训练过程中，ALS 算法会优化模型参数，以最小化预测评分与训练数据集中实际评分之间的差异。该算法采用迭代方法，交替更新用户因子和物品因子，以找到能够捕捉数据中潜在模式的最优值。训练过程完成后，`fit()` 方法会返回一个训练好的 ALS 模型，并将其赋值给变量 `model`。这个训练好的模型随后可用于对新的、未见过的数据进行预测，或基于用户-物品交互生成推荐。

步骤 8：评估模型

```
[In]: predictions = model.transform(test)
[In]: evaluator = RegressionEvaluator(labelCol='rating', metricName='rmse')
[In]: rmse_score = evaluator.evaluate(predictions)
[In]: print('RMSE:', rmse_score)
[Out]: RMSE: 1.96
```

这段代码用于使用训练好的 ALS 模型生成预测，并使用 RMSE（均方根误差）指标评估其性能。

以下是更详细的过程：

*   第一行使用 `transform()` 方法将训练好的 ALS 模型（`model`）应用于测试数据集（`test`）。此方法将模型应用于测试数据，并为测试数据集中的用户-物品组合生成预测。生成的预测结果存储在 `predictions` DataFrame 中。

*   第二行创建了一个 `RegressionEvaluator` 类的实例，用于评估回归模型的性能。实例化时使用了以下参数：
    *   `labelCol='rating'` 指定了 `predictions` DataFrame 中包含实际评分的列名。它被设置为 `rating`，表示实际评分存储在 `rating` 列中。

*   `metricName='rmse'` 指定了要使用的评估指标，在本例中为 RMSE（均方根误差）。

*   第三行将评估器对象的 `evaluate()` 方法应用于 `predictions` DataFrame。此方法计算 `predictions` DataFrame 中预测评分与实际评分之间的 RMSE。计算得到的 RMSE 分数被赋值给变量 `rmse_score`。

*   最后一行打印 RMSE 分数。它指示了 ALS 模型在预测测试数据集评分方面的表现。RMSE 值越低，表示模型性能越好，因为这意味着预测评分与实际评分之间的差异更小。

让我们从预测结果开始。

我们可以比较预测评分和实际评分，以大致了解模型的性能。以下代码打印了前五条记录的样本：

```
[In]: predictions.select("user_id", "product_id", "rating", "prediction").show(5)
[Out]:
+--------------+----------+------+----------+
|       user_id|product_id|rating|prediction|
+--------------+----------+------+----------+
|A2AY4YUOX2N1BQ|B000050ZS3|     4|  4.310914|
|A2AY4YUOX2N1BQ|B0000665V7|     5|   4.56616|
|A2AY4YUOX2N1BQ|B00008R9ML|     4|  5.730056|
|A2AY4YUOX2N1BQ|B00009R76N|     5| 4.4490294|
|A2AY4YUOX2N1BQ|B00009R7BD|     5|  6.400287|
+--------------+----------+------+----------+
only showing top 5 rows
```

输出结果显示实际评分与预测评分之间存在差异，尽管有些差异在四舍五入后相对较小。例如，用户 `A2AY4YUOX2N1BQ` 对产品 `B000050ZS3` 的评分为 4，而模型预测的评分为 4.3，四舍五入后为 4。同样，用户 `A2AY4YUOX2N1BQ` 对产品 `B0000665V7` 的评分为 5，而模型预测的评分为 4.6，四舍五入后为 5。然而，其他评分的差异则较为显著。例如，`A2AY4YUOX2N1BQ` 对产品 `B00009R7BD` 的评分为 5，而模型预测的评分为 6.4，即使四舍五入后，差异仍然很大。

至于 RMSE，报告的值约为 1.96，这意味着 ALS 模型的预测评分平均与实际评分相差约 1.96 个单位。该值指示了模型在预测用户-物品评分方面的准确性。RMSE 越接近零，模型的预测与真实评分的一致性就越好。

## 整合所有步骤

现在，让我们将前面步骤中的所有代码片段整合到一个代码块中。这样，读者可以在 Surprise 和 PySpark 中将其作为一个整体代码块执行。

### Surprise

步骤 1：导入必要的库

```
[In]: import pandas as pd
[In]: from surprise import Dataset, Reader, SVD
[In]: from surprise.model_selection import train_test_split
[In]: from surprise.accuracy import rmse
```

步骤 2：加载数据

```
[In]: column_names = ['user_id', 'product_id', 'rating', 'timestamp']
[In]: pandas_df = pd.read_csv('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/amazon_electronics.csv', names=column_names)
```

步骤 3：定义评分范围

```
[In]: reader = Reader(rating_scale=(1, 5))
```

步骤 4：将数据加载到 Surprise 数据集中

```
[In]: data = Dataset.load_from_df(pandas_df[['user_id', 'product_id', 'rating']], reader)
```

步骤 5：将数据拆分为训练集和测试集

```
[In]: trainset, testset = train_test_split(data, test_size=0.2)
```

步骤 6：构建 SVD 模型

```
[In]: model = SVD()
```

步骤 7：训练模型

```
[In]: model.fit(trainset)
```

步骤 8：在测试集上评估模型

```
[In]: predictions = model.test(testset)
[In]: rmse_score = rmse(predictions)
[In]: print('RMSE:', rmse_score)
```

步骤 9：打印前五个测试用例的实际评分和预测评分

```
[In]: for prediction in predictions[:5]:
rounded_prediction = round(prediction.est, 1)
print(f"{prediction.uid}, {prediction.iid}, " +
f"Actual Rating: {prediction.r_ui}, " +
f"Predicted Rating: {rounded_prediction}")
```

步骤 10：进行新的预测

```
[In]: existing_user_id = 'AK6PVIKDN83MW'
[In]: new_product_id = 'B660005667RX'
[In]: predicted_rating = model.predict(existing_user_id, new_product_id).est
[In]: print(f'Predicted Rating for User {existing_user_id} and Product {new_product_id}: {predicted_rating:.2f}')
```

### PySpark

步骤 1：导入必要的库

```
[In]: from pyspark.ml.feature import StringIndexer
[In]: from pyspark.ml.recommendation import ALS
[In]: from pyspark.ml.evaluation import RegressionEvaluator
[In]: from pyspark.sql import SparkSession
```

步骤 2：创建 SparkSession

```
[In]: spark = SparkSession.builder.getOrCreate()
```

步骤 3：从 DataFrame 加载数据

```
[In]: spark_df = spark.createDataFrame(pandas_df)
```

步骤 4：将 user_id 和 product_id 列转换为数值

```
[In]: user_indexer = StringIndexer(inputCol="user_id", outputCol="user_index")
[In]: product_indexer = StringIndexer(inputCol="product_id", outputCol="product_index")
[In]: indexed_data = user_indexer.fit(spark_df).transform(spark_df)
[In]: indexed_data = product_indexer.fit(indexed_data).transform(indexed_data)
```

步骤 5：将数据拆分为训练集和测试集

```
[In]: train, test = indexed_data.randomSplit([0.8, 0.2], seed=42)
```

步骤 6：构建 ALS 模型

```
[In]: als = ALS(userCol='user_index', itemCol='product_index', ratingCol='rating', coldStartStrategy='drop', nonnegative=True)
```

步骤 7：训练模型

```
[In]: model = als.fit(train)
```

步骤 8：评估模型

```
[In]: predictions = model.transform(test)
[In]: evaluator = RegressionEvaluator(labelCol='rating', metricName='rmse')
[In]: rmse_score = evaluator.evaluate(predictions)
[In]: print('RMSE:', rmse_score)
```

步骤 9：打印前五个预测结果

```
[In]: predictions.select("user_id", "product_id", "rating", "prediction").show(5)
```



## 总结

在本章中，我们探索了监督学习的一个新领域：推荐系统。我们利用协同过滤为亚马逊电子产品开发了一个推荐系统。为了实现该算法，我们采用了矩阵分解技术，该技术在协同过滤推荐系统中被广泛使用。

我们使用 Surprise 和 PySpark 库实现了该推荐系统。Surprise 与 Scikit-Learn 非常相似，专为中小型数据集设计，与 PySpark 不同，它通常不用于大规模、大数据场景。此外，我们还使用了 Pandas 和 PySpark 进行数据处理和探索。通过比较这些库中的实现，我们深入了解了它们的相似之处。目标是帮助那些打算从 Pandas 过渡到 PySpark 的数据科学家，使他们能够利用后者强大的分布式计算 API。

在下一章中，我们将深入探讨监督学习的另一个领域：自然语言处理（NLP），探索如何从文本数据中分析和提取有价值的见解。

