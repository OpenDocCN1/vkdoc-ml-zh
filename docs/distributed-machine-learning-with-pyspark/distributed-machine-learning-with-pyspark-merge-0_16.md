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

