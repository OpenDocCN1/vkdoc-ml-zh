# 6. 使用 Pandas、Scikit-Learn 和 PySpark 进行梯度提升树回归

在本章中，我们继续探讨监督学习和基于树的回归。具体来说，我们将使用前几章中用于决策树和随机森林回归的相同房价数据集，开发一个梯度提升树（GBT）回归模型。这样，通过比较它们的性能指标，我们可以更清楚地了解哪种树类型表现更好。

随机森林和 GBT 回归模型之间存在相似之处。最明显的相似之处在于它们都是集成技术，结合了多棵树的预测结果——区别在于随机森林结合的是强树的预测，而 GBT 结合的是弱树的预测。这一特性赋予了 GBT 强大的预测能力，因为它们利用了多个预测模型的优势。这能够有效捕捉复杂的数据关系，从而减轻过拟合。与随机森林类似，梯度提升可以适应数据中的非线性关系，使其能够适应各种数据结构。

此外，与其他基于树的模型类似，GBT 通过根据特征对模型性能的贡献为其分配权重，从而提供特征重要性信息。这一属性对于识别预测背后的驱动因素非常宝贵。该算法对异常值的鲁棒性是另一个显著优势，因为集成中的决策树可以隔离异常值，从而减少其影响。

另一方面，尽管 GBT 和随机森林都是基于树的集成算法，但它们表现出不同的训练过程。更具体地说，GBT 通过一次训练一棵树来推进，而随机森林则能够同时训练多棵树。因此，与随机森林相比，GBT 可能需要更多训练时间。实际应用中，通常建议对 GBT 使用更小（更浅）的树，而不是像随机森林那样，因为训练较小的树耗时更少。

此外，值得注意的是，虽然随机森林和 GBT 相比决策树都能减轻过拟合，但随机森林往往比 GBT 更不易过拟合。这意味着增加随机森林中的树数量会降低过拟合风险，而增加 GBT 中的树数量则会增加过拟合的可能性。

最后，随机森林提供了更直接的调优过程，因为其性能会随着树数量的增加而持续提升。相比之下，如果树的数量变得过大，GBT 的性能可能会开始下降。

在本章中，我们将展示 Scikit-Learn 和 PySpark 在建模步骤上的相似性，即使语法有所不同。我们将利用这两个库对相同的房价数据集进行预测。此外，我们还将通过评估性能指标，比较迄今为止使用的所有基于树的模型（决策树、随机森林和梯度提升树）的性能。此外，我们将重点介绍 Pandas 和 PySpark 在数据加载和探索方面采用的并行方法。



## 数据集

在本章的项目中，我们将使用与前两章决策树和随机森林建模时相同的房价数据集。这样做的理由是，使用相同的数据集能让读者更容易跟上进度，并比较最后三章的结果。

由于我们在前几章已经非常详细地探索过这个数据集，因此本章对数据的分析将较为简略。主要目的只是为了提醒自己数据集的主要属性，以及如何互换使用 Pandas 和 PySpark 进行探索性数据分析，而 PySpark 因其分布式计算特性，在处理更大数据量方面具有优势。

与前几章类似，我们可以定义一个 Python 函数来加载和读取房价数据集。然后创建一个 Pandas DataFrame，并将其转换为 PySpark DataFrame。以下代码实现了这些目标：

```
[In]: import pandas as pd
[In]: def load_housing_data():
url = ('https://raw.githubusercontent.com/abdelaziztestas/’
'spark_book/main/housing.csv')
return pd.read_csv(url)
[In]: pandas_df = load_housing_data()
[In]: spark_df = spark.createDataFrame(pandas_df)
```

回顾前几章，该数据集有 545 行和 13 列（12 个特征和 1 个目标变量）。我们可以使用 Pandas 的 `shape` 属性来确认这一点：

```
[In]: print(pandas_df.shape)
[Out]: (545, 13)
```

在 PySpark 中，我们结合使用 `count()` 和 `len(columns)` 方法：

```
[In]: print((spark_df.count(), len(spark_df.columns)))
[Out]: (545, 13)
```

我们可以使用 Pandas 和 PySpark 的 `columns` 属性来列出列名或标签：

Pandas：

```
[In]: print(pandas_df.columns)
[Out]: Index(['price', 'area', 'bedrooms', 'bathrooms', 'stories', 'mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'parking', 'prefarea', 'furnishingstatus'],      dtype='object')
```

PySpark：

```
[In]: print(spark_df.columns)
[Out]: ['price', 'area', 'bedrooms', 'bathrooms', 'stories', 'mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'parking', 'prefarea', 'furnishingstatus']
```

正如我们从前几章回忆的那样，该数据集是数值变量和分类变量的混合。要检查哪些列是数值型，哪些是分类型，我们可以在 Pandas 和 PySpark 中使用 `dtypes` 属性：

Pandas：

```
[In]: print(pandas_df.dtypes)
[Out]:
price                int64
area                 int64
bedrooms             int64
bathrooms            int64
stories              int64
mainroad            object
guestroom           object
basement            object
hotwaterheating     object
airconditioning     object
parking              int64
prefarea            object
furnishingstatus    object
dtype: object
```

PySpark：

```
[In]: for col_name, col_type in spark_df.dtypes:
print(col_name, col_type)
[Out]:
price bigint
area bigint
bedrooms bigint
bathrooms bigint
stories bigint
mainroad string
guestroom string
basement string
hotwaterheating string
airconditioning string
parking bigint
prefarea string
furnishingstatus string
```

数值变量在 Pandas 中具有 *int64* 数据类型，在 PySpark 中具有 *bigint* 数据类型，而分类变量分别具有 *object* 和 *string* 数据类型。因此，有七个分类变量需要进行独热编码（mainroad、guestroom、basement、hotwaterheating、airconditioning、prefarea、furnishingstatus）。

其中六个分类变量各有两个类别（是或否，反之亦然），而 furnishingstatus 列有三个类别：furnished、semi-furnished 和 unfurnished。我们可以通过以下代码确认这一点：

