# b) 打印簇计数
[In]: print("Cluster Counts:")
[In]: for cluster, count in cluster_counts.items():
print("Cluster {}: {}".format(cluster, count))
```

### PySpark

步骤 1：导入必要的库

```
[In]: from sklearn.datasets import load_iris
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.clustering import KMeans
[In]: from pyspark.ml.evaluation import ClusteringEvaluator
[In]: from pyspark.ml.feature import VectorAssembler
[In]: import pandas as pd
```

步骤 2：加载鸢尾花数据集

```
[In]: iris = load_iris()
[In]: X = iris.data
```

步骤 3：定义列名

```
[In]: feature_names = iris.feature_names
```

步骤 4：创建带有列名的 DataFrame

```
[In]: pandas_df = pd.DataFrame(X, columns=feature_names)
```

步骤 5：创建 SparkSession

```
[In]: spark = SparkSession.builder.getOrCreate()
```

步骤 6：将 Pandas DataFrame 转换为 PySpark DataFrame

```
[In]: spark_df = spark.createDataFrame(pandas_df)
```

步骤 7：定义输入特征

```
[In]: input_features = feature_names
```

步骤 8：将输入特征组装成一个向量列

```
[In]: assembler = VectorAssembler(inputCols=input_features, outputCol="features")
[In]: spark_df = assembler.transform(spark_df)
```

步骤 9：选择最优的 k 值

```
[In]: k = 3
```

步骤 10：构建并训练 k-means 聚类模型

```
[In]: kmeans = KMeans().setK(k).setSeed(42)
[In]: model = kmeans.fit(spark_df)
```

步骤 11：进行预测

```
[In]: predictions = model.transform(spark_df)
```

步骤 12：评估聚类模型

```
[In]: evaluator = ClusteringEvaluator()
[In]: silhouette = evaluator.evaluate(predictions)
[In]: print("Silhouette Score:", silhouette)
```

步骤 13：统计每个预测簇的出现次数

```
[In]: cluster_counts = predictions.groupBy("prediction").count()
```

步骤 14：打印簇计数

```
[In]: print("Cluster Counts:")
[In]: cluster_counts.show()
```

## 总结

在本章中，我们深入探讨了使用 k-means 聚类的无监督学习领域。我们演示了如何使用开源的鸢尾花数据集构建、训练和评估 k-means 算法。此练习的目的是根据鸢尾花的特征将其品种分组到不同的簇中。

我们利用了 Pandas、Scikit-Learn 和 PySpark 的强大功能。这种方法的主要目标是帮助熟悉 Pandas 和 Scikit-Learn 的数据科学家顺利过渡到 PySpark。通过探索这些平台在步骤上的相似性，我们的目标是为这些机器学习从业者提供无缝过渡到 PySpark 的途径，使他们能够利用其分布式计算能力来增强可扩展性和性能。

在下一章中，我们将深入探讨机器学习的一个新领域：超参数调优。到目前为止，我们一直使用默认参数构建算法。下一章将演示如何微调这些参数以自定义默认值。

# 16. 使用 Scikit-Learn 和 PySpark 进行超参数调优

在本章中，我们将研究超参数调优这一主题。这是机器学习中的一个关键步骤，涉及为给定算法找到最优的超参数集。超参数是在学习过程开始之前设置的参数，它们会影响模型的行为和性能。

超参数调优过程旨在找到最佳的超参数组合，使模型在给定任务或数据集上达到最高性能。这个过程很重要，因为不同的超参数值会显著影响模型从数据中学习和泛化的能力。

在本章中，我们提供了 Scikit-Learn 和 PySpark 中此类参数的示例。我们还将通过一个详细示例，展示如何微调随机森林中的关键超参数。为此，我们使用鸢尾花数据集，这与我们在第 9 章中用于将鸢尾花分类为不同品种的数据集相同。



## 超参数示例

在本节中，我们将提供本书所研究的各种流行机器学习算法的关键超参数示例。特定算法的超参数通常保持不变，无论其用于回归任务还是分类任务。例如，考虑随机森林算法时，诸如树的数量、最大深度以及每次分裂时考虑的特征数量等超参数，无论用于回归还是分类，都是相同的。

下表比较了决策树、随机森林、梯度提升树、神经网络和 K 均值聚类在 Scikit-Learn 和 PySpark 中的关键参数，并描述了每个参数的含义。

| 模型 | Scikit-Learn | PySpark | 描述 |
| --- | --- | --- | --- |
| 决策树 | max_depth | maxDepth | 决策树的最大深度 |
| | min_samples_split | minInstancesPerNode | 分裂内部节点所需的最小样本数 |
| | min_samples_leaf | minInfoGain | 叶节点所需的最小样本数 |
| | max_features | maxBins | 最佳分裂的最大特征数 |
| 随机森林 | n_estimators | numTrees | 随机森林中决策树的数量 |
| | max_depth | maxDepth | 森林中每棵树的最大深度 |
| | min_samples_split | minInstancesPerNode | 分裂内部节点所需的最小样本数 |
| | min_samples_leaf | minInfoGain | 叶节点所需的最小样本数 |
| | max_features | featureSubsetStrategy | 最佳分裂的最大特征数 |
| 梯度提升树 | n_estimators | numTrees | 提升过程中决策树的数量 |
| | learning_rate | stepSize | 每棵树对最终预测的贡献 |
| | max_depth | maxDepth | 每棵决策树的最大深度 |
| | min_samples_split | minInstancesPerNode | 分裂内部节点所需的最小样本数 |
| | min_samples_leaf | minInfoGain | 叶节点所需的最小样本数 |
| 神经网络 | hidden_layers | numLayers | 神经网络中隐藏层的数量 |
| | hidden_units | layerSizes | 每个隐藏层中的神经元数量 |
| | learning_rate | stepSize | 训练过程中更新网络权重的步长 |
| K 均值聚类 | n_clusters | k | 期望的聚类数量 |
| | max_iter | maxIterations | 算法迭代或轮次的最大次数 |

## 调整随机森林的参数

超参数调优在优化机器学习算法性能方面起着至关重要的作用，随机森林也不例外。随机森林是一种稳健的集成学习方法，以其在处理复杂数据任务时的鲁棒性和有效性而闻名。然而，随机森林模型的性能在很大程度上取决于其超参数的赋值。

在本节中，我们将演示如何在 Scikit-Learn 和 PySpark 中微调随机森林的四个关键超参数：

*   决策树的数量（n_estimators / numTrees）
*   每棵树的最大深度（max_depth / maxDepth）
*   分裂内部节点所需的最小样本数（min_samples_split / minInstancesPerNode）
*   叶节点所需的最小样本数（min_samples_leaf / minInfoGain）

通过仔细调整这些超参数，我们旨在找到最佳配置，以最大化模型的预测能力，同时避免过拟合或欠拟合。

我们使用第 9 章中使用的相同鸢尾花数据集，将鸢尾花分类为不同品种。通过利用随机森林中的超参数调优技术，我们旨在构建一个高精度的分类模型，能够有效区分三种鸢尾花品种：山鸢尾、变色鸢尾和维吉尼亚鸢尾。

### 在 Scikit-Learn 中进行超参数调优

以下代码遵循使用 Scikit-Learn 的 `GridSearchCV` 和随机森林分类器进行超参数调优的标准流程。它加载鸢尾花数据集，将其拆分为训练集和测试集，定义用于调优的参数网格，执行网格搜索，检索最佳模型及其参数，对测试数据进行预测，评估模型性能，最后打印最佳参数值。

我们首先导入必要的库。这些库提供了我们分析所需的基本功能和工具。

**步骤 1：导入必要的库**

```
[In]: from sklearn.datasets import load_iris
[In]: from sklearn.ensemble import RandomForestClassifier
[In]: from sklearn.model_selection import train_test_split, GridSearchCV
[In]: from sklearn.metrics import accuracy_score
```

以下是每个导入的作用：

*   `load_iris` 加载鸢尾花数据集。
*   `RandomForestClassifier` 定义随机森林分类器。
*   `train_test_split` 将数据拆分为训练集和测试集。
*   `GridSearchCV`，即网格搜索交叉验证，用于搜索机器学习模型的最佳超参数组合。
*   `accuracy_score` 计算模型的准确率。

**步骤 2：加载鸢尾花数据集**

```
[In]: iris_sklearn = load_iris()
```

这行代码使用 `load_iris()` 函数加载鸢尾花数据集。

**步骤 3：将数据拆分为特征和标签**

```
[In]: X = iris_sklearn.data
[In]: y = iris_sklearn.target
```

这段代码从加载的数据集中分离出特征和标签。

**步骤 4：将数据拆分为训练集和测试集**

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

在此步骤中，我们将数据拆分为训练集和测试集，其中 80% 用于训练，20% 用于测试。

**步骤 5：定义随机森林分类器**

```
[In]: rf = RandomForestClassifier(random_state=42)
```

这行代码定义了一个随机森林分类器实例。随机种子确保了结果的可重复性。

**步骤 6：定义用于调优的参数网格**

```
[In]: param_grid = {
'max_depth': [2, 5, 7, 10],
'n_estimators': [20, 50, 75, 100],
'min_samples_split': [2, 3, 5, 10],
'min_samples_leaf': [1, 2, 3, 4],
}
```

在这段代码中，我们定义了 param_grid，以便网格搜索算法能够系统地评估这些超参数的各种组合，从而找到能带来最高模型性能的最佳组合。

鉴于鸢尾花数据集规模相对较小（150 条记录），我们选择了保守的参数。这是为了防止过拟合，并确保模型不会因数据集大小而变得过于复杂。

作为参考，Scikit-Learn 随机森林分类器的默认参数如下：

*   max_depth: None（默认无限制深度）
*   n_estimators: 100
*   min_samples_split: 2
*   min_samples_leaf: 1

以下是我们用于自定义默认设置的参数详情：

*   `'max_depth': [2, 5, 7, 10]` 指定了随机森林分类器的 max_depth 超参数。网格搜索将考虑 max_depth 的四个值：2、5、7 和 10。模型将使用这些值分别进行训练和评估，以确定最佳的 max_depth 值。
*   `'n_estimators': [20, 50, 75, 100]` 定义了 n_estimators 超参数，该参数决定了随机森林中决策树的数量。网格搜索将探索 n_estimators 的三个值：20、50、75 和 100。
*   `'min_samples_split': [2, 3, 5, 10]` 指定了 min_samples_split 超参数，该参数决定了分裂决策树内部节点所需的最小样本数。网格搜索将使用 min_samples_split 的四个不同值（2、3、5 和 10）来评估模型。



*   `'min_samples_leaf': [1, 2, 3, 4]` 定义了 `min_samples_leaf` 超参数，该参数决定了随机森林中决策树叶子节点所需的最小样本数。网格搜索将使用 `min_samples_leaf` 的四个不同值（1、2、3 和 4）来评估模型性能。

步骤 7：使用 GridSearchCV 执行网格搜索

```
[In]: grid_search = GridSearchCV(estimator=rf, param_grid=param_grid,
cv=5)
[In]: grid_search.fit(X_train, y_train)
```

在这两行代码中，使用 Scikit-Learn 库中的 `GridSearchCV` 类执行网格搜索。代码功能如下：

*   第一行创建了一个 `GridSearchCV` 实例，并使用以下三个参数进行初始化：
    *   `estimator` 参数指定了将要使用的机器学习模型或估计器。此处设置为 `rf`，它是 `RandomForestClassifier` 的一个实例。

*   `param_grid` 参数指定了在网格搜索过程中将要探索的超参数网格及其可能取值。此处设置为之前定义的 `param_grid` 字典。

*   `cv` 参数决定了交叉验证过程中的折数。此处设置为 5，意味着数据集将被分成五折，网格搜索在评估不同的超参数组合时将执行五折交叉验证。对于像鸢尾花（Iris）数据集这样的小型数据集，五折交叉验证已经足够。它在使用大部分数据训练模型与拥有多个验证集进行稳健评估之间取得了平衡。

*   第二行代码启动了网格搜索过程。调用 `GridSearchCV` 对象的 `fit()` 方法，并传入训练数据 `X_train` 和对应的标签 `y_train`。然后，网格搜索算法执行搜索，使用交叉验证来训练和评估具有不同超参数组合的模型。

网格搜索算法会穷举搜索 `param_grid` 字典中指定的所有可能的超参数组合，并使用选定的评估指标来评估模型性能。它会根据该指标跟踪性能最佳的模型。网格搜索完成后，可以通过 `GridSearchCV` 对象的 `best_estimator_` 属性访问最佳模型。

步骤 8：获取最佳模型及其参数

```
[In]: best_model = grid_search.best_estimator_
[In]: best_max_depth = best_model.max_depth
[In]: best_num_estimators = best_model.n_estimators
[In]: best_min_samples_split = best_model.min_samples_split
[In]: best_min_samples_leaf = best_model.min_samples_leaf
```

在这段代码中，执行网格搜索后，提取最佳模型及其对应的超参数以供进一步分析。步骤如下：

1.  在第一行中，`GridSearchCV` 对象的 `best_estimator_` 属性存储了网格搜索过程中找到的最佳模型。该模型代表了根据所选评估指标达到最高性能的超参数组合。

2.  在第二行中，`best_model` 对象的 `max_depth` 属性检索了最佳模型的 `max_depth` 超参数的值。它代表了随机森林分类器中每棵独立树的最大深度。

3.  在第三行中，`best_model` 对象的 `n_estimators` 属性检索了最佳模型的 `n_estimators` 超参数的值。它代表了随机森林分类器中决策树的数量。

4.  在第四行中，`best_model` 对象的 `min_samples_split` 属性检索了最佳模型的 `min_samples_split` 超参数的值。它代表了在随机森林分类器的决策树中，分裂一个内部节点所需的最小样本数。

5.  在第五行中，`best_model` 对象的 `min_samples_leaf` 属性检索了最佳模型的 `min_samples_leaf` 超参数的值。该超参数定义了在随机森林分类器的决策树中，一个叶子节点所需的最小样本数。

通过访问 `best_model` 对象的这些属性，我们可以获得在网格搜索中产生最佳性能的最优超参数值。

步骤 9：使用最佳模型对测试数据进行预测

```
[In]: y_pred = best_model.predict(X_test)
```

在这行代码中，我们使用最佳模型来预测测试数据的标签。

步骤 10：评估模型性能

```
[In]: accuracy = accuracy_score(y_test, y_pred)
[In]: print("Test Accuracy:", accuracy)
[Out]: 1.0
```

此步骤使用准确率指标评估模型性能。第一行通过比较预测标签与真实标签来计算模型的准确率。第二行打印模型在测试数据上的准确率。

步骤 11：打印最佳参数值

```
[In]: print("Best max_depth:", best_max_depth)
[In]: print("Best n_estimators:", best_num_estimators)
[In]: print("Best min_samples_split:", best_min_samples_split)
[In]: print("Best min_samples_leaf:", best_min_samples_leaf)
[Out]:
Best max_depth: 5
Best n_estimators: 50
Best min_samples_split: 3
Best min_samples_leaf: 1
```

在最后一步中，获取最佳模型及其对应的超参数后，代码打印出这些超参数的值。

以下是此过程的详细说明：

1.  第一行打印了最佳模型的 `max_depth` 超参数的值，它代表了随机森林分类器中每棵独立树的最大深度。

2.  第二行打印了最佳模型的 `n_estimators` 超参数的值，它代表了随机森林分类器中决策树的数量。

3.  第三行打印了最佳模型的 `min_samples_split` 超参数的值，它代表了在随机森林分类器的决策树中，分裂一个内部节点所需的最小样本数。

4.  最后一行打印了最佳模型的 `min_samples_leaf` 超参数的值，它代表了在随机森林分类器的决策树中，一个叶子节点所需的最小样本数。

以下是对输出每一行的解释：

*   **测试准确率**：模型在测试数据集上达到了完美的 1.0 准确率，意味着 100% 的样本被正确预测。

*   **最佳最大深度**：随机森林分类器中每棵决策树的最大深度的最优值为 5。

*   **最佳估计器数量**：随机森林分类器中决策树的最优数量为 50。

*   **最佳最小样本分裂数**：在随机森林分类器的决策树中，分裂一个内部节点所需的最小样本数的最优值为 3。

*   **最佳最小叶子样本数**：在随机森林分类器的决策树中，一个叶子节点所需的最小样本数的最优值为 1。该超参数定义了集成中树分裂的粒度，有助于模型有效泛化并对未见数据做出准确预测。



### PySpark 中的超参数调优

在本小节中，我们将使用 PySpark 代码复现 Scikit-Learn 的功能。与 Scikit-Learn 类似，我们首先导入必要的库，然后开始对随机森林的超参数进行微调：

**步骤 1：导入必要的库**

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.feature import VectorAssembler
[In]: from pyspark.ml.classification import RandomForestClassifier
[In]: from pyspark.ml.evaluation import
MulticlassClassificationEvaluator
[In]: from pyspark.ml.tuning import ParamGridBuilder, CrossValidator
[In]: from sklearn.datasets import load_iris
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.metrics import accuracy_score
[In]: import pandas as pd
```

