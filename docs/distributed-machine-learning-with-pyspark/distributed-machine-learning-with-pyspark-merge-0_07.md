# 7. 使用 Pandas、Scikit-Learn 和 PySpark 进行逻辑回归

本章聚焦于分类问题，这是监督学习的一种独特形式。我们的目标是构建、训练并评估一个逻辑回归模型，然后利用它来预测患糖尿病的可能性。

尽管其名称暗示与线性回归有关联，但逻辑回归在目的和方法论上有着根本性的区别。与预测连续数值的线性回归（参见第 3 章）相反，逻辑回归预测的是某个实例属于特定类别的概率——在糖尿病的案例中，即预测一个人是否患有该疾病（类别 1）或未患该疾病（类别 0）。

逻辑回归既有优点也有缺点。其优点之一是简单且可解释性强。另一个优点是效率高，尤其是在处理大型数据集时。这一优势源于其线性特性和简洁的结构，使其能够快速处理数据。此外，逻辑回归以低方差著称，这降低了过拟合的可能性。当数据集有限时，这一特性尤其有益。通过在较小的数据集上保持稳定的性能，逻辑回归成为各种应用场景中的可靠选择。

另一方面，逻辑回归也确实存在一些缺点。其根本局限性在于它假设决策边界是线性的。当实际边界是非线性时，这可能导致性能欠佳，从而使得更复杂的算法（例如，基于树的算法、支持向量机、神经网络）更适合此类分类任务。

另一个局限性在于模型对异常值敏感，异常值可能会不成比例地影响估计系数，可能导致结果出现偏差。此外，逻辑回归假设特征之间相互独立，当这一假设被违反时——换句话说，当特征之间存在相关性时——其性能可能会受到影响。而且，虽然逻辑回归在二分类任务中表现出色，但在处理多分类问题时其通用性较差。尽管存在诸如多项逻辑回归之类的扩展方法，但其性能可能无法与专门为多分类场景设计的算法相媲美。

在本章中，我们将同时使用 Scikit-Learn 和 PySpark 来构建一个逻辑回归模型。我们将证明，尽管语法和函数名称存在差异，但这两个框架中的基本流程是相似的。此外，我们还将展示 Pandas 和 PySpark 在处理数据加载和操作方面共享相似的方法，这简化了诸如读取和探索数据之类的任务。

为了本项目的目的，我们将使用在第 2 章中用于选择算法的同一个糖尿病数据集。在构建我们的逻辑回归模型并在此数据上进行训练之前，让我们先回顾一下该数据集的主要属性。

## 数据集

本项目使用的数据集是我们在第 2 章中用过的皮马印第安人糖尿病数据库。该数据集包含 768 条记录，每条记录代表一位皮马印第安女性，并包含各种与健康相关的属性，以及一个指示该女性是否患有糖尿病的目标变量。

如第 2 章所述，该数据集的来源是 Kaggle——一个为数据科学家设计的网站。贡献者姓名、大致上传日期、数据集名称、网站名称以及我们下载 CSV 文件副本的网址如下：

标题：皮马印第安人糖尿病数据库

来源：Kaggle