Pandas：

```
[In]: for col in ['mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'prefarea', 'furnishingstatus']:
unique_values = pandas_df[col].unique()
print(f"Unique values in {col}: {unique_values}")
[Out]:
Unique values in mainroad: ['yes' 'no']
Unique values in guestroom: ['no' 'yes']
Unique values in basement: ['no' 'yes']
Unique values in hotwaterheating: ['no' 'yes']
Unique values in airconditioning: ['yes' 'no']
Unique values in prefarea: ['yes' 'no']
Unique values in furnishingstatus: ['furnished' 'semi-furnished' 'unfurnished']
```

PySpark：

```
[In]: from pyspark.sql.functions import col
[In]: for col_name in ['mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'prefarea', 'furnishingstatus']: unique_values=spark_df.select(col(col_name)).distinct().rdd.flatMap(lambda x: x).collect()
print(f"Unique values in {col_name}: {unique_values}")
[Out]:
Unique values in mainroad: ['yes', 'no']
Unique values in guestroom: ['no', 'yes']
Unique values in basement: ['no', 'yes']
Unique values in hotwaterheating: ['no', 'yes']
Unique values in airconditioning: ['no', 'yes']
Unique values in prefarea: ['no', 'yes']
Unique values in furnishingstatus: ['semi-furnished', 'unfurnished', 'furnished']
```

现在我们已经回顾了房价数据集的主要属性，可以继续构建梯度提升树（GBT）回归模型了。



## 梯度提升树（GBT）回归

正如本章引言所述，梯度提升是一种集成学习技术，它结合多个弱学习模型（决策树）来创建一个强大的单一学习模型。梯度提升的核心思想是迭代地向模型添加新树，每棵树都用于纠正前一棵树的错误。在每次迭代中，模型都会拟合一棵新树来预测上一次迭代的残差。最终的预测结果是模型中所有树预测结果的加权和。分配给每棵树的权重取决于其在训练集上的表现以及模型中的树的数量。GBT 中的“梯度”一词指的是该算法使用梯度下降优化来最小化模型的损失函数，对于回归问题，该损失函数通常是均方误差。

在本节中，我们将使用 Scikit-Learn 和 PySpark 库来构建、训练和评估一个 GBT 回归模型，并用它来预测房价。我们将使用上一节中已经了解的 13 个特征。

与前面章节中的决策树和随机森林类似，GBT 并不严格要求进行标准缩放，因为基于树的算法不受输入特征尺度的影响。这是因为梯度提升不使用基于距离的方法来寻找决策树中的最优分割点。因此，输入特征的尺度不会影响特征的相对重要性或算法的性能。

**Scikit-Learn / PySpark 相似之处**

Scikit-Learn 和 PySpark 在执行梯度提升树回归时遵循相似的建模步骤。我们将会发现，它们在构建、训练和评估梯度提升树回归器时遵循着可比的过程：

1.  数据准备：

    Scikit-Learn：在 Scikit-Learn 中，使用 `train_test_split` 等函数将数据划分为训练集和测试集。其他函数包括用于特征缩放的 `StandardScaler`、用于分类变量编码的 `OneHotEncoder` 以及用于处理缺失数据的 `SimpleImputer`。

    PySpark：在 PySpark 中，类似的任务通过 DataFrame 完成。使用 `randomSplit` 函数进行数据分割，`StandardScaler` 处理特征缩放，`OneHotEncoder` 管理分类编码，`Imputer` 处理缺失数据。此外，还使用 `VectorAssembler` 将特征列组合成一个向量列，这一步在 Scikit-Learn 的方法中是不需要的。

2.  模型训练：

    Scikit-Learn：要在 Scikit-Learn 中进行梯度提升树回归，需要实例化 `GradientBoostingRegressor` 模型并使用 `fit` 方法。这需要提供特征矩阵（`X_train`）和目标变量（`y_train`）。

    PySpark：在 PySpark 中，使用 `GBTRegressor` 模型及其对应的 `fit` 方法。需要提供一个包含特征和目标变量的 DataFrame（`train_data`）。在开始训练过程之前，需要使用 `VectorAssembler` 步骤将特征组合成一个向量。

3.  模型评估：

    Scikit-Learn：梯度提升树回归模型性能的评估涉及两个指标：`mean_squared_error` 和 `r2_score`。这些指标通过比较预测值（`y_pred`）和实际值（`y_test`）来计算。

    PySpark：PySpark 的 `RegressionEvaluator` 类便于计算指标，包括均方误差和 r2。通过指定包含预测值（`predictions`）和实际值（`y_test`）的列来进行评估。

4.  预测：

    Scikit-Learn：使用训练好的梯度提升树回归模型进行预测涉及使用 `predict` 方法。通过提供特征矩阵（`X_test`），可以获得预测的目标变量值（`y_pred`）。

    PySpark：在 PySpark 框架中，训练好的模型的 `transform` 函数可以对新的数据实例进行预测。这需要提供一个包含特征的 DataFrame（`test_data`），从而创建一个新的列（`prediction`）。需要使用 `VectorAssembler` 步骤将特征组装成适合准确预测的格式。

上述比较表明，尽管由于 Scikit-Learn 基于 Python 而 PySpark 是构建在 Apache Spark 之上的分布式计算框架，导致两者的语法和实现存在差异，但 Scikit-Learn 和 PySpark 的建模步骤在很大程度上是相似的。



### 使用 Scikit-Learn 实现 GBT

在接下来的两个小节中，我们将把前面描述的建模步骤（数据准备、模型创建、模型训练、预测和模型评估）转换为 Scikit-Learn 和 PySpark 代码。先从 Scikit-Learn 开始，让我们简要回顾一下将在 Scikit-Learn 中用于实现随机森林回归器的数据：

```
[In]: import pandas as pd
[In]: def load_housing_data():
url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/housing.csv')
return pd.read_csv(url)
[In]: pandas_df = load_housing_data()
[In]: X = pandas_df.drop('price', axis=1)
[In]: y = pandas_df['price']
```