在第一步中，我们导入必要的库。以下是每个导入的作用：

*   `SparkSession` 是与 Spark 交互的入口点。
*   `VectorAssembler` 用于将输入特征转换为单个向量列。
*   `RandomForestClassifier` 是 PySpark 中随机森林分类器的实现。
*   `MulticlassClassificationEvaluator` 用于通过计算准确率指标来评估模型性能。
*   `ParamGridBuilder` 和 `CrossValidator` 用于在 PySpark 中执行带交叉验证的超参数调优。
*   `load_iris` 用于加载鸢尾花数据集。
*   `train_test_split` 用于将数据集拆分为训练子集和测试子集，以便进行模型评估。
*   `accuracy_score` 用于通过比较预测标签和真实标签来计算模型的准确率。
*   `pandas as pd` 用于创建 Pandas DataFrame。

**步骤 2：加载鸢尾花数据集**

```
[In]: iris_data = load_iris()
```

这段代码简单地使用 `load_iris()` 函数加载了鸢尾花数据集。

**步骤 3：将数据和目标数组转换为 Pandas DataFrame**

```
[In]: X = iris_data.data
[In]: y = iris_data.target
```

这段代码将 `iris_data` 对象中的数据数组和目标数组转换为 Pandas DataFrame。第一行将特征数据赋值给变量 X，其中包含鸢尾花数据集的输入特征。第二行将同一 `iris_data` 对象中的目标标签赋值给变量 y。目标属性包含鸢尾花数据集对应的目标标签，代表花朵的品种。

**步骤 4：创建 SparkSession**

```
[In]: spark = SparkSession.builder.getOrCreate()
```

这行代码创建一个 Spark Session，以便与 Spark 进行交互。

**步骤 5：从鸢尾花数据集创建 Pandas DataFrame**

```
[In]: iris_pandas = pd.DataFrame(data=X,
columns=iris_data.feature_names)
[In]: iris_pandas["label"] = y
```

这段代码从鸢尾花数据集创建一个名为 `iris_pandas` 的 Pandas DataFrame，该数据集已在步骤 3 中加载到变量 X（特征数据）和 y（目标标签）中。更准确地说，第一行使用 Pandas DataFrame 构造函数创建了一个名为 `iris_pandas` 的新 DataFrame。它接受两个参数：`data` 和 `columns`。`data` 参数（X）是特征数据数组，`columns` 参数指定 DataFrame 的列名，这些列名从 `iris_data` 对象的 `feature_names` 属性中提取。

第二行向 `iris_pandas` DataFrame 添加一个名为 `label` 的新列，并将其值赋为 y 数组中的值（代表鸢尾花数据集的目标标签）。

**步骤 6：从 Pandas DataFrame 创建 PySpark DataFrame**

```
[In]: iris_df = spark.createDataFrame(iris_pandas)
```

此步骤的目的是将 Pandas DataFrame 转换为 PySpark DataFrame，以便我们可以对其应用 PySpark 代码。Spark 中的 `createDataFrame()` 函数用于执行此转换。

**步骤 7：将数据拆分为特征和标签，并组装特征**

```
[In]: feature_columns = iris_data.feature_names
[In]: assembler = VectorAssembler(inputCols=feature_columns,
outputCol="features")
[In]: iris_df = assembler.transform(iris_df)
```

这段代码将 PySpark DataFrame 中的数据拆分为特征和标签。第一行将特征列表赋值给变量 `feature_columns`。第二行创建一个名为 `assembler` 的 `VectorAssembler` 对象。这是 PySpark 中的一个转换器，用于将多个列合并为单个向量列。它接受两个参数：`inputCols` 指定输入特征列的名称（`feature_columns`），`outputCol` 指定输出向量列的名称（`features`）。

最后一行对 PySpark 的 `iris_df` DataFrame 应用 `assembler` 对象的 `transform` 方法。该方法接收输入 DataFrame，并向其中添加一个名为 `features` 的新列。这个新列包含由 `inputCols` 指定的输入特征的组合向量表示。

**步骤 8：将数据拆分为训练集和测试集**

```
[In]: X_train, X_test = iris_df.randomSplit([0.8, 0.2], seed=42)
```

这段代码将 `iris_df` 中的数据拆分为训练集和测试集。`randomSplit` 函数接受两个参数。第一个参数 `[0.8, 0.2]` 表示拆分数据的比例：80% 的数据将分配给训练集，20% 的数据将分配给测试集。第二个参数 `seed=42` 设置随机种子，以确保拆分的可重复性。

结果是一个包含两个 PySpark DataFrame 的列表：`X_train` 和 `X_test`。`X_train` DataFrame 包含随机抽样的 80% 数据，将用于训练分类器。`X_test` DataFrame 包含剩余的 20% 数据，将用于评估训练好的模型在未见数据上的性能。

**步骤 9：定义随机森林分类器**

```
[In]: spark.conf.set("spark.seed", 42)
[In]: rf = RandomForestClassifier(featuresCol="features",
labelCol="label")
```

这段代码创建了一个名为 `rf` 的 `RandomForestClassifier` 类实例，这是一种基于随机森林集成方法的分类任务算法。它接受两个参数：`featuresCol` 指定 PySpark DataFrame 中输入特征列的名称（`featuresCol=“features”`），`labelCol` 指定目标标签列的名称（`labelCol=“label”`）。

请注意，在 PySpark 的 `RandomForestClassifier` 中，没有可以直接在分类器构造函数中设置以控制随机性的名为 `seed` 的参数。与 Scikit-Learn 的 `RandomForestClassifier`（可以在构造函数中设置 `random_state` 参数）不同，PySpark 的 `RandomForestClassifier` 依赖于全局的 Spark Session 配置来控制随机性。

**步骤 10：定义用于调优的参数网格**

```
[In]: param_grid = ParamGridBuilder() \
.addGrid(rf.maxDepth, [2, 5, 7, 10]) \
.addGrid(rf.numTrees, [20, 50, 75, 100]) \
.addGrid(rf.minInstancesPerNode, [2, 3, 5, 10]) \
.addGrid(rf.minInfoGain, [0.0, 0.01, 0.02, 0.03]) \
.build()
```

这段代码定义了一个用于调优随机森林分类器超参数的参数网格。通过定义此网格，代码为每个超参数设置了一系列将在超参数调优过程中进行测试的值。网格构建器允许系统地评估不同的超参数组合，以找到随机森林分类器的最佳配置。

作为参考，PySpark 随机森林分类器的默认超参数如下：

*   maxDepth：5
*   numTrees：20
*   minInstancesPerNode：1
*   minInfoGain：0.0

现在，让我们解释一下我们为自定义默认值而设置的参数网格：

*   `param_grid = ParamGridBuilder()` 创建一个 `ParamGridBuilder` 类的实例，用于构建超参数组合的网格。
*   `.addGrid(rf.maxDepth, [2, 5, 7, 10])` 为随机森林分类器的 `maxDepth` 超参数添加一个值网格。它指定了一个值范围 `[2, 5, 7, 10]`，用于测试 `maxDepth` 参数。



*   `.addGrid(rf.numTrees, [20, 50, 75, 100])` 为模型的 numTrees 超参数添加一个值网格。它指定了要为 numTrees 参数测试的值范围 [20, 50, 75, 100]。

*   `.addGrid(rf.minInstancesPerNode, [2, 3, 5, 10])` 为模型的 minInstancesPerNode 超参数添加一个值网格。它指定了要为 minInstancesPerNode 参数测试的值范围 [2, 3, 5, 10]。

*   `.addGrid(rf.minInfoGain, [0.0, 0.01, 0.02, 0.03])` 为随机森林分类器的 minInfoGain 超参数添加一个值网格。它指定了要为 minInfoGain 参数测试的值范围 [0.0, 0.01, 0.02, 0.03]。该超参数控制决策树中进行分裂所需的最小信息增益。值越高意味着分裂标准越严格，值越低则允许更多分裂，可能导致模型更复杂。请注意，PySpark 对 minInfoGain 超参数使用小数，这与 Scikit-Learn 不同，后者对 min_samples_leaf 使用整数 [1, 2, 3, 4]。这种表示上的差异反映了两平台在实现上的不同，可能会影响准确率和最佳参数组合。