网址：[`www.kaggle.com/uciml/pima-indians-diabetes-database`](http://www.kaggle.com/uciml/pima-indians-diabetes-database)

贡献者：UCI 机器学习库

日期：2016 年

与第 2 章一样，我们首先定义一个名为 `load_diabetes_data()` 的函数，用于从位于 GitHub 仓库中名为 `diabetes.csv` 的 CSV 文件加载数据。数据使用 `read_csv()` 函数加载，该函数将文件读入一个 Pandas DataFrame。

```
[In]: import pandas as pd
[In]: def load_diabetes_data():
url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/diabetes.csv')
return pd.read_csv(url)
```

然后，我们编写一个 Python 函数，用于打印关于 `pandas_df` 的基本信息：

```
[In]: def explore_pandas_df():
pandas_df = load_diabetes_csv()
columns = ['Pregnancies', 'Glucose', 'BloodPressure',
'BMI', 'DiabetesPedigreeFunction', 'Age']
selected_df = pandas_df[columns]
selected_df = selected_df.loc[(pandas_df['Glucose'] != 0)
& (pandas_df['BloodPressure'] != 0)
& (pandas_df['BMI'] != 0)]
print(f'前 5 行:\n{selected_df.head()}\n')
print(f'形状: {selected_df.shape}\n')
print("信息:")
selected_df.info()
print(f'汇总统计:\n'
f'{selected_df.describe().round(2)}\n')
```

`explore_pandas_df()` 函数通过创建一个列名列表，然后使用该列表仅选择相关列，从而从 `pandas_df` 中选择一个列子集。选定的列被赋值给一个名为 `selected_df` 的新 DataFrame。

接下来，该函数使用 `loc[]` 方法过滤 `selected_df`，仅选择那些 `Glucose`、`BloodPressure` 和 `BMI` 列不等于零的行。这将过滤掉这些列中存在缺失或无效数据的任何行。

进行此过滤的原因在第 2 章中已有解释，当时我们使用相同的数据集来说明 k 折交叉验证的概念。我们发现 `Glucose`、`BloodPressure`、`SkinThickness`、`Insulin` 和 `BMI` 存在无效读数，因为它们有值为 0 的情况。我们认为排除 `SkinThickness` 和 `Insulin` 是合理的，因为值为 0 的案例数量太多。我们还决定排除 `Glucose`、`BloodPressure` 和 `BMI` 值为 0 的行。无效案例的数量不算太多，因此排除这些行不会显著影响样本量。

然后，该函数使用 `head()` 方法打印 `selected_df` 的前五行，并使用 `shape` 属性显示其形状。接着，该函数使用 `info()` 方法显示关于 `selected_df` 的摘要信息。这提供了 DataFrame 结构的简明摘要，包括行数、列数、每列的非空值数量以及数据类型。最后一步，`explore_pandas_df()` 函数使用 `describe()` 方法显示 `selected_df` 的汇总统计信息，该方法提供描述性统计，例如每列的均值、标准差和四分位数。输出使用 f-string 格式化为字符串并打印。

现在，我们可以调用 `explore_pandas_df()` 函数来查看输出：

|   | 怀孕次数 | 血糖 | 血压 | 身体质量指数 | 糖尿病遗传函数 | 年龄 |
| --- | --- | --- | --- | --- | --- | --- |
| 0 | 6 | 148 | 72 | 33.6 | 0.627 | 50 |
| 1 | 1 | 85 | 66 | 26.6 | 0.351 | 31 |
| 2 | 8 | 183 | 64 | 23.3 | 0.672 | 32 |
| 3 | 1 | 89 | 66 | 28.1 | 0.167 | 21 |
| 4 | 0 | 137 | 40 | 43.1 | 2.288 | 33 |

```
[输入]: print(explore_pandas_df())
[输出]:
前五行：
```

|   | 怀孕次数 | 血糖 | 血压 | 身体质量指数 | 糖尿病遗传函数 | 年龄 |
| --- | --- | --- | --- | --- | --- | --- |
| 计数 | 724 | 724 | 724 | 724 | 724 | 724 |
| 均值 | 3.87 | 121.88 | 72.4 | 32.47 | 0.47 | 33.35 |
| 标准差 | 3.36 | 30.75 | 12.38 | 6.89 | 0.33 | 11.77 |
| 最小值 | 0 | 44 | 24 | 18.2 | 0.08 | 21 |
| 25%分位数 | 1 | 99.75 | 64 | 27.5 | 0.24 | 24 |
| 50%分位数 | 3 | 117 | 72 | 32.4 | 0.38 | 29 |
| 75%分位数 | 6 | 142 | 80 | 36.6 | 0.63 | 41 |
| 最大值 | 17 | 199 | 122 | 67.1 | 2.42 | 81 |

```
形状: (724, 6)
信息:

Int64Index: 724 个条目，从 0 到 767
数据列 (共 6 列):

#   列名                      非空计数    数据类型
---  ------                    --------------  -----
0   怀孕次数 (Pregnancies)               724 非空    int64
1   血糖 (Glucose)                   724 非空    int64
2   血压 (BloodPressure)             724 非空    int64
3   身体质量指数 (BMI)                       724 非空    float64
4   糖尿病遗传函数 (DiabetesPedigreeFunction)  724 非空    float64
5   年龄 (Age)                       724 非空    int64
数据类型: float64(2), int64(4)
汇总统计:
```

我们得到一组四个输出。输出的第一部分显示了 DataFrame 的前五行。每一行代表怀孕次数、血糖、血压、身体质量指数、糖尿病遗传函数和年龄这些列的一组值。形状部分指示了 DataFrame 的形状，显示了行数和列数。在本例中，DataFrame 有 724 行和 6 列。信息部分提供了关于 DataFrame 的信息，包括每列的数据类型和非空值的计数。最后，汇总统计部分展示了 DataFrame 中每列的汇总统计信息。它提供了每个数值列的关键统计指标，如计数、均值、标准差、最小值、25%分位数、中位数（50%分位数）、75%分位数和最大值。

我们可以使用 PySpark 代码实现相同的结果：

```
[输入]: from pyspark.sql import SparkSession
[输入]: from pyspark.sql.functions import col
[输入]: def explore_spark_df():
pandas_df = load_diabetes_csv()
spark = SparkSession.builder.getOrCreate()
spark_df = spark.createDataFrame(pandas_df)
columns = ['Pregnancies', 'Glucose', 'BloodPressure',
'BMI', 'DiabetesPedigreeFunction', 'Age']
spark_df = spark_df.select(columns)
spark_df = spark_df.filter((col('Glucose') != 0)
& (col('BloodPressure') != 0)
& (col('BMI') != 0))
spark_df.show(5)
print(
f'Shape: ({spark_df.count()}, {len(spark_df.columns)})\n')
spark_df.printSchema()
spark_df.summary().show()
```

该代码首先导入必要的类（`SparkSession` 和 `col`），并定义了 `explore_spark_df()` 函数，该函数使用 `load_diabetes_csv()` 函数将糖尿病 CSV 文件加载到 `pandas_df` DataFrame 中。然后，它使用 `SparkSession.builder.getOrCreate()` 方法创建一个名为 `spark` 的 SparkSession 对象，并使用 `createDataFrame()` 方法将 `pandas_df` 转换为 `spark_df`。

接下来，通过选择特定列并排除血糖、血压和身体质量指数值等于零（这些被视为无效读数）的行来过滤 `spark_df`。然后，该函数使用 `show(5)` 方法显示过滤后的 Spark DataFrame 的前五行。它还使用 `count()` 和 `len()` 方法显示 `spark_df` 的形状，并使用 `printSchema()` 方法显示其模式。最后，该函数使用 `summary()` 方法显示一个包含所选列统计信息的摘要。

我们可以通过调用 `explore_spark_df()` 函数来查看输出：

| 怀孕次数 | 血糖 | 血压 | 身体质量指数 | 糖尿病遗传函数 | 年龄 |
| --- | --- | --- | --- | --- | --- |
| 6 | 148 | 72 | 33.6 | 0.627 | 50 |
| 1 | 85 | 66 | 26.6 | 0.351 | 31 |
| 8 | 183 | 64 | 23.3 | 0.672 | 32 |
| 1 | 89 | 66 | 28.1 | 0.167 | 21 |
| 0 | 137 | 40 | 43.1 | 2.288 | 33 |

```
[输入]: print(explore_spark_df())
[输出]:
```

| 汇总 | 怀孕次数 | 血糖 | 血压 | 身体质量指数 | 糖尿病遗传函数 | 年龄 |
| --- | --- | --- | --- | --- | --- | --- |
| **计数** | 724 | 724 | 724 | 724 | 724 | 724 |
| **均值** | 3.87 | 121.88 | 72.40 | 32.47 | 0.47 | 33.35 |
| **标准差** | 3.36 | 30.75 | 12.38 | 6.89 | 0.33 | 11.77 |
| **最小值** | 0 | 44 | 24 | 18.2 | 0.078 | 21 |
| **25%分位数** | 1 | 99 | 64 | 27.5 | 0.245 | 24 |
| **50%分位数** | 3 | 117 | 72 | 32.4 | 0.378 | 29 |
| **75%分位数** | 6 | 142 | 80 | 36.6 | 0.627 | 41 |
| **最大值** | 17 | 199 | 122 | 67.1 | 2.42 | 81 |

```
形状: (724, 6)
根节点
|-- 怀孕次数: 长整型 (可空 = true)
|-- 血糖: 长整型 (可空 = true)
|-- 血压: 长整型 (可空 = true)
|-- 身体质量指数: 双精度浮点型 (可空 = true)
|-- 糖尿病遗传函数: 双精度浮点型 (可空 = true)
|-- 年龄: 长整型 (可空 = true)
```

Pandas 和 PySpark 代码的输出是可比的，糖尿病数据集有 724 行和 6 个特征。这些特征涵盖了全面的健康相关指标：怀孕次数指怀孕发生的次数；血糖捕获了口服葡萄糖耐量试验中测得的两个小时血浆葡萄糖浓度；血压表示舒张压，以毫米汞柱（mm Hg）为单位；身体质量指数代表身体质量指数，是身体成分的重要指标；糖尿病遗传函数根据家族病史量化了患糖尿病的可能性；最后，年龄表示个体的年龄，以年为单位。

输出中未显示的目标变量表示皮马女性是否患有糖尿病（赋值为 1）或未患有糖尿病（赋值为 0）。该变量是我们预测的重点。

## 逻辑回归

在本节中，我们将构建、训练并评估一个逻辑回归模型，并利用它根据一组预测变量来预测皮马印第安女性患糖尿病的可能性。我们使用六个特征：怀孕次数、血压、BMI、血糖水平、糖尿病遗传函数和年龄。这些特征是预测糖尿病患病情况的重要因素。

逻辑回归模型会估算每个预测变量的系数，并利用这些系数来计算该女性患糖尿病的概率。模型输出的概率得分介于 0 到 1 之间，得分越接近 1，表示患糖尿病的可能性越高。例如，如果一位女性有过四次怀孕经历、血压高、BMI 高、血糖水平高、有糖尿病家族史且年龄为 50 岁，逻辑回归模型可能会输出 0.8 的概率得分，表明该女性患糖尿病的可能性很高。另一方面，一位仅有一次怀孕经历、血压正常、BMI 低、血糖水平低、无糖尿病家族史且年龄为 30 岁的女性，其概率得分可能为 0.1，表明患糖尿病的可能性很低。

我们将同时使用 Scikit-Learn 和 PySpark。虽然 Scikit-Learn 是构建逻辑回归模型的常用库，但 PySpark 在处理大数据方面具有多项优势。通过从 Scikit-Learn 迁移到 PySpark，数据科学家可以利用 PySpark 的分布式计算能力，实现对大型数据集的并行处理，并与其他大数据工具无缝集成。尽管 Scikit-Learn 和 PySpark 使用的语法和函数可能不同，但逻辑回归的基本原理是相同的。因此，熟悉 Scikit-Learn 的数据科学家可以轻松过渡到 PySpark，并为大数据问题构建逻辑回归模型。

在构建逻辑回归模型之前，让我们先审视一下 Scikit-Learn 和 PySpark 在建模步骤以及所使用的函数和类方面的相似之处。

**Scikit-Learn 和 PySpark 在逻辑回归上的相似性**

Scikit-Learn 和 PySpark 在实现逻辑回归时都遵循相似的工作流程，包括数据准备、模型训练、模型评估和预测。以下是两个框架中针对逻辑回归的关键机器学习功能概述。

1.  数据准备：

Scikit-Learn 利用 `train_test_split` 等函数将数据集拆分为训练集和测试集，使用 `StandardScaler` 进行特征缩放，使用 `OneHotEncoder` 对分类变量进行独热编码，并使用 `SimpleImputer` 处理缺失数据。

在 PySpark 中，我们可以使用 DataFrame 进行操作，例如使用 `randomSplit` 将数据拆分为训练集和测试集，使用 `StandardScaler` 进行特征缩放，使用 `OneHotEncoder` 处理分类变量，并使用 `Imputer` 处理缺失数据。此外，`VectorAssembler` 将特征列合并为一个向量列。

2.  模型训练：

Scikit-Learn 和 PySpark 中的逻辑回归模型分别使用 `LogisticRegression` 类和 `LogisticRegression` 类进行实例化。模型使用 `fit()` 方法进行训练。在 Scikit-Learn 中，需要提供特征（X_train）和二元目标变量（y_train）。

在 PySpark 中，`fit()` 方法接受一个包含特征和二元目标变量列的 DataFrame（train_data）。在 PySpark 中，需要先使用 `VectorAssembler` 来合并特征列。

3.  模型评估：

Scikit-Learn 中的评估指标，如准确率（使用 `accuracy_score` 计算）、ROC 曲线下面积（AUC，使用 `roc_auc_score` 计算）、精确率（使用 `precision_score` 计算）、召回率（使用 `recall_score` 计算）、F1 分数（使用 `f1_score` 计算）以及混淆矩阵（使用 `confusion_matrix` 计算），是通过直接比较预测值和实际的二元目标变量值（y_pred, y_test）来计算的。

在 PySpark 中，`BinaryClassificationEvaluator` 用于计算 AUC。这需要指定预测值和实际值（predictions, y_test）及其对应的列。虽然 PySpark 没有内置函数来计算二元分类的其他指标，但手动计算也很直接。

4.  预测：

Scikit-Learn 模型使用 `predict()` 方法，利用特征（X_test）对新数据生成二元预测，得到预测的目标变量值（y_pred）。

类似地，PySpark 模型使用 `transform()` 方法生成预测。该方法使用包含特征的 DataFrame（test_data），并将预测结果存储在新列（prediction）中。与训练时一样，`VectorAssembler` 确保新数据的特征向量格式统一。

这些比较表明，尽管 Scikit-Learn 和 PySpark 在语法、函数和类上偶尔存在差异，但基本的建模步骤在很大程度上是一致的。在接下来的小节中，我们将把这些步骤转化为代码。从一开始就需要注意，我们使用的是默认超参数构建模型。这意味着，由于两个框架的默认参数不同，模型的概率和评估指标可能会有所差异。在第 16 章中，我们将演示如何微调和对齐这些参数。

## 使用 Scikit-Learn 进行逻辑回归

让我们从 Scikit-Learn 开始。为了使用皮马印第安人糖尿病数据集预测患糖尿病的可能性，我们遵循上一小节概述的相同步骤：数据准备、模型训练、模型评估和预测。代码将被封装在函数中，以提高执行和理解的便捷性。

通常，建模过程从导入必要的库开始，这些库提供了用于数据操作、预处理、模型训练和评估的函数与类。

**步骤 1：导入必要的包**

```
[In]: import pandas as pd
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.linear_model import LogisticRegression
[In]: from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, confusion_matrix
[In]: from sklearn.preprocessing import StandardScaler
```

**步骤 2：加载糖尿病数据集**

此步骤定义了一个函数 `load_diabetes_csv(url)`，用于从提供的 URL 加载糖尿病数据集。数据集被加载到一个 Pandas DataFrame 中。

```
[In]: def load_diabetes_csv(url):
"""
从 URL 加载 diabetes.csv 文件并返回一个 Pandas DataFrame。
参数：
url (str)：CSV 文件的 URL。
返回：
pandas.DataFrame：加载的 DataFrame。
"""
pandas_df = pd.read_csv(url)
return pandas_df
```

**步骤 3：数据准备**

**3.1. 将数据拆分为训练集和测试集**

在数据准备步骤的这个子步骤中，定义了函数 `split_data(X, y, test_size, random_state)`，用于将特征矩阵 X 和目标变量 y 拆分为训练集和测试集。

```
[In]: def split_data(X, y, test_size=0.2, random_state=42):
"""
将数据拆分为训练集和测试集。
参数：
X (pd.DataFrame)：特征矩阵。
y (pd.Series)：目标变量。
test_size (float)：用于测试集的数据集比例。
random_state (int)：随机数生成器的种子。
返回：
tuple：X_train, X_test, y_train, y_test。
"""
return train_test_split(X, y, test_size=test_size,
random_state=random_state)
```

**3.2. 缩放数据**

在这里，函数 `scale_data(X_train, X_test)` 使用 `StandardScaler` 对训练集和测试集的特征进行缩放，以确保所有特征具有相似的尺度。

```
[In]: def scale_data(X_train, X_test):
"""
使用 StandardScaler 缩放特征。
参数：
X_train (pd.DataFrame)：缩放后的训练特征。
X_test (pd.DataFrame)：缩放后的测试特征。
返回：
tuple：X_train_scaled, X_test_scaled。
"""
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)
return X_train_scaled, X_test_scaled
```

**步骤 4：模型训练**

在此步骤中，函数 `train_lr(X_train, y_train)` 使用 Scikit-Learn 中的 `LogisticRegression` 类训练逻辑回归模型。

```
[In]: def train_lr(X_train, y_train):
"""
在缩放后的训练数据上训练逻辑回归模型。
参数：
X_train (pd.DataFrame)：缩放后的训练特征。
y_train (pd.Series)：训练目标变量。
返回：
sklearn.linear_model.LogisticRegression：训练好的逻辑回归模型。
"""
model = LogisticRegression()
model.fit(X_train, y_train)
return model
```

**步骤 5：模型评估**

在此评估步骤中，函数 `evaluate_model(model, X_test, y_test)` 使用各种指标（如准确率、精确率、召回率、F1 分数和混淆矩阵）评估训练好的模型。这些指标会被打印出来，同时显示一个包含实际值和预测值的 DataFrame。

```
[In]: def evaluate_model(model, X_test, y_test):
"""
使用各种指标评估模型并打印结果。
参数：
model (sklearn.linear_model.LogisticRegression)：训练好的逻辑回归模型。
X_test (pd.DataFrame)：缩放后的测试特征。
y_test (pd.Series)：测试目标变量。
"""
y_pred = model.predict(X_test)
accuracy = accuracy_score(y_test, y_pred)
precision = precision_score(y_test, y_pred)
recall = recall_score(y_test, y_pred)
f1 = f1_score(y_test, y_pred)
confusion = confusion_matrix(y_test, y_pred)
print(f'准确率: {accuracy:.2f}')
print(f'精确率: {precision:.2f}')
print(f'召回率: {recall:.2f}')
print(f'F1 分数: {f1:.2f}')
print(f'混淆矩阵:\n{confusion}')
result_df = pd.DataFrame({'实际值': y_test, '预测值':
y_pred})
print(result_df.head())
```

**步骤 6：使用函数**

在最后一步中，我们调用之前定义的函数。这里包含九个子步骤，如下所示：

**6.1. 定义数据集的 URL**

```
[In]: url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/diabetes.csv')
```

**6.2. 调用 load_diabetes_csv() 函数读取数据集**

```
[In]: pandas_df = load_diabetes_csv(url)
```

**6.3. 指定要使用的数据列**

```
[In]: columns = ['Pregnancies', 'Glucose', 'BloodPressure',
'BMI', 'DiabetesPedigreeFunction', 'Age', 'Outcome']
```

**6.4. 仅选择指定的列，并过滤掉关键特征中值为零的行**

```
[In]: pandas_df = pandas_df[columns]
[In]: pandas_df = pandas_df.loc[(pandas_df['Glucose'] != 0)
& (pandas_df['BloodPressure'] != 0)
& (pandas_df['BMI'] != 0)]
```

**6.5. 分离特征 (X) 和目标变量 (y)**

```
[In]: X = pandas_df.drop('Outcome', axis=1)
[In]: y = pandas_df['Outcome']
```

**6.6. 将数据拆分为训练集和测试集**

```
[In]: X_train, X_test, y_train, y_test = split_data(X, y)
```

**6.7. 缩放训练集和测试集中的特征**

```
[In]: X_train_scaled, X_test_scaled = scale_data(X_train, X_test)
```

**6.8. 在缩放后的训练数据上训练逻辑回归模型**

```
[In]: model = train_lr(X_train_scaled, y_train)
```

**6.9. 在缩放后的测试数据上评估模型**

```
[In]: evaluate_model(model, X_test_scaled, y_test)
[Out]:
准确率: 0.79
精确率: 0.66
召回率: 0.63
F1 分数: 0.64
混淆矩阵:
[[88 14]
[16 27]]
实际值  预测值
0          0
0          0
1          1
0          1
1          1
```

执行完这九个子步骤后，我们得到如上所示的输出。在解释这个输出之前，让我们先逐步过一遍 Scikit-Learn 代码。

**步骤 1：导入必要的库**

在第一步中，导入所需的库，以便使用它们的函数和类进行数据操作、模型训练、评估和预处理：

*   `pandas` 用于数据操作
*   `train_test_split` 用于将数据拆分为训练集和测试集
*   `LogisticRegression` 用于模型训练
*   `accuracy_score`、`precision_score`、`recall_score`、`f1_score` 和 `confusion_matrix` 用于模型评估
*   `StandardScaler` 用于特征缩放

**步骤 2：加载糖尿病数据集**

在此步骤中，定义了 `load_diabetes_csv(url)` 函数。该函数接受一个参数 url，它代表 CSV 格式数据集的 URL。在函数内部，代码行 `pandas_df = pd.read_csv(url)` 使用 Pandas 的 `read_csv()` 函数从指定的 URL 获取数据，并将其转换为 Pandas DataFrame。该函数返回生成的 DataFrame。

**步骤 3：数据准备**

此步骤包含两个子步骤。第一个子步骤定义了一个名为 `split_data()` 的函数，用于将数据集拆分为训练集和测试集。该函数接受几个参数：

*   X：包含用于预测的自变量的矩阵。
*   y：与每个数据点相关的因变量。
*   test_size：指定分配给测试集的数据集比例。默认值为 0.2，对应 20% 的分配比例。

*   `random_state`：设置随机数生成器的种子，确保数据划分的可重复性。默认值为 42。

在 `split_data()` 函数内部，使用 `train_test_split()` 函数执行划分操作。该函数接收特征矩阵 X 和目标变量 y，以及指定的 `test_size` 和 `random_state` 值。它返回四个独立的数据集：

*   `X_train`：用于训练的特征矩阵
*   `X_test`：用于测试的特征矩阵
*   `y_train`：与训练集对应的目标变量
*   `y_test`：与测试集对应的目标变量

最后，该函数以元组形式返回这四个数据集。

第二个子步骤定义了 `scale_data()` 函数。该函数对数据集的特征进行标准化，确保它们具有相似的尺度。这在机器学习中尤为重要，因为像逻辑回归这样的模型可能对特征的不同量级很敏感。该函数接收两个参数：

*   `X_train`：表示训练数据的特征矩阵。这是一个包含自变量的 Pandas DataFrame。
*   `X_test`：表示测试数据的特征矩阵。它包含测试集的自变量。

在函数内部，创建了一个 `StandardScaler` 对象。该对象是 Scikit-Learn 库中的一个工具，用于简化特征的缩放过程。它用于确保每个特征的均值为 0，标准差为 1。

下一步是使用代码行 `X_train_scaled=scaler.fit_transform(X_train)` 将缩放变换应用于训练数据。这里，使用了缩放器对象的 `fit_transform()` 方法，从训练数据中计算缩放参数，并同时相应地变换训练特征。缩放后的训练特征存储在变量 `X_train_scaled` 中。

接着，使用代码行 `X_test_scaled=scaler.transform(X_test)` 将相同的缩放变换应用于测试数据。然而，在这种情况下，使用的是 `transform()` 方法，因为缩放参数已经使用训练数据计算过了。缩放后的测试特征存储在变量 `X_test_scaled` 中。

最后，该函数返回缩放后的训练和测试特征矩阵。

**步骤 4：模型训练**

在此步骤中，定义了一个名为 `train_lr` 的函数。该函数接收两个输入参数：`X_train` 和 `y_train`。其目的是使用提供的训练数据训练一个逻辑回归模型，然后返回训练好的模型。

在函数内部，创建了一个 `LogisticRegression` 对象并赋值给变量 `model`。`LogisticRegression` 是 Scikit-Learn 提供的一个类，它实现了用于分类任务的逻辑回归算法。

进入下一步，使用训练数据 `X_train` 和对应的标签 `y_train` 调用 `LogisticRegression` 模型的 `fit` 方法。该方法通过调整模型参数以拟合给定的训练样本，从而使用提供的数据训练逻辑回归模型。模型训练完成后，将其作为输出从函数中返回。

**步骤 5：模型评估**

在此步骤中，代码定义了一个名为 `evaluate_model` 的函数，该函数接收三个输入参数：训练好的模型、`X_test` 和 `y_test`。此函数用于使用各种指标评估逻辑回归模型在给定测试数据上的性能，并打印结果。

在函数内部，使用测试特征 `X_test` 调用分类模型的 `predict` 方法，对测试数据进行预测。预测的标签被赋值给变量 `y_pred`。

接下来，计算模型的以下关键评估指标：

*   **准确率**指标衡量的是数据集中所有实例中正确预测的实例（包括真正例和真负例）所占的比例。换句话说，准确率告诉我们模型的预测与实际标签匹配的频率。该指标通过比较预测标签 `y_pred` 与真实标签 `y_test` 来计算。
*   **精确率**衡量的是模型在所有被预测为正类的实例中，正确预测为正类实例的能力。
*   **召回率**，也称为灵敏度或真正例率，衡量的是模型在所有实际正类实例中，正确预测为正类实例的能力。
*   **F1 分数**是精确率和召回率的加权平均值，提供了两者之间的平衡度量。
*   **混淆矩阵**是一个表格，显示了真正例、真负例、假正例和假负例预测的数量。

然后使用打印语句显示计算出的指标和混淆矩阵。之后，创建一个 Pandas DataFrame 来存储实际的测试标签 (`y_test`) 和预测标签 (`y_pred`)，而最后一行代码打印该 DataFrame 的前五行，以显示实际标签和预测标签之间的对比。

**步骤 6：使用函数**

在最后一步中，调用之前定义的用于准备数据、训练和评估模型以及进行预测的函数。使用这些函数的顺序如下：

*   定义数据集的 URL。
*   调用 `load_diabetes_csv()` 函数读取数据集。
*   指定要从数据集中使用的列。
*   仅选择指定的列，并过滤掉关键特征中包含零值的行。
*   分离特征 (X) 和目标变量 (y)。
*   通过调用 `split_data(X, y)` 函数将数据划分为训练集和测试集。
*   通过使用 `scale_data(X_train, X_test)` 函数对训练集和测试集中的特征进行缩放。
*   使用 `train_lr(X_train_scaled, y_train)` 函数在缩放后的训练数据上训练模型。
*   使用 `evaluate_model(model, X_test_scaled, y_test)` 函数在缩放后的测试数据上评估模型。

按这些步骤运行代码后，我们得到使用各种指标（包括准确率、精确率、召回率、F1 分数和混淆矩阵）评估分类模型的输出。以下是对输出的解释：

*   **准确率 (0.79)**：这意味着模型的预测对于数据集中大约 79% 的实例是正确的。它是正确预测的实例数与总实例数的比率。
*   **精确率 (0.66)**：精确率是正确预测的正类实例（真正例）与所有被预测为正类的实例（真正例 + 假正例）的比率。在此示例中，模型的精确率约为 66%，这意味着当它预测为正类结果时，大约有 66% 的时间是正确的。
*   **召回率 (0.63)**：召回率，也称为灵敏度或真正例率，是正确预测的正类实例（真正例）与所有实际正类实例（真正例 + 假负例）的比率。在此示例中，模型的召回率约为 63%，表明它正确识别了大约 63% 的实际正类实例。
*   **F1 分数 (0.64)**：F1 分数是精确率和召回率的加权平均值。它在精确率和召回率之间提供了平衡。在此示例中，F1 分数约为 64%。
*   **混淆矩阵**：这是一个表格，显示了真正例（左上角）、真负例（右下角）、假正例（右上角）和假负例（左下角）的数量。在此示例中，有 88 个真负例（正确预测为负类的实例）、27 个真正例（正确预测为正类的实例）、14 个假正例（错误预测为正类的实例）和 16 个假负例（错误预测为负类的实例）。

*   **实际值与预测值**：表格展示了前五个实例的实际标签与预测标签的对比。在五个实例中，有四个（即 80%）模型的预测结果与实际标签（0 和 1）相符。这一准确率略高于考虑了所有样本（而不仅仅是前五行）的总体准确率 79%。

## 使用 PySpark 进行逻辑回归

在本小节中，我们将编写 PySpark 代码来准备数据、训练和评估逻辑回归模型，并进行预测。与上一小节使用 Scikit-Learn 类似，我们将代码封装在函数中，以便于顺利执行和理解。

需要注意的是，与 Scikit-Learn 不同，PySpark 没有内置函数来计算二分类的评估指标，例如准确率、精确率、召回率、F1 分数和混淆矩阵。不过，我们可以轻松地手动计算这些指标，这将在评估步骤中演示。此外，与 Scikit-Learn 相比，使用 PySpark 时有两个额外的初始步骤：创建 Spark Session（用于建立与 Spark 集群的连接），以及使用 PySpark 的 `VectorAssembler` 类将特征组装到单个列中。

与 Scikit-Learn 一样，我们首先导入必要的包。

**步骤 1：导入必要的库**

在此步骤中，我们导入必要的库，包括用于 SparkSession、LogisticRegression、VectorAssembler、StandardScaler、col 和 Pandas 的库。

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.classification import LogisticRegression
[In]: from pyspark.ml.feature import VectorAssembler, StandardScaler
[In]: from pyspark.sql.functions import col
[In]: import pandas as pd
```

**步骤 2：创建 Spark Session**

此步骤使用 `SparkSession.builder` 创建一个名为“DiabetesPrediction”的 Spark Session。

```
[In]: spark = SparkSession.builder.appName("DiabetesPrediction").getOrCreate()
```

**步骤 3：从 CSV 文件读取数据**

此步骤定义一个函数，用于从 URL 读取数据，并使用 `pd.read_csv` 和 `spark.createDataFrame` 将其转换为 Spark DataFrame。

```
[In]: def read_data(url):
"""
从给定的 URL 读取数据并将其转换为 Spark
DataFrame。
参数：
url (str)：CSV 文件的 URL。
返回：
pyspark.sql.DataFrame：包含数据的 Spark DataFrame。
"""
pandas_df = pd.read_csv(url)
spark_df = spark.createDataFrame(pandas_df)
return spark_df
```

**步骤 4：数据准备**

此步骤包含三个子步骤：

4.1\. 创建一个使用 `randomSplit` 将数据拆分为训练集和测试集的函数

```
[In]: def split_data(spark_df):
"""
将数据拆分为训练集和测试集。
参数：
spark_df (pyspark.sql.DataFrame)：输入的 Spark DataFrame。
返回：
tuple：包含训练集和测试集 Spark DataFrame 的元组。
"""
return spark_df.randomSplit([0.8, 0.2], seed=42)
```

4.2\. 定义一个使用 `VectorAssembler` 将特征列转换为单个向量列的函数

```
[In]: def vectorize_features(train_data, test_data, feature_cols):
"""
将特征列转换为单个向量列。
参数：
train_data (pyspark.sql.DataFrame)：训练集 Spark DataFrame。
test_data (pyspark.sql.DataFrame)：测试集 Spark DataFrame。
feature_cols (list)：特征列名称列表。
返回：
tuple：包含向量化后的训练集和测试集 Spark DataFrame 的元组。
"""
assembler = VectorAssembler(inputCols=feature_cols,
outputCol='features')
return assembler.transform(train_data),
assembler.transform(test_data)
```

4.3\. 创建一个使用 `StandardScaler` 缩放特征的函数

```
[In]: def scale_features(train_data, test_data):
"""
缩放数据集中的特征。
参数：
train_data (pyspark.sql.DataFrame)：训练集 Spark DataFrame。
test_data (pyspark.sql.DataFrame)：测试集 Spark DataFrame。
返回：
tuple：包含缩放后的训练集和测试集 Spark DataFrame 的元组。
"""
scaler = StandardScaler(inputCol='features',
outputCol='scaled_features')
scaler_model = scaler.fit(train_data)
return scaler_model.transform(train_data),
scaler_model.transform(test_data)
```

**步骤 5：模型训练**

在此步骤中，我们定义一个函数，该函数使用 `LogisticRegression` 在训练数据集上训练逻辑回归模型。

```
[In]: def train_model(train_data):
"""
在训练数据集上训练逻辑回归模型。
设置随机种子以确保结果可重复。
参数:
train_data (pyspark.sql.DataFrame): 训练用的 Spark DataFrame。
返回:
pyspark.ml.classification.LogisticRegressionModel: 训练好的逻辑回归模型。
"""
spark.conf.set("spark.seed", "42")
lr = LogisticRegression(labelCol='Outcome',
featuresCol='scaled_features')
return lr.fit(train_data)
```

**步骤 6：模型评估**

此步骤创建一个函数，用于在测试数据集上评估训练好的模型。该函数计算准确率、精确率、召回率和 F1 分数等评估指标，并生成混淆矩阵。

```
[In]: def evaluate_model(lr_model, test_data):
"""
在测试数据集上评估训练好的模型。
参数:
lr_model
(pyspark.ml.classification.LogisticRegressionModel): 训练好的逻辑回归模型。
test_data (pyspark.sql.DataFrame): 测试用的 Spark DataFrame。
"""
predictions = lr_model.transform(test_data)
predictions = predictions.withColumn('prediction',
col('prediction').cast('int'))
predictions_and_labels = predictions.select(['Outcome',
'prediction'])
tp = predictions_and_labels[(predictions_and_labels.Outcome
== 1) & (predictions_and_labels.prediction == 1)].count()
tn = predictions_and_labels[(predictions_and_labels.Outcome
== 0) & (predictions_and_labels.prediction == 0)].count()
fp = predictions_and_labels[(predictions_and_labels.Outcome
== 0) & (predictions_and_labels.prediction == 1)].count()
fn = predictions_and_labels[(predictions_and_labels.Outcome
== 1) & (predictions_and_labels.prediction == 0)].count()
acc = (tp + tn) / predictions_and_labels.count()
prec = tp / (tp + fp)
rec = tp / (tp + fn)
f1 = 2 * prec * rec / (prec + rec)
confusion = [[tp, fp], [fn, tn]]
print(f'准确率: {acc:.2f}')
print(f'精确率: {prec:.2f}')
print(f'召回率: {rec:.2f}')
print(f'F1 分数: {f1:.2f}')
print(f'混淆矩阵:\n{confusion}')
predictions.select('Outcome', 'prediction').show(5)
```

**步骤 7：使用函数**

此步骤调用前面步骤中定义的函数。同时提供 URL 地址。

**7.1. 提供 URL 地址**

```
[In]: url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/diabetes.csv')
```

**7.2. 调用 read_data() 函数读取数据集并创建 Spark DataFrame**

```
[In]: spark_df = read_data(url)
columns = ['Pregnancies', 'Glucose', 'BloodPressure',
'BMI', 'DiabetesPedigreeFunction', 'Age', 'Outcome']
[In]: spark_df = spark_df.select(columns)
[In]: spark_df = spark_df.filter((col('Glucose') != 0)
& (col('BloodPressure') != 0)
& (col('BMI') != 0))
```

**7.3. 调用 split_data() 函数将数据拆分为训练集和测试集**

```
[In]: train_data, test_data = split_data(spark_df)
```

**7.4. 定义特征列列表**

```
[In]: feature_cols = ['Pregnancies', 'Glucose', 'BloodPressure', 'BMI', 'DiabetesPedigreeFunction', 'Age']
```

**7.5. 调用 vectorize_features() 函数将特征转换为单个向量**

```
[In]: train_data, test_data = vectorize_features(train_data, test_data, feature_cols)
```

**7.6. 调用 scale_features() 函数对特征进行缩放**

```
[In]: train_data, test_data = scale_features(train_data, test_data)
```

**7.7. 调用 train_model() 函数训练逻辑回归模型**

```
[In]: lr_model = train_model(train_data)
```

**7.8. 调用 evaluate_model() 函数在测试集上评估模型**

```
[In]: evaluate_model(lr_model, test_data)
[Out]:
准确率: 0.78
精确率: 0.62
召回率: 0.56
F1 分数: 0.59
混淆矩阵:
[[23, 14],
[18, 91]]
+-------+----------+
|Outcome|prediction|
+-------+----------+
|      0|         0|
|      0|         1|
|      0|         0|
|      0|         0|
|      0|         0|
+-------+----------+
仅显示前 5 行
```

在解释输出之前，我们先花点时间回顾一下用于生成输出的代码。在导入使用 PySpark 不同方面和执行机器学习任务所需的必要库之后，我们创建了一个 Spark Session，以便与 PySpark API 进行交互。然后，我们定义了一个函数（`read_data`），该函数从指向 CSV 文件的给定 URL 读取数据，并将其转换为 Spark DataFrame。该函数将 URL 作为输入，并返回一个 Spark DataFrame。在函数内部，利用 Pandas 库从 URL 读取数据到名为 `pandas_df` 的 Pandas DataFrame 中。使用 `spark.createDataFrame(pandas_df)` 方法将 Pandas DataFrame 转换为名为 `spark_df` 的 Spark DataFrame。然后，该函数返回生成的 Spark DataFrame。

接下来是准备数据、训练和评估模型以及进行预测的步骤：

**数据准备** **（步骤 4）**

此步骤包含三个子步骤。在第一个子步骤中，定义了一个名为 `split_data` 的函数，该函数接受一个名为 `spark_df` 的 Spark DataFrame 作为参数。此函数的目的是将输入的 DataFrame 划分为两个子集：一个用于训练，另一个用于测试。

该函数在 `spark_df` DataFrame 上使用 PySpark 的 `randomSplit` 方法。它根据用户指定的权重将 DataFrame 分成两部分，权重设置为 [0.8, 0.2]。这意味着大约 80% 的数据用于训练，20% 用于测试。为了确保多次运行之间结果的一致性，使用了 `seed=42` 参数。执行该函数时，它会生成一个包含两个独立 DataFrame 的元组：一个用于训练，另一个用于测试。

在第二个子步骤中，代码定义了一个名为 `vectorize_features` 的函数，该函数接受三个参数：

*   `train_data`：包含训练数据的 Spark DataFrame
*   `test_data`：包含测试数据的 Spark DataFrame
*   `feature_cols`：表示要合并为单个特征向量的特征列名称列表

此函数的目的是将训练数据和测试数据中指定的特征列转换为单个向量列。这样做是为了准备数据，以便机器学习算法使用，该算法要求输入特征采用向量格式。

在函数内部，代码首先从 PySpark 的 `pyspark.ml.feature` 模块创建一个 `VectorAssembler` 类的实例。该类用于将特征列组装成单个向量列。它接受两个关键参数：

*   `inputCols`：设置为 `feature_cols` 参数中提供的列名称列表。它指定了应合并的特征列。
*   `outputCol`：设置为 `features`，这是将要创建的新向量列的名称。

然后，对 `train_data` 和 `test_data` DataFrame 应用 `assembler` 实例的 `transform()` 方法。此转换获取指定的特征列，并将它们合并到一个名为 `features` 的新向量列中。

该函数返回一个包含两个 DataFrame 的元组：一个是带有新创建的 `features` 列的 `train_data` DataFrame，另一个是应用了相同转换的 `test_data` DataFrame。

在数据准备步骤的最后一个子步骤中，代码定义了一个名为 `scale_features` 的函数，该函数接受两个参数：

*   `train_data`：包含训练数据且带有 `features` 列的 Spark DataFrame
*   `test_data`：包含测试数据且带有 `features` 列的 Spark DataFrame

此函数的目的是使用 PySpark 机器学习库提供的 `StandardScaler` 转换来缩放 `features` 列中的特征。

该函数首先创建一个 `StandardScaler` 类的实例，该类用于特征缩放。它接受两个关键参数：

*   `inputCol`：设置为 `features`，表示包含需要缩放的特征的列的名称。

*   `outputCol`：设置为 `scaled_features`，这是存储缩放后特征的新列的名称。

函数内部的 `scaler.fit(train_data)` 行使用训练数据拟合缩放器模型。这会计算缩放特征所需的均值和标准差。然后，对 `scaler_model` 应用 `transform()` 方法，分别处理 `train_data` 和 `test_data` 两个 DataFrame。此转换会缩放 `features` 列中的特征，并将缩放后的值存储在 `scaled_features` 列中。

该函数返回一个包含两个 DataFrame 的元组：一个是包含新创建的 `scaled_features` 列（其中包含缩放后的特征）的 `train_data` DataFrame，另一个是应用了相同缩放特征的 `test_data` DataFrame。

**模型训练（第 5 步）**

此步骤中的代码定义了一个名为 `train_model` 的函数，该函数接受一个参数：

*   `train_data`：一个 Spark DataFrame，包含训练数据，其中缩放后的特征位于名为 `scaled_features` 的列中。

该函数的目的是使用训练数据训练逻辑回归模型。在开始训练模型之前，代码使用 `spark.conf.set("spark.seed", "42")` 设置一个种子以确保结果可重现。这确保了模型训练期间执行的随机操作（如初始化或数据打乱）在不同运行中会产生一致的结果。

接下来，创建一个 `LogisticRegression` 类的实例。这用于定义和训练逻辑回归模型。它接受两个关键参数：

*   `labelCol`：设置为 `Outcome`，指示包含逻辑回归二元标签（0 或 1）的列。
*   `featuresCol`：设置为 `scaled_features`，指示包含将用作模型输入的缩放后特征的列。

逻辑回归模型通过 `lr` 实例上的 `fit()` 方法进行训练。提供包含缩放后特征和目标标签的 `train_data` DataFrame 作为训练数据集。训练好的逻辑回归模型随后从函数中返回。

**模型评估（第 6 步）**

在建模过程的这一步中，代码定义了一个名为 `evaluate_model` 的函数，该函数接受两个参数：

*   `lr_model`：一个训练好的逻辑回归模型
*   `test_data`：一个 Spark DataFrame，包含带有缩放后特征的测试数据

该函数的目的是使用各种评估指标来评估训练好的逻辑回归模型的性能。

函数首先使用训练好的逻辑回归模型 (`lr_model`) 的 `transform` 方法对 `test_data` 进行预测。预测结果作为新列添加到生成的 DataFrame 中，列名为 `prediction`。

函数内部的第二行将预测值转换为整数，因为它们通常是浮点格式。在下一行中，函数创建一个名为 `predictions_and_labels` 的新 DataFrame，该 DataFrame 仅从 `predictions` DataFrame 中选择 `Outcome` 和 `prediction` 列。

`evaluate_model(lr_model, test_data)` 函数内部的后续代码行计算各种评估指标。以下是每个指标的说明：

*   **真正例 (tp)**：实际结果为阳性 (1) 且模型预测也为阳性 (1) 的实例数量。
*   **真负例 (tn)**：实际结果为阴性 (0) 且模型预测也为阴性 (0) 的实例数量。
*   **假正例 (fp)**：实际结果为阴性 (0) 但模型预测为阳性 (1) 的实例数量。
*   **假负例 (fn)**：实际结果为阳性 (1) 但模型预测为阴性 (0) 的实例数量。
*   **准确率 (acc)**：正确预测的实例数与总实例数的比率。
*   **精确率 (prec)**：真正例数与真正例数和假正例数之和的比率。
*   **召回率 (rec)**：真正例数与真正例数和假负例数之和的比率。
*   **F1 分数 (f1)**：精确率和召回率的加权平均值。
*   **混淆矩阵 (confusion)**：一个 2`×`2 矩阵，表示真正例、假正例、假负例和真负例的数量。

然后，该函数打印这些性能指标，并显示 `predictions` DataFrame 中前五个实例的 `Outcome` 和 `prediction` 列。

最后一步（第 7 步）涉及调用函数，包括以下八个子步骤：

*   提供 URL 位置。
*   使用 `read_data` 读取和准备数据，过滤行并选择列。
*   使用 `split_data` 将数据拆分为训练集和测试集。
*   定义特征列列表。
*   使用 `vectorize_features` 向量化特征。
*   使用 `scale_features` 缩放特征。
*   使用 `train_model` 训练逻辑回归模型。
*   使用 `evaluate_model` 在测试集上评估模型，计算并打印评估指标。

执行这些步骤后，我们得到以下结果：

*   **准确率 (0.78)**：这表明逻辑回归模型达到了 78% 的准确率，意味着模型做出的预测中约有 78% 是正确的。
*   **精确率 (0.62)**：精确率为 0.62，这意味着在模型预测为阳性的所有实例中，62% 是真正的阳性。精确率衡量模型正确识别阳性实例且没有太多假正例的能力。
*   **召回率 (0.56)**：召回率为 0.56，表明模型成功捕获了 56% 的实际阳性实例。召回率（也称为灵敏度或真正例率）衡量模型识别所有阳性实例且不遗漏太多的能力。
*   **F1 分数 (0.59)**：F1 分数为 0.59，是精确率和召回率的加权平均值。它在两个指标之间提供了平衡，通常在需要同时考虑精确率和召回率时使用。

混淆矩阵以 2`×`2 数组的形式呈现：

*   **真正例 (TP)**：23 个实例是真正的阳性，并被正确预测为阳性。
*   **假正例 (FP)**：14 个实例实际上是阴性，但被错误地预测为阳性。
*   **假负例 (FN)**：18 个实例实际上是阳性，但被错误地预测为阴性。
*   **真负例 (TN)**：91 个实例是真正的阴性，并被正确预测为阴性。

输出还显示了测试数据中前五个实例的 `Outcome`（真实标签）和 `prediction`（预测标签）列。这提供了模型预测与这些实例的真实结果相比如何的快速概览。结果表明，列出的五个预测中有四个（即 80%）与实际标签匹配。这个准确率略高于考虑所有样本的总体准确率 78%。

## 整合所有内容

在本章的前面部分，我们展示了代码片段，分别演示了建模过程的每个步骤。然而，在本节中，我们旨在将所有这些步骤中的相关代码整合到一个代码块中。这将使数据科学家能够全面了解代码片段如何协同工作，并允许他们将代码作为一个整体来执行。将每个步骤的代码组合到一个块中，也将简化修改代码的任务，并便于使用不同参数和配置进行实验。

### Scikit-Learn

在本小节中，提供了使用皮马印第安人糖尿病数据集准备数据、构建和训练逻辑回归模型以及在测试数据上对其进行评估的完整 Scikit-Learn 代码：

```
[In]: import pandas as pd
[In]: from sklearn.model_selection import train_test_split
[In]: from sklearn.linear_model import LogisticRegression
[In]: from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, confusion_matrix
[In]: from sklearn.preprocessing import StandardScaler
[In]: def load_diabetes_csv(url):
"""
从 URL 加载 diabetes.csv 文件并返回一个 Pandas DataFrame。

参数：
url (str)：CSV 文件的 URL。

返回：
pandas.DataFrame：加载的 DataFrame。
"""
pandas_df = pd.read_csv(url)
return pandas_df
[In]: def split_data(X, y, test_size=0.2, random_state=42):
"""
将数据拆分为训练集和测试集。

参数：
X (pd.DataFrame)：特征矩阵。
y (pd.Series)：目标变量。
test_size (float)：用于测试拆分的数据集比例。
random_state (int)：随机数生成器的种子。

返回：
tuple：X_train, X_test, y_train, y_test。
"""
return train_test_split(X, y, test_size=test_size,
random_state=random_state)
[In]: def scale_data(X_train, X_test):
"""
使用 StandardScaler 对特征进行缩放。

参数：
X_train (pd.DataFrame)：缩放后的训练特征。
X_test (pd.DataFrame)：缩放后的测试特征。

返回：
tuple：X_train_scaled, X_test_scaled。
"""
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)
return X_train_scaled, X_test_scaled
[In]: def train_lr(X_train, y_train):
"""
在缩放后的训练数据上训练逻辑回归模型。

参数：
X_train (pd.DataFrame)：缩放后的训练特征。
y_train (pd.Series)：训练目标变量。

返回：
sklearn.linear_model.LogisticRegression：训练好的逻辑回归模型。
"""
model = LogisticRegression()
model.fit(X_train, y_train)
return model
[In]: def evaluate_model(model, X_test, y_test):
"""
使用多种指标评估模型并打印结果。

参数：
model (sklearn.linear_model.LogisticRegression)：训练好的逻辑回归模型。
X_test (pd.DataFrame)：缩放后的测试特征。
y_test (pd.Series)：测试目标变量。
"""
y_pred = model.predict(X_test)
accuracy = accuracy_score(y_test, y_pred)
precision = precision_score(y_test, y_pred)
recall = recall_score(y_test, y_pred)
f1 = f1_score(y_test, y_pred)
confusion = confusion_matrix(y_test, y_pred)
print(f'准确率: {accuracy:.2f}')
print(f'精确率: {precision:.2f}')
print(f'召回率: {recall:.2f}')
print(f'F1 分数: {f1:.2f}')
print(f'混淆矩阵:\n{confusion}')
result_df = pd.DataFrame({'实际值': y_test, '预测值':
y_pred})
print(result_df.head())