在这段代码中，我们定义了一个名为 `load_housing_data()` 的函数，其中指向 CSV 文件位置的 URL 被传递给 Pandas 的 `read_csv()` 方法。该方法读取 CSV 文件并返回一个 Pandas DataFrame。一旦我们调用 `load_housing_data()` 函数，就会创建 pandas_df DataFrame。

然后，代码创建了两个新的 DataFrame：X 包含 12 个特征，y 包含目标变量（价格）。将 X 和 y 分开，使得梯度提升树算法能够基于 X 输入特征预测 y 目标值。

生成 X 和 y DataFrame 后，我们现在可以通过首先导入必要的库来开始 Scikit-Learn 中的建模过程。

**步骤 1：导入必要的库**

```
[In]: import pandas as pd
[In]: from sklearn.ensemble import GradientBoostingRegressor
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.metrics import r2_score, mean_squared_error
[In]: from sklearn.preprocessing import OneHotEncoder
```

**步骤 2：数据准备**

2.1\. 对分类变量执行独热编码

```
[In]: cat_cols = ['mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'prefarea', 'furnishingstatus']
[In]: onehot_encoder = OneHotEncoder(sparse=False)
[In]: X_encoded = onehot_encoder.fit_transform(X[cat_cols])
[In]: X_encoded_df = pd.DataFrame(X_encoded, columns=onehot_encoder.get_feature_names_out(cat_cols))
[In]: X.drop(cat_cols, axis=1, inplace=True)
[In]: X = pd.concat([X, X_encoded_df], axis=1)
```

2.2\. 将数据拆分为训练集和测试集

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

**步骤 3：训练 GBT 回归模型**

```
[In]: gbt_regressor = GradientBoostingRegressor()
[In]: gbt_regressor.fit(X_train, y_train)
```

**步骤 4：预测**

```
[In]: y_pred = gbt_regressor.predict(X_test)
```

**步骤 5：模型评估**

```
[In]: r2 = r2_score(y_test, y_pred)
[In]: rmse = np.sqrt(mean_squared_error(y_test, y_pred))
```

从上述步骤中可以观察到的一个关键点是，在不到 20 行代码中，我们就完成了数据准备、创建并训练梯度提升树模型、进行预测以及评估模型性能。

以下是这些步骤的详细说明：

**步骤 1：导入必要的库**

在建模过程的第一步，导入必要的库以支持构建、训练和评估 GBT 回归模型的各个方面。这些库包括用于数据操作的 Pandas、用于创建回归模型的 Scikit-Learn 的 `GradientBoostingRegressor` 类、用于将数据拆分为训练集和测试集的 `train_test_split` 函数、用于模型评估的 `r2_score` 和 `mean_squared_error`，以及用于编码分类变量的 `OneHotEncoder`。

**步骤 2：数据准备**

2.1\. 分类变量的独热编码

准备了七个分类列（mainroad、guestroom、basement、hotwaterheating、airconditioning、prefarea、furnishingstatus）用于建模。使用 `OneHotEncoder` 将这些分类变量转换为数值表示。分类列名称存储在 cat_cols 列表中。创建了一个 `OneHotEncoder` 实例，指定输出不应为稀疏格式。编码器对特征数据 X 中的分类列进行拟合，编码后的特征存储在 X_encoded DataFrame 中。使用这些编码后的特征创建一个 DataFrame（X_encoded_df），并带有适当标记的列。然后从 X 中删除原始分类列，并将编码后的列拼接回 X，形成完全编码的输入数据，现在这些数据是机器学习算法能够理解的格式。

2.2\. 数据拆分

使用 `train_test_split` 函数将数据划分为训练集和测试集。这种划分对于在数据的一个子集上训练模型并在另一个独立子集上评估其性能非常重要。该函数将特征数据 (X) 和目标变量 (y) 作为输入，并将它们拆分为 X_train、X_test、y_train 和 y_test。测试集大小设置为 20%，随机状态为 42 以确保结果可重现。

**步骤 3：训练梯度提升树回归模型**

初始化一个 `GradientBoostingRegressor` 模型，未指定任何超参数，这意味着模型使用默认参数。该模型将使用训练数据（X_train 和 y_train）通过 `fit()` 方法进行训练。

**步骤 4：预测**

在此步骤中，使用训练好的 GBT 回归模型，通过 `predict()` 方法对测试数据（X_test）预测目标变量（y_pred）。此步骤使我们能够评估模型的预测与实际值的一致程度。

**步骤 5：模型评估**

此步骤中的代码计算两个关键评估指标：R 平方（r2）和均方根误差（RMSE）。R 平方衡量因变量（此处为价格）中可由自变量预测的方差比例。RMSE 量化预测值与实际值之间的平均差异，从而提供对模型预测准确性和整体性能的洞察。

在打印模型的 RMSE 和 r2 之前，值得考虑几点。首先，由于我们没有指定估计器数量（也称为提升阶段），模型将使用默认的树数量，在 Scikit-Learn 中为 100。我们可以通过以下代码确认这一点：

```
[In]: print(gbt_regressor.n_estimators_)
[Out]: 100
```

该模型还使用单个树（基学习器）的默认最大深度，设置为 3。以下代码和输出证实了这一点：

```
[In]: print(gbt_regressor.max_depth)
[Out]: 3
```

决策树的深度是指其结构中分割的层级数。每一层代表一个基于特征的决策点，树的叶子节点保存最终预测结果。在我们的示例中，100 棵树中的每一棵的最大深度都为 3，这意味着每棵树都被分割成最多 3 层深的一系列决策。它可能创建一个具有 2³（8）个叶子节点的树结构。

我们可以通过分别将 n_estimators 和 max_depth 参数设置为不同的值来自定义树的数量和最大深度。在本章中，我们坚持使用默认参数，而在第 16 章中，我们将演示如何微调模型的超参数。

与前几章中的决策树和随机森林一样，梯度提升树算法允许我们提取特征重要性列表。我们可以通过以下代码实现这一点：