*   最后，`.build()` 通过组合所有指定的超参数网格来构建参数网格。

第 11 步：创建交叉验证器

```
[In]: evaluator =
MulticlassClassificationEvaluator(metricName="accuracy")
[In]: cross_validator = CrossValidator(estimator=rf,
estimatorParamMaps=param_grid, evaluator=evaluator, numFolds=5)
```

此代码创建一个交叉验证器对象，用于通过 k 折交叉验证执行模型选择和超参数调优。它利用随机森林分类器、参数网格和评估器，根据指定的指标（准确率）来评估并选择最佳模型配置。

以下是代码的详细说明：

*   第一行创建了一个名为 evaluator 的 `MulticlassClassificationEvaluator` 类实例。它用于根据指定的指标（准确率）评估分类模型的性能。该指标衡量的是正确分类实例的比例。

*   第二行创建了一个名为 cross_validator 的 `CrossValidator` 类实例。它用于通过 k 折交叉验证进行模型选择和超参数调优，并包含以下参数：
    *   estimator=rf 指定了要评估的模型，即随机森林分类器（rf）。

*   estimatorParamMaps=param_grid 指定了包含待测试的不同超参数组合的参数网格。

*   evaluator=evaluator 指定了用于评估模型性能的评估器对象。

*   numFolds=5 指定了交叉验证过程中要使用的折数或分区数。在本例中，将执行五折交叉验证。

第 12 步：将交叉验证器拟合到训练数据

```
[In]: cv_model = cross_validator.fit(X_train)
```

此行代码将交叉验证器（cross_validator）拟合到训练数据（X_train）。它会训练并评估多个具有不同超参数配置的随机森林模型。这有助于识别出能在训练数据上产生最高准确率的最佳超参数组合。得到的 cv_model 对象随后可用于对新的、未见过的数据进行预测。

第 13 步：获取最佳模型及其参数

```
[In]: best_model = cv_model.bestModel
[In]: best_max_depth = best_model.getMaxDepth()
[In]: best_num_estimators = best_model.getNumTrees
[In]: best_min_samples_split = best_model.getMinInstancesPerNode()
[In]: best_min_info_gain = best_model.getMinInfoGain()
```

此代码从训练好的交叉验证模型中检索最佳模型及其对应的超参数。这使我们能够访问并使用通过超参数调优过程找到的最优配置。

以下是每一步的说明：



*   第一行将交叉验证过程中找到的最佳模型赋值给变量 `best_model`。`cv_model` 对象包含交叉验证期间训练的所有模型的信息，而 `bestModel` 则根据评估指标（准确率）检索出性能最高的模型。

*   第二行从 `best_model` 对象中检索 `maxDepth` 超参数的值。它使用 `getMaxDepth()` 方法来获取随机森林中决策树的最大深度设置。

*   第三行从 `best_model` 对象中检索 `numTrees` 超参数的值。它使用 `getNumTrees` 属性来获取随机森林中决策树的数量。

*   第四行从 `best_model` 对象中检索 `minInstancesPerNode` 超参数的值。它使用 `getMinInstancesPerNode()` 方法来获取决策树中拆分内部节点所需的最小样本数。

*   最后一行从 `best_model` 对象中检索 `minInfoGain` 超参数的值。它使用 `getMinInfoGain()` 方法来获取决策树中发生拆分所需的最小信息增益。此参数控制拆分标准的严格程度，值越高意味着拆分标准越严格，而值越低则允许更多拆分，可能导致模型更复杂。

步骤 14：使用最佳模型对测试数据进行预测

```
[In]: predictions = best_model.transform(X_test)
```

此代码使用从超参数调优过程中获得的最佳模型对测试数据进行预测。这使我们能够评估模型在未见数据上的性能。可以进一步分析 `predictions` 对象，并将其与真实标签进行比较，以评估训练模型的准确性和有效性。

该代码对 `best_model` 对象应用 `transform` 方法，并传入测试数据 (`X_test`)。此方法将训练好的模型应用于测试数据并生成预测，预测结果存储在 `predictions` 对象中。

步骤 15：评估模型性能

```
[In]: accuracy = evaluator.evaluate(predictions)
```

此代码计算模型在测试数据上所做预测的准确率。这是通过将预测标签与测试数据中的真实标签进行比较来完成的。计算出的准确率值被赋值给变量 `accuracy`，可用于评估模型在测试数据上的性能。

步骤 16：打印测试准确率和最佳参数值

```
[In]: print("Test Accuracy:", accuracy)
[In]: print("Best maxDepth:", best_max_depth)
[In]: print("Best numTrees:", best_num_estimators)
[In]: print("Best minInstancesPerNode:", best_min_samples_split)
[In]: print("Best minInfoGain:", best_min_info_gain)
[Out]:
Test Accuracy: 0.97
Best maxDepth: 5
Best numTrees: 50
Best minInstancesPerNode: 5
Best minInfoGain: 0.0
```

在此步骤中，代码打印出与训练模型及其在测试数据上性能相关的各种指标和超参数。这提供了模型在测试数据上性能的概览，并突出了在训练过程中调优的超参数的最佳设置。

以下是代码每一行的解释：

*   `print("Test Accuracy:", accuracy)` 打印测试准确率的值。该值由变量 `accuracy` 表示，该变量是之前使用评估指标计算得出的。

*   `print("Best maxDepth:", best_max_depth)` 打印最佳最大深度超参数的值。

*   `print("Best numTrees:", best_num_estimators)` 打印最佳树数量超参数的值。

*   `print("Best minInstancesPerNode:", best_min_samples_split)` 打印最佳每节点最小实例数超参数的值。

*   `print("Best minInfoGain:", best_min_info_gain)` 打印最佳最小信息增益超参数的值。

接下来看输出结果，以下是这些结果的含义：

*   **测试准确率（约 0.97）**：这表示随机森林分类器在给定测试数据集上达到的测试准确率。值 0.97 代表准确率分数，是衡量模型预测与测试数据中真实标签一致程度的指标。该模型达到了 97% 的准确率。

*   **最佳 maxDepth（5）**：这揭示了在超参数调优过程中为最大深度超参数找到的最佳值。值 5 表示随机森林中决策树的最佳最大深度设置为 5。

*   **最佳 numTrees（50）**：这表示为随机森林分类器选择的最佳树数量。值 50 表示森林中最佳树数量设置为 50。

*   **最佳 minInstancesPerNode（5）**：这显示了每个树节点最小实例数的最佳值，这是一个控制决策树拆分过程的超参数。值 5 表示每节点最小实例数的最佳设置为 5，这意味着一个节点只有在包含至少 5 个实例时才会被进一步拆分。

*   **最佳 minInfoGain（0.0）**：这表示最小信息增益超参数的最佳值。在这种情况下，`minInfoGain` 的最佳设置为 0.0，意味着即使信息增益有很小的改进也足以触发决策树中的拆分。值为 0.0 表示拆分标准不是很严格，可能导致模型更复杂，拆分更细致。

最后，值得注意的是，PySpark 的结果与 Scikit-Learn 的结果略有不同。两种算法在最佳 `maxDepth` 和最佳 `numTrees` 上达成一致，两者分别为 5 和 50。然而，在最佳 `minInstancesPerNode`（Scikit-Learn 中为 3，PySpark 中为 5）和最佳 `minInfoGain`（Scikit-Learn 中为 1，PySpark 中为 0）上存在差异。PySpark 中的准确率也略低（97% 对比 100%）。这些差异反映了两个框架之间一些细微的实现差异。

## 整合所有内容

现在，让我们将前面步骤中的所有相关代码编译成一个统一的代码块。这将允许读者在 Scikit-Learn 和 PySpark 中将代码作为一个整体来执行。



### Scikit-Learn

步骤 1：导入必要的库

```
[In]: from sklearn.datasets import load_iris
[In]: from sklearn.ensemble import RandomForestClassifier
[In]: from sklearn.model_selection import train_test_split, GridSearchCV
[In]: from sklearn.metrics import accuracy_score
```

步骤 2：使用 `load_iris()` 函数加载鸢尾花数据集

```
[In]: iris_sklearn = load_iris()
```

步骤 3：将数据拆分为特征和标签

```
[In]: X = iris_sklearn.data
[In]: y = iris_sklearn.target
```

步骤 4：将数据拆分为训练集和测试集

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

步骤 5：定义随机森林分类器

```
[In]: rf = RandomForestClassifier(random_state=42)
```

步骤 6：定义用于调优的参数网格

```
[In]: param_grid = {
'max_depth': [2, 5, 7, 10],
'n_estimators': [20, 50, 75, 100],
'min_samples_split': [2, 3, 5, 10],
'min_samples_leaf': [1, 2, 3, 4],
}
```

步骤 7：使用 `GridSearchCV` 执行网格搜索

```
[In]: grid_search = GridSearchCV(estimator=rf, param_grid=param_grid,
cv=5)
[In]: grid_search.fit(X_train, y_train)
```

步骤 8：获取最佳模型及其参数

```
[In]: best_model = grid_search.best_estimator_
[In]: best_max_depth = best_model.max_depth
[In]: best_num_estimators = best_model.n_estimators
[In]: best_min_samples_split = best_model.min_samples_split
[In]: best_min_samples_leaf = best_model.min_samples_leaf
```

步骤 9：使用最佳模型对测试数据进行预测

```
[In]: y_pred = best_model.predict(X_test)
```

步骤 10：评估模型性能

```
[In]: accuracy = accuracy_score(y_test, y_pred)
[In]: print("Test Accuracy:", accuracy)
```

步骤 11：打印最佳参数值

```
[In]: print("Best max_depth:", best_max_depth)
[In]: print("Best n_estimators:", best_num_estimators)
[In]: print("Best min_samples_split:", best_min_samples_split)
[In]: print("Best min_samples_leaf:", best_min_samples_leaf)
```

### PySpark

