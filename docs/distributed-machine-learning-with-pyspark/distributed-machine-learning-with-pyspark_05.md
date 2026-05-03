# 5. 使用 Pandas、Scikit-Learn 和 PySpark 进行随机森林回归

在上一章中，我们开发了一个决策树回归模型来预测房价。在本章中，我们将介绍一种称为随机森林的替代模型。尽管这两种回归模型都使用决策树，但它们存在显著差异。

首先，决策树是更简单的模型，其特点是单一的树结构，而随机森林则更为复杂，由多个决策树组成。此外，决策树容易过拟合，而随机森林通过平均众多树的预测结果来抵消这种趋势。而且，由于采用了集成学习，随机森林通常比单个决策树具有更高的准确性。

从可解释性的角度来看，决策树更容易理解，因为它们可以被可视化，而随机森林由于组合了多个树，在解释上更具挑战性。在速度方面，决策树的训练速度更快，而随机森林由于其集成方法而速度较慢。更重要的是，决策树表现出高方差（意味着它们容易过拟合，即在训练数据上表现良好，但在新的、未见过的数据上表现不佳）和低偏差（意味着它们容易欠拟合，即在训练数据和新数据上均表现不佳），而随机森林则在偏差和方差之间实现了平衡的权衡，最终减少了过拟合和欠拟合。

本章的目标有两个。首先，我们将使用 Scikit-Learn 和 PySpark 来构建、训练和评估一个随机森林回归模型，同时在这两个框架之间进行类比。随后，我们将通过将随机森林模型应用于上一章中用于决策树算法的相同房价数据集，来评估随机森林优于决策树的假设。这意味着，如果这个假设得到证实，我们应该会看到随机森林的 r² 更高，RMSE 更低。

在构建随机森林回归模型之前，让我们重新审视房价数据集，以刷新对其基本属性的理解。此外，我们可以证明，我们可以像通常使用 Pandas 一样，利用 PySpark 的分布式计算能力，使用 PySpark 执行相同的探索性数据分析。

## 数据集

与我们在上一章中所做的类似，我们可以定义一个 Python 函数来加载和读取房价数据集。然后，我们创建一个 Pandas DataFrame 并将其转换为 PySpark DataFrame。以下代码实现了这些目标：

```
[In]: import pandas as pd
[In]: def load_housing_data():
url = ('https://raw.githubusercontent.com/abdelaziztestas/’
'spark_book/main/housing.csv')
return pd.read_csv(url)
[In]: pandas_df = load_housing_data()
[In]: spark_df = spark.createDataFrame(pandas_df)
```

回想上一章，该数据集有 545 行和 13 列。我们可以使用 Pandas 的 `shape` 属性来确认这一点：

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

Pandas:

```
[In]: print(pandas_df.columns)
[Out]: Index(['price', 'area', 'bedrooms', 'bathrooms', 'stories', 'mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'parking', 'prefarea', 'furnishingstatus'],     dtype='object')
```

PySpark:

```
[In]: print(spark_df.columns)
[Out]: ['price', 'area', 'bedrooms', 'bathrooms', 'stories', 'mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'parking', 'prefarea', 'furnishingstatus']
```

请记住，`columns` 属性在 PySpark 输出中不显示索引，因为这两个库处理索引的方式不同。

我们可以选择关闭 Pandas 中的索引显示，以与 PySpark 保持一致，代码如下：

```
[In]: column_labels = '[' + ', '.join([f"'{col}'" for col in pandas_df.columns]) + ']'
[In]: print(column_labels)
[Out]: ['price', 'area', 'bedrooms', 'bathrooms', 'stories', 'mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'parking', 'prefarea', 'furnishingstatus']
```

price 变量是我们的标签或目标变量，而其余变量是特征。

正如我们在上一章中回忆的那样，该数据集是数值变量和分类变量的混合。要检查哪些列是数值型，哪些是分类型，我们可以在 Pandas 和 PySpark 中使用 `dtypes` 属性：

Pandas:

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

PySpark:

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

其中六个分类变量有两个类别（是或否，反之亦然），而 furnishingstatus 列有三个类别（已装修、半装修和未装修）。我们可以通过以下代码打印每个分类变量的唯一值来确认这一点：

Pandas:



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

PySpark:

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

现在我们已经回顾了房屋数据集的主要属性，并演示了 Pandas 和 PySpark 的相似方法，接下来可以着手构建随机森林回归模型了。

## 随机森林回归

随机森林模型属于集成方法家族，因为它结合了多个决策树的预测结果，每棵树都在数据和特征的不同子集上进行训练。虽然随机森林可用于分类和回归问题，但在本章中，我们将重点放在回归上，因为我们的目标变量（房价）是连续值，而非分类值。在第 9 章中，我们将演示如何将随机森林应用于涉及分类目标变量的分类问题。

在构建随机森林回归模型之前，我们先比较一下 Scikit-Learn 和 PySpark 中的建模步骤。

**Scikit-Learn/PySpark 相似之处**