```
[In]: importances = gbt_regressor.feature_importances_
[In]: feature_names = X.columns
[In]: indices = np.argsort(importances)[::-1]
[In]: print('Feature importances:')
[In]: for i in indices:
print(feature_names[i], ':', importances[i])
[Out]:
area : 0.4607
bathrooms : 0.1673
airconditioning_no : 0.0556
parking : 0.0511
stories : 0.0455
bedrooms : 0.0441
airconditioning_yes : 0.0350
furnishingstatus_unfurnished : 0.0297
basement_no : 0.0222
prefarea_yes : 0.0199
hotwaterheating_no : 0.0134
mainroad_no : 0.0129
basement_yes : 0.0106
guestroom_no : 0.0089
prefarea_no : 0.0069
guestroom_yes : 0.0048
mainroad_yes : 0.0037
furnishingstatus_furnished : 0.0030
furnishingstatus_semi-furnished : 0.0023
hotwaterheating_yes : 0.0023
```

从输出结果中得出的主要结论是，房产面积和浴室数量是模型预测的关键驱动因素。相反，半装修状态和热水供暖的可用性贡献最小。

**步骤 6：在测试集上进行预测**

训练好的随机森林模型用于预测测试特征集（X_test）的目标变量。预测值存储在 `y_pred` 数组中。

我们可以通过以下方式比较实际目标值与预测目标值的样本：

| 价格 | 预测值 |
| --- | --- |
| 4,060,000 | 4,502,828 |
| 6,650,000 | 7,301,498 |
| 3,710,000 | 3,697,702 |
| 6,440,000 | 4,415,743 |
| 2,800,000 | 3,722,737 |

```
[In]: results_df = pd.DataFrame({'Price': y_test, 'Prediction': y_pred})
[In]: print(results_df.head())
[Out]:
```

从输出结果可以看出，模型的准确率并非 100%，因为实际价格与预测价格之间存在差异。例如，在第一行中，实际价格为 4,060,000 美元，而预测价格为 4,502,828 美元，这表明模型高估了价格。模型在第二行（7,301,498 对比 6,650,000）和第五行（3,722,737 对比 2,800,000）也高估了价格。然而，在第三行和第四行中，模型低估了价格。

**步骤 7：模型评估**

在最后一步中，使用两个评估指标来评估梯度提升树回归模型的性能：决定系数（R 平方，r2）和均方根误差（RMSE，rmse）。`r2_score()` 函数计算 R 平方值，该值表示目标变量中可由特征预测的方差比例。`mean_squared_error()` 函数计算预测值与实际目标值之间的均方误差，然后取其平方根得到 RMSE。这些指标有助于了解模型的预测与实际目标值的吻合程度，从而评估模型的性能。

我们可以通过以下代码打印这两个性能指标：

```
[In]: print('R-squared score:', r2)
[In]: print('Root mean squared error:', rmse)
[Out]: R-squared score: 0.67 Root mean squared error: 1298307
```

现在我们已经获得了梯度提升树回归模型的评估指标值，可以将它们与前两章中随机森林和决策树回归模型的值进行比较。

以下是比较结果：

| 模型 | RMSE | r2 |
| --- | --- | --- |
| 梯度提升树 | 1,298,307 | 0.67 |
| 随机森林 | 1,388,213 | 0.62 |
| 决策树 | 1,715,691 | 0.42 |

在此表中，第一列表示所使用的算法类型。第二列包含 RMSE 的值，该值衡量预测值与实际值之间的平均差异。RMSE 越低，表示模型性能越好。最后一列包含 r2（R 平方），它是衡量模型预测与实际数据变异性匹配程度的指标。其取值范围为 0 到 1，值越高表示拟合效果越好。

我们可以看到，梯度提升树回归模型的性能优于随机森林和决策树回归模型，因为它的 RMSE 更低，r2 更高。然而，这种性能差异可能归因于默认超参数的不同，我们将在第 16 章详细探讨这一主题。例如，Scikit-Learn 中随机森林的默认最大深度设置为 None，允许树扩展到任意深度。这与梯度提升中默认最大深度为 3 形成对比，表明该模型可以更浅。正如本章引言中提到的，较浅的 GBT 往往比深的 GBT 表现更好，通常能产生更稳健和泛化能力更强的结果。

性能差异也可能意味着我们正在处理的特定数据集具有 GBT 特别擅长捕捉的模式和关系。GBT 具有顺序性质，旨在纠正先前树的错误，这在处理数据中的复杂关系时可能具有优势。



### 使用 PySpark 实现 GBT

现在，我们将使用 PySpark 构建相同的 GBT 回归模型，并使用相同的房价数据集对其进行训练。首先，让我们快速回顾一下在“数据集”部分中用于设置 PySpark 的代码。

如下代码所示，我们首先创建一个 Pandas DataFrame，然后使用 `createDataFrame()` 方法将其转换为 PySpark DataFrame。

```
[In]: import pandas as pd
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.appName("GBTRegression").getOrCreate()
[In]: def load_housing_data():
url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/housing.csv')
return pd.read_csv(url)
[In]: pandas_df = load_housing_data()
[In]: spark_df = spark.createDataFrame(pandas_df)
```

在这段代码中，我们通过几个步骤，同时使用 Pandas 库和 PySpark 框架来处理房价数据。第一行导入了 Pandas 库并将其命名为 pd。第二行导入了 `SparkSession`，这是使用 Spark 功能的入口点。然后，代码使用 `SparkSession.builder.appName()` 方法建立了一个 Spark 会话。此会话充当与 Spark 集群的连接，并提供执行分布式数据处理的环境。下一步定义了一个名为 `load_housing_data()` 的函数，该函数使用 Pandas 的 `read_csv()` 函数从 URL 获取房价数据。它从指定的 URL 读取一个 CSV 文件，并返回一个包含数据的 Pandas DataFrame。接着，代码使用定义的 `load_housing_data()` 函数将房价数据读入一个名为 pandas_df 的 Pandas DataFrame。最后，代码使用 `createDataFrame()` 方法将 Pandas DataFrame pandas_df 转换为一个名为 spark_df 的 Spark DataFrame。此转换允许使用 Spark 的分布式计算能力来处理和分析数据。