步骤 1：导入必要的库

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.feature import VectorAssembler
[In]: from pyspark.ml.classification import RandomForestClassifier
[In]: from pyspark.ml.evaluation import
MulticlassClassificationEvaluator
[In]: from pyspark.ml.tuning import ParamGridBuilder, CrossValidator
[In]: from sklearn.datasets import load_iris
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.metrics import accuracy_score
[In]: import pandas as pd
```

步骤 2：使用 `load_iris()` 函数加载鸢尾花数据集

```
[In]: iris_data = load_iris()
```

步骤 3：将数据和目标数组转换为 Pandas DataFrame

```
[In]: X = iris_data.data
[In]: y = iris_data.target
```

步骤 4：创建一个 SparkSession

```
[In]: spark = SparkSession.builder.getOrCreate()
```

步骤 5：从鸢尾花数据集创建一个 Pandas DataFrame

```
[In]: iris_pandas = pd.DataFrame(data=X,
columns=iris_data.feature_names)
[In]: iris_pandas["label"] = y
```

步骤 6：从 Pandas DataFrame 创建一个 PySpark DataFrame

```
[In]: iris_df = spark.createDataFrame(iris_pandas)
```

步骤 7：将数据拆分为特征和标签，并组装特征

```
[In]: feature_columns = iris_data.feature_names
[In]: assembler = VectorAssembler(inputCols=feature_columns,
outputCol="features")
[In]: iris_df = assembler.transform(iris_df)
```

步骤 8：将数据拆分为训练集和测试集

```
[In]: X_train, X_test = iris_df.randomSplit([0.8, 0.2], seed=42)
```

步骤 9：定义随机森林分类器

```
[In]: spark.conf.set("spark.seed", 42)
[In]: rf = RandomForestClassifier(featuresCol="features",
labelCol="label")
```

步骤 10：定义用于调优的参数网格

```
[In]: param_grid = ParamGridBuilder() \
.addGrid(rf.maxDepth, [2, 5, 7, 10]) \
.addGrid(rf.numTrees, [20, 50, 75, 100]) \
.addGrid(rf.minInstancesPerNode, [2, 3, 5, 10]) \
.addGrid(rf.minInfoGain, [0.0, 0.01, 0.02, 0.03]) \
.build()
```

步骤 11：创建交叉验证器

```
[In]: evaluator =
MulticlassClassificationEvaluator(metricName="accuracy")
[In]: cross_validator = CrossValidator(estimator=rf,
estimatorParamMaps=param_grid, evaluator=evaluator, numFolds=5)
```

步骤 12：将交叉验证器拟合到训练数据

```
[In]: cv_model = cross_validator.fit(X_train)
```

步骤 13：获取最佳模型及其参数

```
[In]: best_model = cv_model.bestModel
[In]: best_max_depth = best_model.getMaxDepth()
[In]: best_num_estimators = best_model.getNumTrees
[In]: best_min_samples_split = best_model.getMinInstancesPerNode()
[In]: best_min_info_gain = best_model.getMinInfoGain()
```

步骤 14：使用最佳模型对测试数据进行预测

```
[In]: predictions = best_model.transform(X_test)
```

步骤 15：评估模型性能

```
[In]: accuracy = evaluator.evaluate(predictions)
```

步骤 16：打印测试准确率

```
[In]: print("Test Accuracy:", accuracy)
[In]: print("Best maxDepth:", best_max_depth)
[In]: print("Best numTrees:", best_num_estimators)
[In]: print("Best minInstancesPerNode:", best_min_samples_split)
[In]: print("Best minInfoGain:", best_min_info_gain)
```

## 总结

在本章中，我们探讨了机器学习中的一个重要主题：超参数调优。这是模型构建中的一个关键步骤，涉及为给定算法找到最优的超参数组合。我们还提供了 Scikit-Learn 和 PySpark 中此类参数的示例，并深入探讨了如何使用鸢尾花数据集微调随机森林中关键超参数的详细示例。

在下一章中，我们将探讨另一个同样重要的主题：流水线。通过利用流水线的强大功能，数据科学家可以自动化和标准化建模工作流程中的步骤。这有助于构建稳健且可扩展的模型，增强模型的可解释性，并促进额外预处理步骤和特征工程技术的集成。



# 17. 使用 Scikit-Learn 与 PySpark 构建流水线

在本章中，我们将探讨 Scikit-Learn 和 PySpark 中的流水线技术。通过利用流水线的强大功能，数据科学家可以自动化和标准化建模工作流程中的各个步骤。这有助于构建稳健且可扩展的模型，增强模型的可解释性，并促进额外预处理步骤和特征工程技术的集成。

为了说明流水线如何简化建模流程并提高效率，我们以模拟数据集为例，实现回归流水线。我们生成反映回归问题的模拟数据，其中包含一组输入特征和一个连续目标变量。通过使用流水线技术，我们演示了如何在 Scikit-Learn 和 PySpark 中对数据进行预处理、应用特征缩放以及构建回归模型。虽然我们使用回归来演示流水线方法，但该技术可迁移至任何其他算法，无论是回归任务还是分类任务。

首先，我们介绍流水线的概念，并讨论其在机器学习背景下的重要性。我们将探讨流水线如何使我们能够将各种数据预处理和建模步骤封装并排序成一个连贯的工作流程。通过以这种方式构建分析，我们可以确保可重复性和可维护性，并轻松尝试不同的预处理和建模技术组合。

接着，我们探索 Scikit-Learn 中的流水线功能。我们演示如何构建一个流水线，其中包含使用 `StandardScaler` 进行特征缩放的步骤，以及使用 `LinearRegression` 类进行回归的步骤。我们将逐步讲解如何将流水线拟合到训练数据、对测试数据进行预测以及评估模型性能。

在本章的最后部分，我们将重点转向 PySpark，使计划从 Scikit-Learn 迁移的数据科学家能够受益于强大的分布式数据处理框架。PySpark 为处理大规模数据集提供了可扩展且高效的解决方案。我们将说明如何使用 PySpark 的 MLlib 库构建回归流水线。我们的流水线包含用于组装输入特征的 `VectorAssembler`、用于特征缩放的 `StandardScaler` 以及用于回归建模的 `LinearRegression` 类。我们将探讨将流水线拟合到训练数据、对测试数据进行预测以及评估模型性能的过程。

在本章结束时，读者将深入了解如何利用流水线技术在这两个广泛使用的框架中简化机器学习工作流程。

## 流水线的重要性

在机器学习领域，管理和组织数据预处理与建模的各个阶段可能是一项复杂的任务。它通常涉及多个步骤，例如数据清洗、特征缩放、特征工程和模型拟合。跟踪这些步骤并确保其正确排序可能具有挑战性，尤其是在尝试不同的技术组合或处理大型数据集时。

这正是流水线概念发挥作用的地方。流水线是一种强大的工具，它允许我们将这些预处理和建模步骤封装并排序成一个连贯且精简的工作流程。通过使用流水线来构建分析，我们可以获得几个关键优势。

一个关键优势是封装预处理和建模步骤。流水线提供了一个框架，用于封装机器学习过程中的各个步骤。每个步骤，例如特征缩放或特征工程，都可以定义为流水线内的一个独立组件。这种模块化方法增强了代码的组织性和可读性。此外，它使我们能够轻松修改或替换特定步骤，而不会影响流水线的其余部分。通过封装这些步骤，我们可以保持清晰的关注点分离，并确保每个步骤有效地执行其指定任务。

第二个优势是无缝的工作流程和可重复性。流水线使我们能够为机器学习定义一个清晰且结构化的流程。每个步骤按顺序执行，确保数据从一个阶段无缝流向另一个阶段。这确保了可重复性，因为整个工作流程可以轻松地在新数据集上复制或与同事共享。通过提供定义明确的结构，流水线减少了因手动干预或临时数据处理而可能出现的错误和不一致的可能性。

第三个优势是实验性和灵活性。通过将预处理和建模步骤封装在流水线内，我们可以轻松修改和尝试不同的技术组合。例如，我们可以在不同的特征缩放方法之间切换，或者尝试替代的特征工程方法，而只需进行最少的代码更改。这种灵活性使我们能够高效地探索各种途径并优化回归模型。

最后，流水线增强了可维护性。随着项目的发展，维护和更新机器学习模型可能变得越来越具有挑战性。借助流水线，我们可以通过拥有一个封装整个工作流程的单一代码库来确保可维护性。这使我们能够进行修改或添加新的预处理或建模步骤，而不会破坏整体结构。流水线还有助于代码重用，因为各个步骤可以在多个机器学习项目之间共享。通过采用模块化和可维护的方法，流水线帮助我们有效管理机器学习项目的复杂性。

在本章的后续部分，我们将探讨在两个广泛使用的框架（Scikit-Learn 和 PySpark）中实现流水线。我们将深入探讨构建流水线的细节，并演示它们在回归分析中的实际应用。通过清晰的示例和逐步解释，我们将强调使用流水线来简化建模流程并提高机器学习工作流程效率的好处。



### 使用 Scikit-Learn 构建流水线

Scikit-Learn 充分利用了流水线（Pipeline）概念的优势。通过使用 `Pipeline` 类，你可以无缝地连接转换器和模型，将整个过程视为一个单一的 Scikit-Learn 模型。这种方法简化了工作流程，并实现了高效且直观的数据转换和建模。

该过程的第一步是导入必要的库：

**步骤 1：导入必要的库**

以下代码导入了流水线过程所需的几个函数和类：

```
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.pipeline import Pipeline
[In]: from sklearn.preprocessing import StandardScaler
[In]: from sklearn.linear_model import LinearRegression
[In]: from sklearn.metrics import mean_squared_error
[In]: import numpy as np
```

以下是每个函数和类的作用说明：

*   `train_test_split:` 此函数用于将数据集拆分为训练子集和测试子集。它会将数据随机分成两部分：一部分用于训练回归模型，另一部分用于评估其性能。

*   `Pipeline:` 此类提供了一种便捷的方式，可以将多个数据处理步骤链接到一个单一对象中。它允许我们将一系列转换器（数据预处理步骤）和一个估计器（机器学习模型）定义为一个整体单元。

*   `StandardScaler`: 此类用于通过减去均值并除以标准差来缩放每个特征，从而得到一个均值为 0、标准差为 1 的转换后特征。

*   `LinearRegression:` 此类代表一个线性回归模型，该模型用于建模一个因变量与一个或多个自变量之间的关系。

*   `mean_squared_error:` 此函数是一个常见的评估指标，用于衡量回归问题中预测值与实际值之间的平均平方差。它提供了一种评估回归模型性能的方法。

*   `numpy as np:` 这是 Python 中的一个计算库，为大型多维数组提供支持，并提供一组用于操作这些数组的数学函数。我们用它来生成模拟数据集。

**步骤 2：生成数据**

```
[In]: np.random.seed(0)
[In]: num_samples = 1000
[In]: X = np.random.rand(num_samples, 2) * 10
[In]: y = np.random.rand(num_samples) * 20 - 10
```

在此步骤中，我们生成一些随机数据来说明流水线过程。第一行设置了 NumPy 中随机数生成器的种子值。通过设置特定的种子（例如 0），我们可以确保每次运行代码时都会生成相同的随机数序列。

第二行将值 1000 赋给变量 `num_samples`。这表示我们要生成的样本（数据点）数量。

第三行使用 `rand` 函数生成一个介于 0 和 1 之间的随机数的二维数组（X）。X 的形状是 `(num_samples, 2)`，表示将有 `num_samples` 行和 2 列。将随机数乘以 10 可将范围从 [0, 1) 缩放至 [0, 10)。

最后一行使用 `rand` 函数生成一个介于 0 和 1 之间的随机数的一维数组（y）。y 的形状是 `(num_samples)`，表示它将有 `num_samples` 个元素。将随机数乘以 20 可将范围扩展至 [0, 20)，再减去 10 可将范围移至 [-10, 10)。

我们可以通过以下方式查看 X 和 y 的前五行：

X 的前五行：

```
[In]: print(X[0:5])
[Out]:
[[5.48813504 7.15189366]
[6.02763376 5.44883183]
[4.23654799 6.45894113]
[4.37587211 8.91773001]
[9.63662761 3.83441519]]
```

y 的前五个元素：

```
[In]: print(y[0:5])
[Out]: [ 6.23036941 -0.47832028  0.4631198  -4.98958827  2.10086034]
```

在生成上述输出的代码中，`X[0:5]` 这一行对 X 变量进行切片，以检索从索引 0 到索引 4（含）的行，这些行对应 X 的前五行。然后打印出这些行，显示 X 的前五行。类似地，`y[0:5]` 对 y 变量进行切片，以检索从索引 0 到索引 4（含）的元素，这些元素对应 y 的前五个元素。然后打印出这些元素，显示 y 的前五个元素。

**步骤 3：将数据拆分为训练集和测试集**

以下代码使用 `train_test_split` 函数将数据集拆分为训练子集和测试子集：

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y,
test_size=0.2, random_state=0)
```

在这段代码中，X 代表输入特征或自变量，y 代表相应的目标变量或因变量。`test_size=0.2` 参数指定 20% 的数据将保留用于测试，其余 80% 将用于训练机器学习模型。`random_state=0` 参数设置随机种子以确保可重复性。变量 `X_train`、`X_test`、`y_train`、`y_test` 代表拆分后得到的数据子集。`X_train` 和 `y_train` 将包含训练数据，而 `X_test` 和 `y_test` 将包含测试数据。

**步骤 4：定义流水线阶段**

这是流水线过程中的一个重要步骤。在以下代码中，定义了机器学习流水线的两个阶段（我们可以定义任意数量的阶段）：

```
[In]: scaler = StandardScaler()
[In]: regression = LinearRegression()
```

第一行创建了 `StandardScaler` 类的一个实例，并将其赋给变量 `scaler`。这是一个用于标准化数值特征的数据预处理步骤。它通过减去均值并除以标准差来缩放特征，从而得到具有零均值和单位方差的转换后特征。

第二行创建了 `LinearRegression` 类的一个实例，并将其赋给变量 `regression`。这个类是我们的机器学习模型，用于线性回归，其目标是通过将线性方程拟合到观测数据来建模因变量与自变量之间的关系。

在典型的流水线中，如下一步所示，这些阶段会使用 `Pipeline` 类组合在一起，其中每个阶段被定义为一个由名称（可选）和相应对象组成的元组。

**步骤 5：创建流水线**

在以下代码中，使用 `Pipeline` 类创建了一个流水线：

```
[In]: pipeline = Pipeline(steps=[('scaler', scaler), ('regression',
regression)])
```

这行代码创建了 `Pipeline` 类的一个实例，并将其赋给变量 `pipeline`。此类允许我们将一系列数据处理步骤和一个估计器定义为一个整体单元。

`Pipeline` 构造函数的 `steps` 参数接受一个元组列表，其中每个元组代表流水线中的一个步骤。在本例中，该列表包含两个元组：

1.  `('scaler', scaler)`：这为步骤定义了名称 `'scaler'`，并将 `scaler` 对象分配给它。这对应于流水线中的 `StandardScaler` 阶段，负责缩放输入特征。

2.  `('regression', regression)`：这为步骤定义了名称 `regression`，并将 `regression` 对象分配给它。这对应于流水线中的 `LinearRegression` 阶段，代表线性回归模型。

需要注意的是，列表中元组的顺序决定了步骤的执行顺序。

**步骤 6：将流水线拟合到训练数据**

在此步骤中，我们将之前定义的流水线拟合到训练数据：

```
[In]: pipeline.fit(X_train, y_train)
```