Scikit-Learn 和 PySpark 在机器学习方面采用的方法相似，因为它们都遵循类似的流程来构建、训练和评估决策树回归器。我们可以通过探索它们提供的主要机器学习功能来了解这两个平台之间的共同点：

1.  数据准备：

    Scikit-Learn：在 Scikit-Learn 中，使用 `train_test_split` 等函数将决策树回归数据划分为训练集和测试集。其他函数包括用于特征缩放的 `StandardScaler`、用于编码分类变量的 `OneHotEncoder` 以及用于处理缺失数据的 `SimpleImputer`。

    PySpark：在 PySpark 中，可以使用 DataFrame 执行类似的任务。`randomSplit` 函数用于将数据划分为训练集和测试集，`StandardScaler` 处理特征缩放，`OneHotEncoder` 执行分类编码，`Imputer` 处理缺失数据。此外，还使用 `VectorAssembler` 将特征列合并为单个向量列，这一步与 Scikit-Learn 的方法有所不同。

2.  模型训练：

    Scikit-Learn：对于 Scikit-Learn 中的随机森林回归，可以实例化 `RandomForestRegressor` 模型，然后使用 `fit` 方法。这需要提供特征矩阵（`X_train`）和目标变量（`y_train`）。

    PySpark：在 PySpark 中，我们也使用 `RandomForestRegressor` 模型及其对应的 `fit` 方法。需要一个包含特征和目标变量列的 DataFrame（`train_data`）。在开始训练过程之前，务必应用 `VectorAssembler` 步骤。

3.  模型评估：

    Scikit-Learn：评估随机森林回归模型性能涉及两个关键指标：`mean_squared_error` 和 `r2_score`。这些指标通过比较预测值（`y_pred`）和实际目标变量值（`y_test`）来计算。

    PySpark：PySpark 中的 `RegressionEvaluator` 类有助于计算相同的评估指标（平方误差和 r2）。这是通过指定包含预测值（`predictions`）和实际值（`y_test`）的列进行评估来实现的。

4.  预测：

    Scikit-Learn：使用训练好的随机森林回归器进行预测需要使用 `predict` 方法。通过提供特征矩阵（`X_test`），我们获得预测的目标变量值（`y_pred`）。

    PySpark：在 PySpark 中，训练模型的 `transform` 方法可对新数据实例生成预测。提供一个包含特征的 DataFrame（`test_data`），预测结果将存储在新列（`prediction`）中。确保应用 `VectorAssembler` 步骤以维持必要的特征向量结构。

从前面的比较可以看出，Scikit-Learn 和 PySpark 之间的建模步骤惊人地相似。然而，正如我们将在下一小节中看到的，由于 Scikit-Learn 基于 Python，而 PySpark 是构建在 Apache Spark 之上的分布式计算框架，因此实现细节和语法可能会有所不同。



### 使用 Scikit-Learn 的随机森林

在本小节中，我们将前面描述的建模步骤转换为 Python 代码。首先，回顾一下我们将用于在 Scikit-Learn 中训练随机森林回归器的数据：

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

这段代码定义了一个名为 `load_housing_data()` 的函数。函数中的 URL 指向 CSV 文件的位置，它被传递给 Pandas 的 `read_csv()` 方法。该方法读取 CSV 文件并返回一个 Pandas DataFrame。一旦调用了 `load_housing_data()` 函数，就会创建 pandas_df DataFrame。

下一步，代码创建了两个新的 DataFrame：X 包含 12 个输入特征，y 包含目标变量（price）。X 和 y 之间的这种分离使得可以进行监督学习任务，其目标是根据 X 中的输入特征来预测目标值。

接下来进入建模步骤，与所有机器学习算法一样，第一步是导入必要的库。

**步骤 1：导入必要的库**

在此步骤中，我们导入数据准备、模型训练、模型评估和数据操作所需的类和函数：

```
[In]: from sklearn.ensemble import RandomForestRegressor
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.metrics import r2_score, mean_squared_error
[In]: from sklearn.preprocessing import OneHotEncoder
[In]: import pandas as pd
[In]: import numpy as np
```

**步骤 2：准备数据**

这里涉及两个子步骤：将分类变量编码为数值，以及将数据集拆分为训练集和测试集：

**2.1. 对分类变量执行独热编码**

```
[In]: cat_cols = ['mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'prefarea', 'furnishingstatus']
[In]: onehot_encoder = OneHotEncoder(sparse=False)
[In]: X_encoded = onehot_encoder.fit_transform(X[cat_cols])
[In]: X_encoded_df = pd.DataFrame(X_encoded, columns=onehot_encoder.get_feature_names_out(cat_cols))
[In]: X.drop(cat_cols, axis=1, inplace=True)
[In]: X = pd.concat([X, X_encoded_df], axis=1)
```

**2.2. 将数据拆分为训练集和测试集**