现在，我们将编写用于数据准备、构建和训练模型以及使用测试集评估其性能等步骤的 PySpark 代码。首先，让我们导入必要的库：

**步骤 1：导入必要的库**

```
[In]: from pyspark.ml.feature import StringIndexer, OneHotEncoder, VectorAssembler
[In]: from pyspark.ml.regression import GBTRegressor
[In]: from pyspark.ml.evaluation import RegressionEvaluator
[In]: import pandas as pd
```

**步骤 2：数据准备**

**2.1. 对分类列进行标签编码**

```
[In]: cat_columns = ['mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'prefarea', 'furnishingstatus']
[In]: indexers = [StringIndexer(inputCol=col, outputCol=col+'_label', for indexer in indexers:
spark_df = indexer.fit(spark_df).transform(spark_df)
```

**2.2. 对所有分类列进行独热编码**

```
[In]: encoder = OneHotEncoder(inputCols=[col+'_label' for col in cat_columns], outputCols=[col+'_encoded' for col in cat_columns])
[In]: spark_df = encoder.fit(spark_df).transform(spark_df)
```

**2.3. 将所有特征组合成一个特征向量**

```
[In]: feature_cols = ['area', 'bedrooms', 'bathrooms', 'stories', 'parking'] + [col+'_encoded' for col in cat_columns]
[In]: assembler = VectorAssembler(inputCols=feature_cols, outputCol='features')
[In]: spark_df = assembler.transform(spark_df)
```

**2.4. 将数据拆分为训练集和测试集**

```
[In]: (training_data, test_data) = spark_df.randomSplit([0.8, 0.2], seed=42)
```

**步骤 3：训练梯度提升树回归模型**

```
[In]: gbt = GBTRegressor(featuresCol='features', labelCol='price')
[In]: spark_model = gbt.fit(training_data)
```

**步骤 4：对测试数据进行预测**

```
[In]: predictions = spark_model.transform(test_data)
```

**步骤 5：使用 RMSE 和 R2 评估模型**

```
[In]: evaluator_rmse = RegressionEvaluator(labelCol='price', predictionCol='prediction', metricName='rmse')
[In]: rmse = evaluator_rmse.evaluate(predictions)
[In]: evaluator_r2 = RegressionEvaluator(labelCol='price', predictionCol='prediction', metricName='r2')
[In]: r2 = evaluator_r2.evaluate(predictions)
```

上述步骤展示了我们如何仅用几行 PySpark 代码就能高效地解决一个回归问题。

下面，我们将对前面概述的每个建模步骤进行详细解释：准备数据、创建和训练 GBT 回归模型、对测试数据进行预测，以及使用 RMSE 和 r2 评估模型性能。

**步骤 1：导入必要的库**

在建模过程的第一步，导入了用于处理 PySpark 的库。其中包括用于数据准备的 `StringIndexer`、`OneHotEncoder` 和 `VectorAssembler`，用于执行回归分析的 `GBTRegressor`，以及用于评估模型性能的 `RegressionEvaluator`。同时还导入了 `Pandas` 库并将其命名为 pd。

**步骤 2：数据准备**

本节重点介绍如何准备用于训练和评估的数据。

**2.1. 对分类列进行标签编码**

在数据准备的这个子步骤中，首先识别数据集中的分类列。然后使用 `StringIndexer` 对每个分类列进行标签编码，在 DataFrame 中生成一个带有 `_label` 后缀的对应列。

第一行创建了一个名为 cat_columns 的列表，其中包含数据集中需要编码的分类列的名称。这些分类列表示二元属性，例如 mainroad、guestroom、basement 等。然后，代码创建了一个名为 indexers 的列表，该列表将存储每个分类列的 `StringIndexer` 转换实例。通过指定输入列（例如 mainroad）和所需的输出列（例如 mainroad_label，并附加 `_label` 后缀）来初始化转换。这个新列将存储与分类值对应的数字标签。

在一个循环中，使用存储在 indexers 列表中的 `StringIndexer` 实例处理每个分类列。将 `fit()` 方法应用于索引器，并使用当前数据集 spark_df 作为输入。此步骤计算分类值到数字标签的映射。然后，使用 `transform()` 方法将计算出的转换应用于数据集，生成带有数字标签的新列。

**2.2. 独热编码**

在此子步骤中，使用 `OneHotEncoder` 对经过标签编码的分类列应用独热编码。这将创建带有 `_encoded` 后缀的新列。

代码首先通过指定两个列表 inputCols 和 outputCols 来创建 `OneHotEncoder` 的实例。inputCols 列表是使用列表推导式生成的，该推导式遍历存储在 cat_columns 中的分类列，并为每个列名附加 `_label`。这与之前从 `StringIndexer` 步骤创建的分类标签列相对应。类似地，outputCols 列表是通过为每个对应的分类列名附加 `_encoded` 来生成的。

一旦编码器实例初始化完成，就使用 `fit()` 方法将其应用于 DataFrame spark_df。此步骤根据提供的数据计算编码过程所需的参数。随后，使用 `transform()` 方法将计算出的编码应用于 DataFrame。这会生成新的列，其中包含分类值的二进制向量表示。每个新列对应一个不同的分类标签，包含指示 DataFrame 中每一行是否存在该特定标签的二进制值。

**2.3. 特征向量组装**

所有相关的特征列，包括编码后的分类列，都使用 `VectorAssembler` 组合成一个单一的特征向量。此步骤对于模型训练是必要的。



第一行代码定义了一个名为 `feature_cols` 的列表，其中包含将用于建模的特征名称。这些特征包括面积、卧室数、浴室数、楼层数和停车位等数值属性。此外，列表推导式还添加了经过编码的分类列的名称，每个列名后都附加了 `_encoded`。这些编码列是在上一步中使用 `OneHotEncoder` 技术生成的。