这行代码在管道对象上调用了 `fit` 方法，该方法将管道拟合到训练数据上。`X_train` 和 `y_train` 变量代表用于拟合管道的训练数据。`X_train` 包含输入特征，`y_train` 包含对应的目标变量或标签。当我们在管道上调用 `fit` 时，它会应用 `StandardScaler` 来缩放特征，然后将 `LinearRegression` 模型拟合到转换后的数据上。

拟合过程涉及根据提供的训练数据估计线性回归模型的系数。这一步使管道能够从训练数据中学习，并为模型进行预测做好准备。

**步骤 7：打印系数**

此步骤从拟合后的管道回归模型中提取系数和截距：

```
[In]: coefficients = pipeline.named_steps['regression'].coef_
[In]: intercept = pipeline.named_steps['regression'].intercept_
[In]: print("Coefficients:", coefficients)
[In]: print("Intercept:", intercept)
[Out]:
Coefficients: [0.1555 -0.0093]
Intercept: 0.0938
```

第一行代码检索作为管道一部分的回归模型的系数。管道的 `named_steps` 属性允许通过特定名称访问各个步骤。在此例中，`regression` 是分配给 `LinearRegression` 步骤的名称。通过在 `LinearRegression` 对象上使用 `.coef_`，我们获得了模型的权重或参数。

第二行代码检索回归模型的截距（或偏置）项。与上一行类似，它使用名称 `regression` 访问 `LinearRegression` 步骤，并通过 `.intercept_` 检索截距值。

最后两条语句打印系数和截距。系数表示线性回归模型中分配给每个特征的权重，指示每个特征对预测结果的贡献。截距项表示当所有特征值为零时的基线预测值。

**步骤 8：在测试数据上进行预测**

以下代码使用拟合后的管道对测试数据进行预测：

```
[In]: y_pred = pipeline.predict(X_test)
```

这行代码使用拟合后的管道对象的 `predict` 方法对测试数据 `X_test` 进行预测。该方法将管道步骤中定义的转换应用于输入数据，然后使用训练好的模型生成预测。`X_test` 变量代表测试数据，其中包含我们想要进行预测的输入特征。

通过在管道上调用 `predict`，它会使用 `StandardScaler` 步骤进行缩放。然后，它使用训练好的 `LinearRegression` 模型基于缩放后的数据生成预测。得到的预测结果被赋值给变量 `y_pred`，这是一个包含与测试数据对应的目标变量预测值的数组或向量。

**步骤 9：打印前五个实际值与预测值**

在此步骤中，我们查看目标变量的前五个实际值及对应的预测值：

|     | 实际值 | 预测值 |
| --- | --- | --- |
| 0   | 7.59 | 0.03 |
| 1   | 8.62 | -0.07 |
| 2   | 6.43 | 0.02 |
| 3   | 7.07 | 0.12 |
| 4   | -1.04 | 0.19 |

```
[In]: import pandas as pd
[In]: data = {'Actual': y_test[:5], 'Predicted': y_pred[:5]}
[In]: df = pd.DataFrame(data)
[In]: print(df.head())
[Out]:
```

代码的第一行将 Pandas 导入为 pd。第二行创建了一个名为 `data` 的 Python 字典，其键代表我们想要在 DataFrame 中创建的列名。值是通过切片 `y_test` 和 `y_pred` 的前五个元素获得的，这些元素包含来自机器学习模型的实际值和预测值。在下一行代码中，通过将 `data` 字典作为参数传递给 `DataFrame()` 构造函数，创建了 Pandas DataFrame `df`。最后，`head()` 方法显示 DataFrame 的前几行。

从输出中我们可以看到，实际值和预测值之间存在很大差异，这表明模型的性能并不理想。对于当前任务来说，这没问题，因为我们使用的模拟数据纯粹是为了说明目的。

**步骤 10：评估模型**

在最后一步中，我们通过计算测试数据上的均方根误差（RMSE）来评估模型的性能：

```
[In]: rmse = np.sqrt(mean_squared_error(y_test, y_pred))
[In]: print("Root Mean Squared Error (RMSE) on test data:", rmse)
[Out]: Root Mean Squared Error (RMSE) on test data: 5.55
```

代码的第一行使用 `mean_squared_error` 函数计算 RMSE。该函数接受两个参数：实际目标值（`y_test`）和预测目标值（`y_pred`）。它计算两组值之间的均方误差。然后对均方误差应用 `np.sqrt` 函数以获得 RMSE。这是一个常用的指标，用于衡量预测值与实际值之间的平均差异。它表示平方差平均值的平方根。计算出的 RMSE 值被赋值给变量 `rmse`。

下一行打印在测试数据上计算出的 RMSE，约为 5.55。该值提供了模型准确性或拟合优度的度量。较低的 RMSE 表示更好的预测性能，因为它代表预测值与实际值之间的平均差异更小。



### 使用 PySpark 构建流水线

与 Scikit-Learn 类似，PySpark 也充分利用了流水线（Pipeline）这一概念。通过使用 `Pipeline` 类，我们可以轻松地将转换器和模型连接起来，将整个过程视为一个连贯的 PySpark 模型。这种方法简化了工作流程，使得数据转换和建模能够无缝、高效地进行。

与我们使用 Scikit-Learn 时类似，首先需要导入必要的 PySpark 库：

**步骤 1：导入必要的库**

流水线过程的第一步是从 PySpark 库中导入必要的类：

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.feature import VectorAssembler, StandardScaler
[In]: from pyspark.ml.regression import LinearRegression
[In]: from pyspark.ml import Pipeline
[In]: from pyspark.ml.evaluation import RegressionEvaluator
```

以下是这些类用途的描述：

*   `SparkSession:` 这是 Spark 功能的入口点。它允许我们创建 Spark 应用程序并以结构化的方式与数据进行交互。

*   `VectorAssembler:` 这是一个特征转换器，它将给定的列列表合并成一个单一的向量列。它接收一组输入列，并生成一个包含这些列值向量的新列。

*   `StandardScaler:` 这个类是一个特征转换器，通过减去均值并缩放到单位方差来标准化数值特征。它转换特征值，使其均值为零，标准差为一。

*   `LinearRegression:` 这是一种基于一个或多个预测变量来预测连续目标变量的算法。它根据输入数据拟合一个线性方程，其中假设目标变量是预测变量的线性组合。

*   `Pipeline:` 这是一个实用工具，允许我们将多个转换和估计器阶段链接在一起，形成一个工作流程。它有助于组织和执行对数据的一系列操作，例如特征工程、模型训练和评估。

*   `RegressionEvaluator:` 这是一个用于在 PySpark 中评估回归模型的类。它提供了各种指标和方法来评估回归模型的性能，例如均方根误差（RMSE）。

**步骤 2：创建 SparkSession**

```
[In]: spark = SparkSession.builder.getOrCreate()
```

这行代码创建了一个 Spark Session，使我们能够与 Spark 进行交互。

**步骤 3：生成模拟数据**

在此步骤中，我们生成模拟数据来演示 PySpark 中流水线的概念：

| id | feature1 | feature2 | label |
| --- | --- | --- | --- |
| 0 | 3.51 | 4.01 | -2.85 |
| 1 | 1.97 | 4.49 | 2.36 |
| 2 | 8.14 | 3.70 | -6.81 |
| 3 | 7.40 | 2.95 | 0.93 |
| 4 | 3.64 | 1.12 | -3.44 |

```
[In]: num_samples = 1000
[In]: simulated_data = spark.range(num_samples).selectExpr(
"id as id",
"(RAND() * 10) as feature1",
"(RAND() * 5) as feature2",
"(RAND() * 20 - 10) as label")
[Out]:
```

在上述代码中，我们首先将值 1000 赋给变量 num_samples，它表示我们要为模拟数据集生成的行数或样本数。然后，我们使用 `spark.range()` 函数创建了一个名为 simulated_data 的 DataFrame。这会生成一个包含单列 id 的 DataFrame，其中的数字从 0 到 num_samples-1。这作为模拟数据的索引列。

第二行中的 `selectExpr()` 方法用于选择和转换 DataFrame 中的列。它接受多个参数，每个参数指定一个要应用于列的转换表达式：

*   表达式 “(RAND() * 10) as feature1” 为每一行生成一个介于 0 和 10 之间的随机值，并将其分配给名为 feature1 的新列。`RAND()` 函数生成一个介于 0 和 1 之间的随机数，乘以 10 后将其范围缩放至 0 到 10。

*   表达式 “(RAND() * 5) as feature2” 为每一行生成一个介于 0 和 5 之间的随机值，并将其分配给名为 feature2 的新列。与上一个表达式类似，它使用 `RAND()` 函数生成一个介于 0 和 1 之间的随机数，并将其缩放至 0 到 5。

*   表达式 “(RAND() * 20 - 10) as label” 为每一行生成一个介于 -10 和 10 之间的随机值，并将其分配给名为 label 的新列。`RAND()` 函数生成一个介于 0 和 1 之间的随机数，通过乘以 20 再减去 10，其范围被缩放至 -10 到 10。

如输出所示，生成的 simulated_data DataFrame 有四列：id（由 `spark.range()` 生成）、feature1（0 到 10 之间的随机值）、feature2（0 到 5 之间的随机值）和 label（-10 到 10 之间的随机值）。

**步骤 4：将数据拆分为训练集和测试集**

```
[In]: train_ratio = 0.8
[In]: test_ratio = 1 - train_ratio
[In]: training_data, testing_data = simulated_data.randomSplit([train_ratio, test_ratio], seed=42)
```

在这段代码中，我们使用 `randomSplit()` 函数将 simulated_data DataFrame 拆分为训练集和测试集。第一行将值 0.8 赋给变量 train_ratio，表示 80% 的数据将用于训练。第二行通过从 1 中减去 train_ratio 来计算测试集的比例。由于训练和测试比例之和应等于 1，因此从 1 中减去 train_ratio 得到剩余部分，即测试比例。

最后一行执行将数据实际拆分为训练集和测试集的操作。`randomSplit()` 函数接受两个参数：一个用于训练集和测试集的比例数组，以及一个种子参数，以确保数据的随机拆分是可重现的。

**步骤 5：定义流水线阶段**

在此步骤中，使用 PySpark ML 库定义机器学习工作流程的流水线阶段：

```
[In]: assembler = VectorAssembler(
inputCols=["feature1", "feature2"], outputCol="features")
[In]: scaler = StandardScaler(
inputCol="features", outputCol="scaledFeatures")
[In]: regression = LinearRegression(
featuresCol="ScaledFeatures", labelCol="label")
```

第一行创建了一个 `VectorAssembler` 对象，这是流水线中的一个转换阶段。该组装器将多个输入列（feature1 和 feature2）合并成一个单一的输出列（features）。`inputCols` 参数指定要组装成向量的输入列的名称。在这种情况下，它包括 feature1 和 feature2 列。`outputCol` 参数指定输出列的名称，该列将包含组装后的特征向量。

第二行创建了一个 `StandardScaler` 对象，用于标准化从前一阶段获得的特征向量列。它将特征向量列 features 作为输入（由 `inputCol` 参数指定），并创建一个名为 scaledFeatures 的新列，其中包含标准化后的特征值。缩放是通过减去每个特征的均值并除以标准差来执行的。

第三行创建了一个 `LinearRegression` 对象，这是流水线中的一个估计器阶段，用于构建线性回归模型。`featuresCol` 参数指定包含特征的输入列的名称。它与 `VectorAssembler` 产生的输出列名称（scaledFeatures）相匹配。`labelCol` 参数指定包含标签的输入列的名称。

通过定义这些流水线阶段，我们为机器学习流水线设置了数据转换和建模步骤。这些阶段可以与其他阶段（例如特征转换或附加模型）进一步组合，以在 PySpark 中构建一个用于数据预处理和建模的综合流水线。

**步骤 6：创建流水线**



```
[In]: pipeline_stages = [assembler, scaler, regression]
[In]: pipeline = Pipeline(stages=pipeline_stages)
```

在这段代码中，实际的流水线是通过 PySpark 的 `Pipeline` 类创建的。PySpark 使用与 Scikit-Learn 相同的名称和概念并非巧合，因为它正是从 Scikit-Learn 的流水线实现中汲取了灵感。

该流水线包含三个阶段：assembler（特征组装器）、scaler（缩放器）和 regression（回归器）。

*   assembler 阶段负责将输入特征组装成一个向量。它接收 feature1 和 feature2 这两列作为输入，并创建一个名为 features 的新列，该列包含特征的向量表示。

*   scaler 阶段是一个 `StandardScaler`，用于对上一阶段生成的特征向量列进行标准化。它接收特征向量列 features 作为输入，并创建一个名为 scaledFeatures 的新列，该列包含标准化后的特征值。

*   regression 阶段是一个 `LinearRegression` 估计器，它对缩放后的特征向量列执行线性回归。它接收缩放后的特征向量列 scaledFeatures 和标签列 label 作为输入，以训练线性回归模型。

代码中的 pipeline_stages 列表按执行顺序包含了各个阶段。`Pipeline` 对象以 pipeline_stages 作为输入创建，代表了整个流水线。

**步骤 7：将流水线拟合到训练数据**

```
[In]: pipeline_model = pipeline.fit(training_data)
```

在这一行代码中，使用 `fit()` 方法将流水线拟合到训练数据。拟合流水线意味着按顺序执行流水线的每个阶段，应用定义的转换，并在训练数据上训练指定的模型。

`fit()` 方法接收训练数据作为输入，并依次应用每个阶段的转换，从第一阶段（assembler）开始，到最后阶段（regression）结束。每个阶段的输出作为输入传递给流水线中的下一个阶段。在拟合过程中，流水线阶段中定义的转换被应用于训练数据，并且最后阶段（regression）中指定的模型使用转换后的数据进行训练。

调用 `fit()` 的结果是一个拟合后的流水线模型（pipeline_model），它封装了训练好的模型以及已应用的转换。这个拟合后的流水线模型可用于对新数据进行预测或评估模型性能。

**步骤 8：打印系数**

```
[In]: coefficients = pipeline_model.stages[-1].coefficients
[In]: intercept = pipeline_model.stages[-1].intercept
[In]: print("Coefficients:", coefficients)
[In]: print("Intercept:", intercept)
[Out]: Coefficients: [0.2150,-0.2897] Intercept: 0.1336
```

在这段代码中，我们从拟合后的流水线模型（pipeline_model）中检索训练好的线性回归模型的系数和截距。由于线性回归模型是流水线的最后一个阶段，我们使用 `pipeline_model.stages[-1]` 来访问它。流水线模型的 `stages` 属性包含流水线中所有阶段的列表，而 `-1` 指向最后一个阶段。由于我们在线性回归模型中使用了两个特征（feature1 和 feature2），因此我们得到两个系数，其近似值分别为 0.22 和 -0.29。无论特征数量多少，都会产生截距（约 0.13）。这些结果与 Scikit-Learn 回归模型的结果不同，这表明两个平台之间存在实现上的差异。

**步骤 9：对测试数据进行预测**

```
[In]: predictions = pipeline_model.transform(testing_data)
```

在这一行代码中，我们使用训练好的流水线模型（pipeline_model）对测试数据（testing_data）进行预测。使用 pipeline_model 对测试数据应用 `transform` 方法。该方法按顺序将流水线的每个阶段应用于测试数据。它接收测试数据作为输入，并执行流水线阶段中定义的所有必要转换。

输出的 DataFrame predictions 包含测试数据中的原始列，以及 scaledFeatures 和 prediction 等附加列。prediction 列包含训练好的线性回归模型为测试数据中每一行生成的预测值。

**步骤 10：显示前五个实际值与预测值**

| label | prediction |
| --- | --- |
| 8.46 | -0.22 |
| -9.10 | -0.16 |
| -7.05 | -0.14 |
| 7.65 | -0.15 |
| 5.21 | -0.04 |

```
[In]: predictions.select("label", "prediction").show(5)
[Out]:
```

在这一步中，我们想查看 predictions DataFrame 中前五行的实际标签和对应的预测值。使用 `select` 方法选择 label 和 prediction 列。然后使用 `show()` 方法检索所选列的前五行。

如输出所示，两组值之间存在较大差异，表明模型存在预测误差。考虑到我们使用的是模拟数据集，这并不奇怪，因为我们的目的是说明流水线的概念。

**步骤 11：评估模型**

在最后一步中，我们通过计算测试数据上的均方根误差（RMSE）指标来评估模型的性能：

```
[In]: evaluator = RegressionEvaluator(labelCol="label",
predictionCol="prediction", metricName="rmse")
[In]: rmse = evaluator.evaluate(predictions)
[In]: print("Root Mean Squared Error (RMSE) on test data:", rmse)
[Out]: Root Mean Squared Error (RMSE) on test data: 5.88
```

在第一行代码中，我们创建了一个名为 evaluator 的 `RegressionEvaluator` 对象。我们分别使用 `labelCol` 和 `predictionCol` 参数指定标签列为 label，预测列为 prediction。我们还设置了 metricName 参数为 rmse，以表明我们要计算 RMSE。

在下一行中，我们使用 evaluator 对象的 `evaluate` 方法计算 RMSE。我们将包含实际标签和预测值的 predictions DataFrame 作为参数传递给 evaluate 方法。然后，RMSE 值被赋给变量 rmse。

最后，我们使用格式化字符串打印 RMSE 值（约 5.88），并附带一条描述性消息。输出显示了测试数据上的 RMSE，这指示了模型在实际值与预测值之间的平均差异方面的表现。较低的 RMSE 表示模型性能更好。

值得注意的是，PySpark 的 RMSE 值（5.88）与 Scikit-Learn 获得的值（5.55）不同。包括回归在内的机器学习算法在训练过程中涉及随机性，这可能导致不同运行之间产生略有差异的结果。此外，PySpark 和 Scikit-Learn 可能对其回归模型使用不同的优化技术，从而导致模型预测和 RMSE 值存在差异。

## 整合所有步骤

现在，让我们将前面步骤中的所有相关代码整合到一个代码块中。这将使读者能够在 Scikit-Learn 和 PySpark 中作为一个连贯的整体来执行代码。



### Scikit-Learn

步骤 1：导入必要的库

```
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.pipeline import Pipeline
[In]: from sklearn.preprocessing import StandardScaler
[In]: from sklearn.linear_model import LinearRegression
[In]: from sklearn.metrics import mean_squared_error
[In]: import numpy as np
[In]: import pandas as pd
```

步骤 2：生成模拟数据

```
[In]: np.random.seed(0)
[In]: num_samples = 1000
[In]: X = np.random.rand(num_samples, 2) * 10
[In]: y = np.random.rand(num_samples) * 20 - 10
```

步骤 3：将数据拆分为训练集和测试集

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y,
test_size=0.2, random_state=0)
```