# 使用这些函数

# 定义数据集的 URL
[In]: url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/diabetes.csv')

# 调用 load_diabetes_csv() 函数读取数据集
[In]: pandas_df = load_diabetes_csv(url)

# 指定感兴趣的列
[In]: columns = ['Pregnancies', 'Glucose', 'BloodPressure',
'BMI', 'DiabetesPedigreeFunction', 'Age', 'Outcome']

# 仅选择指定的列，并过滤掉关键特征中值为零的行
[In]: pandas_df = pandas_df[columns]
[In]: pandas_df = pandas_df.loc[(pandas_df['Glucose'] != 0)
& (pandas_df['BloodPressure'] != 0)
& (pandas_df['BMI'] != 0)]

# 分离特征 (X) 和目标变量 (y)
[In]: X = pandas_df.drop('Outcome', axis=1)
[In]: y = pandas_df['Outcome']

# 将数据拆分为训练集和测试集
[In]: X_train, X_test, y_train, y_test = split_data(X, y)

# 对训练集和测试集中的特征进行缩放
[In]: X_train_scaled, X_test_scaled = scale_data(X_train, X_test)

# 在缩放后的训练数据上训练逻辑回归模型
[In]: model = train_lr(X_train_scaled, y_train)

# 在缩放后的测试数据上评估模型
[In]: evaluate_model(model, X_test_scaled, y_test)
```

### PySpark

在本小节中，我们提供与 Scikit-Learn 代码等效的 PySpark 代码，用于准备数据、构建和训练逻辑回归模型，并使用皮马印第安人糖尿病数据集对其进行评估。

```
[In]: from pyspark.sql import SparkSession
[In]: from pyspark.ml.classification import LogisticRegression
[In]: from pyspark.ml.feature import VectorAssembler, StandardScaler
[In]: from pyspark.sql.functions import col
[In]: import pandas as pd
[In]: spark = SparkSession.builder.appName("DiabetesPrediction").getOrCreate()
[In]: def read_data(url):
"""
从给定的 URL 读取数据，并将其转换为 Spark DataFrame。

参数：
url (str)：CSV 文件的 URL。

返回：
pyspark.sql.DataFrame：包含数据的 Spark DataFrame。
"""
pandas_df = pd.read_csv(url)
spark_df = spark.createDataFrame(pandas_df)
return spark_df
[In]: def split_data(spark_df):
"""
将数据拆分为训练集和测试集。

参数：
spark_df (pyspark.sql.DataFrame)：输入的 Spark DataFrame。

返回：
tuple：包含训练集和测试集 Spark DataFrame 的元组。
"""
return spark_df.randomSplit([0.8, 0.2], seed=42)
[In]: def vectorize_features(train_data, test_data, feature_cols):
"""
将特征列转换为单个向量列。

参数：
train_data (pyspark.sql.DataFrame)：训练集 Spark DataFrame。
test_data (pyspark.sql.DataFrame)：测试集 Spark DataFrame。
feature_cols (list)：特征列名称列表。

返回：
tuple：包含向量化后的训练集和测试集 Spark DataFrame 的元组。
"""
assembler = VectorAssembler(inputCols=feature_cols,
outputCol='features')
return assembler.transform(train_data),
assembler.transform(test_data)
[In]: def scale_features(train_data, test_data):
"""
对数据集中的特征进行缩放。

参数：
train_data (pyspark.sql.DataFrame)：训练集 Spark DataFrame。
test_data (pyspark.sql.DataFrame)：测试集 Spark DataFrame。

返回：
tuple：包含缩放后的训练集和测试集 Spark DataFrame 的元组。
"""
scaler = StandardScaler(inputCol='features',
outputCol='scaled_features')
scaler_model = scaler.fit(train_data)
return scaler_model.transform(train_data),
scaler_model.transform(test_data)
[In]: def train_model(train_data):
"""
在训练数据集上训练逻辑回归模型。

参数：
train_data (pyspark.sql.DataFrame)：训练集 Spark DataFrame。

返回：
pyspark.ml.classification.LogisticRegressionModel：训练好的逻辑回归模型。
"""