第二行代码初始化了一个 `VectorAssembler` 实例。这个转换器接收 `feature_cols` 列表中指定的输入列，并将它们合并成一个名为 `features` 的单一向量列，该列名在 `outputCol` 参数中指定。

最后，第三行代码使用 `transform()` 方法将 `VectorAssembler` 应用于 DataFrame `spark_df`。这一步会在 DataFrame 中生成一个名为 `features` 的新列。该列中的每一行都包含一个向量，该向量封装了该特定行所有选定特征的值。

2.4\. 数据划分

数据集按 80-20 的比例划分为训练集和测试集。这种划分对于评估模型在未见过的数据上的性能至关重要。

查看代码，`randomSplit()` 方法被应用于 DataFrame `spark_df`。该方法接受两个参数：一个列表 `[0.8, 0.2]` 和一个种子值 `42`。该列表分别定义了分配给训练集和测试集的数据比例。在本例中，80% 的数据分配给训练集，其余 20% 分配给测试集。该方法会随机打乱 DataFrame 的行，并根据指定的比例进行分离。`seed` 参数确保了可重复性，使得在不同执行中能够生成相同的划分。

此操作的结果是创建了两个独立的 DataFrame，分别命名为 `training_data` 和 `test_data`，每个 DataFrame 包含分配给训练集和测试集的行子集。这些数据集将用于训练和评估 GBT 回归模型。

步骤 3：训练梯度提升树回归模型

在此步骤中，训练一个 GBT 回归模型。使用指定的参数实例化一个 `GBTRegressor`，包括输入特征列（`features`）和目标标签列（`price`）。该模型使用默认超参数，并通过 `fit()` 方法在训练数据上进行训练。

在第一行中，创建了一个 `GBTRegressor` 类的实例，并将其赋值给变量 `gbt`。`featuresCol` 参数设置为 `features`，对应于之前组装好的特征向量所在的列。`labelCol` 参数设置为 `price`，对应于包含目标变量（本例中为房产价格）的列。

在第二行中，使用 `training_data` DataFrame 作为输入，对 `gbt` 实例应用 `fit()` 方法。该方法启动了 GBT 回归模型的训练过程。在训练期间，模型学习根据训练数据集中的特征向量（`features`）来预测目标变量（`price`）。此操作的结果是创建一个训练好的模型，存储在 `spark_model` 变量中。

有了这个训练好的模型，我们现在可以访问 GBT 模型中的树的数量和最大深度：

```
[In]: num_trees = spark_model.getNumTrees
[In]: max_depth = spark_model._java_obj.getMaxDepth()
```

然后按如下方式打印树的数量和最大深度：

```
[In]: print("Number of Trees:", num_trees)
[In]: print("Maximum Depth:", max_depth)
[Out]: Number of Trees: 20 Maximum Depth: 5
```

输出表明 GBT 模型由 20 棵独立的决策树集成而成。在 GBT 中，多棵决策树按顺序训练，它们的预测结果被组合起来以提高整体模型的性能。拥有更多的树可能会增强模型捕捉数据中复杂关系的能力，并带来更好的预测精度。然而，模型复杂度和过拟合之间存在权衡，因此选择合适的树的数量至关重要。不过，在本项目中，为了演示，我们选择了默认的树的数量，即 20 棵。在第 16 章中，我们将演示如何调整模型的超参数以覆盖默认值。

输出还显示了这 20 棵树中每一棵的最大深度。树的深度表示它可以有多少个分裂点或节点，这使其能够学习数据中的复杂模式。深度为 5 表示 GBT 模型中的每棵决策树最多可以有 5 层分裂，可能能够捕捉数据中相对详细的特征。然而，更深的树也可能导致过拟合，尤其是在数据集不够大、无法支持这种复杂度的情况下。

我们还可以从训练好的 GBT 模型中访问特征重要性：

```
[In]: feature_importances = spark_model.featureImportances.toArray()
[In]: feature_names = feature_cols
[In]: feature_importance_tuples = list(zip(feature_names, feature_importances))
[In]: sorted_feature_importance = sorted(feature_importance_tuples, key=lambda x: x[1], reverse=True)
```

然后使用以下循环打印每个特征重要性的值：

```
[In]: print("Sorted Feature Importances:")
[In]: for feature_name, importance in sorted_feature_importance:
print(f"Feature '{feature_name}': {importance:.4f}")
[Out]:
Sorted Feature Importances:
area: 0.3818
bathrooms: 0.1089
stories: 0.1085
parking: 0.0900
bedrooms: 0.0808
airconditioning_encoded: 0.0343
mainroad_encoded: 0.0240
basement_encoded: 0.0227
furnishingstatus_encoded: 0.0081
guestroom_encoded: 0.0000
hotwaterheating_encoded: 0.0000
prefarea_encoded: 0.0000
```

输出证实了我们从前一小节的 Scikit-Learn GBT 回归模型中已经了解到的信息：房产的面积和浴室数量是模型中的关键驱动因素。输出还显示，房屋是否有客房、热水供暖系统或是否位于优选区域完全不重要。这表明，从模型中移除这些特征可能会通过降低模型复杂度及其过拟合的可能性来提升性能。

步骤 4：在测试数据上进行预测

使用训练好的模型对测试数据集进行预测。这里使用了 `transform()` 方法，该方法将模型应用于测试数据，并生成目标变量（`price`）的预测值。

生成的 DataFrame 命名为 `predictions`，它包含 `test_data` 中的原始列以及一个名为 `prediction` 的新列。`prediction` 列中的每一行都包含模型根据相应特征向量对目标变量（`price`）的预测值。

我们可以按如下方式打印 `price` 和 `prediction` 列：

```
[In]: predictions.select('price', 'prediction').show(5)
[Out]:
+---------+-----------+
|  Price  | Prediction|
+---------+-----------+
| 6930000 | 5365539   |
| 7070000 | 5303199   |
| 7210000 | 7355716   |
| 7350000 | 5057867   |
| 7455000 | 2507188   |
+---------+-----------+
only showing top 5 rows
```