步骤 4：定义流水线阶段

```
[In]: scaler = StandardScaler()
[In]: regression = LinearRegression()
```

步骤 5：创建流水线

```
[In]: pipeline = Pipeline(steps=[('scaler', scaler), ('regression', regression)])
```

步骤 6：将流水线拟合到训练数据

```
[In]: pipeline.fit(X_train, y_train)
```

步骤 7：打印系数

```
[In]: coefficients = pipeline.named_steps['regression'].coef_
[In]: intercept = pipeline.named_steps['regression'].intercept_
[In]: print("Coefficients:", coefficients)
[In]: print("Intercept:", intercept)
```

步骤 8：对测试数据进行预测

```
[In]: y_pred = pipeline.predict(X_test)
```

步骤 9：打印前五个实际值与预测值

```
[In]: data = {'Actual': y_test[:5], 'Predicted': y_pred[:5]}
[In]: df = pd.DataFrame(data)
[In]: print(df.head())
```

步骤 10：评估模型

```
[In]: rmse = np.sqrt(mean_squared_error(y_test, y_pred))
[In]: print("Root Mean Squared Error (RMSE) on test data:", rmse)
```

### PySpark

步骤 1：导入必要的库

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.feature import VectorAssembler, StandardScaler
[In]: from pyspark.ml.regression import LinearRegression
[In]: from pyspark.ml import Pipeline
[In]: from pyspark.ml.evaluation import RegressionEvaluator
```

步骤 2：创建 SparkSession

```
[In]: spark = SparkSession.builder.getOrCreate()
```

步骤 3：生成模拟数据

```
[In]: num_samples = 1000
[In]: simulated_data = spark.range(num_samples).selectExpr(
"id as id",
"(RAND() * 10) as feature1",
"(RAND() * 5) as feature2",
"(RAND() * 20 - 10) as label")
```

步骤 4：将数据拆分为训练集和测试集

```
[In]: train_ratio = 0.8
[In]: test_ratio = 1 - train_ratio
[In]: training_data, testing_data =
simulated_data.randomSplit([train_ratio, test_ratio], seed=42)
```

步骤 5：定义流水线阶段

```
[In]: assembler = VectorAssembler(
inputCols=["feature1", "feature2"], outputCol="features")
[In]: scaler = StandardScaler(
inputCol="features", outputCol="scaledFeatures")
[In]: regression = LinearRegression(
featuresCol="scaledFeatures", labelCol="label")
```

步骤 6：创建流水线

```
[In]: pipeline_stages = [assembler, scaler, regression]
[In]: pipeline = Pipeline(stages=pipeline_stages)
```

步骤 7：将流水线拟合到训练数据

```
[In]: pipeline_model = pipeline.fit(training_data)
```

步骤 8：打印系数

```
[In]: coefficients = pipeline_model.stages[-1].coefficients
[In]: intercept = pipeline_model.stages[-1].intercept
[In]: print("Coefficients:", coefficients)
[In]: print("Intercept:", intercept)
```

步骤 9：对测试数据进行预测

```
[In]: predictions = pipeline_model.transform(testing_data)
```

步骤 10：评估模型

```
[In]: evaluator = RegressionEvaluator(labelCol="label",
predictionCol="prediction", metricName="rmse")
[In]: rmse = evaluator.evaluate(predictions)
[In]: print("Root Mean Squared Error (RMSE) on test data:", rmse)
```

步骤 11：显示前五个实际值与预测值

```
[In]: predictions.select("label", "prediction").show(5)
```

## 总结

在本章中，我们探讨了 Scikit-Learn 和 PySpark 中的流水线技术。通过利用流水线的强大功能，数据科学家可以自动化和标准化建模工作流程中的各个步骤。这有助于创建稳健且可扩展的模型，增强模型的可解释性，并促进额外预处理步骤和特征工程技术的集成。

我们演示了如何构建一个流水线，该流水线包含使用 `StandardScaler` 进行特征缩放的步骤，以及使用 `LinearRegression` 类进行回归的步骤。我们逐步讲解了将流水线拟合到训练数据、对测试数据进行预测以及评估模型性能的完整过程。

在下一章（也是本书的最后一章）中，我们将深入探讨使用 Scikit-Learn 和 PySpark 在生产环境中部署机器学习模型的实际方面，以此作为本书的总结。



# 18. 使用 Scikit-Learn 和 PySpark 在生产环境中部署模型

在本书的最后一章中，我们将探讨使用 Scikit-Learn 和 PySpark 部署机器学习模型的实际操作。模型部署是指让机器学习模型能够在生产环境中使用，从而基于真实世界的数据进行预测或执行任务的过程。它涉及将训练好的机器学习模型集成到系统或应用程序中，以便向最终用户或其他系统提供预测结果。

模型部署是机器学习生命周期中的关键步骤，其重要性体现在多个方面。首先，机器学习模型只有在真实环境中用于预测或自动化任务时才能体现其价值。部署确保模型得到实际应用，从而可以节省成本并提高效率。此外，部署使组织能够扩展其机器学习模型的使用范围，以服务大量用户或处理海量数据。在生产环境中，模型可以实时或批量处理数据，使其适用于各种应用场景。

随后，已部署的模型可以自动化原本需要人工干预或专业知识的决策过程，从而实现更快、更一致的决策。更重要的是，模型可以集成到现有工作流程中，成为现有流程的无缝组成部分。这种集成对于在特定业务环境中利用模型的能力至关重要，例如自动化评估贷款或信用卡申请，以根据用户的信用度和财务历史判断其是否符合资格。

此外，部署允许在生产环境中监控模型的性能，包括跟踪指标、检测模型漂移等问题，并根据需要更新模型以确保其持续有效性。成功部署和利用机器学习模型的组织可以通过做出数据驱动的决策、改善客户体验并在行业中保持领先地位来获得竞争优势。

模型部署之所以重要的另一个关键原因是，已部署的模型可以被包括数据科学家、分析师、开发人员和业务用户在内的各种利益相关者访问，从而促进跨职能协作和知识共享。

本章将演示如何使用 Scikit-Learn 和 PySpark 进行模型部署。但读者应该知道，还有其他在生产环境中部署模型的方法，尽管这些方法不在本章的讨论范围内。一种常见的方法是将算法部署为 API 服务，允许其他应用程序和系统向模型发送 HTTP 请求以获取预测结果。像 Flask、Django 和 FastAPI 这样的框架通常用于构建 API 端点。另一种选择是容器化。Docker 容器用于将机器学习模型及其依赖项打包成可移植的单元。Kubernetes 和其他容器编排工具支持对容器化模型进行扩展和管理。

在复杂的工作流程中，组织使用 Apache Airflow 等工具以协调的方式编排多个模型和数据管道的部署。同样，在面向客户的应用程序中，组织将机器学习模型集成到用户界面中，使最终用户能够与模型的预测或建议进行交互并从中受益。对于离线或批处理，组织将模型部署在 Apache Hadoop 或 Apache Spark 等分布式计算平台上，这些平台可以处理大规模数据处理和模型执行。此外，像 TensorFlow Serving 和 MLflow 这样的专用模型服务框架专为在生产环境中部署和管理机器学习模型而设计，通常包含版本控制、监控和扩展功能。

最后，还有基于云的部署选项，云提供商提供托管的机器学习服务（例如 AWS SageMaker、Azure Machine Learning、Google AI Platform），这些服务简化了在其基础设施上部署模型的过程，通常包括自动扩展和监控功能。

为了演示使用 Scikit-Learn 和 PySpark 进行模型部署，我们将利用一个多层感知器（MLP）并使用鸢尾花数据集。在下一节中，我们将概述模型部署所涉及的关键步骤。随后，我们将深入探讨在 Scikit-Learn 中部署模型的过程。在本章的最后一节，我们将把重点转向 PySpark，使正在考虑从 Scikit-Learn 过渡的数据科学家能够利用这个强大的分布式数据处理框架所提供的优势。

## 模型部署的步骤

在 Scikit-Learn 和 PySpark 中，将模型部署到生产环境通常涉及三个主要步骤：模型训练与评估、模型持久化以及部署。

步骤 1：模型训练与评估

首先，您需要使用提供的算法（例如 Scikit-Learn 中的 `MLPClassifier` 和 PySpark 中用于神经网络的 `MultilayerPerceptronClassifier`）来训练和评估您的模型。这包括使用可用的模块预处理数据，将其拆分为训练集和测试集，并使用准确率等评估指标来评估模型性能。

步骤 2：模型持久化

训练完成后，将训练好的模型保存到磁盘以供将来部署至关重要。在 Scikit-Learn 中，您可以使用 `joblib` 库（或 pickle）来高效地序列化 Python 对象，包括训练好的模型。通过使用 `joblib.dump()` 函数，您可以将模型保存为文件，以便稍后加载用于部署。同样，PySpark 提供了 ML 持久化 API，允许您使用 `save()` 方法保存模型配置和训练好的权重。

步骤 3：模型部署

最后一步涉及使用已保存的模型。在 Scikit-Learn 中，您可以使用 `joblib` 库和 `joblib.load()` 函数将保存的模型文件加载到内存中。这会加载模型，使其准备好对新数据进行预测。同样，在 PySpark 中，可以通过使用 ML 持久化 API 加载已保存的模型来进行部署。然后，您可以通过调用 `transform()` 方法使用加载的模型来转换新数据。

通过遵循这些步骤和技术，您可以有效地使用 Scikit-Learn 和 PySpark 部署您的机器学习模型，确保它们为生产环境做好准备。

## 部署多层感知器（MLP）

在以下各节中，我们将提供使用 Scikit-Learn 和 PySpark 在生产环境中部署模型的实际示例。为了演示，我们将使用鸢尾花数据集训练、评估和部署一个多层感知器（MLP）。我们已经熟悉这个数据集，因为在第 15 章中我们曾用它来训练和评估 k-means 聚类算法。需要注意的是，这里讨论的关于 MLP 分类的原理也适用于其他算法，无论它们是用于回归任务还是分类任务。



### 使用 Scikit-Learn 进行部署

在本小节中，我们将使用 Scikit-Learn 在鸢尾花数据集上构建、训练、评估、持久化并部署一个 MLP 分类器。第一步是导入所需的库。

**步骤 1：导入必要的库**

以下函数和类使我们能够访问并利用 Scikit-Learn 库中的必要功能，以训练、评估、保存和部署 MLP 分类器：

```
[In]: import numpy as np
[In]: from sklearn.datasets import load_iris
[In]: from sklearn.neural_network import MLPClassifier
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.metrics import accuracy_score
[In]: import joblib
```

以下是每个库的功能说明：

*   `numpy:` 该库将用于生成一些新预测所需的数据。

*   `load_iris`: 此函数用于加载鸢尾花数据集。

*   `MLPClassifier:` 这是一个用于实现多层感知机（MLP）的类——MLP 是一种用于分类任务的人工神经网络。

*   `train_test_split:` 这是一个实用函数，用于将数据集拆分为训练子集和测试子集，以便评估机器学习模型的性能。

*   `accuracy_score:` 这是一个用于计算准确率指标的函数，通过将分类模型预测结果与测试数据的真实标签进行比较，来评估模型预测的准确性。

*   `joblib:` 该模块提供了保存和加载 Python 对象（包括训练好的机器学习模型）的工具。它常用于模型持久化，使我们能够将训练好的模型保存到磁盘，并在之后加载以进行部署或进一步使用。

**步骤 2：加载鸢尾花数据**

```
[In]: iris = load_iris()
[In]: X = iris.data
[In]: y = iris.target
```

在此步骤中，我们使用 Scikit-Learn 内置的 `load_iris()` 函数加载鸢尾花数据集。特征被赋值给变量 X，而对应的目标值被赋值给变量 y。

**步骤 3：将数据拆分为训练集和测试集**

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y,
test_size=0.2, random_state=42)
```