# 设置随机种子以确保可重复性
spark.conf.set("spark.seed", "42")
lr = LogisticRegression(labelCol='Outcome',
featuresCol='scaled_features')
return lr.fit(train_data)
[In]: def evaluate_model(lr_model, test_data):
"""
在测试数据集上评估训练好的模型。

参数：
lr_model
(pyspark.ml.classification.LogisticRegressionModel)：训练好的逻辑回归模型。
test_data (pyspark.sql.DataFrame)：测试集 Spark DataFrame。
"""
predictions = lr_model.transform(test_data)
predictions = predictions.withColumn('prediction',
col('prediction').cast('int'))
predictions_and_labels = predictions.select(['Outcome',
'prediction'])
tp = predictions_and_labels[(predictions_and_labels.Outcome
== 1) & (predictions_and_labels.prediction == 1)].count()
tn = predictions_and_labels[(predictions_and_labels.Outcome
== 0) & (predictions_and_labels.prediction == 0)].count()
fp = predictions_and_labels[(predictions_and_labels.Outcome
== 0) & (predictions_and_labels.prediction == 1)].count()
fn = predictions_and_labels[(predictions_and_labels.Outcome
== 1) & (predictions_and_labels.prediction == 0)].count()
acc = (tp + tn) / predictions_and_labels.count()
prec = tp / (tp + fp)
rec = tp / (tp + fn)
f1 = 2 * prec * rec / (prec + rec)
confusion = [[tp, fp], [fn, tn]]