```
[In]: X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

**步骤 3：训练随机森林模型**

```
[In]: random_forest_model = RandomForestRegressor()
[In]: random_forest_model.fit(X_train, y_train)
```

**步骤 4：对测试集进行预测**

```
[In]: y_pred = random_forest_model.predict(X_test)
```

**步骤 5：评估模型**

```
[In]: r2 = r2_score(y_test, y_pred)
[In]: rmse = np.sqrt(mean_squared_error(y_test, y_pred))
```

上述五个建模步骤表明，仅用几行代码，我们就完成了数据准备、构建随机森林模型、训练模型、评估模型以及对测试集进行预测。

让我们更详细地了解这些步骤：

**步骤 1：导入必要的库**

在此步骤中，导入了用于准备数据、构建、训练和评估随机森林回归模型的库。这些模块包括用于构建随机森林模型的 `RandomForestRegressor`、用于将数据集拆分为训练集和测试集的 `train_test_split`，以及诸如 `r2_score` 和 `mean_squared_error` 之类的评估指标。此外，还导入了 `Pandas` 和 `NumPy` 库，用于数据操作和数值计算。

**步骤 2：数据准备**

**2.1. 对分类变量执行独热编码**

为了处理房屋数据集中的分类变量，定义了一个分类列名列表（cat_cols）。该列表包含七个分类变量：mainroad、guestroom、basement、hotwaterheating、airconditioning、prefarea 和 furnishingstatus。

使用参数 `sparse=False` 实例化 `OneHotEncoder` 类，以便在独热编码后生成密集数组。使用此编码器转换原始特征矩阵 X 中的分类变量，得到一个新的数组 X_encoded。然后，将编码后的特征存储在一个新的 DataFrame X_encoded_df 中，其列名来自编码器的 `get_feature_names_out()` 方法。使用 `drop()` 方法从 X DataFrame 中删除原始的分类列，并使用 Pandas 的 `concat()` 方法将编码后的特征重新连接到 X。

**2.2. 将数据拆分为训练集和测试集**

使用 `train_test_split()` 方法将数据集划分为训练子集和测试子集。参数 `test_size` 指定分配给测试的数据比例（0.2 或 20%），而 `random_state` 确保拆分的可重复性。由此产生的训练和测试特征集（X_train 和 X_test）以及相应的目标集（y_train 和 y_test）用于后续的模型训练和评估。

**步骤 3：训练随机森林模型**

一个随机森林回归模型被实例化为 random_forest_model。该模型属于集成学习家族，它结合多个决策树来进行预测。使用训练数据（X_train 和 y_train）通过 `fit()` 方法对其进行训练。

现在随机森林回归模型已经训练完成，我们可以打印森林中树的数量，如下所示：

```
[In]: print(random_forest_model.n_estimators)
[Out]: 100
```

这表明该模型是使用 100 棵树构建的，这是 Scikit-Learn 中的默认树数量。这是一个超参数，可以更改，我们将在第 16 章中更详细地探讨超参数调整的主题时看到。

我们还可以打印模型的特征重要性，就像我们在上一章中对决策树算法所做的那样：

```
[In]: importances = random_forest_model.feature_importances_
[In]: feature_names = X.columns
[In]: indices = np.argsort(importances)[::-1]
[In]: print('Feature importances:')
[In]: for i in indices:
print(feature_names[i], ':', importances[i])
[Out]:
area: 0.4571
bathrooms: 0.1592
stories: 0.0580
parking: 0.0542
bedrooms: 0.0463
furnishingstatus_unfurnished: 0.0350
airconditioning_yes: 0.0282
airconditioning_no: 0.0281
basement_no: 0.0188
basement_yes: 0.0167
prefarea_yes: 0.0145
prefarea_no: 0.0140
furnishingstatus_furnished: 0.0110
hotwaterheating_no: 0.0110
furnishingstatus_semi-furnished: 0.0109
hotwaterheating_yes: 0.0099
guestroom_no: 0.0094
guestroom_yes: 0.0073
mainroad_no: 0.0055
mainroad_yes: 0.0049
```

与我们在上一章中关于决策树回归模型的结论类似，我们发现房屋面积和浴室数量仍然是模型预测的主要驱动因素。相反，从上面的输出中，我们观察到是否位于主干道（是或否）对模型预测的影响最小。

我们还可以打印每棵树的深度：

```
[In]: tree_depths = [estimator.tree_.max_depth for estimator in random_forest_model.estimators_]
[In]: print('Depth of each tree:', tree_depths)
[Out]:
Depth of each tree: [16, 18, 17, 19, 16, 18, 15, 15, 18, 15, 18, 17, 17, 18, 18, 15, 19, 17, 17, 16, 19, 19, 18, 17, 16, 15, 17, 15, 19, 18, 16, 19, 17, 16, 14, 14, 17, 17, 15, 18, 18, 16, 19, 14, 17, 16, 18, 18, 19, 17, 15, 19, 18, 18, 17, 16, 17, 18, 16, 17, 15, 17, 16, 19, 20, 17, 15, 18, 17, 19, 16, 15, 17, 17, 17, 15, 16, 17, 16, 14, 20, 17, 17, 16, 14, 17, 19, 15, 14, 17, 18, 16, 17, 18, 17, 20, 19, 18, 16, 17]
```



输出结果代表了随机森林模型中 100 棵独立决策树各自的最大深度。决策树的深度指的是其结构中分裂层级的数量。每一层级代表一个基于特征做出的决策点，而树的叶节点则保存最终的预测结果。

上述输出中有七个不同的数值（14、15、16、17、18、19、20），这意味着多棵树共享相同的数值。在 Scikit-Learn 中，各决策树的最大深度通常设置为 `None`，这意味着树会不断扩展，直到其包含的样本数少于分裂一个节点所需的最小样本数。这与 PySpark 形成对比（我们将在下一小节看到），因为 PySpark 中树的默认最大深度设置为 5。

让我们看看列表中的几个数值并理解其含义。例如，前四个数值 [16, 18, 17, 19] 表明集成模型中的第一棵决策树的最大深度为 16。这意味着该树被分裂成了一系列最多 16 层深度的决策。它可能创建一个最多包含 2¹⁶（65,536）个叶节点的树结构。类似地，第二棵决策树的最大深度为 18。这表明它比第一棵树略深，可能捕捉到数据中更复杂的模式。它最多可以有 2¹⁸（262,144）个叶节点。第三棵决策树的最大深度为 17，第四棵的最大深度为 19。这些数值表明这些树正在捕捉数据中的复杂关系，可能导致更多的决策路径和更细致的预测。

通常，更深的决策树可以更准确地建模训练数据，因为它能捕捉到特征之间更细微的细节和交互作用。随机森林的集成特性通过聚合多棵决策树的预测结果，有助于减轻过拟合。（但请注意，过深的树也可能导致过拟合，即模型过于贴合训练数据，无法很好地泛化到新的、未见过的数据。）

**步骤 4：在测试集上进行预测**

在此步骤中，使用训练好的随机森林模型来预测测试特征集（X_test）的目标变量。预测值存储在 `y_pred` 数组中。

我们可以如下比较实际目标值与预测目标值的样本：

| 价格 | 预测值 |
| --- | --- |
| 4060000 | 5499270 |
| 6650000 | 7122220 |
| 3710000 | 3826900 |
| 6440000 | 4461100 |
| 2800000 | 3644757 |

```
[In]: results_df = pd.DataFrame({'Price': y_test, 'Prediction': y_pred})
[In]: print(results_df.head())
```

如果模型 100%准确，那么“价格”列（房屋实际价格）和“预测值”列（预测价格）将完全相同。然而，这两列之间存在差异。例如，在第一行中，实际价格为 4,060,000 美元，而预测价格为 5,499,270 美元。这表明模型高估了价格 1,439,270 美元，即实际价格的 35%。

**步骤 5：评估模型**

在最后一步中，使用两个评估指标来评估随机森林回归模型的性能：决定系数（R-squared, r2）和均方根误差（RMSE, rmse）。`r2_score()` 函数计算 R 平方值，该值表示目标变量中可由特征预测的方差比例。`mean_squared_error()` 函数计算预测值与实际目标值之间的均方误差，然后取其平方根得到 RMSE。这些指标有助于评估模型的预测与实际目标值的一致程度，从而帮助评估模型的性能。

我们可以使用以下代码打印这两个指标的值：

```
[In]: print('R-squared score:', r2)
[In]: print('Root mean squared error:', rmse)
[Out]: R-squared score: 0.62 Root mean squared error: 1388213
```

有了随机森林回归模型生成的评估指标，我们现在可以将它们的值与上一章中决策树回归模型得到的值进行比较。这是为了检验随机森林回归模型性能优于决策树的假设。以下是比较结果：

| 模型 | RMSE | r2 |
| --- | --- | --- |
| 随机森林 | 1,388,213 | 0.62 |
| 决策树 | 1,715,691 | 0.42 |

上述输出表明，随机森林回归模型确实优于决策树回归模型。这体现在其显著更低的 RMSE（1,388,213 对比 1,715,691）和明显更高的 r2（0.62 对比 0.42）。在随机森林模型的背景下，这意味着大约 62%的房价变化可以归因于房屋特征的变化。相比之下，决策树回归模型解释了大约 42%的变化。

这一结果并不令人意外，因为随机森林结合了多棵决策树，利用它们的集体预测能力来提供更准确、更稳健的结果。

然而，即使随机森林模型的 r2 达到 62%，也仍然不算高。这表明，尽管该模型显著缓解了决策树模型的过拟合问题，但在实际场景中，还需要采取更多步骤来改进模型，例如通过特征提取或特征选择来降低其复杂度。正如我们将在第 16 章中看到的，微调模型的超参数以找到最优的参数组合是另一种提升其性能的方法。



### 使用 PySpark 的随机森林

我们现在将使用 PySpark 构建随机森林回归模型，并使用相同的房价数据集对其进行训练。首先，让我们快速回顾一下用于生成 PySpark DataFrame 的代码。

在以下代码中，我们首先创建一个 Pandas DataFrame，然后使用 `createDataFrame()` 方法将其转换为 PySpark DataFrame。

```
[In]: import pandas as pd
[In]: from pyspark.sql import SparkSession
[In]: spark = SparkSession.builder.appName("Random Forest Regression").getOrCreate()
[In]: def load_housing_data():
url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/housing.csv')
return pd.read_csv(url)
[In]: pandas_df = load_housing_data()
[In]: spark_df = spark.createDataFrame(pandas_df)
```

在这段代码中，第一行导入了 Pandas 库并将其命名为 pd。第二行从 PySpark 库中导入了 `SparkSession` 类，这是使用 Spark 功能的入口点。第三行使用 `SparkSession.builder.appName()` 方法建立了一个名为“Random Forest Regression”的 Spark 会话。此会话充当与 Spark 集群的连接，并提供执行分布式数据处理的环境。

下一步定义了一个名为 `load_housing_data()` 的函数，该函数使用 `read_csv()` 方法从 URL 获取房价数据。随后，代码使用定义的 `load_housing_data()` 函数将房价数据读入名为 pandas_df 的 Pandas DataFrame 中。最后，代码使用 `createDataFrame()` 方法将 Pandas DataFrame pandas_df 转换为名为 spark_df 的 Spark DataFrame。此转换允许使用 Spark 的分布式计算能力来处理和分析数据。

我们现在将编写 PySpark 代码来准备房价数据，使用训练子样本构建和训练随机森林回归模型，然后使用测试集对其进行评估。作为第一步，我们需要导入必要的库：

**步骤 1：导入必要的库**

```
[In]: from pyspark.ml.feature import StringIndexer, OneHotEncoder, VectorAssembler
[In]: from pyspark.ml.regression import RandomForestRegressor
[In]: from pyspark.ml.evaluation import RegressionEvaluator
```

**步骤 2：数据准备**

2.1\. 对分类列进行标签编码

```
[In]: cat_columns = ['mainroad', 'guestroom', 'basement', 'hotwaterheating', 'airconditioning', 'prefarea', 'furnishingstatus']
[In]: indexers = [StringIndexer(inputCol=col, outputCol=col+'_label', handleInvalid='keep') for col in cat_columns]
[In]: for indexer in indexers:
spark_df = indexer.fit(spark_df).transform(spark_df)
```

2.2\. 对分类列进行独热编码

```
[In]: encoder = OneHotEncoder(inputCols=[col+'_label' for col in cat_columns], outputCols=[col+'_encoded' for col in cat_columns])
[In]: spark_df = encoder.fit(spark_df).transform(spark_df)
```

2.3 将所有特征组合成一个特征向量

```
[In]: feature_cols = ['area', 'bedrooms', 'bathrooms', 'stories', 'parking'] + [col+'_encoded' for col in cat_columns]
[In]: assembler = VectorAssembler(inputCols=feature_cols, outputCol='features')
[In]: spark_df = assembler.transform(spark_df)
```

2.4\. 将数据拆分为训练集和测试集

```
[In]: (training_data, test_data) = spark_df.randomSplit([0.8, 0.2], seed=42)
```

**步骤 3：模型训练**

```
[In]: rf = RandomForestRegressor(featuresCol='features', labelCol='price')
[In]: spark_model = rf.fit(training_data)
```

**步骤 4：预测**

```
[In]: predictions = spark_model.transform(test_data)
```

**步骤 5：模型评估**

```
[In]: evaluator_rmse = RegressionEvaluator(labelCol='price', predictionCol='prediction', metricName='rmse')
[In]: rmse = evaluator_rmse.evaluate(predictions)
[In]: evaluator_r2 = RegressionEvaluator(labelCol='price', predictionCol='prediction', metricName='r2')
[In]: r2 = evaluator_r2.evaluate(predictions)
```

前面的步骤展示了我们如何仅用几行代码就能高效地准备数据、构建模型、训练模型并评估其性能。在概述了这些步骤之后，现在让我们对每一步进行详细解释。

**步骤 1：导入必要的库**

在此步骤中，代码导入了支持模型构建各个阶段的库：

*   使用 `StringIndexer`、`OneHotEncoder` 和 `VectorAssembler` 进行数据准备
*   使用 `RandomForestRegressor` 进行模型构建
*   使用 `RegressionEvaluator` 进行模型评估

这些类和函数支持数据准备过程，例如特征索引、独热编码和数据转换。例如，分类列首先使用 `StringIndexer` 类进行标记，然后使用 `OneHotEncoder` 类进行独热编码。`VectorAssembler` 类将特征组合成一个向量。此外，代码使用 `RandomForestRegressor` 类构建回归模型，并使用 `RegressionEvaluator` 类通过 RMSE 和 R 平方来评估模型性能。

**步骤 2：数据准备**

2.1\. 对分类列进行标签编码

为了准备建模数据，房价数据集中的分类列使用字符串索引（也称为标签编码）进行编码。七个分类特征（主路、客房、地下室、热水供暖、空调、优选区域和家具状况）使用 `StringIndexer` 类进行索引。每个分类值都被分配一个数字标签，并在数据集中添加一个带有 `_label` 后缀的新列。此转换使分类数据与下一步中使用的 `OneHotEncoder` 兼容。

2.2\. 对分类列进行独热编码

在此子步骤中，对先前标记的分类列应用独热编码。使用 `OneHotEncoder` 类将每个分类标签转换为二进制向量表示。原始分类列被替换为它们对应的独热编码版本，从而增强了模型捕捉特征之间非线性关系的能力。

以下是独热编码后分类特征的表示形式，以家具状况为例进行说明：

```
+----------------+----------------------+------------------------+
|furnishingstatus|furnishingstatus_label|furnishingstatus_encoded|
+----------------+----------------------+------------------------+
|       furnished|                   2.0|           (3,[2],[1.0])|
|  semi-furnished|                   0.0|           (3,[0],[1.0])|
|     unfurnished|                   1.0|           (3,[1],[1.0])|
+----------------+----------------------+------------------------+
```

furnishingstatus 列显示此特征的原始分类值（furnished、semi-furnished、unfurnished）。furnishingstatus_label 列显示家具状况特征的标签编码版本。我们通过在子步骤 2.1 中应用 `StringIndexer` 获得了此版本。标签编码将分类值转换为数字标签。例如，furnished 被分配标签 2.0，semi-furnished 被分配标签 0.0，unfurnished 被分配标签 1.0。

furnishingstatus_encoded 列表示 furnishingstatus 特征的独热编码版本。这是通过在子步骤 2.2 中将 `OneHotEncoder` 应用于索引特征而获得的。独热编码将分类值转换为二进制向量，其中每个向量在对应类别的位置为 1，在其他所有位置为 0。这种格式更适合机器学习算法。符号 (3,[2],[1.0]) 表示该向量的长度为 3（反映了家具状况列的三个类别），并且在对应于标签 2（代表 furnished）的位置上值为 1.0。类似地，(3,[0],[1.0]) 代表 semi-furnished，(3,[1],[1.0]) 代表 unfurnished。



2.3 将所有特征合并为单一特征向量

为了满足 PySpark 的要求，经过独热编码的分类特征会被合并成一个单一的数值向量。此任务使用 `VectorAssembler` 类来完成。指定的输入列被连接起来，以创建一个名为 features 的新列。

以下是 features 列中某一行的示例，其中包含了组装后的特征：

```
(20,[0,1,2,3,4,5,7,9,11,14,16,19],
[7420.0,4.0,2.0,3.0,2.0,1.0,1.0,1.0,1.0,1.0,1.0,1.0])
```

方括号前的数字表示共有 20 个特征。这是原始数值特征（12 个）与经过独热编码的特征（8 个）的总和。换句话说，包括五个原始数值特征（面积、卧室数、浴室数、楼层数和停车位数），七个独热编码特征（对应七个分类列：主干道、客房、地下室、热水供暖、空调、优选区域和装修状况，每个特征有两个唯一值，即“是”或“否”），以及一个额外的槽位，用于表示具有三个唯一值（已装修、半装修、未装修）的装修状况列。

第一个方括号中有 12 个索引，第二个方括号中有 12 个对应的值。这些索引和值对应数据集中的 12 个特征，既包括原始数值特征，也包括独热编码特征。

2.4 将数据拆分为训练集和测试集

数据集被划分为训练集和测试集，用于模型开发和评估。使用 `randomSplit()` 方法，数据被分割为 80% 的训练集和 20% 的测试集。为了结果的可复现性，拆分时指定了一个特定的随机种子值（42）。

步骤 3：训练随机森林模型

使用训练样本对算法进行训练。初始化一个 `RandomForestRegressor` 实例，其参数包括输入特征列（features）和目标列（price）。该模型使用训练数据集进行训练。

我们可以通过以下代码检查模型中使用的决策树数量以及每棵树的深度：

```
[In]: from collections import Counter
[In]: tree_depths = []
[In]: for tree in individual_trees:
depth = tree.depth
tree_depths.append(depth)
[In]: depth_counter = Counter(tree_depths)
[In]: print("Tree Depths and Counts:")
[In]: for depth, count in depth_counter.items():
print(f"Depth {depth}: {count} trees")
[Out]: Depth 5: 20 trees
```

从 `collections` 模块导入 `Counter` 类后，代码初始化了一个空列表来存储树的深度。接着，它遍历每棵树并获取其深度。然后，代码统计每个树深度的出现次数，最后打印出深度及其对应的数量。

输出结果显示有 20 棵树，每棵树的深度为 5。这些是 PySpark 中的默认值，与 Scikit-Learn 算法的默认值不同：Scikit-Learn 默认使用 100 棵树，并且没有为树的深度设置特定的默认值。这些差异可能导致模型性能上的不同。

在基于树的算法中，树的深度指的是它包含的分裂层级数。每次分裂都会根据特定的特征和阈值将数据划分为子集。更深的树可以捕捉数据中更复杂的模式。在我们的具体示例中，每棵树的深度都构建为 5，表明这些树相对较浅。这意味着 PySpark 模型的性能可能低于 Scikit-Learn 模型，因为后者的树深度范围在 14 到 20 之间，能够从数据集中捕捉更复杂的信息。

我们还可以按如下方式打印模型的特征重要性：

```
[In]: feature_importances = spark_model.featureImportances.toArray()
[In]: feature_names = feature_cols
[In]: feature_importance_tuples = list(zip(feature_names, feature_importances))
[In]: sorted_feature_importance = sorted(feature_importance_tuples, key=lambda x: x[1], reverse=True)
[In]: print("Sorted Feature Importances:")
[In]: for feature_name, importance in sorted_feature_importance:
print(f"Feature '{feature_name}': {importance:.4f}")
[Out]:
Sorted Feature Importances:
area: 0.3693
bathrooms: 0.2030
stories: 0.0604
bedrooms: 0.0545
parking: 0.0446
furnishingstatus_encoded: 0.0096
basement_encoded: 0.0094
prefarea_encoded: 0.0087
guestroom_encoded: 0.0080
mainroad_encoded: 0.0072
airconditioning_encoded: 0.0072
hotwaterheating_encoded: 0.0045
```

代码首先直接打印从随机森林模型获取的特征重要性。然后，它从 `feature_cols` 列表中检索列名。接着，它将特征名称和重要性组合成一个元组列表。之后，代码根据重要性值对元组列表进行降序排序，并打印排序后的特征重要性及其对应的名称。

输出结果表明，房屋面积和浴室数量是随机森林预测的主要驱动因素。空调和热水供暖是模型中重要性最低的特征。

步骤 4：预测

使用训练好的模型对测试数据进行预测。将 `transform` 方法应用于测试数据集，使用训练好的模型生成目标变量的预测值。

正如我们使用 Scikit-Learn 所做的那样，让我们打印一个实际价格和预测价格的样本进行比较（请注意，Scikit-Learn 和 PySpark 实现中实际价格的前 5 行存在差异。这些差异可归因于数据拆分和后续预测过程的不同。具体来说，Scikit-Learn 代码使用 `train_test_split`，而 PySpark 代码使用 `randomSplit`，后者使用了不同的测试数据子集进行预测，导致了这些差异）：

| 价格 | 预测值 |
| --- | --- |
| 6930000 | 5198444 |
| 7070000 | 4911813 |
| 7210000 | 7193449 |
| 7350000 | 5423289 |
| 7455000 | 3970584 |

```
[In]: predictions.select('price', 'prediction').show(5)
[Out]:
```

此样本输出表明，模型在所有情况下都低估了目标价格。例如，在第一个观测值中，实际价格为 6,930,000 美元，而预测价格为 5,198,444 美元。类似地，在最后一个观测值中，预测价格远低于实际价格（3,970,584 美元 vs. 7,455,000 美元）。

步骤 5：模型评估

在 PySpark 建模过程的最后一步，使用两个评估指标（均方根误差 RMSE 和决定系数 R²）来评估模型的性能。使用 `RegressionEvaluator` 类来计算这些指标。RMSE 衡量平均预测误差，而 R² 评估模型对数据的拟合优度。`evaluate` 方法根据预测值和实际目标值计算这些指标，从而提供关于模型准确性和预测能力的见解。

我们可以使用以下代码打印这两个指标的值：

```
[In]: print(f"RMSE: {rmse}")
[In]: print(f"R2: {r2}")
[Out]: RMSE: 1237365 R2: 0.50
```

RMSE 相对较大（1,237,365），而 R² 相对较小（0.50），表明 PySpark 随机森林回归模型的性能相当一般。然而，与前一章的决策树回归器相比，本章中的模型表现出明显的改进。这在下表中显而易见，随机森林的 RMSE 低于决策树（1,237,365 vs. 1,463,512），并且其 R² 更高（0.50 vs. 0.32）。这种改进是意料之中的，因为随机森林的集成特性使其比决策树更不容易过拟合。

| 模型 | RMSE | R² |
| --- | --- | --- |
| 随机森林 | 1,237,365 | 0.50 |
| 决策树 | 1,463,512 | 0.32 |



## 整合所有步骤

在本章前面的小节中，我们通过代码片段分别演示了建模过程的每一步。然而，在本节中，我们将把这些步骤中的所有相关代码整合到一个代码块中。这将使数据科学家能够看到所有代码片段如何协同工作，并将代码作为一个整体来执行。将每一步的代码合并到一个代码块中，也便于数据科学家修改代码，并尝试不同的参数或配置。

## Scikit-Learn

以下是准备数据、构建和训练模型以及评估其性能的代码：

```
# 导入必要的库
[In]: import pandas as pd
[In]: from sklearn.ensemble import RandomForestRegressor
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.metrics import r2_score, mean_squared_error
[In]: from sklearn.preprocessing import OneHotEncoder
[In]: import numpy as np
# 定义加载数据的函数
[In]: def load_housing_data():
url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/housing.csv')
return pd.read_csv(url)
# 通过调用函数创建一个 Pandas DataFrame：
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
# 训练随机森林模型
[In]: random_forest_model = RandomForestRegressor()
[In]: random_forest_model.fit(X_train, y_train)
# 打印森林中树的数量
[In]: print("随机森林中树的数量:", random_forest_model.n_estimators)
# 打印特征重要性
[In]: importances = random_forest_model.feature_importances_
[In]: feature_names = X.columns
[In]: indices = np.argsort(importances)[::-1]
[In]: print('特征重要性:')
[In]: for i in indices:
print(feature_names[i], ':', importances[i])
# 打印每棵树的深度
[In]: tree_depths = [estimator.tree_.max_depth for estimator in random_forest_model.estimators_]
[In]: print('每棵树的深度:', tree_depths)
# 对测试集进行预测
[In]: y_pred = random_forest_model.predict(X_test)
# 打印实际值和预测值
[In]: results_df = pd.DataFrame({'价格': y_test, '预测值': y_pred})
[In]: print(results_df.head())
# 计算评估指标
[In]: r2 = r2_score(y_test, y_pred)
[In]: rmse = np.sqrt(mean_squared_error(y_test, y_pred))
# 打印评估指标
[In]: print('R-squared 分数:', r2)
[In]: print('均方根误差:', rmse)
```

## PySpark

以下是准备数据、在 PySpark 中构建和训练随机森林模型以及评估其性能的代码：

```
# 导入必要的库
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.feature import StringIndexer, OneHotEncoder, VectorAssembler
[In]: from pyspark.ml.regression import RandomForestRegressor
[In]: from pyspark.ml.evaluation import RegressionEvaluator
[In]: import pandas as pd
[In]: import numpy as np
# 创建一个 Spark 会话
[In]: spark = SparkSession.builder.appName("RandomForestRegressorExample").getOrCreate()
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
# 训练随机森林模型
[In]: rf = RandomForestRegressor(featuresCol='features', labelCol='price')
[In]: spark_model = rf.fit(training_data)
# 从随机森林模型中访问各个决策树
[In]: individual_trees = spark_model.trees
# 初始化一个空列表来存储树的深度
[In]: tree_depths = []
# 遍历每棵树并获取其深度
[In]: for tree in individual_trees:
depth = tree.depth
tree_depths.append(depth)
# 打印树的深度
[In]: print("树的深度:", tree_depths)
# 直接从随机森林模型打印特征重要性
[In]: feature_importances = spark_model.featureImportances.toArray()
# 从 feature_cols 获取列名
[In]: feature_names = feature_cols
# 将特征名称和重要性组合成元组列表
[In]: feature_importance_tuples = list(zip(feature_names, feature_importances))
# 按重要性降序对元组列表进行排序
[In]: sorted_feature_importance = sorted(feature_importance_tuples, key=lambda x: x[1], reverse=True)
# 打印排序后的特征重要性及其名称
[In]: print("排序后的特征重要性:")
[In]: for feature_name, importance in sorted_feature_importance:
print(f"特征 '{feature_name}': {importance:.4f}")
# 对测试数据进行预测
[In]: predictions = spark_model.transform(test_data)
# 打印实际值和预测值
[In]: predictions.select('price', 'prediction').show(5)
# 使用 RMSE 和 R2 评估模型
[In]: evaluator_rmse = RegressionEvaluator(labelCol='price', predictionCol='prediction', metricName='rmse')
[In]: rmse = evaluator_rmse.evaluate(predictions)
[In]: evaluator_r2 = RegressionEvaluator(labelCol='price',
[In]: predictionCol = 'prediction', metricName='r2')
[In]: r2 = evaluator_r2.evaluate(predictions)
# 打印 RMSE 和 R2 值
[In]: print(f"RMSE: {rmse}")
[In]: print(f"R2: {r2}")
```



## 总结

本章介绍了随机森林作为决策树回归的替代方案。重点阐述了两类算法的优缺点。例如，决策树更简单但更容易过拟合，而随机森林则缓解了这一担忧。此外，与单个决策树相比，随机森林的准确性更高。在可解释性方面，决策树由于可以可视化，因此更易于理解。

本章还展示了 Scikit-Learn 和 PySpark 在数据准备和模型开发步骤上的相似性。在此过程中，对两个框架进行了类比。随后，通过将随机森林模型应用于相同的房价数据集，评估了随机森林优于决策树的假设。该假设被接受，因为随机森林显示出更高的 R² 值和更低的 RMSE 值。本章还演示了 Pandas 和 PySpark 在读取和探索数据方面采用了相似的方法。

在下一章中，我们将继续探讨基于树的机器学习，进而介绍梯度提升树（GBT）回归。该章将演示如何构建 GBT，并将其性能与决策树和随机森林回归的性能进行比较。