这行代码使用 `train_test_split` 函数将数据拆分为训练集和测试集。X 和 y 分别是我们要拆分的特征集和标签集。`test_size=0.2` 参数指定 20% 的数据将用于测试集，而 80% 将用于训练集。`random_state=42` 参数确保了结果的可复现性。通过使用相同的种子值，每次运行代码时都会得到相同的数据拆分结果。训练集和测试集分别被赋值给变量 X_train、X_test、y_train 和 y_test。

**步骤 4：创建 MLP 分类器**

```
[In]: mlp = MLPClassifier(hidden_layer_sizes=(100, 50), max_iter=1000, random_state=42)
```

这行代码创建了一个 `MLPClassifier` 类的实例。`hidden_layer_sizes` 参数通过定义每个隐藏层中的神经元数量来指定神经网络的架构。在本例中，神经网络有两个隐藏层，第一个隐藏层有 100 个神经元，第二个隐藏层有 50 个神经元。

`max_iter` 参数决定了神经网络训练的最大迭代次数或轮数。在训练过程中，模型会根据训练数据迭代地更新权重以提高性能。在本例中，`max_iter` 参数将最大迭代次数设置为 1000。

最后，`random_state` 参数允许结果的可复现性。通过将 `random_state` 设置为 42，每次运行代码时都会生成相同的随机数序列。这确保了神经网络权重的初始化以及算法中的其他随机过程在不同运行中保持一致。

**步骤 5：训练分类器**

```
[In]: mlp.fit(X_train, y_train)
```

这行代码用于在训练数据上训练 MLP 分类器。`fit()` 方法使用提供的输入特征（X_train）和相应的目标标签（y_train）来训练模型。

在训练过程中，MLP 分类器学习输入特征与目标标签之间的关系。它根据训练数据迭代地调整神经网络的权重和偏置，旨在最小化误差或损失函数。迭代次数由我们在创建 `MLPClassifier` 对象时指定的 `max_iter` 参数决定。执行 `fit()` 方法后，MLP 分类器即训练完成，并准备好对新的、未见过的数据进行预测。

**步骤 6：对测试数据进行预测**

```
[In]: y_pred = mlp.predict(X_test)
```

这行代码用于使用训练好的 MLP 分类器模型对测试数据进行预测。`predict()` 方法将训练好的模型应用于输入数据，并返回预测的类别标签。对于测试数据中的每个样本，它根据从训练数据中学到的模式和关联来预测相应的目标标签。得到的预测标签存储在 y_pred 变量中。

**步骤 7：评估准确率**

```
[In]: accuracy = accuracy_score(y_test, y_pred)
[In]: print("Accuracy:", accuracy)
[Out]: Accuracy: 1.0
```

这段代码用于评估 MLP 分类器在测试数据上预测的准确率。`accuracy_score()` 是一个实用函数，通过将预测标签（y_pred）与真实标签（y_test）进行比较，来计算分类模型预测的准确率。通过调用 `accuracy_score(y_test, y_pred)`，该函数将每个预测标签与其对应的真实标签进行比较，从而计算出预测的准确率。它返回一个介于 0 和 1 之间的值，表示正确预测标签的比例。

输出结果显示 MLP 分类器的准确率得分为 1.0，这意味着所有预测中 100% 是正确的。这表明模型已准确地对鸢尾花的每个品种进行了分类。

**步骤 8：保存训练好的模型以供部署**

在训练并评估了模型性能之后，我们现在应该能够进入持久化步骤：

```
[In]: joblib.dump(mlp, 'mlp_model.pkl')
```

这行代码使用 `joblib` 库，通过 `dump()` 函数将机器学习模型保存到名为 `mlp_model.pkl` 的文件中。`mlp` 参数代表机器学习模型对象。`mlp_model.pkl` 参数是序列化模型将要保存到的文件名。`.pkl` 扩展名通常用于存储使用 pickle 协议序列化的 Python 对象的文件。

**步骤 9：加载保存的模型**

此步骤涉及使用保存的模型。我们可以使用 `joblib` 库和 `joblib.load()` 函数将保存的模型文件加载到内存中。这将加载模型，使其准备好对新数据进行预测：

```
[In]: mlp = joblib.load('mlp_model.pkl')
```

这行代码使用 `joblib` 库中的 `load()` 函数从 `mlp_model.pkl` 文件中加载序列化的 MLP 模型，并将其赋值给变量 mlp。执行这行代码后，mlp 将包含反序列化后的模型对象，该对象可用于预测。

**步骤 10：生成新数据（模拟示例）**

```
[In]: np.random.seed(42)
[In]: X_new = np.random.rand(10, 4)
```

这段代码使用 NumPy 的随机模块生成新数据。其目的是使用这些新数据来演示如何使用加载的模型。第一行代码为随机数生成器设置了随机种子。将种子设置为 42 可确保生成的随机数具有可复现性。



第二行创建了一个包含随机数的 NumPy 数组（X_new）。`rand()` 函数从 0 到 1 之间的均匀分布中生成随机数。参数 (10, 4) 指定了要创建的数组形状，其中 10 代表行数，4 代表列数。

步骤 11：对新数据进行预测

```
[In]: y_new_pred = mlp.predict(X_new)
[In]: print("Predictions:", y_new_pred)
[Ou]: Predictions: [1 1 1 0 1 1 0 1 1 1]
```

在最后一步中，我们首先使用 mlp 模型的 `predict()` 方法对新数据集 X_new 进行预测。`predict()` 方法接收输入数据，并基于训练好的模型返回预测值。预测结果存储在变量 y_new_pred 中。然后，我们使用 `print()` 函数打印模型生成的预测结果。

### PySpark

我们现在将焦点转向 PySpark，以展示如何使用 Spark ML 训练和评估一个 MLP 分类器，持久化模型，然后加载模型对新数据进行预测。

第一步是导入必要的库：