该输出表明模型既高估也低估了房产价格。它低估价格的频率往往高于高估。



对于实际价格为 6,930,000 美元的第一处房产，模型预测值为 5,365,539 美元。该预测值低于实际价格，表明模型存在低估。同样，实际价格为 7,070,000 美元的第二处房产，预测值为 5,303,199 美元，也低于实际价格，同样表明低估。实际价格为 7,210,000 美元的第三处房产，预测值为 7,355,716 美元，略高于实际价格，表明存在高估。对于实际价格为 7,350,000 美元的第四处房产，模型预测值为 5,057,867 美元，反映了低估。最后，实际价格为 7,455,000 美元的第五处房产，预测值为 2,507,188 美元，显著低于实际价格，表明存在严重低估。

**步骤 5：评估模型**

此步骤侧重于使用 RMSE 和 R²来评估模型的性能。在第一行中，创建了一个`RegressionEvaluator`类的实例，并将其赋值给变量`evaluator_rmse`。`labelCol`参数设置为`price`，代表实际的目标变量；`predictionCol`参数设置为`prediction`，对应模型生成的预测值。`metricName`参数设置为`rmse`，表示评估器将计算 RMSE 指标。

在第二行中，对`evaluator_rmse`实例应用`evaluate()`方法，并将`predictions` DataFrame 作为输入。该方法计算`predictions` DataFrame 中实际价格（`price`列）与预测值（`prediction`列）之间的 RMSE。计算出的 RMSE 值存储在变量`rmse`中。

对 R 平方指标重复相同的过程。在第三行中，创建了另一个`RegressionEvaluator`类的实例，并将其赋值给变量`evaluator_r2`。`metricName`参数设置为`r2`，表示评估器将计算 R 平方指标。

在第四行中，对`evaluator_r2`实例应用`evaluate()`方法，并将`predictions` DataFrame 作为输入。该方法计算 R 平方值，该值量化了模型预测与实际数据变异性的吻合程度。计算出的 R 平方值存储在变量`r2`中。

我们可以按如下方式打印 RMSE 和 r2 的值：

```
[In]: print(f"RMSE: {rmse}")
[In]: print(f"R2: {r2}")
[Out]: RMSE: 1335830 R2: 0.42
```

根据这些结果，让我们将本章中 GBT 回归模型的性能与前面章节中其他基于树的回归算法（决策树和随机森林）进行比较。基于下表中显示的评估指标，随机森林模型似乎优于 GBT 和决策树模型。这一结论源于随机森林模型更低的 RMSE 值和更高的 R 平方（r2）值，具体阐述如下：

*   **更低的 RMSE**：随机森林模型的均方根误差（RMSE）最低，为 1,237,365，表明其预测值平均而言更接近数据集中房产的实际价格。较低的 RMSE 意味着更高的预测精度，因为它衡量了预测误差的大小。随机森林模型具有最低的 RMSE 这一事实表明，其做出的预测与实际价格更为一致。

*   **更高的 R 平方（r2）值**：随机森林模型还拥有最高的 R 平方（r2）值，为 0.50。这表明模型可以解释大约 50%的房产价格方差。更高的 r2 值意味着模型的预测能更好地捕捉数据集中的变异性。在这种情况下，随机森林模型能够解释更大比例的房产价格方差，表明它能更有效地捕捉数据中的潜在模式。

另一方面，尽管随机森林模型在三个被评估的模型中表现最佳，具有较低的 RMSE 和相对较高的 R 平方（r2）值 0.50，但其预测能力仍有提升空间。虽然 0.50 的 R 平方值表明模型解释了大约 50%的房产价格方差，但仍有潜力增强其捕捉数据中更多细微差别和模式的能力。这表明，进一步优化模型的超参数，并可能移除重要性最低的特征，可以带来更准确的预测和更高的 r2 值。

| 模型 | RMSE | r2 |
| --- | --- | --- |
| 梯度提升树 | 1,335,830 | 0.42 |
| 随机森林 | 1,237,365 | 0.50 |
| 决策树 | 1,463,512 | 0.32 |

## 整合所有内容

在上一节中，我们通过使用不同的独立代码片段来解释每个步骤，讨论了 GBT 回归方法。然而，在本节中，我们将所有代码片段分别整合到 Scikit-Learn 和 PySpark 框架的单个代码块中。这使得查看整个代码如何组合在一起变得更加容易，并且便于运行。此代码的副本也将在 GitHub 上提供。

### Scikit-Learn

在本小节中，我们将用于准备数据、构建和训练 GBT 回归模型、进行预测以及评估模型性能的所有 Scikit-Learn 代码片段组合在一起。

```
# 导入必要的库
[In]: from sklearn.ensemble import GradientBoostingRegressor
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.metrics import r2_score, mean_squared_error
[In]: from sklearn.preprocessing import OneHotEncoder
[In]: import pandas as pd
[In]: import numpy as np
# 定义加载数据的函数
[In]: def load_housing_data():
url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/housing.csv')
return pd.read_csv(url)
# 通过调用函数创建 Pandas DataFrame：
[In]: pandas_df = load_housing_data()
# 将数据拆分为特征和目标变量
[In]: X = pandas_df.drop('price', axis=1)
[In]: y = pandas_df['price']
# 对分类变量执行独热编码
[In]: cat_cols = ['mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'prefarea', 'furnishingstatus']
[In]: onehot_encoder = OneHotEncoder(sparse=False)
[In]: X_encoded = onehot_encoder.fit_transform(X[cat_cols])
[In]: X_encoded_df = pd.DataFrame(X_encoded, columns=onehot_encoder.get_feature_names_out(cat_cols))
[In]: X.drop(cat_cols, axis=1, inplace=True)
[In]: X = pd.concat([X, X_encoded_df], axis=1)
# 将数据拆分为训练集和测试集
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
# 训练梯度提升树回归模型
[In]: gbt_regressor = GradientBoostingRegressor()
[In]: gbt_regressor.fit(X_train, y_train)
# 打印提升阶段的数量
[In]: print("Number of boosting stages:", gbt_regressor.n_estimators_)
# 打印树的最大深度
[In]: print("Maximum tree depth:", gbt_regressor.max_depth)
# 打印特征重要性
[In]: importances = gbt_regressor.feature_importances_
[In]: feature_names = X.columns
[In]: indices = np.argsort(importances)[::-1]
[In]: print('Feature importances:')
[In]: for i in indices:
print(feature_names[i], ':', importances[i])
# 对测试集进行预测
[In]: y_pred = gbt_regressor.predict(X_test)
# 打印实际值和预测值
[In]: results_df = pd.DataFrame({'Price': y_test, 'Prediction': y_pred})
[In]: print(results_df.head())
# 计算评估指标
[In]: r2 = r2_score(y_test, y_pred)
[In]: rmse = np.sqrt(mean_squared_error(y_test, y_pred))
# 打印评估指标
[In]: print('R-squared score:', r2)
[In]: print('Root mean squared error:', rmse)
```