# 打印评估指标
print(f'准确率: {acc:.2f}')
print(f'精确率: {prec:.2f}')
print(f'召回率: {rec:.2f}')
print(f'F1 分数: {f1:.2f}')
print(f'混淆矩阵:\n{confusion}')
predictions.select('Outcome', 'prediction').show(5)
[In]: url = ('https://raw.githubusercontent.com/abdelaziztestas/'
'spark_book/main/diabetes.csv')
[In]: spark_df = read_data(url)
[In]: columns = ['Pregnancies', 'Glucose', 'BloodPressure',
'BMI', 'DiabetesPedigreeFunction', 'Age', 'Outcome']
[In]: spark_df = spark_df.select(columns)
[In]: spark_df = spark_df.filter((col('Glucose') != 0)
& (col('BloodPressure') != 0)
& (col('BMI') != 0))
[In]: train_data, test_data = split_data(spark_df)
[In]: feature_cols = ['Pregnancies', 'Glucose', 'BloodPressure', 'BMI', 'DiabetesPedigreeFunction', 'Age']
[In]: train_data, test_data = vectorize_features(train_data, test_data, feature_cols)
[In]: train_data, test_data = scale_features(train_data, test_data)
[In]: lr_model = train_model(train_data)
[In]: evaluate_model(lr_model, test_data)
```

## 总结

本章介绍了逻辑回归，并演示了如何在 Scikit-Learn 和 PySpark 中使用逻辑回归算法构建分类模型。此外，本章还讨论了逻辑回归的优缺点，并深入探讨了标准评估指标，包括准确率、精确率、召回率、F1 分数和混淆矩阵。同时，还使用 Pandas 和 PySpark 代码来比较这两个库在处理数据操作和探索方面的异同。

在下一章中，我们将继续监督学习中分类算法的学习之旅。具体来说，我们将介绍一种基于决策树的逻辑回归替代分类模型。我们将演示如何准备数据、构建、训练和评估模型，并对新的、未见过的数据进行预测。届时将同时使用 Scikit-Learn 和 PySpark，并对 Pandas 和 PySpark 进行比较。