步骤 1：导入必要的库

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.classification import
MultilayerPerceptronClassificationModel,
MultilayerPerceptronClassifier
[In]: from pyspark.ml.feature import VectorAssembler
[In]: from pyspark.ml.evaluation import
MulticlassClassificationEvaluator
[In]: import numpy as np
```

这段代码导入了在 PySpark 中构建、训练、评估和部署 MLP 分类模型所需的类。以下是每个导入类的描述：

*   `SparkSession:` 这是 Spark 功能的入口点；它支持与 Spark API 的交互。

*   `MultilayerPerceptronClassifier:` 该类提供了 Spark 中 MLP 分类器的实现。

*   `MultilayerPerceptronClassificationModel:` 该类代表一个训练好的 MLP 分类模型。它包含神经网络学习到的权重和偏置，以及其他与模型相关的信息。

*   `VectorAssembler:` 该类用于将多个特征列合并成一个单一的向量列。

*   `MulticlassClassificationEvaluator:` 该类为多类分类模型提供评估指标，由于鸢尾花数据集包含三个品种（setosa、versicolor 和 virginica），使其成为一个多类数据集，因此该评估器在此场景下特别相关。

*   `numpy as np`: 该库将用于生成一些用于新预测的随机输入数据。

步骤 2：创建一个 SparkSession

```
[In]: spark = SparkSession.builder.getOrCreate()
```

这段代码创建了一个 Spark Session，以便与 Spark 进行交互。

步骤 3：创建 PySpark DataFrame

```
[In]: spark = SparkSession.builder.getOrCreate()
[In]: data = [(float(X[i, 0]), float(X[i, 1]), float(X[i, 2]), float(X[i, 3]), float(y[i])) for i in range(len(X))]
[In]: spark_df = spark.createDataFrame(data, ["sepal_length", "sepal_width", "petal_length", "petal_width", "species"])
```

在这一步中，我们首先初始化一个 SparkSession，它是利用 Spark 功能的入口点。然后，我们通过创建一个列表来准备数据，列表中的每个元素对应一朵鸢尾花的特征，包括花萼长度、花萼宽度、花瓣长度、花瓣宽度及其品种标签。这些数据被转换成适合 PySpark 的格式。最后，使用 `createDataFrame()` 方法，我们创建了一个名为 spark_df 的 PySpark DataFrame。

步骤 4：将数据拆分为训练集和测试集

```
[In]: train_data, test_data = spark_df.randomSplit([0.8, 0.2],
seed=42)
```

这行代码根据指定的拆分比例，将 Spark DataFrame (spark_df) 拆分为两个独立的 DataFrame，即 train_data 和 test_data。Spark DataFrame 提供的 `randomSplit()` 方法随机执行此拆分。它接受两个参数：

1.  [0.8, 0.2] 参数是一个列表，分别指定训练数据和测试数据的拆分比例。在这种情况下，80% 的数据将被分配给 train_data，20% 的数据将被分配给 test_data。

2.  seed=42 参数设置了随机种子。它确保了随机拆分过程的可重复性。当使用相同的种子时，如果多次应用随机拆分，将产生相同的训练-测试集划分。

步骤 5：将特征组装成一个单一的向量列

```
[In]: feature_cols = ["sepal_length", "sepal_width", "petal_length", "petal_width"]
[In]: assembler = VectorAssembler(inputCols=feature_cols, outputCol="features")
[In]: train_data = assembler.transform(train_data)
[In]: test_data = assembler.transform(test_data)
```



在此步骤中，使用 `VectorAssembler` 将特征组合成一个向量。第一行中，`feature_cols` 被定义为一个列表，包含我们想要纳入机器学习模型的特征列名称。这些列代表了鸢尾花的特征：花萼长度、花萼宽度、花瓣长度和花瓣宽度。

第二行创建了一个名为 `assembler` 的 `VectorAssembler` 对象。`VectorAssembler` 是一个 PySpark 特征转换器，用于将来自多个列的特征值向量组装成一个单一的向量列。在此例中，它被配置为将 `feature_cols` 中指定的列作为输入列，并生成一个名为 `features` 的新列，该列包含所选特征的向量表示。

最后两个命令使用 `VectorAssembler` 的 `transform` 方法将转换应用于训练数据和测试数据。此转换从 DataFrame（`train_data` 和 `test_data`）的每一行中提取指定的特征列，并将它们组合成一个名为 `features` 的单一向量列。此转换对于 PySpark 中的机器学习算法至关重要，因为它们期望输入特征采用向量格式。

步骤 6：创建 MLP 分类器

```
[In]: mlp = MultilayerPerceptronClassifier(labelCol="species", featuresCol="features", layers=[4, 100, 50, 3], seed=42, maxIter=500)
```

在这行代码中，使用 `MultilayerPerceptronClassifier` 类创建了多层感知机（MLP）分类模型。这个神经网络由多个相互连接的节点（神经元）层组成。

`labelCol="species"` 参数指定了 DataFrame 中包含分类标签或目标变量的列名。在此例中，列名为 `species`。`featuresCol="features"` 参数指定了 DataFrame 中包含分类器输入特征的列名。在此例中，列名为 `features`。

`layers=[4, 100, 50, 3]` 参数定义了多层感知机网络的架构。该列表指定了从输入层到输出层每一层的节点数。在此示例中，MLP 有 1 个包含 4 个节点（代表特征）的输入层，2 个分别包含 100 和 50 个节点的隐藏层，以及 1 个包含 3 个节点（代表鸢尾花种类）的输出层。

`seed=42` 参数为 MLP 分类器设置了随机种子。它通过将随机数生成器初始化为特定状态来确保结果的可复现性。

`maxIter=500` 参数决定了 MLP 分类器的最大迭代次数或训练轮数。它控制算法遍历训练数据以更新神经网络权重和偏置的次数。

步骤 7：训练分类器

```
[In]: mlp_model = mlp.fit(train_data)
```

在这单行代码中，MLP 分类器（`mlp`）正在训练数据（`train_data`）上进行训练。调用 `fit` 方法来启动训练过程并创建一个训练好的模型。`mlp` 是之前定义的多层感知机分类器实例。它封装了 MLP 网络的架构和设置。`fit(train_data)` 是启动训练过程的方法调用。它接收 `train_data` DataFrame 作为输入，其中包含带有相应特征和标签的带标签训练样本。`fit` 方法执行一个迭代优化过程，在此过程中，它使用训练样本更新 MLP 网络的权重和偏置。迭代次数由创建 MLP 分类器时指定的 `maxIter` 参数决定。

训练过程完成后，`fit` 方法返回一个训练好的 MLP 模型（`mlp_model`）。该模型代表了在训练数据中学到的模式和关系，可用于对新的、未见过的数据进行预测。

步骤 8：对测试数据进行预测

```
[In]: predictions = mlp_model.transform(test_data)
```

在这单行代码中，训练好的多层感知机模型（`mlp_model`）被用于对测试数据（`test_data`）进行预测。在 `mlp_model` 上调用 `transform` 方法，将训练好的模型应用于测试数据并生成预测。

`predictions` DataFrame 现在包含了多层感知机模型对测试数据做出的预测。我们可以使用这个 DataFrame 来评估模型的性能。

步骤 9：评估准确率

```
[In]: evaluator = MulticlassClassificationEvaluator(labelCol="species", predictionCol="prediction", metricName="accuracy")
[In]: accuracy = evaluator.evaluate(predictions)
[In]: print("Accuracy:", accuracy)
[Out]: Accuracy: 1.0
```

在这段代码中，正在评估多层感知机模型对测试数据所做预测的准确率。使用 `MulticlassClassificationEvaluator` 来计算准确率指标，并打印结果。`evaluator` 是 `MulticlassClassificationEvaluator` 类的一个实例。它通过以下三个参数创建：

1.  `labelCol="species"` 参数指定了 DataFrame 中包含测试数据实际标签的列名。
2.  `predictionCol="prediction"` 参数指定了 DataFrame 中包含模型生成的预测标签的列名。
3.  `metricName="accuracy"` 参数指定了要计算的指标，在此例中为准确率。

在第二行代码中，`accuracy` 是一个变量，用于保存对预测结果评估准确率指标的结果。在 `evaluator` 实例上调用 `evaluate` 方法，并传入 `predictions` DataFrame。`evaluate` 方法将 `predictions` DataFrame 的 `prediction` 列中的预测标签与 `species` 列中的实际标签进行比较。它计算准确率，即测试数据中正确预测的标签数量占总实例数的比例。

最后，使用 `print` 语句打印准确率值。该值为 1.0 或 100%，表明模型对每种鸢尾花都做出了准确的预测。这意味着模型的预测与真实标签完全匹配，分类准确率达到 100%，这有力地表明了模型在测试数据上的性能。

步骤 10：保存训练好的模型以供部署

```
[In]: model_dir = "/dbfs/mlp_model"
[In]: mlp_model.write().overwrite().save(model_dir)
```

在这段代码中，训练好的多层感知机模型（`mlp_model`）被保存以供部署。在 `mlp_model` 上调用 `write()` 方法，随后调用 `overwrite()` 方法和 `save()` 方法。

第一行定义了训练好的模型将被保存的目录路径（`model_dir`）。在此示例中，使用 Databricks 将目录路径设置为 “/dbfs/mlp_model”。

在第二行中，在训练好的多层感知机模型（`mlp_model`）上调用 `write()` 方法来启动保存过程。`overwrite()` 方法确保如果指定目录路径中已存在模型，它将被正在保存的新模型覆盖。此步骤保证保存的模型是最新版本。

`save()` 方法将训练好的模型保存到指定的目录路径（`model_dir`）。训练好的模型被序列化并存储在指定位置以供将来使用。

步骤 11：加载保存的模型

```
[In]: loaded_model = MultilayerPerceptronClassificationModel.load(model_dir)
```

在这段代码中，加载一个保存好的多层感知机模型以供进一步使用。在 `MultilayerPerceptronClassificationModel` 类上调用 `load()` 方法，并将加载的模型赋值给变量 `loaded_model`。



该类表示 Apache Spark 的 MLlib 中训练好的多层感知器模型。通过调用该类的 `load()` 方法，可从指定目录路径（model_dir）加载模型。该方法会读取并反序列化已保存的模型文件，并构造一个新的 `MultilayerPerceptronClassificationModel` 类实例。

变量 model_dir 包含之前保存训练模型时的目录路径。变量 loaded_model 被赋值为加载后的多层感知器模型，现在可用于对新数据进行预测。

**步骤 12：生成新的（模拟）数据**

```
[In]: np.random.seed(42)
[In]: X_new = np.random.rand(10, 4)
```

要使用加载的多层感知器模型对新数据进行预测，我们首先需要生成这些数据。这通过前面展示的代码片段实现，该代码演示了如何使用 Python 的 NumPy 库生成随机数并创建一个包含随机特征的新数组。

代码的第一行设置了 NumPy 随机数生成器的随机种子。设置种子可以确保每次运行代码时生成的随机数相同。第二行生成一个名为 X_new 的新 NumPy 数组，其维度为 10`×`4。`rand` 函数用于生成 0 到 1 之间的随机数。数组中的每个元素代表一个随机特征值。第一个参数 10 表示数组中的行数或样本数。第二个参数 4 表示数组中的列数或特征数。因此，X_new 是一个包含随机特征值的 10`×`4 数组。

**步骤 13：将 NumPy 数组转换为 Spark DataFrame**

```
[In]: new_data = spark.createDataFrame(X_new.tolist(), ["sepal_length", "sepal_width", "petal_length", "petal_width"])
```

这一行代码演示了如何使用 Spark 的 `createDataFrame` 方法从 NumPy 数组创建 Spark DataFrame。更准确地说，该代码从 NumPy 数组 X_new 创建了一个名为 new_data 的新 Spark DataFrame。`createDataFrame` 方法在 spark 对象上调用，该对象是使用 Spark 功能的入口点。

`tolist()` 方法将 NumPy 数组 X_new 转换为嵌套的 Python 列表。这种转换是必要的，因为 `createDataFrame` 期望输入与 Python 数据结构兼容。`["sepal_length", "sepal_width", "petal_length", "petal_width"]` 提供了 DataFrame 的列名。它指定了 new_data DataFrame 中的列名，对应于 X_new 数组中的四个特征。生成的 DataFrame new_data 将包含四列：sepal_length、sepal_width、petal_length 和 petal_width，其值来自 NumPy 数组中对应的元素。变量 new_data 保存了新创建的 Spark DataFrame。

**步骤 14：为新数据将特征组装成单个向量列**

```
[In]: new_data = assembler.transform(new_data)
```

在这段代码中，在 assembler 对象上调用了 `transform()` 方法，以对 new_data DataFrame 应用特征转换。该方法将 new_data 作为输入，并对其列应用指定的转换操作。生成的 DataFrame new_data 将增加一个名为 features 的列，其中包含组装好的特征向量。

**步骤 15：对新数据进行预测**

```
[In]: new_predictions = loaded_model.transform(new_data)
[In]: new_predictions.select("prediction").show()
[Out]:
+----------+
|prediction|
+----------+
|       1.0|
|       1.0|
|       1.0|
|       0.0|
|       0.0|
|       1.0|
|       0.0|
|       1.0|
|       1.0|
|       1.0|
+----------+
```

在步骤 15 中，使用加载的多层感知器模型（loaded_model）对新数据集进行预测。然后显示预测结果。更多细节如下文所述。

第一行代码将训练好的多层感知器模型（loaded_model）应用于 new_data_scaled DataFrame。在 loaded_model 对象上调用了 `transform()` 方法，并将 new_data 作为参数传入。该方法将模型应用于输入的 DataFrame，根据学习到的模型权重和架构进行预测。生成的 DataFrame new_predictions 将包含 new_data 中的原始列，以及一个名为 prediction 的新列，该列保存预测的标签或值。

第二行从 new_predictions DataFrame 中选择 prediction 列并显示其内容。在 new_predictions 上调用了 `select()` 方法，并将列名 prediction 作为参数。然后调用 `show()` 方法来显示选定的列。

## 整合所有步骤

在本节中，我们将前面步骤中的所有相关代码合并到一个代码块中。这将使读者能够在 Scikit-Learn 和 PySpark 中作为一个连贯的整体来执行代码。

### Scikit-Learn

```