### PySpark

在本小节中，我们将使用 PySpark 代码完成相同的任务：整合所有用于准备房价数据、构建并训练 GBT 回归模型、进行预测以及评估模型性能的代码片段。

```
# 导入必要的库
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.feature import StringIndexer, OneHotEncoder, VectorAssembler
[In]: from pyspark.ml.regression import GBTRegressor
[In]: from pyspark.ml.evaluation import RegressionEvaluator
[In]: import pandas as pd
[In]: import numpy as np
# 创建 Spark 会话
[In]: spark = SparkSession.builder.appName("GBTRegressorExample").getOrCreate()
[In]: def load_housing_data():
url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/housing.csv')
return pd.read_csv(url)
# 加载数据并创建 Spark DataFrame
[In]: pandas_df = load_housing_data()
[In]: spark_df = spark.createDataFrame(pandas_df)
# 对分类列进行标签编码
[In]: cat_columns = ['mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'prefarea', 'furnishingstatus']
[In]: indexers = [StringIndexer(inputCol=col, outputCol=col+'_label', handleInvalid='keep') for col in cat_columns]
[In]: for indexer in indexers:
spark_df = indexer.fit(spark_df).transform(spark_df)
# 对所有分类列进行独热编码
[In]: encoder = OneHotEncoder(inputCols=[col+'_label' for col in cat_columns], outputCols=[col+'_encoded' for col in cat_columns])
[In]: spark_df = encoder.fit(spark_df).transform(spark_df)
# 将所有特征组合成一个特征向量
[In]: feature_cols = ['area', 'bedrooms', 'bathrooms', 'stories', 'parking'] + [col+'_encoded' for col in cat_columns]
[In]: assembler = VectorAssembler(inputCols=feature_cols, outputCol='features')
[In]: spark_df = assembler.transform(spark_df)
# 将数据拆分为训练集和测试集
[In]: (training_data, test_data) = spark_df.randomSplit([0.8, 0.2], seed=42)
# 训练梯度提升树回归模型
[In]: gbt = GBTRegressor(featuresCol='features', labelCol='price')
[In]: spark_model = gbt.fit(training_data)
# 从 GBT 模型中获取树的数量和最大深度
[In]: num_trees = spark_model.getNumTrees
[In]: max_depth = spark_model._java_obj.getMaxDepth()
# 打印树的数量和最大深度
[In]: print("Number of Trees:", num_trees)
[In]: print("Maximum Depth:", max_depth)
# 从 GBT 模型中获取特征重要性
[In]: feature_importances = spark_model.featureImportances.toArray()
# 从 feature_cols 获取列名
[In]: feature_names = feature_cols
# 将特征名称和重要性组合成元组列表
[In]: feature_importance_tuples = list(zip(feature_names, feature_importances))
# 按重要性降序排列元组列表
[In]: sorted_feature_importance = sorted(feature_importance_tuples, key=lambda x: x[1], reverse=True)
# 打印排序后的特征重要性及其名称
[In]: print("Sorted Feature Importances:")
[In]: for feature_name, importance in sorted_feature_importance:
print(f"Feature '{feature_name}': {importance:.4f}")
# 对测试数据进行预测
[In]: predictions = spark_model.transform(test_data)
# 打印实际值和预测值
[In]: predictions.select('price', 'prediction').show(5)
# 使用 RMSE 和 R2 评估模型
[In]: evaluator_rmse = RegressionEvaluator(labelCol='price', predictionCol='prediction', metricName='rmse')
[In]: rmse = evaluator_rmse.evaluate(predictions)
[In]: evaluator_r2 = RegressionEvaluator(labelCol='price', predictionCol='prediction', metricName='r2')
[In]: r2 = evaluator_r2.evaluate(predictions)
# 打印 RMSE 和 R2 值
[In]: print(f"RMSE: {rmse}")
[In]: print(f"R2: {r2}")
```

## 总结

本章介绍了梯度提升树（GBT）回归，并演示了如何使用梯度提升算法构建回归模型。本章还探讨了 Pandas 和 PySpark 在数据读取和探索方面的相似之处，并展示了 Scikit-Learn 和 PySpark 遵循相似的建模步骤，包括数据转换、分类编码、数据缩放、模型构建、预测和评估。

此外，本章还将 GBT 回归模型的性能与其他两种基于树的算法（决策树和随机森林）的性能进行了比较。关键结论是，即使在这三种模型中表现最佳的模型，仍然可以通过进一步的超参数优化和降维来提升性能。

在下一章中，我们将重点介绍分类，这是一种独特的监督学习形式。我们的目标是构建、训练和评估一个逻辑回归模型，然后使用它来预测患糖尿病的可能性。目的是证明尽管语法和函数名称存在差异，但在 Scikit-Learn 和 PySpark 中进行逻辑回归的基本流程是相似的。此外，我们还将展示 Pandas 和 PySpark 在处理数据加载和操作方面共享类似的方法，这简化了诸如读取和探索数据等任务。



